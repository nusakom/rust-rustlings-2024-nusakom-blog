# 重写系统调用：恢复 `sys_get_time` 和 `sys_task_info`，实现简化版的 `mmap` 和 `munmap`

在引入虚拟内存机制后，原有内核中的系统调用实现可能会失效。在本篇博客中，我们将探讨如何重写 `sys_get_time` 和 `sys_task_info` 函数，以恢复其正常功能，并实现简化版本的内存映射系统调用 `mmap` 和 `munmap`。

## 一、重写 `sys_get_time` 和 `sys_task_info`

### 1.1 系统调用概述

在操作系统中，系统调用是用户空间与内核空间之间的接口。`sys_get_time` 函数通常用于获取系统时间，而 `sys_task_info` 用于获取当前任务的信息。在引入虚拟内存后，我们需要重新实现这两个函数，以确保它们能够正常工作。

### 1.2 重写 `sys_get_time`

`sys_get_time` 函数的实现可以通过查询系统时钟来获得当前时间。以下是重写的示例代码：

```rust
use crate::time::get_system_time;

pub fn sys_get_time() -> Time {
    get_system_time()
}
```

在这个实现中，我们调用 `get_system_time` 函数，它返回当前的系统时间。这样，我们就能有效地恢复 `sys_get_time` 的功能。

### 1.3 重写 `sys_task_info`

`sys_task_info` 函数需要获取当前任务的相关信息，例如任务的状态、优先级等。下面是一个简单的实现：

```rust
use crate::task::{get_current_task, TaskInfo};

pub fn sys_task_info() -> TaskInfo {
    let current_task = get_current_task();
    current_task.get_info()
}
```

这里，我们使用 `get_current_task` 函数获取当前任务，并调用 `get_info` 方法返回任务的信息。这确保了在虚拟内存机制下，`sys_task_info` 函数依然能够正常工作。

## 二、实现简化版的 `mmap` 和 `munmap`

### 2.1 `mmap` 的功能

`mmap` 系统调用在 Linux 中主要用于将文件映射到内存中。然而在我们的实验中，我们将简化其功能，仅用于申请内存。以下是简化版 `mmap` 的实现：

```rust
use crate::memory::{allocate_memory, MemoryRegion};

pub fn sys_mmap(size: usize) -> *mut u8 {
    let addr = allocate_memory(size);
    if addr.is_null() {
        return std::ptr::null_mut(); // 申请失败，返回 null
    }
    addr
}
```

在这个实现中，我们调用 `allocate_memory` 函数来分配指定大小的内存，并返回分配内存的指针。如果内存分配失败，则返回空指针。

### 2.2 `munmap` 的功能

`munmap` 系统调用用于释放之前通过 `mmap` 申请的内存。在我们的实现中，`munmap` 将调用相应的内存释放函数。下面是 `munmap` 的实现示例：

```rust
use crate::memory::deallocate_memory;

pub fn sys_munmap(addr: *mut u8, size: usize) {
    deallocate_memory(addr, size);
}
```

在这里，我们调用 `deallocate_memory` 函数释放指定地址和大小的内存。这样，我们就实现了简化版的 `munmap` 功能。

## 总结

通过重新实现 `sys_get_time` 和 `sys_task_info`，我们成功恢复了它们在引入虚拟内存机制后的正常功能。此外，我们也实现了简化版的 `mmap` 和 `munmap`，使其能够仅用于内存申请和释放。这些改动为内核的稳定性和功能完整性提供了保障。

希望这篇博客能帮助你理解如何在虚拟内存机制下重写系统调用，以及如何简化内存映射功能。如果你有任何问题或建议，欢迎在评论区分享！
