---
author: "Denis Nu\u021Biu"
categories:
- Programming
- Reverse Engineering
- Security
date: '2019-10-13T13:51:07+00:00'
guid: https://nuculabs.wordpress.com/?p=502
id: 502
layout: post
permalink: /2019/10/13/ghidra-scripting-annotating-linux-system-calls/
tags:
- Ghidra
- scripting
title: 'Ghidra Scripting: Annotating Linux system calls'
---
I had some fun this weekend messing around with [Ghidra](https://ghidra-sre.org/). Having such a powerful tool for free is truly a game changer.


To start scripting in Ghidra, I downloaded the latest Eclipse for Java Developers [Version: 2019-09 R (4.13.0)](https://www.eclipse.org/downloads/packages/release/2019-09/r/eclipse-ide-java-developers), Ghidra and [Open JDK](https://jdk.java.net/), I believe any JDK version 11+ will work.


After downloading the JDK, extract the zip, put it somewhere and modify your PATH and JAVA\_HOME environment variables to point to it:


```
export JAVA_HOME="/home/denis/jdk-13"
export PATH="/home/denis/jdk-13/bin:$PATH"
```


Next, you should start Ghidra in order to associate the JDK with it, now close it, then start Eclipse. In Eclipse, install the GhidraDev extension from the archive which is found in `ghidra_9.1-BETA_DEV/Extensions/Eclipse`.


After installing GhidraDev, you may now create a new Ghidra Scripts project.


- 
![](/wp-content/uploads/2019/10/eclipse_0.png?w=619)
- 
![](/wp-content/uploads/2019/10/eclipse_1.png?w=616)
- 
![](/wp-content/uploads/2019/10/eclipse_2.png?w=621)
- 
![](/wp-content/uploads/2019/10/eclipse_3.png?w=622)
- 
![](/wp-content/uploads/2019/10/eclipse_4.png?w=622)
- 
![](/wp-content/uploads/2019/10/eclipse_5.png?w=1024)


This is cool because all your scripts are stored in `~/ghidra_scripts` and linked to the project, if you change the JDK you may safely delete the project and recreate it without losing files.


If you click **Run** or **Debug**, Eclipse will be prompted to start a new Ghidra or Ghidra Headless instance, by clicking Debug, you can set breakpoints in the scripts you want to Debug. Run the scripts by opening *Ghidra’s Script Manager* and clicking *Run*. When the code reaches the breakpoints, the script’s execution will stop and you can debug it in Eclipse.


- ![](/wp-content/uploads/2019/10/eclipse_6.png?w=1024)


You can then *resume* the script, *cancel* it from Ghidra or *modify* the script without having to restart Ghidra. If you hit the **stop** button Ghidra will shut down, I’ve encountered some popups complaining that hot code replacement won’t work when I tried to save a script, I ignored it and it worked just fine.


By opening Ghidra Script Manager and right clicking on a script then clicking on **Ghidra API Help** will open a web browser with the scripting documentation.


- - - - - -


 To make my script I started with `InstructionSearchScript.java` as a base, iterated through all instructions, saved the value of EAX, searched for `int 0x80` and added a plate comment with the system call’s name.


The final script:


https://github.com/dnutiu/GhidraScripts/blob/master/CommentLinuxSysCall.java


Please note the ugly `systemCallNames.put(1, "sys_exit");`, I haven’t used Java that much and I couldn’t find a good way to initialize maps in an inline statement. If you have a suggestion please write it in the comments.


To get the system calls names I used a reference table from [Shell Storm](http://shell-storm.org/shellcode/files/syscalls.html) and some JavaScript to parse the table.


```
table = document.getElementsByTagName("table")[0];
tableRows = table.children[0].children;

// for each tableRows starting from 1 skip header
for (index = 1; index < tableRows.length; index++) {
    rowData = tableRows[index].children;
    syscallValue = rowData[0].innerText;
    syscallName = rowData[1].innerText;
    console.log(`systemCallNames.put(${syscallValue}, "${syscallName}");`)
}
```


```
VM66:9 systemCallNames.put(0, "sys_read");
VM66:9 systemCallNames.put(1, "sys_write");
VM66:9 systemCallNames.put(2, "sys_open");
VM66:9 systemCallNames.put(3, "sys_close");
VM66:9 systemCallNames.put(4, "sys_stat");
VM66:9 systemCallNames.put(5, "sys_fstat");
VM66:9 systemCallNames.put(6, "sys_lstat");
VM66:9 systemCallNames.put(7, "sys_poll");
VM66:9 systemCallNames.put(8, "sys_lseek");
VM66:9 systemCallNames.put(9, "sys_mmap");
VM66:9 systemCallNames.put(10, "sys_mprotect");
VM66:9 systemCallNames.put(11, "sys_munmap");
VM66:9 systemCallNames.put(12, "sys_brk");
VM66:9 systemCallNames.put(13, "sys_rt_sigaction");
VM66:9 systemCallNames.put(14, "sys_rt_sigprocmask");
VM66:9 systemCallNames.put(15, "sys_rt_sigreturn");
VM66:9 systemCallNames.put(16, "sys_ioctl");
VM66:9 systemCallNames.put(17, "sys_pread64");
VM66:9 systemCallNames.put(18, "sys_pwrite64");
VM66:9 systemCallNames.put(19, "sys_readv");
VM66:9 systemCallNames.put(20, "sys_writev");
VM66:9 systemCallNames.put(21, "sys_access");
VM66:9 systemCallNames.put(22, "sys_pipe");
VM66:9 systemCallNames.put(23, "sys_select");
VM66:9 systemCallNames.put(24, "sys_sched_yield");
VM66:9 systemCallNames.put(25, "sys_mremap");
VM66:9 systemCallNames.put(26, "sys_msync");
VM66:9 systemCallNames.put(27, "sys_mincore");
VM66:9 systemCallNames.put(28, "sys_madvise");
VM66:9 systemCallNames.put(29, "sys_shmget");
VM66:9 systemCallNames.put(30, "sys_shmat");
VM66:9 systemCallNames.put(31, "sys_shmctl");
VM66:9 systemCallNames.put(32, "sys_dup");
VM66:9 systemCallNames.put(33, "sys_dup2");
VM66:9 systemCallNames.put(34, "sys_pause");
VM66:9 systemCallNames.put(35, "sys_nanosleep");
VM66:9 systemCallNames.put(36, "sys_getitimer");
VM66:9 systemCallNames.put(37, "sys_alarm");
VM66:9 systemCallNames.put(38, "sys_setitimer");
VM66:9 systemCallNames.put(39, "sys_getpid");
VM66:9 systemCallNames.put(40, "sys_sendfile");
VM66:9 systemCallNames.put(41, "sys_socket");
VM66:9 systemCallNames.put(42, "sys_connect");
VM66:9 systemCallNames.put(43, "sys_accept");
VM66:9 systemCallNames.put(44, "sys_sendto");
VM66:9 systemCallNames.put(45, "sys_recvfrom");
VM66:9 systemCallNames.put(46, "sys_sendmsg");
VM66:9 systemCallNames.put(47, "sys_recvmsg");
VM66:9 systemCallNames.put(48, "sys_shutdown");
VM66:9 systemCallNames.put(49, "sys_bind");
VM66:9 systemCallNames.put(50, "sys_listen");
VM66:9 systemCallNames.put(51, "sys_getsockname");
VM66:9 systemCallNames.put(52, "sys_getpeername");
VM66:9 systemCallNames.put(53, "sys_socketpair");
VM66:9 systemCallNames.put(54, "sys_setsockopt");
VM66:9 systemCallNames.put(55, "sys_getsockopt");
VM66:9 systemCallNames.put(56, "sys_clone");
VM66:9 systemCallNames.put(57, "sys_fork");
VM66:9 systemCallNames.put(58, "sys_vfork");
VM66:9 systemCallNames.put(59, "sys_execve");
VM66:9 systemCallNames.put(60, "sys_exit");
VM66:9 systemCallNames.put(61, "sys_wait4");
VM66:9 systemCallNames.put(62, "sys_kill");
VM66:9 systemCallNames.put(63, "sys_uname");
VM66:9 systemCallNames.put(64, "sys_semget");
VM66:9 systemCallNames.put(65, "sys_semop");
VM66:9 systemCallNames.put(66, "sys_semctl");
VM66:9 systemCallNames.put(67, "sys_shmdt");
VM66:9 systemCallNames.put(68, "sys_msgget");
VM66:9 systemCallNames.put(69, "sys_msgsnd");
VM66:9 systemCallNames.put(70, "sys_msgrcv");
VM66:9 systemCallNames.put(71, "sys_msgctl");
VM66:9 systemCallNames.put(72, "sys_fcntl");
VM66:9 systemCallNames.put(73, "sys_flock");
VM66:9 systemCallNames.put(74, "sys_fsync");
VM66:9 systemCallNames.put(75, "sys_fdatasync");
VM66:9 systemCallNames.put(76, "sys_truncate");
VM66:9 systemCallNames.put(77, "sys_ftruncate");
VM66:9 systemCallNames.put(78, "sys_getdents");
VM66:9 systemCallNames.put(79, "sys_getcwd");
VM66:9 systemCallNames.put(80, "sys_chdir");
VM66:9 systemCallNames.put(81, "sys_fchdir");
VM66:9 systemCallNames.put(82, "sys_rename");
VM66:9 systemCallNames.put(83, "sys_mkdir");
VM66:9 systemCallNames.put(84, "sys_rmdir");
VM66:9 systemCallNames.put(85, "sys_creat");
VM66:9 systemCallNames.put(86, "sys_link");
VM66:9 systemCallNames.put(87, "sys_unlink");
VM66:9 systemCallNames.put(88, "sys_symlink");
VM66:9 systemCallNames.put(89, "sys_readlink");
VM66:9 systemCallNames.put(90, "sys_chmod");
VM66:9 systemCallNames.put(91, "sys_fchmod");
VM66:9 systemCallNames.put(92, "sys_chown");
VM66:9 systemCallNames.put(93, "sys_fchown");
VM66:9 systemCallNames.put(94, "sys_lchown");
VM66:9 systemCallNames.put(95, "sys_umask");
VM66:9 systemCallNames.put(96, "sys_gettimeofday");
VM66:9 systemCallNames.put(97, "sys_getrlimit");
VM66:9 systemCallNames.put(98, "sys_getrusage");
VM66:9 systemCallNames.put(99, "sys_sysinfo");
VM66:9 systemCallNames.put(100, "sys_times");
VM66:9 systemCallNames.put(101, "sys_ptrace");
VM66:9 systemCallNames.put(102, "sys_getuid");
VM66:9 systemCallNames.put(103, "sys_syslog");
VM66:9 systemCallNames.put(104, "sys_getgid");
VM66:9 systemCallNames.put(105, "sys_setuid");
VM66:9 systemCallNames.put(106, "sys_setgid");
VM66:9 systemCallNames.put(107, "sys_geteuid");
VM66:9 systemCallNames.put(108, "sys_getegid");
VM66:9 systemCallNames.put(109, "sys_setpgid");
VM66:9 systemCallNames.put(110, "sys_getppid");
VM66:9 systemCallNames.put(111, "sys_getpgrp");
VM66:9 systemCallNames.put(112, "sys_setsid");
VM66:9 systemCallNames.put(113, "sys_setreuid");
VM66:9 systemCallNames.put(114, "sys_setregid");
VM66:9 systemCallNames.put(115, "sys_getgroups");
VM66:9 systemCallNames.put(116, "sys_setgroups");
VM66:9 systemCallNames.put(117, "sys_setresuid");
VM66:9 systemCallNames.put(118, "sys_getresuid");
VM66:9 systemCallNames.put(119, "sys_setresgid");
VM66:9 systemCallNames.put(120, "sys_getresgid");
VM66:9 systemCallNames.put(121, "sys_getpgid");
VM66:9 systemCallNames.put(122, "sys_setfsuid");
VM66:9 systemCallNames.put(123, "sys_setfsgid");
VM66:9 systemCallNames.put(124, "sys_getsid");
VM66:9 systemCallNames.put(125, "sys_capget");
VM66:9 systemCallNames.put(126, "sys_capset");
VM66:9 systemCallNames.put(127, "sys_rt_sigpending");
VM66:9 systemCallNames.put(128, "sys_rt_sigtimedwait");
VM66:9 systemCallNames.put(129, "sys_rt_sigqueueinfo");
VM66:9 systemCallNames.put(130, "sys_rt_sigsuspend");
VM66:9 systemCallNames.put(131, "sys_sigaltstack");
VM66:9 systemCallNames.put(132, "sys_utime");
VM66:9 systemCallNames.put(133, "sys_mknod");
VM66:9 systemCallNames.put(134, "sys_uselib");
VM66:9 systemCallNames.put(135, "sys_personality");
VM66:9 systemCallNames.put(136, "sys_ustat");
VM66:9 systemCallNames.put(137, "sys_statfs");
VM66:9 systemCallNames.put(138, "sys_fstatfs");
VM66:9 systemCallNames.put(139, "sys_sysfs");
VM66:9 systemCallNames.put(140, "sys_getpriority");
VM66:9 systemCallNames.put(141, "sys_setpriority");
VM66:9 systemCallNames.put(142, "sys_sched_setparam");
VM66:9 systemCallNames.put(143, "sys_sched_getparam");
VM66:9 systemCallNames.put(144, "sys_sched_setscheduler");
VM66:9 systemCallNames.put(145, "sys_sched_getscheduler");
VM66:9 systemCallNames.put(146, "sys_sched_get_priority_max");
VM66:9 systemCallNames.put(147, "sys_sched_get_priority_min");
VM66:9 systemCallNames.put(148, "sys_sched_rr_get_interval");
VM66:9 systemCallNames.put(149, "sys_mlock");
VM66:9 systemCallNames.put(150, "sys_munlock");
VM66:9 systemCallNames.put(151, "sys_mlockall");
VM66:9 systemCallNames.put(152, "sys_munlockall");
VM66:9 systemCallNames.put(153, "sys_vhangup");
VM66:9 systemCallNames.put(154, "sys_modify_ldt");
VM66:9 systemCallNames.put(155, "sys_pivot_root");
VM66:9 systemCallNames.put(156, "sys__sysctl");
VM66:9 systemCallNames.put(157, "sys_prctl");
VM66:9 systemCallNames.put(158, "sys_arch_prctl");
VM66:9 systemCallNames.put(159, "sys_adjtimex");
VM66:9 systemCallNames.put(160, "sys_setrlimit");
VM66:9 systemCallNames.put(161, "sys_chroot");
VM66:9 systemCallNames.put(162, "sys_sync");
VM66:9 systemCallNames.put(163, "sys_acct");
VM66:9 systemCallNames.put(164, "sys_settimeofday");
VM66:9 systemCallNames.put(165, "sys_mount");
VM66:9 systemCallNames.put(166, "sys_umount2");
VM66:9 systemCallNames.put(167, "sys_swapon");
VM66:9 systemCallNames.put(168, "sys_swapoff");
VM66:9 systemCallNames.put(169, "sys_reboot");
VM66:9 systemCallNames.put(170, "sys_sethostname");
VM66:9 systemCallNames.put(171, "sys_setdomainname");
VM66:9 systemCallNames.put(172, "sys_iopl");
VM66:9 systemCallNames.put(173, "sys_ioperm");
VM66:9 systemCallNames.put(174, "sys_create_module");
VM66:9 systemCallNames.put(175, "sys_init_module");
VM66:9 systemCallNames.put(176, "sys_delete_module");
VM66:9 systemCallNames.put(177, "sys_get_kernel_syms");
VM66:9 systemCallNames.put(178, "sys_query_module");
VM66:9 systemCallNames.put(179, "sys_quotactl");
VM66:9 systemCallNames.put(180, "sys_nfsservctl");
VM66:9 systemCallNames.put(181, "sys_getpmsg");
VM66:9 systemCallNames.put(182, "sys_putpmsg");
VM66:9 systemCallNames.put(183, "sys_afs_syscall");
VM66:9 systemCallNames.put(184, "sys_tuxcall");
VM66:9 systemCallNames.put(185, "sys_security");
VM66:9 systemCallNames.put(186, "sys_gettid");
VM66:9 systemCallNames.put(187, "sys_readahead");
VM66:9 systemCallNames.put(188, "sys_setxattr");
VM66:9 systemCallNames.put(189, "sys_lsetxattr");
VM66:9 systemCallNames.put(190, "sys_fsetxattr");
VM66:9 systemCallNames.put(191, "sys_getxattr");
VM66:9 systemCallNames.put(192, "sys_lgetxattr");
VM66:9 systemCallNames.put(193, "sys_fgetxattr");
VM66:9 systemCallNames.put(194, "sys_listxattr");
VM66:9 systemCallNames.put(195, "sys_llistxattr");
VM66:9 systemCallNames.put(196, "sys_flistxattr");
VM66:9 systemCallNames.put(197, "sys_removexattr");
VM66:9 systemCallNames.put(198, "sys_lremovexattr");
VM66:9 systemCallNames.put(199, "sys_fremovexattr");
VM66:9 systemCallNames.put(200, "sys_tkill");
VM66:9 systemCallNames.put(201, "sys_time");
VM66:9 systemCallNames.put(202, "sys_futex");
VM66:9 systemCallNames.put(203, "sys_sched_setaffinity");
VM66:9 systemCallNames.put(204, "sys_sched_getaffinity");
VM66:9 systemCallNames.put(205, "sys_set_thread_area");
VM66:9 systemCallNames.put(206, "sys_io_setup");
VM66:9 systemCallNames.put(207, "sys_io_destroy");
VM66:9 systemCallNames.put(208, "sys_io_getevents");
VM66:9 systemCallNames.put(209, "sys_io_submit");
VM66:9 systemCallNames.put(210, "sys_io_cancel");
VM66:9 systemCallNames.put(211, "sys_get_thread_area");
VM66:9 systemCallNames.put(212, "sys_lookup_dcookie");
VM66:9 systemCallNames.put(213, "sys_epoll_create");
VM66:9 systemCallNames.put(214, "sys_epoll_ctl_old");
VM66:9 systemCallNames.put(215, "sys_epoll_wait_old");
VM66:9 systemCallNames.put(216, "sys_remap_file_pages");
VM66:9 systemCallNames.put(217, "sys_getdents64");
VM66:9 systemCallNames.put(218, "sys_set_tid_address");
VM66:9 systemCallNames.put(219, "sys_restart_syscall");
VM66:9 systemCallNames.put(220, "sys_semtimedop");
VM66:9 systemCallNames.put(221, "sys_fadvise64");
VM66:9 systemCallNames.put(222, "sys_timer_create");
VM66:9 systemCallNames.put(223, "sys_timer_settime");
VM66:9 systemCallNames.put(224, "sys_timer_gettime");
VM66:9 systemCallNames.put(225, "sys_timer_getoverrun");
VM66:9 systemCallNames.put(226, "sys_timer_delete");
VM66:9 systemCallNames.put(227, "sys_clock_settime");
VM66:9 systemCallNames.put(228, "sys_clock_gettime");
VM66:9 systemCallNames.put(229, "sys_clock_getres");
VM66:9 systemCallNames.put(230, "sys_clock_nanosleep");
VM66:9 systemCallNames.put(231, "sys_exit_group");
VM66:9 systemCallNames.put(232, "sys_epoll_wait");
VM66:9 systemCallNames.put(233, "sys_epoll_ctl");
VM66:9 systemCallNames.put(234, "sys_tgkill");
VM66:9 systemCallNames.put(235, "sys_utimes");
VM66:9 systemCallNames.put(236, "sys_vserver");
VM66:9 systemCallNames.put(237, "sys_mbind");
VM66:9 systemCallNames.put(238, "sys_set_mempolicy");
VM66:9 systemCallNames.put(239, "sys_get_mempolicy");
VM66:9 systemCallNames.put(240, "sys_mq_open");
VM66:9 systemCallNames.put(241, "sys_mq_unlink");
VM66:9 systemCallNames.put(242, "sys_mq_timedsend");
VM66:9 systemCallNames.put(243, "sys_mq_timedreceive");
VM66:9 systemCallNames.put(244, "sys_mq_notify");
VM66:9 systemCallNames.put(245, "sys_mq_getsetattr");
VM66:9 systemCallNames.put(246, "sys_kexec_load");
VM66:9 systemCallNames.put(247, "sys_waitid");
VM66:9 systemCallNames.put(248, "sys_add_key");
VM66:9 systemCallNames.put(249, "sys_request_key");
VM66:9 systemCallNames.put(250, "sys_keyctl");
VM66:9 systemCallNames.put(251, "sys_ioprio_set");
VM66:9 systemCallNames.put(252, "sys_ioprio_get");
VM66:9 systemCallNames.put(253, "sys_inotify_init");
VM66:9 systemCallNames.put(254, "sys_inotify_add_watch");
VM66:9 systemCallNames.put(255, "sys_inotify_rm_watch");
VM66:9 systemCallNames.put(256, "sys_migrate_pages");
VM66:9 systemCallNames.put(257, "sys_openat");
VM66:9 systemCallNames.put(258, "sys_mkdirat");
VM66:9 systemCallNames.put(259, "sys_mknodat");
VM66:9 systemCallNames.put(260, "sys_fchownat");
VM66:9 systemCallNames.put(261, "sys_futimesat");
VM66:9 systemCallNames.put(262, "sys_newfstatat");
VM66:9 systemCallNames.put(263, "sys_unlinkat");
VM66:9 systemCallNames.put(264, "sys_renameat");
VM66:9 systemCallNames.put(265, "sys_linkat");
VM66:9 systemCallNames.put(266, "sys_symlinkat");
VM66:9 systemCallNames.put(267, "sys_readlinkat");
VM66:9 systemCallNames.put(268, "sys_fchmodat");
VM66:9 systemCallNames.put(269, "sys_faccessat");
VM66:9 systemCallNames.put(270, "sys_pselect6");
VM66:9 systemCallNames.put(271, "sys_ppoll");
VM66:9 systemCallNames.put(272, "sys_unshare");
VM66:9 systemCallNames.put(273, "sys_set_robust_list");
VM66:9 systemCallNames.put(274, "sys_get_robust_list");
VM66:9 systemCallNames.put(275, "sys_splice");
VM66:9 systemCallNames.put(276, "sys_tee");
VM66:9 systemCallNames.put(277, "sys_sync_file_range");
VM66:9 systemCallNames.put(278, "sys_vmsplice");
VM66:9 systemCallNames.put(279, "sys_move_pages");
VM66:9 systemCallNames.put(280, "sys_utimensat");
VM66:9 systemCallNames.put(281, "sys_epoll_pwait");
VM66:9 systemCallNames.put(282, "sys_signalfd");
VM66:9 systemCallNames.put(283, "sys_timerfd_create");
VM66:9 systemCallNames.put(284, "sys_eventfd");
VM66:9 systemCallNames.put(285, "sys_fallocate");
VM66:9 systemCallNames.put(286, "sys_timerfd_settime");
VM66:9 systemCallNames.put(287, "sys_timerfd_gettime");
VM66:9 systemCallNames.put(288, "sys_accept4");
VM66:9 systemCallNames.put(289, "sys_signalfd4");
VM66:9 systemCallNames.put(290, "sys_eventfd2");
VM66:9 systemCallNames.put(291, "sys_epoll_create1");
VM66:9 systemCallNames.put(292, "sys_dup3");
VM66:9 systemCallNames.put(293, "sys_pipe2");
VM66:9 systemCallNames.put(294, "sys_inotify_init1");
VM66:9 systemCallNames.put(295, "sys_preadv");
VM66:9 systemCallNames.put(296, "sys_pwritev");
VM66:9 systemCallNames.put(297, "sys_rt_tgsigqueueinfo");
VM66:9 systemCallNames.put(298, "sys_perf_event_open");
VM66:9 systemCallNames.put(299, "sys_recvmmsg");
VM66:9 systemCallNames.put(300, "sys_fanotify_init");
VM66:9 systemCallNames.put(301, "sys_fanotify_mark");
VM66:9 systemCallNames.put(302, "sys_prlimit64");
VM66:9 systemCallNames.put(303, "sys_name_to_handle_at");
VM66:9 systemCallNames.put(304, "sys_open_by_handle_at");
VM66:9 systemCallNames.put(305, "sys_clock_adjtime");
VM66:9 systemCallNames.put(306, "sys_syncfs");
VM66:9 systemCallNames.put(307, "sys_sendmmsg");
VM66:9 systemCallNames.put(308, "sys_setns");
VM66:9 systemCallNames.put(309, "sys_getcpu");
VM66:9 systemCallNames.put(310, "sys_process_vm_readv");
VM66:9 systemCallNames.put(311, "sys_process_vm_writev");
VM66:9 systemCallNames.put(312, "sys_kcmp");
VM66:9 systemCallNames.put(313, "sys_finit_module");
VM66:9 systemCallNames.put(314, "sys_sched_setattr");
VM66:9 systemCallNames.put(315, "sys_sched_getattr");
VM66:9 systemCallNames.put(316, "sys_renameat2");
VM66:9 systemCallNames.put(317, "sys_seccomp");
VM66:9 systemCallNames.put(318, "sys_getrandom");
VM66:9 systemCallNames.put(319, "sys_memfd_create");
VM66:9 systemCallNames.put(320, "sys_kexec_file_load");
VM66:9 systemCallNames.put(321, "sys_bpf");
VM66:9 systemCallNames.put(322, "stub_execveat");
VM66:9 systemCallNames.put(323, "userfaultfd");
VM66:9 systemCallNames.put(324, "membarrier");
VM66:9 systemCallNames.put(325, "mlock2");
VM66:9 systemCallNames.put(326, "copy_file_range");
VM66:9 systemCallNames.put(327, "preadv2");
VM66:9 systemCallNames.put(328, "pwritev2");
```


- - - - - -


Running the script on a binary will annotate the system calls it finds by adding a plate comment.


- ![](/wp-content/uploads/2019/10/eclipse_7.png?w=1024)
- 
![](/wp-content/uploads/2019/10/ghidra_0-1.png?w=1024)


- - - - - -


In conclusion, creating my first Ghidra script wasn’t that hard and once I’ve figured out how to setup Eclipse and link it properly the development experience was a bliss. I hope more and more people will adopt Ghidra and contribute to it. Some Ghidra trainings and exercises can be found in `ghidra_9.1-BETA_DEV/docs`.


Thanks for reading and have a great day!