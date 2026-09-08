```
 @@@@@@    @@@@@@@@    @@@@@@     @@@@@@                                   
@@@@@@@@  @@@@@@@@@@  @@@@@@@@   @@@@@@@                                   
     @@@  @@!   @@@@       @@@  !@@                                        
    @!@   !@!  @!@!@      @!@   !@!                                        
   !!@    @!@ @! !@!     !!@    !!@@!@!                                    
  !!:     !@!!!  !!!    !!:     @!!@!!!!                                   
 !:!      !!:!   !!!   !:!      !:!  !:!                                   
:!:       :!:    !:!  :!:       :!:  !:!                                   
:: :::::  ::::::: ::  :: :::::  :::: :::                                   
:: : :::   : : :  :   :: : :::   :: : :                                    
```

# turnkeylinux-apps - Test 19

### moodle-522  ➡️ [Test ISO](#)
* Status: ?
* Versions: Moodle Version Moodle 5.2.2 (tag "v5.2.2") / TurnKey Linux 19
* Pull request: ?
* Git command: git clone --branch perms-enhancement  https://github.com/turnkeylinux-apps-test/turnkey-moodle.git turnkey-moodle-5.2.2
* Bugs/Notes: ?

```
Moodle: Your server does not seem to fully support the following languages:

* English ‎(en)‎
* Français ‎(fr)‎
* Italiano ‎(it)‎
Instead, the global locale (en_AU.UTF-8) will be used to format certain strings such as dates or numbers.
```
* Date: 4 Sep 2026

-----

# turnkeylinux-apps - Test 18.1

### moodle405  ➡️ [Test ISO](https://github.com/UncleDan/turnkey-moodle/releases/tag/v4-18.1-RC2)
* Status: Builds OK. Login OK.
* Versions: Moodle Version Moodle 4 ( MOODLE_405_STABLE ) / TurnKey Linux 18.1
* Pull request: https://github.com/turnkeylinux-apps/moodle/pull/31
* Git command: git clone --branch turnkey-moodle4-18.1-RC2 https://github.com/turnkeylinux-apps-test/turnkey-moodle.git turnkey-moodle405
* Bugs/Notes: *Hotfix for MySql performance needed on TKLdev, for example https://github.com/UncleDan/turnkeylinux-apps-test-18/blob/main/hotfix-mysqltuner-perl-link.sh* "Moodle: Your server does not seem to fully support the following languages:

* English ‎(en)‎
* Français ‎(fr)‎
* Italiano ‎(it)‎
Instead, the global locale (en_AU.UTF-8) will be used to format certain strings such as dates or numbers.
Server is debian bookworm"
Re-checked, works downloading sources via git branch MOODLE_405_STABLE.
* Date: 10 Jun 2026
-----
### moodle501  ➡️ [Test ISO](https://github.com/UncleDan/turnkey-moodle/releases/tag/v5-18.1-RC2)
* Status: Builds OK. Login OK. Install plugin ok.
* Versions: Moodle Version Moodle 5.1 ( MOODLE_501_STABLE ) / TurnKey Linux 18.1
* Pull request: https://github.com/turnkeylinux-apps/moodle/pull/32
* Git command: git clone --branch turnkey-moodle5-18.1-RC2 https://github.com/turnkeylinux-apps-test/turnkey-moodle.git turnkey-moodle501
* Bugs/Notes: *Hotfix for MySql performance needed on TKLdev, for example https://github.com/UncleDan/turnkeylinux-apps-test-18/blob/main/hotfix-mysqltuner-perl-link.sh*. "Moodle: Your server does not seem to fully support the following languages:

* English ‎(en)‎
* Français ‎(fr)‎
* Italiano ‎(it)‎
Instead, the global locale (en_AU.UTF-8) will be used to format certain strings such as dates or numbers.
Server is debian bookworm"
Re-checked, works downloading sources via git branch MOODLE_501_STABLE.
* Date: 6 Jun 2026
-----
### gitea ➡️ [Test ISO](https://github.com/UncleDan/turnkey-gitea/releases/tag/v18.1-RC1)
* Status: Builds OK. Still can't login :-(
* Versions: Gitea 1.25.4 (as of today) / TurnKey Linux 18.1
* Pull request: https://github.com/turnkeylinux-apps/gitea/pull/17
* Git command: git clone https://github.com/UncleDan/turnkey-gitea.git -b turnkey-gitea-18.1-RC1
* Bugs/Notes: *Hotfix for MySql performance needed on TKLdev, for example https://github.com/UncleDan/turnkeylinux-apps-test-18/blob/main/hotfix-mysqltuner-perl-link.sh* Works out of the box, only updated versions in docs. Still can't login as "gitea" admin.
* Date: 18 Feb 2026
-----
### mattermost  ➡️ [Test ISO](https://github.com/UncleDan/turnkey-mattermost/releases/tag/turnkey-mattermost-18.1-RC1)
* Status: Builds OK. Login OK.
* Versions: Moodle 11.3.1 as of today / TurnKey Linux 18.1
* Pull request: https://github.com/turnkeylinux-apps/mattermost/pull/16
* Git command: git clone --branch turnkey-mattermost-18.1-RC1 https://github.com/UncleDan/turnkey-mattermost.git
* Bugs/Notes: -
* Date: 13 Feb 2026

