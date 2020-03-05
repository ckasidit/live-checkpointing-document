# qemu-live-checkpointing
How to run MPLCR on NVM using a modified qemu 2.9.0. 
<p><p>
First, we perform live checkpointing to memory.
<pre>
$ ./src-mplcrKvmOvs-npb331.sh
$ ./dest-mplcrKvmOvs-npb331-receiver-NVMe.sh 1
$ ./src-qmp-set-mplm-migration.sh qmp-sock-6101
$ ./src-qmp-enable-live-checkpointing.sh qmp-sock-6101
$ ./src-qmp-enable-max-rate-limit.sh qmp-sock-6101
$ ./src-qmp-migrate.sh qmp-sock-6101
</pre>
Then, we store memory state to NVM storage. 
<pre>
$ ./src-qmp-enable-max-rate-limit.sh qmp-sock-7101
$ time sudo nc -U ./moni-sock-7101
  Migrate_set_speed 100G
  Migrate “exec: dd of=/media/nvme/migchk1.state”
$ ls
$ ./src-moni-cont.sh moni-sock-7101
$ ./src-moni-quit.sh moni-sock-6101
</pre>
Finally, we restore the VM from the saved state. 
<pre>
$ ./dest-Restore-mplcrKvmOvs-npb331-NVMe-incomingMig.sh 1 /media/nvme/kasidit/migchk1.state
$ ./src-moni-quit.sh moni-sock-7101
</pre>
