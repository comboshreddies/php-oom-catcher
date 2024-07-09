# php-oom-catcher
php oom catcher bpf

We had multiple php batch processing tasks within cgroup/pod/container, and they would get OOM killed.
In some cases OOM would hit single process, so parent could catch termination info. In most cases
whole cgroup, with all running php processes would be OOM killed.

On OOM kill kernel would show stack, list of processes within cgroup/pod/container with their oom score, rss, and global (host) process id.

We knew that some processes were killed, but we do not know which and what they were doing/processing at the moment of kill.

Fortunately php code is using cli_set_process_title to set info of what each pid is running, 
so if you jump into cgroup/container you could see some meaninfull ps output.
Unfortunately that output, is not available when kernel OOM arrives, and host pids not namespace/container pids are shown.

Here is a small bpftrace script that tracks those php processes that set process title catched with php bpf uprobe set_ps_title.
Process title is being kept in associative array for each pid, so it is reported back to bpftrace run of this script if
tracked php process is being OOM killed.


