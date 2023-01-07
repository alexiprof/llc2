This project aims to provide a support for debugging stackfull coroutines
from [boost.Coroutine2](https://www.boost.org/doc/libs/1_81_0/libs/coroutine2/doc/html/index.html). <br>
An **ll**db plugin for boost.**C**oroutine**2**, hence the name **llc2**.

The plugin is very limited in functionality and is focused exclusively
on [uServer](https://github.com/userver-framework/userver), <br>
and although it may work for other use cases this isn't a priority for now.

### Quick start

Start an LLDB session, load the plugin, configure it and backtrace sleeping coroutines.

```
(lldb) plugin load /home/itrofimow/work/llc2/build/libllc2.so
(lldb) llc2 init -s 262144 -c fcontext
LLC2 plugin initialized. Settings:
stack_size: 262144
context implementation: fcontext
with magic: false
filter by: (null)
truncate at: (null)
(lldb) llc2 bt 
----------------------------------------------------------------------------------------- found sleeping coroutine -----------------------------------------------------------------------------------------
frame #0: 0x0000000000304dd9 userver-samples-hello_service`boost::coroutines2::detail::pull_coroutine<userver::engine::impl::TaskContext*>::control_block::resume() [inlined] std::enable_if<__and_<std::__not_<std::__is_tuple_like<void*> >, std::is_move_constructible<void*>, std::is_move_assignable<void*> >::value, void>::type std::swap<void*>(__a=0x00007fffffffd230, __b=<unavailable>) at move.h:204:19
frame #1: 0x0000000000304dd9 userver-samples-hello_service`boost::coroutines2::detail::pull_coroutine<userver::engine::impl::TaskContext*>::control_block::resume() [inlined] boost::context::fiber::swap(this=0x00007fffffffd230, other=<unavailable>) at fiber_fcontext.hpp:335
frame #2: 0x0000000000304dd9 userver-samples-hello_service`boost::coroutines2::detail::pull_coroutine<userver::engine::impl::TaskContext*>::control_block::resume() at fiber_fcontext.hpp:269
frame #3: 0x0000000000304dd9 userver-samples-hello_service`boost::coroutines2::detail::pull_coroutine<userver::engine::impl::TaskContext*>::control_block::resume(this=0x00007fffffffd230) at pull_control_block_cc.ipp:147
frame #4: 0x0000000000301146 userver-samples-hello_service`userver::engine::impl::TaskContext::Sleep(userver::engine::impl::WaitStrategy&) [inlined] boost::coroutines2::detail::pull_coroutine<userver::engine::impl::TaskContext*>::operator(this=<unavailable>)() at pull_coroutine.ipp:77:10
frame #5: 0x000000000030113e userver-samples-hello_service`userver::engine::impl::TaskContext::Sleep(this=0x00007fffdc831400, wait_strategy=0x00007fffffffd310) at task_context.cpp:326
frame #6: 0x000000000039cbcd userver-samples-hello_service`userver::engine::io::FdPoller::Wait(userver::engine::Deadline) [inlined] userver::engine::io::FdPoller::Impl::DoWait(this=<unavailable>, deadline=<unavailable>) at fd_poller.cpp:127:22
frame #7: 0x000000000039cb7b userver-samples-hello_service`userver::engine::io::FdPoller::Wait(this=0x00007fffdc83e200, deadline=<unavailable>) at fd_poller.cpp:184
frame #8: 0x0000000000396fe4 userver-samples-hello_service`userver::engine::io::Socket::Accept(userver::engine::Deadline) [inlined] userver::engine::io::impl::Direction::Wait(this=<unavailable>, deadline=Deadline @ r14) at fd_control.cpp:72:58
frame #9: 0x0000000000396fdc userver-samples-hello_service`userver::engine::io::Socket::Accept(userver::engine::Deadline) [inlined] userver::engine::io::Socket::WaitReadable(this=0x00007fffdc81f3e0, deadline=Deadline @ r14) at socket.cpp:209
frame #10: 0x0000000000396fd8 userver-samples-hello_service`userver::engine::io::Socket::Accept(this=0x00007fffdc81f3e0, deadline=Deadline @ r14) at socket.cpp:348
----------------------------------------------------------------------------------------- found sleeping coroutine -----------------------------------------------------------------------------------------
...
```

### Build

Install lldb development headers (liblldb-XX-dev) of matching lldb version, <br>
fix include_directories in CMakeLists.txt (yes i was too lazy to automate it) and then <br>
`mkdir build && cd build && cmake -DCMAKE_BUILD_TYPE=Release .. && make` <br>
Now you have a `libllc2.so` and should be able to `plugin load` it into lldb.