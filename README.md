###### man 1 "02 February 2019" "3.4" "mkdesktop man page"

## NAME
mkdesktop

## SYNOPSIS
mkdesktop \- configure a FreeBSD desktop in a jiffy.

## DESCRIPTION
If you setup a desktop, for instance KDE, under a fresh FreeBSD
installation by yourself, you will have to do all of the following :

1) Install X (xorg)

2) Install KDE (kde5)

3) Configure the system files under /etc and /boot

If you use the command mkdesktop, it will do all the above on its own,
and a bit more if you so desire - it can set up your graphics subsystem
correctly and then install (unattended) anything else you need. The
application then winds up by setting up any emulation layers (Wine /
Linuxulator) you might wish to use and optionally configuring login
management with SLiM.

If you wish to use Lumina (meta-package lumina) instead of the default
desktop kde5, just launch mkdesktop and wait for it to initialize itself,
and then - when prompted - run the command :

echo "lumina" > ~/mkdesktop/pkg_list/desktop

In any subsequent run, whatever is the list of packages in the file
~/mkdesktop/pkg_list/desktop overrides the default definition of
desktop. Package list files are newline-separated.

Although you could lump all packages into a single list, breaking up the
list into more stages allows for better, fine-grained control.

By default, 5 stages are considered, in order :

pre_x  ->  x  ->  post_x  ->  desktop  ->  post_desktop

pre_x (level 1) presumes there is no X around. The default definition of
this stage is empty, but this is a good place to specify command-line
utilities - wget:cdrtools:vim-console   (Replace ':' with newline)

x (level 2) is the stage for installing the base X system. By default,
this installs just the xorg meta-distribution.

post_x (level 3) is suited for X stuff that is not desktop-specific. The
default definition of this stage is empty, but you can pull in a lot of
stuff - firefox:libreoffice:mplayer   (Replace ':' with newline)

desktop (level 4) is the stage for the base desktop. The default for this
stage is KDE5, which essentially means the meta-package kde5.

(Like stage x, the stage definition for desktop should preferably be as
lean as possible).

post_desktop (level 5) is where you get the stuff that is either
desktop-specific or cross-desktop, although the default setting for this
stage is empty. The following list is an example for this stage -
k3b:gnome-keyring:thunar   (Replace ':' with newline)

Levels 1 (the lowest user-defined stage) through 5 (the highest by
default) are considered regular stages. Level 0 (which sets up the
graphics subsystem) is a special stage - it cannot be customized.

Customizing a regular stage is easy enough. If a file named <stage>
(without the enclosing arrows) exists in the package list directory, its
contents become the definition for that stage. This script therefore by
default looks for files named pre_x, x, post_x, desktop and
post_desktop. Package list files are newline-separated. If the first
character on a line is '#' (hash without quotes), the line is ignored as
a comment.

mkdesktop also bundles in 2 special functions : stagezero and postproc.

stagezero corresponds to Level 0 and is launched by default. This stage
caters to setting up the graphics environment for the various graphics
chipsets (Radeon/NVidia/Intel/AMDGPU), pkg's and kld's for which are
configured as needed.

postproc gets automatically invoked when the regular stages have been
processed.  It sets up - if the user chooses y[es] for each of these -
Wine (Windows emulation layer); Linuxulator (Linux emulation layer);
configuration of system files :

Under /etc/ : fstab; devfs.conf; devfs.rules; rc.conf; sysctl.conf;

Under /boot/ : loader.conf.

The final step the postproc function executes is optionally setting up
X login management with SLiM.

Tip : If you regularly install FreeBSD, you will appreciate the
convenience mkdesktop soon becomes.

## OPTIONS
--begin <level>	: Use <level> as the beginning stage
(default: 0)

--end <level> : Use <level> as the last stage
(default: highest available)

--pkg-list-dir <dir> : Use <dir> as the package list directory
(default: ~/mkdesktop/pkg_list)

--no-postproc : Disable the postproc function that kicks in
automatically upon processing of regular stages to configure emulation
layers and system files.

--dry : Test without making changes to the system. --dry is available to
non-root users too, and can be used to print the complete list of
packages (automatic + non-automatic) to be installed / download size.

--echo : Just list the top-level (non-automatic) packages that would be
installed. Regular stage levels will get processed through 'echo', not
via the normal route 'pkg install'. --echo takes precedence over --dry,
and is available to non-root users too.

--yes : Pass '--yes' to 'pkg install' by default. The option applies
only to regular stages (levels 1 through 5): level 0 and postprocessor
always use '--yes' for 'pkg install' ops. If you do not pass in --yes at
the command-line yourself, the script gives you another chance to apply
the --yes configuration when the processing of regular stages begins.

--retry <retries> : In case of 'pkg install' operational timeouts, retry
the operation <retries> times. 0 is the default and -1 can be used for
infinite retries.

--append : In append mode, packages mentioned in package list files are
appended to the stage definition lists, which otherwise would be
superseded. Not recommended in regular usage: if you need --append,
perhaps you should first use your text editor to look at the file
~/mkdesktop/stage-definitions where the defaults can be tweaked.

--help : Print the man page (same as `man mkdesktop`).

--usage : Print usage information.

## SEE ALSO
desktop-installer(1)

## BUGS
No known bugs.

## AUTHOR
Manish Jain (bourne.identity@hotmail.com)
