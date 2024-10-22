## 系统调用 `sys_task_info` 的实现与改进

在现代操作系统中，任务管理是一个关键组件，负责创建、调度和维护任务的状态。随着系统的复杂性增加，我们希望在任务管理中引入更多的信息，以便对当前任务进行更好的监控与管理。在这一节中，我们将介绍一个新的系统调用 `sys_task_info`，该系统调用旨在获取当前任务的相关信息，包括任务的状态、系统调用次数及运行时间。

### 1. 系统调用接口定义

我们首先定义 `sys_task_info` 函数，其原型如下：

```rust
fn sys_task_info(ti: *mut TaskInfo) -> isize;
```

该系统调用的 syscall ID 为 `410`。`TaskInfo` 结构体的定义如下：

```rust
struct TaskInfo {
    status: TaskStatus,
    syscall_times: [u32; MAX_SYSCALL_NUM],
    time: usize,
}
```

- **`status`**: 表示任务的状态。
- **`syscall_times`**: 记录任务调用的系统调用次数。
- **`time`**: 记录任务从第一次被调度到当前的时间，单位为毫秒。

### 2. 任务控制块（TCB）

任务信息的存储在任务控制块（TCB）中。每个任务都有一个 `TaskControlBlock` 结构体，其中包含任务的状态、上下文和系统调用计数器等信息。

```rust
pub struct TaskControlBlock {
    pub task_status: TaskStatus,
    pub task_cx: TaskContext,
    pub task_syscall_times: [u32; MAX_SYSCALL_NUM],
    pub task_time: usize,
}
```

### 3. `sys_task_info` 的实现

接下来，我们实现 `sys_task_info` 函数：

```rust
pub fn sys_task_info(ti: *mut TaskInfo) -> isize {
    trace!("kernel: sys_task_info");

    if ti.is_null() {
        return -1; 
    }
    if let Some(_) = TaskInfo::modify_task_info(ti) {
        // 成功更新任务信息
    } else {
        return -1; 
    }

    0 
}
```

#### 3.1 更新任务信息

为了更新任务信息，我们实现了 `TaskInfo::modify_task_info` 方法：

```rust
impl TaskInfo {
    pub fn modify_task_info(task_info: *mut Self) -> Option<()> {
        unsafe {
            (*task_info).status = Running; // 获取当前任务状态
            (*task_info).syscall_times = get_syscall_times(); // 获取系统调用次数
            (*task_info).time = get_task_time(); // 获取任务运行时间
        }
        Some(())
    }
}
```

该方法通过指针获取当前任务的信息，并将这些信息更新到 `TaskInfo` 结构中。

### 4. 系统调用处理

在系统调用处理函数中，我们将 `sys_task_info` 添加到 syscall 调用表中：

```rust
pub fn syscall(syscall_id: usize, args: [usize; 3]) -> isize {
    update_syscall_times(syscall_id);
    match syscall_id {
        // 其他系统调用...
        SYSCALL_TASK_INFO => sys_task_info(args[0] as *mut TaskInfo),
        _ => panic!("Unsupported syscall_id: {}", syscall_id),
    }
}
```

### 5. 整体设计思路

1. **任务状态管理**: `TaskControlBlock` 结构体包含了任务的状态、上下文及系统调用次数等信息，使得每个任务的管理更加高效。
  
2. **系统调用的灵活性**: 通过动态更新任务信息，系统调用 `sys_task_info` 使得用户空间可以方便地获取到当前任务的详细信息。

3. **错误处理**: 在函数中对空指针进行了处理，确保系统调用的安全性和稳定性。

### 6. 总结

通过实现 `sys_task_info` 系统调用，我们增强了任务管理系统的能力，使得开发者能够轻松获取任务的详细信息。这一改进将为后续任务调度和管理的优化奠定基础，提升系统的可监控性和可调试性。

这种设计不仅能帮助开发者在调试阶段快速获取任务信息，还能为操作系统性能优化提供必要的数据支持。未来，我们可以考虑在此基础上增加更多的系统调用，以提供更全面的任务管理功能。