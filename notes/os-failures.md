# OS failures

Briefly documents Linux OS failures; why a particular distro is unsuitable to
be used as a virtualized development environment.

## 2017-12-26

- [Deepin 15.5][8]: Semi-retarded OS. The English language used is pretty much
  broken and will cause confusion as early as during the installation. The OS
  has a childlish feel to it; it plays a rather long melodic sound on boot that
  pisses in your ears for several seconds, it wiggles the application icons when
  they are clicked (because lame animations are soooo modern, rite? \*irony*),
  all system settings is managed through a giant side panel that spans the
  entire height of the screen and scrolls on forever - with no visible scroll
  bars because users hate knowing where exactly on a page they are or how much
  left of the page there is (\*even more irony*). This control center should, of
  course, have been a regular application/window. Despite its disguise as a
  ["notification"][9] slash ["action"][10] center, it is launched as a regular
  app through a yuge icon in the taskbar/dock, there is no shortcut to this
  thing anywhere on the right side of the screen. You click on a regular
  application icon thinking a window will open, but instead, a yuge panel folds
  in from the right side - a panel that is nothing like the panels you are used
  too from other operating systems. As icing on the cake, you will notice a lot
  more disturbances. For example, in order to resize the terminal window, you
  must click in hidden secret areas of the window border - the mouse pointer
  does not change when you hover over these and you bet; the bottom right corner
  of the window which you will most likely try first has no such secret area.
  After the first set of updates has been installed and you agree to restart
  the machine, you will be asked for the administrator password. Because it is
  such a huge security risk to let applications initiate a shutdown, forget that
  you actually clicked "yes" in order for this to happen. In short, this OS is
  very lame. Feels like it was put together by a thirteen year old with some
  Photoshop skills. Avoid like the plague.

## 2017-12-24

Using `Windows 10` as host running `VirtualBox 5.2.4`.

- [`Elementary OS 0.4.1`][6]: This OS is a no-go for a billion of reasons.
  Firstly, they literally have zero documentation other than a primitive
  ["learn the basics" guide][7]. But the most troubling thing is the application
  dock, by default located on the left side of the screen. This guy collects
  quick-launch icons and other icons of running applications. It does an
  incredibly poor job at distinguishing between vanilla icons versus apps that
  are running. By default, the dock folds away as to not disturb or overlap
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
  pritty screenshots of a dock widget, must stay the hell away from Elementary OS.

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
[8]: https://www.deepin.org
[9]: https://en.wikipedia.org/wiki/Notification_Center
[10]: https://en.wikipedia.org/wiki/Action_Center