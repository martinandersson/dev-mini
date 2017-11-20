# Unattended upgrades

As was discovered 2017-09-03, the then latest releases of Ubuntu (and other
Linux distros) had a weird-ass feature installed called "unattended upgrades".
This guy updates/upgrades/whatever repositories and/or packages in the
background, yes even immediately after boot. And while this is running, `apt`
will lock a particular directory/file and `apt` processes started by Vagrant and
Ansible will immediately fail, whining about not being able to acquire the lock
directory/file.

The box provider should have removed this "feature" because it ruins all
provisioning done by Vagrant and Ansible. Of course, no one did.

As noticed on 2017-11-11, the problem seems to have disappeared in
`fso/artful64`. In lack of more experience with newish boxes and as a preemptive
guide to fix the problem shall it ever show its ugly face again, I will include
this document which documents my previous attempts to solve the problem.

I spent - as usual - a whole day trying to implement all kinds of hacks. For
example waiting for the lock using `flock`, waiting for and killing processes
both left and right. Despite all false promises, absolutely nothing worked.

Put this in the Vagrantfile before any other provisioning takes place:
([source1][1], [source2][2], [source3][3]):

    cfg.vm.provision 'stop apt-daily- timer & service', type: :shell, inline: <<~'EOM'
      systemctl --now stop apt-daily.timer
      systemctl --now stop apt-daily.service
    EOM

    cfg.vm.provision 'remove unattended-upgrades', type: :shell, inline:
      'sudo apt-get autoremove unattended-upgrades -y --purge'

Removing `unattended-upgrades` will _minimize_ the frequency of the problem but
does not fully resolve it. Also, please note that if you let Vagrant install
Ansible, then the `unattended-upgrades` package will also be installed right
back again. lol.

On Ubuntu 17.10, here's how you can find out if the package is installed
([source][4]):

    $ apt -qq list unattended-upgrades
    unattended-upgrades/artful,artful,now 0.98ubuntu1 all [installed]

After our provisioning hacks have been executed, the output is largely the same
but there's no trailing text "[installed]".

Todo: With the aforementioned hacks in place then after provisioning/first boot,
why does the machine popup a window asking to install Ubuntu updates
(`unattended-upgrades` has been removed)? Smells fishy.

I might also have tried this, which if I did didn't work ([source][5]):

    cfg.vm.provision 'disable apt periodic updates', type: :shell, inline:
      "echo 'APT::Periodic::Enable \"0\";' > /etc/apt/apt.conf.d/02periodic"

Might be noteworthy; the file `/etc/apt/apt.conf.d/02periodic` does not exist in `fso/artful64-desktop` version `2017-11-01`. There is a `10periodic` in the same
folder which also happens to have "APT periodic enable" set to 0.

[1]: https://medium.com/@koalallama/vagrant-up-failing-could-not-get-lock-var-lib-dpkg-lock-13c73225782d
[2]: https://unix.stackexchange.com/a/315517
[3]: https://askubuntu.com/a/380030
[4]: https://askubuntu.com/a/823630
[5]: https://github.com/mitchellh/vagrant/issues/7508#issuecomment-281332526