```                                                                           
@@@@@@@   @@@@@@@   @@@@@@@@  @@@  @@@  @@@   @@@@@@   @@@  @@@   @@@@@@   
@@@@@@@@  @@@@@@@@  @@@@@@@@  @@@  @@@  @@@  @@@@@@@@  @@@  @@@  @@@@@@@   
@@!  @@@  @@!  @@@  @@!       @@!  @@@  @@!  @@!  @@@  @@!  @@@  !@@       
!@!  @!@  !@!  @!@  !@!       !@!  @!@  !@!  !@!  @!@  !@!  @!@  !@!       
@!@@!@!   @!@!!@!   @!!!:!    @!@  !@!  !!@  @!@  !@!  @!@  !@!  !!@@!!    
!!@!!!    !!@!@!    !!!!!:    !@!  !!!  !!!  !@!  !!!  !@!  !!!   !!@!!!   
!!:       !!: :!!   !!:       :!:  !!:  !!:  !!:  !!!  !!:  !!!       !:!  
:!:       :!:  !:!  :!:        ::!!:!   :!:  :!:  !:!  :!:  !:!      !:!   
 ::       ::   :::   :: ::::    ::::     ::  ::::: ::  ::::: ::  :::: ::   
 :         :   : :  : :: ::      :      :     : :  :    : :  :   :: : :    
```

# turnkeylinux-apps - Test Previous Versions

### lamp
* Status: Builds OK. Login OK.
* Versions: LAMP stack / TurnKey Linux 18.0
* Pull request: -
* Git command: git clone --branch master https://github.com/UncleDan/turnkey-lamp.git
* Bugs/Notes: Re-checked still works.
* Date: 21 Nov 2024
-----

