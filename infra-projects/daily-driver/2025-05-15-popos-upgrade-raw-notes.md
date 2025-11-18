# POP_! OS Upgrade Raw Notes
## The Problem
I know the new Pop os version has been released. I wanted to see if the upgrade from 22.04 to 24.04 was available yet On POP_! OS.  
First off, now is a great time to make sure Backups are occurring on time. The backup program Timeshift is timeshifting. Backups occorring weekly and saving properly.  
Updated all packages on the Cosmic Store  
Settings > OS Upgrade and Recovery. Upgrade not available. Attempted a recovery, Recovery failed, could not initiate OS Recovery   
I knew that this was the ***"easy way"***, it's time to do this right. Time for a command line install (like a badass).  
Opening the COSMIC Text Editor.  
I ran the command
```bash
sudo apt update && upgrade
```
While attempting to update  
E: The repository 'https://ppa.launchpadcontent.net/bookworm-team/bookworm/ubuntu jammy Release' does not have a Release file.  
N: Updating from such a repository can't be done securely, and is therefore disabled by default.  
N: See apt-secure(8) manpage for repository creation and user configuration details.  
Followed the link to the repository index. I followed the bookworm ppa to the parent directory.The parent directory shows the related project is the bookworm app and not any Debian Bookworm related dependencies.  
I said (to myself) I will do this the right way, but I have to check something.   
Opening the Software and Updates app. I could easily delete the this ppa (or repository) from here, but I decieded to do this the right way so...  
Back to the command line.  
```bash
sudo ppa-purge ppa:launchpadcontent.net/bookworm-team/bookworm/ubuntu
sudo: ppa-purge: command not found.
sudo add-apt-repository --remove ppa:launchpadcontent.net/bookworm-team/bookworm/ubuntu
```
Unable to handle repository shortcut 'ppa:launchpadcontent.net/bookworm-team/bookworm/ubuntu'
Running  
```bash
ls /etc/apt/sources.list.d
```
Returned: bookworm-team-ubuntu-bookworm-jammy.list  pop-os-apps.sources  pop-os-release.sources  system.sources  
Double checking everything before moving to this solution. error found.  
Running 
```bash
sudo apt install ppa-purge.
sudo ppa-purge ppa-launchpadcontent.net/bookworm-team/bookworm/ubuntu
```
Returned Updating packages lists
E: The repository 'https://ppa.launchpadcontent.net/bookworm-team/bookworm/ubuntu jammy Release' does not have a Release file.
Warning:  apt-get update failed for some reason  
OK, enough is enough. I did want to do this at the command line. I know that running  
```bash
sudo rm -i /etc/apt/sources.list.d/bookworm-team-ubuntu-bookworm-jammy.list
```
will absolutely work, it will also likely create more messes and headaches down the road. To the GUI Software & Updates (like less of a badass)  
Removing ppa through Software & Updates. Done  
Checking by running 
```bash
ls /etc/apt/sources.list.d
```
Returned  bookworm-team-ubuntu-bookworm-jammy.list  bookworm-team-ubuntu-bookworm-jammy.list.save  pop-os-apps.sources  pop-os-release.sources  system.sources
```bash
Sudo Apt Update
```
Just to check nothing had changed. Update Failed.  
Attempting through Software & Updates one last time.  
Returned bookworm-team-ubuntu-bookworm-jammy.list  bookworm-team-ubuntu-bookworm-jammy.list.save  pop-os-apps.sources  pop-os-release.sources  system.sources
Running 
```bash
sudo rm -i /etc/apt/sources.list.d/bookworm-team-ubuntu-bookworm-jammy.list
```
Returned rm: remove regular empty file '/etc/apt/sources.list.d/bookworm-team-ubuntu-bookworm-jammy.list'? (answer y).
```bash
sudo apt update
```
Successful
```bash
sudo apt upgrade
```
Done  
Closing all open running applications due to a past bug in upgrading Pop.  
Running
```bash
sudo pop-upgrade release upgrade
```
Returned checking if pop-upgrade requires an update (notihng else)  
Powercycling laptop  
attempted GUI upgrade, not available. recovery failed. Originating Error cause -- failed to set up OS refresh: client error: calling RefreshOS method failed:Could not activate remote peer: activation request failed: unit is masked.  
```bash
pop-upgrade recovery upgrade from-release
sudo rm -f /etc/apt/sources.list.d/pop-os-ppa.sources
systemctl restart pop-upgrade.
```
Response -- Failed to restart pop-upgrade.service: Unit pop-upgrade.service is masked.
```bash
systemctl unmask pop-upgrade
systemctl restart pop-upgrade
pop-upgrade release upgrade
```
Recovery partition now available. Recovering. Downloading the recovery partition.  
Refresh OS reinstall initiated and running the recovery program. Computer rebooting.  
Unfortunately, there was no OS Upgrade available, just a fresh(ish) install of pop.  
System changes observed. Software & Updates is missing from the OS. Opening Repoman. Repo settings confirmed.  
Opening Bookworn, bookworm operational. All systems appear to be recovered and working properly.  
Checking for Timeshift logs. Timeshift not found on os.  
Attempting to download Timeshift from the COSMIC Store. Cosmic Store is gone as are all apps installed through the COSMIC Store (flatpack). Opening pop!_shop.  
Downloading COSMIC Store. I will not reinstall the cosmic apps as none were much better that what they are intended to replace. I will risk having to fix this issue in the future,so i can update apps quickly,instead of using pop!_shop. Its the worst.  
Updating through pop_shop  
One last try at it. Running
```bash
pop-upgrade release upgrade
```
Returned - checking if pop-upgrade requires an update  
Current Release: 22.04  
Upgrading to: 24.04  
New version available: false  
no release available to upgrade to

