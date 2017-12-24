# OS failures

Briefly documents Linux OS failures; why a particular distro is unsuitable to
be used as a virtualized development environment.

## 2017-12-24

Using `Windows 10` as host running `VirtualBox 5.2.4`.

- [`Elementary OS 0.4.1`][6]: This OS is a no-go for a billion of reasons.
  Firstly, they literally have zero documentation other than a primitive
  ["learn the basics" guide][7]. But the most troubling thing is the application
  dock, by default located on the left side of the screen. This guy collects
  quick-launch icons and other icons of running applications. It does an
  incredibly poor job at distinguishing between unactivated apps versus which
  apps are running. By default, the dock folds away as to not disturb or overlap
  other windows. Because this sort of behavior rarely works as intended and
  always end up causing dramatic amounts of user irritation as he routinely has
  to fight the dock with his mouse pointer to make it visible again; 99 percent
  of us will most likely disable this feature and let the dock always be
  visible. But if always visible, then a big chunk of the screen space will
  become totally unusable for other windows - the dock itself does not span the
  entire width of the screen. There exists another "panel" which by default sits
  on top of the screen and it does span the entire width. This panel is what
  most other operating systems would use as a "taskbar". But not Elementary OS.
  They took all the icons and extracted them into a separate dock. But, the
  panel is still left behind with mostly completely unused black, empty and
  annoyingly unallocated space. It appears not to be possible to revert this
  ludicrous mistake by merging the dock or its app icons into the panel. At the
  end of the day, rational OS users who want to maximize screen-pixel utility
  and use their OS to accomplish real work instead of spending their days taking
  screenshots of a dock widget, must stay the hell away from Elementary OS.

## Sometime during 2017

Using `Windows 10` as host running `VirtualBox 5.2.2`.

- [`GeckoLinux Rolling Budgie 999.170302.0`][5]: Well, it is an openSUSE
  distribution so it suffers from the same problems listed below. Furthermore,
  the distro author has managed to downgrade the Budgie experience
  significantly. So sad.

- [`openSUSE Tumbleweed 20171206`][3]: The KDE desktop is semi-retarded, most
  definitely bloated. How to update/upgrade the system seems to be a complete
  underdocumented nightmare. Plus, they still have the problem of asking for a
  password every single time you try to accomplish the most mundane tasks (see
  [this][4]).

- [`Fedora LXQt 27.1.6`][2]: The desktop is just too retarded, to be honest.
  Plus, a script that Fedora invokes on shutdown freeze every time. The script
  eventually aborts after a timeout but the shutdown procedure takes about
  one and a half minute every single time. Obviously dysfunctional.

- [`Ubuntu Budgie 17.10 x64`][1]: Slow and buggy. Error messages keep popping up
  all the time.

[1]: https://ubuntubudgie.org/
[2]: https://spins.fedoraproject.org/en/lxqt/
[3]: https://www.opensuse.org/#Tumbleweed
[4]: https://www.theregister.co.uk/2012/02/29/torvalds_tantrum_opensuse/
[5]: https://geckolinux.github.io/
[6]: https://elementary.io
[7]: https://elementary.io/docs/learning-the-basics