### gitlab
* Status: Builds OK. Login OK.
* Versions: GitLab CE  (as of today) / TurnKey Linux 18.1
* Pull request: -
* Git command: git clone https://github.com/turnkeylinux-apps/gitlab.git
* Bugs/Notes: Re-checked still works.
* Date: 4 Jun 2025
-----
### odoo
* Status: Builds OK. Login OK. Install CRM module OK.
* Versions: Odoo 16 (from Odoo apt repository) / TurnKey Linux 18.0
* Pull request: https://github.com/turnkeylinux-apps/odoo/pull/23
* Git command: git clone --branch 18-update-UncleDan https://github.com/UncleDan/turnkey-odoo.git
* Bugs/Notes: Re-checked still works.
* Date: 26 Sep 2023
-----
### phpbb
* Status: Builds OK. Login OK.
* Versions: phpBB 3.3.10 / TurnKey Linux 18.0
* Pull request: https://github.com/turnkeylinux-apps/phpbb/pull/16
* Git command: git clone --branch 18-update https://github.com/UncleDan/turnkey-phpbb.git
* Bugs/Notes: -
* Date: 26 Sep 2023
-----
## From here: buggy or to be checked against TKL 18.0
-----
### foswiki
* Status: Builds OK. Login OK.
* Versions: Foswiki 2.1.8 (as of today) / TurnKey Linux 18RC1
* Pull request: https://github.com/turnkeylinux-apps/foswiki/pull/7
* Git command: git clone --branch 18-update-UncleDan https://github.com/UncleDan/foswiki.git
* Bugs/Notes: -
* Date: 7 Aug 2023
-----
### bookstack
* Status: Builds OK. Login OK.
* Versions: BookStack v23.06.2 (as of today) / TurnKey Linux 18RC1
* Pull request: https://github.com/turnkeylinux-apps/bookstack/pull/3
* Git command: 
* Bugs/Notes: -git clone --branch 18-update-UncleDan https://github.com/UncleDan/bookstack.git
* Date: 4 Aug 2023
-----
### dokuwiki
* Status: Builds OK. Login OK.
* Versions: DokuWiki Release 2023-04-04a "Jack Jackrum" (as of today) / TurnKey Linux 18RC1
* Pull request: https://github.com/turnkeylinux-apps/dokuwiki/pull/21
* Git command: git clone --branch 18-update-UncleDan https://github.com/UncleDan/dokuwiki.git
* Bugs/Notes: -
* Date: 4 Aug 2023
-----
### mediawiki
* Status: Builds OK. Login OK.
* Versions: MediaWiki 1.40.0 / TurnKey Linux 18RC1
* Pull request: https://github.com/turnkeylinux-apps/mediawiki/pull/18
* Git command: git clone --branch 18-update-UncleDan https://github.com/UncleDan/mediawiki.git
* Bugs/Notes: -
* Date: 4 Aug 2023
-----
### omeka
* Status: Builds OK. Login OK.
* Versions: Omeka 3.1.1 / TurnKey Linux 18RC1
* Pull request: https://github.com/turnkeylinux-apps/omeka/pull/20
* Git command: git clone --branch 18-update-UncleDan https://github.com/UncleDan/omeka.git
* Bugs/Notes: -
* Date: 4 Aug 2023
-----
### mahara
* Status: Build OK. Login OK.
* Versions: Mahara 22.10.0 / TurnKey Linux 18RC1
* Pull request: https://github.com/turnkeylinux-apps/mahara/pull/17
* Git command: git clone --branch 18-update-UncleDan https://github.com/UncleDan/mahara.git
* Bugs/Notes: Thanks @OnGle for the hint on the version. Still issues after installation
* Date: 8 Aug 2023
-----
### \*BUG\* ghost
* Status: Builds OK. Runs OK. Login?
* Versions: Ghost 5.57.3 (as of today) / TurnKey Linux 18RC1
* Pull request: https://github.com/turnkeylinux-apps/ghost/pull/19
* Git command: git clone --branch 18-update-UncleDan https://github.com/UncleDan/ghost.git
* Bugs/Notes: I couldn't figure out how to login.
* Date: 3 Aug 2023
-----
### \*BROKEN\* canvas
* Status: Does NOT build.
* Versions: Canvas x.y.z (as of today) / TurnKey Linux 18RC1
* Pull request: -
* Git command: git clone --branch 18-update-UncleDan https://github.com/UncleDan/canvas.git
* Bugs/Notes: "Unable to locate package libssl1.1" - maybe it depends on Ruby but I think it is not updated yet
* Date: 3 Aug 2023
```
update-initramfs: Generating /boot/initrd.img-6.1.0-10-amd64
W: No zstd in /usr/bin:/sbin:/bin, using gzip
Setting up linux-image-amd64 (6.1.38-2) ...
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
E: Unable to locate package libssl1.1
E: Couldn't find any package by glob 'libssl1.1'
E: Couldn't find any package by regex 'libssl1.1'
Traceback (most recent call last):
  File "/usr/bin/fab-install", line 608, in <module>
    cmd_install(
  File "/usr/bin/fab-install", line 247, in cmd_install
    installer.install(packages, ignore_errors)
  File "/usr/lib/python3.11/dist-packages/fablib/installer.py", line 334, in install
    self._install(packages, ignore_errors)
  File "/usr/lib/python3.11/dist-packages/fablib/installer.py", line 185, in _install
    raise Error(
fablib.installer.Error: Errors encountered installing packages
make: *** [/usr/share/fab/product.mk:569: build/stamps/root.build] Error 1
```

