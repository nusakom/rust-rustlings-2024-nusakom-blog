
###  **设计思路**

我们将实现以下几个功能：

1. **任务信息结构**：定义一个结构体 `TaskInfo`，用来存储任务的状态、系统调用次数以及运行时间。
2. **系统调用统计**：实现 `get_syscall_times()` 和 `get_task_time()` 函数，分别用于获取当前任务的系统调用次数和运行时间。
3. **系统调用接口**：在 `syscall()` 函数中实现对不同系统调用的分发逻辑，确保能够调用任务信息查询的系统调用。
4. **信息更新与返回**：实现一个 `sys_task_info()` 系统调用，允许用户程序查询当前任务的信息。

### **具体实现**

#### 任务信息结构

首先，我们定义 `TaskInfo` 结构体。这个结构体将包含任务的状态、系统调用的次数和总运行时间。我们还需要一个 `TaskStatus` 类型来表示任务的状态。

```rust
#[repr(C)]
#[derive(Debug)]
pub struct TimeVal {
    pub sec: usize,
    pub usec: usize,
}

pub struct TaskInfo {
    status: TaskStatus,
    syscall_times: [u32; MAX_SYSCALL_NUM],
    time: usize,
}
```

####  获取系统调用次数与运行时间

我们需要定义两个函数：`get_syscall_times()` 和 `get_task_time()`。它们将从任务的上下文中提取信息，并返回相应的数据。

```rust
pub fn get_syscall_times() -> [u32; MAX_SYSCALL_NUM] {
    // 在这里，你需要访问任务结构体中的 syscall_times，并返回该数组
}

pub fn get_task_time() -> usize {
    // 返回任务的总运行时间（以微秒为单位）
    // 这里通过调度器或其他计时器机制计算任务的累计运行时间
}
```

这些函数将用于在系统调用时获取当前任务的状态。

####  系统调用分发

接下来，我们需要一个 `syscall()` 函数，它将根据 `syscall_id` 分发请求。每次系统调用时，我们会更新该任务的系统调用次数。

```rust
pub fn syscall(syscall_id: usize, args: [usize; 3]) -> isize {
    update_syscall_times(syscall_id); // 更新系统调用次数
    match syscall_id {
        SYSCALL_WRITE => sys_write(args[0], args[1] as *const u8, args[2]),
        SYSCALL_EXIT => sys_exit(args[0] as i32),
        SYSCALL_YIELD => sys_yield(),
        SYSCALL_GET_TIME => sys_get_time(args[0] as *mut TimeVal, args[1]),
        SYSCALL_TASK_INFO => sys_task_info(args[0] as *mut TaskInfo),
        _ => panic!("Unsupported syscall_id: {}", syscall_id),
    }
}
```

在这个函数中，我们首先调用 `update_syscall_times(syscall_id)` 更新系统调用的统计信息。然后，通过 `match` 语句处理各种系统调用。

####  更新与查询任务信息

为了查询任务的信息，我们需要实现一个 `modify_task_info` 函数，来更新传入的 `TaskInfo` 结构体，并通过 `sys_task_info()` 系统调用来返回信息。

```rust
impl TaskInfo {
    pub fn modify_task_info(task_info: *mut Self) -> Option<()> {
        unsafe {
            (*task_info).status = Running;  // 假设当前任务正在运行
            (*task_info).syscall_times = get_syscall_times();  // 获取系统调用次数
            (*task_info).time = get_task_time();  // 获取任务的运行时间
        }
        Some(())
    }
}

pub fn sys_task_info(ti: *mut TaskInfo) -> isize {
    trace!("kernel: sys_task_info");
    match TaskInfo::modify_task_info(ti) {
        None => -1,
        Some(_) => 0
    }
}
```

在 `modify_task_info()` 函数中，我们更新任务状态、系统调用次数和运行时间，并通过 `sys_task_info()` 将这些信息返回给调用的用户程序。

###  **总结与扩展**

通过上述实现，我们构建了一个简单而有效的系统调用管理模块。我们可以通过收集任务的系统调用次数和运行时间，来优化操作系统的性能。这些统计信息对于后续的性能分析和任务调度算法优化都非常重要。

在未来的工作中，可以考虑进一步扩展这些功能，例如：

- **支持更多系统调用**：根据需要添加文件操作、网络功能等系统调用。
- **性能分析工具**：开发工具分析系统调用统计数据，找出性能瓶颈。
- **动态调度算法**：基于任务的运行时间和系统调用行为，动态调整任务的优先级。

###  **代码实现**

```rust
//! Process management syscalls
use crate::{
    config::MAX_SYSCALL_NUM,
    task::{exit_current_and_run_next, suspend_current_and_run_next, TaskStatus},
    timer::get_time_us,
};


/// 任务信息
#[allow(dead_code)]
pub struct TaskInfo {
    /// 任务的生命周期状态
    status: TaskStatus,
    /// 任务调用的系统调用次数
    syscall_times: [u32; MAX_SYSCALL_NUM],
    /// 任务的总运行时间
    time: usize,
}

impl TaskInfo {
    pub fn modify_task_info(task_info: *mut Self) -> Option<()> {
        unsafe {
            (*task_info).status = Running;
            (*task_info).syscall_times = get_syscall_times();
            (*task_info).time = get_task_time();
        }
        Some(())
    }
}

/// 任务退出并提交退出代码
pub fn sys_exit(exit_code: i32) -> ! {
    trace!("[kernel] Application exited with code {}", exit_code);
    exit_current_and_run_next();
    panic!("Unreachable in sys_exit!");
}

/// 当前任务放弃资源以供其他任务使用
pub fn sys_yield() -> isize {
    trace!("kernel: sys_yield");
    suspend_current_and_run_next();
    0
}

/// 获取时间（以秒和微秒为单位）
pub fn sys_get_time(ts: *mut TimeVal, _tz: usize) -> isize {
    trace!("kernel: sys_get_time");
    let us = get_time_us();
    unsafe {
        *ts = TimeVal {
            sec: us / 1_000_000,
            usec: us % 1_000_000,
        };
    }
    0
}

/// 完成 sys_task_info 以通过测试用例
pub fn sys_task_info(ti: *mut TaskInfo) -> isize {
    trace!("kernel: sys_task_info");
    match TaskInfo::modify_task_info(ti) {
        None => -1,
        Some(_) => 0
    }
}

pub fn get_syscall_times() -> [u32; MAX_SYSCALL_NUM] {
    // 返回任务的系统调用次数数组
    // 实现具体的逻辑以获取系统调用次数
}

pub fn get_task_time() -> usize {
    // 返回任务的总运行时间（以微秒为单位）
    // 实现具体的逻辑以计算运行时间
}
```