I'm done, I will restore apps and wait for the official release

Lessons learned 
1. A distribution upgrade is not a casual button-press. I went in assuming this would be a fast hop from 22.04 to 24.04. It wasn’t. Pop might look friendly on the surface, but major version jumps are real system surgery, not a firmware update.
2. One stray PPA can wreck the entire process. That Bookworm PPA turned out to be the single blocker that prevented the upgrade path from even being detected. Lesson: if the upgrade tool is acting confused, check for PPAs before anything else.
3. The CLI is powerful, but it’s not magic. My instinct was to fix this “properly” through the terminal. Turns out the terminal can’t save you from a malformed or dead repo source. In some cases, the GUI is simply the faster and safer route.
4. Repository cleanup must be precise, not impulsive. I could have nuked the culprit file from /etc/apt/sources.list.d in one stroke. That would have “worked,” but it also would have created a clean-up puzzle later. Precision beats bravado here.
5. If a service is masked, something upstream is broken. pop-upgrade being masked wasn’t a random glitch. It was a symptom. Upgrades rely on a whole chain of services functioning, and once one link is disabled, everything above it crumbles.
6. Pop’s recovery system is not the OS upgrade path. The presence of a recovery partition makes it feel like you’re upgrading. You aren’t. You’re reinstalling the same version cleanly unless 24.04 is officially released to Pop users.
7. Flatpak and the COSMIC Store are tied closely together. Removing or resetting parts of the system can strip out more than expected. Losing Timeshift, COSMIC, and installed apps wasn’t a corruption event. It was a dependency house-cleaning.
8. Pop!_Shop is still Pop!_Shop. It remains the slowest and least reliable part of the ecosystem. If speed matters, the COSMIC Store is worth reinstalling even if it risks another round of “fixing Pop after fixing Pop.”
9. An upgrade not offered is intentionally not offered. When the tools all insist “no release available,” they’re not being stubborn. They’re telling the truth. Forcing it early is a great way to break a system that was perfectly stable five minutes earlier.
10. The OS should be upgraded as soon as possible. I shouldn’t. None of the failures were catastrophic. The system worked as designed. I just tried to outrun the official release schedule and got reminded that patience is part of system maintenance too.