-----
### \*BROKEN\* gnusocial
* Status: Builds OK. PHP error on boot
* Versions: GNU Social x.y.z (as of today) / TurnKey Linux 18RC1
* Pull request: https://github.com/turnkeylinux-apps/gnusocial/pull/25
* Git command: git clone --branch 18-update-UncleDan https://github.com/UncleDan/gnusocial.git
* Bugs/Notes: "mysqli_num_rows(): Argument #1 ($result) must be of type mysqli_result, bool given" on start page - maybe PHP version issue? Code is quite old.
* Date: 3 Aug 2023
-----
### \*BROKEN\* moinmoin
* Status: Does NOT build.
* Versions: MoinMoin 2.6.11 (stale from 2014) / TurnKey Linux 18RC1
* Pull request: https://github.com/turnkeylinux-apps/moinmoin/pull/13
* Git command: git clone --branch 18-update-UncleDan https://github.com/UncleDan/moinmoin.git
* Bugs/Notes: Very old software. Seems that "libapache2-mod-wsgi python-flup python-moinmoin" are not recognized in Python3.
* Date: 4 Aug 2023
-----
### \*BROKEN\* redmine
* Status: Does NOT build.
* Versions: Redmine 5.0.5 / TurnKey Linux 18RC1
* Pull request: https://github.com/turnkeylinux-apps/redmine/pull/26
* Git command: git clone --branch 18-update-UncleDan https://github.com/UncleDan/turnkey-redmine.git
* Bugs/Notes: Broken. See error below.
* Date: 8 Aug 2023
```
+ echo 'RBY_VER not set - falling back to latest stable Ruby'
RBY_VER not set - falling back to latest stable Ruby
+ RBY_VER=-l
++ nproc
+ MAKE_OPTS=-j2
+ RUBY_CONFIGURE_OPTS=--disable-install-doc
+ rbenv install -l
+ local command
+ command=install
+ '[' 2 -gt 0 ']'
+ shift
+ case "$command" in
+ command rbenv install -l
3.0.6
3.1.4
3.2.2
jruby-9.4.3.0
mruby-3.2.0
picoruby-3.0.0
truffleruby-23.0.0
truffleruby+graalvm-23.0.0

Only latest stable releases for each Ruby implementation are shown.
Use 'rbenv install --list-all / -L' to show all local versions.
+ rbenv global -l
+ local command
+ command=global
+ '[' 2 -gt 0 ']'
+ shift
+ case "$command" in
+ command rbenv global -l
rbenv: version `-l' not installed
make: *** [/usr/share/fab/product.mk:569: build/stamps/root.patched] Error 1
```

----- 
### \*ABANDONED\* vtigercrm
* Status: ALPHA
* Pull request: -
* Git command: git clone https://github.com/UncleDan/turnkey-vtigercrm.git
* Bugs/Notes: -
* Date: 8 Aug 2023
-----
## From here: already merged TKL 18.0/18.1
-----
### \*MERGED\* concrete-cms
* Status: Builds OK. Login OK.
* Versions: Concrete CMS 9.2.1 / TurnKey Linux 18.0
* Pull request: https://github.com/turnkeylinux-apps/concrete-cms/pull/24
* Git command: git clone --branch 18-update-UncleDan https://github.com/UncleDan/turnkey-concrete-cms.git
* Bugs/Notes: -
* Date: -
-----
### \*MERGED\* drupal10
* Status: Builds OK. Login OK.
* Versions: Drupal 10.x.y (as of today) / TurnKey Linux 18.0
* Pull request: fixed and merged by @JedMeister via https://github.com/turnkeylinux-apps/drupal10/pull/9
* Git command: git clone https://github.com/turnkeylinux-apps/drupal10.git
* Bugs/Notes: -

* Date: 22 Sep 2023
-----
### \*MERGED\* e107
* Status: Builds OK. Login OK.
* Versions: e107 2.3.2 / TurnKey Linux 18.0
* Pull request: https://github.com/turnkeylinux-apps/e107/pull/19
* Git command: git clone --branch 18.0-update-UncleDan https://github.com/UncleDan/turnkey-e107.git
* Bugs/Notes: -
* Date: -
-----
### \*MERGED\* espocrm
* Status: Builds OK. Login OK.
* Versions: EspoCRM 8.0.2 / TurnKey Linux 18.0
* Pull request: https://github.com/turnkeylinux-apps/espocrm/pull/22
* Git command: git clone --branch 18-update-UncleDan https://github.com/UncleDan/turnkey-espocrm.git
* Bugs/Notes: Bump version and Re-checked still works.
* Date: -
-----
### \*MERGED\* joomla4
* Status: Builds OK. Login OK.
* Versions: Joomla 4.2.9 (as of today) / TurnKey Linux 18RC1
* Pull request: https://github.com/turnkeylinux-apps/joomla4/pull/2
* Git command: git clone https://github.com/turnkeylinux-apps/joomla4.git
* Bugs/Notes: -
* Date: 27 Sep 2023
-----
### \*MERGED\* suitecrm
* Status: Builds OK. Login OK.
* Versions: SuiteCRM 8.4 / TurnKey Linux 18.0
* Pull request: merged by @JedMeister via https://github.com/turnkeylinux-apps/suitecrm/pull/14
* Git command: git clone https://github.com/turnkeylinux-apps/suitecrm.git
* Bugs/Notes: -
* Date: 25 Sep 2023
-----
### \*MERGED\* wordpress @OnGle
* Status: Builds OK. Login OK.
* Versions: WordPress 6.2.2 (as of today) / TurnKey Linux 18RC1
* Pull request: https://github.com/OnGle/wordpress/pull/1
* Git command: git clone --branch 18-update-UncleDan https://github.com/UncleDan/turnkey-wordpress.git
* Bugs/Notes: -
* Date: -
-----

## MEMO
```
git clone https://github.com/turnkeylinux-apps/myappname
cd myappname
make
mkdir -p ../mybuilds ; mv build/product.iso ../mybuilds/$(basename $(pwd))-18.1-bookworm-amd64_$(date -r build/product.iso +%Y-%m-%d_%H-%MUTC).iso
mkdir -p ../mybuilds ; mv build/product.iso ../mybuilds/turnkey-$(basename $(pwd))-18.1-bookworm-amd64_$(date -r build/product.iso +%Y-%m-%d_%H-%MUTC).iso
```

Copy of this in repository:
```
https://github.com/UncleDan/turnkeylinux-apps-test-18
```
