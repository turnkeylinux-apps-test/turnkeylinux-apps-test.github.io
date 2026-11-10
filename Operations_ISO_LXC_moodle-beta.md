# From buildcode to LXC template — Moodle beta appliances

Version 1.8 — 2026-11-10 18:31 (Europe/Rome). Environment: TKLDev 19 (Debian 13 Trixie).

🇬🇧 English translation of the Italian guide [Operazioni_ISO_LXC_moodle-beta.md](https://github.com/turnkeylinux-apps-test/turnkeylinux-apps-test.github.io/blob/recheck-26.06/Operazioni_ISO_LXC_moodle-beta.md) 🇮🇹

## Appliances

The buildcode lives in the repository `https://github.com/turnkeylinux-apps-test/turnkey-moodle.git`,
one branch per appliance:

| Branch / name      | Moodle                       | ISO name                                 | LXC template                                      |
|--------------------|------------------------------|------------------------------------------|---------------------------------------------------|
| `moodle53-beta`    | 5.3 branch (MOODLE_503_STABLE) | `turnkey-moodle53-beta-19.0-trixie-amd64`    | `debian-13-turnkey-moodle53-beta_19.0-1_amd64.tar.gz`    |
| `moodle45up-beta`  | 4.5 branch (MOODLE_405_STABLE) | `turnkey-moodle45up-beta-19.0-trixie-amd64`  | `debian-13-turnkey-moodle45up-beta_19.0-1_amd64.tar.gz`  |
| `moodle42up-beta`  | 4.2 branch (MOODLE_402_STABLE) | `turnkey-moodle42up-beta-19.0-trixie-amd64`  | `debian-13-turnkey-moodle42up-beta_19.0-1_amd64.tar.gz`  |
| `moodle41up-beta`  | 4.1 branch (MOODLE_401_STABLE) | `turnkey-moodle41up-beta-19.0-trixie-amd64`  | `debian-13-turnkey-moodle41up-beta_19.0-1_amd64.tar.gz`  |
| `moodle405up-beta` | 4.0.5 (tag v4.0.5)           | `turnkey-moodle405up-beta-19.0-trixie-amd64` | `debian-13-turnkey-moodle405up-beta_19.0-1_amd64.tar.gz` |

The name comes from the first line of the branch's `changelog` (`turnkey-<name>-19.0`) and must match
the branch name. buildtasks only accepts lowercase letters, digits and hyphens: no uppercase,
underscores or dots.

All commands in this guide are on a single line and must be run as root on TKLDev.

## 1. Downloading and installing the scripts

The scripts are downloaded in raw form from a base address set in the `RAW` variable
(for now the `main` branch of the repository
`https://github.com/turnkeylinux-apps-test/turnkeylinux-apps-test.github.io.git`, in the root,
with the names `tkl-build-lxc` and `tkl-build-lxc-setup`). To use a different repository, branch or
folder, just change `RAW` at the start of the line.

With `wget`:

```bash
RAW=https://raw.githubusercontent.com/turnkeylinux-apps-test/turnkeylinux-apps-test.github.io/main; for s in tkl-build-lxc tkl-build-lxc-setup tkl-build-clean; do wget -q -O "/tmp/$s" "$RAW/$s" && head -1 "/tmp/$s" | grep -q '^#!/bin/bash' && install -m 755 "/tmp/$s" "/usr/local/bin/$s" && sed -n 2p "/usr/local/bin/$s" || echo "ERRORE: $s non scaricato da $RAW/$s"; rm -f "/tmp/$s"; done
```

With `curl`:

```bash
RAW=https://raw.githubusercontent.com/turnkeylinux-apps-test/turnkeylinux-apps-test.github.io/main; for s in tkl-build-lxc tkl-build-lxc-setup tkl-build-clean; do curl -fsSL -o "/tmp/$s" "$RAW/$s" && head -1 "/tmp/$s" | grep -q '^#!/bin/bash' && install -m 755 "/tmp/$s" "/usr/local/bin/$s" && sed -n 2p "/usr/local/bin/$s" || echo "ERRORE: $s non scaricato da $RAW/$s"; rm -f "/tmp/$s"; done
```

Each script is installed only if the download succeeded and the file really is a bash script
(no error pages in place of the script). For each one, the line with name and release is shown,
for example `# tkl-build-lxc by Daniele Lolli (UncleDan) feat. Claude AI - Release 1.0 - …`.
To update the scripts, just run the same command again.

Alternatively, from files downloaded from the chat, from the folder where they are:

```bash
install -m 755 tkl-build-lxc_v1.0_20261010-2018 /usr/local/bin/tkl-build-lxc && install -m 755 tkl-build-lxc-setup_v1.0_20261010-2018 /usr/local/bin/tkl-build-lxc-setup && install -m 755 tkl-build-clean /usr/local/bin/tkl-build-clean
```

| Script                | What it does                                                                              |
|-----------------------|-------------------------------------------------------------------------------------------|
| `tkl-build-lxc-setup` | one-time buildtasks steps; only does the missing ones                                     |
| `tkl-build-lxc`       | from a repository branch to the archived ISO and LXC template; stops at the first error   |
| `tkl-build-clean`     | lists and deletes all existing builds (ISOs, templates, leftovers, `build/` folders) to free space (see 6.6) |

The two scripts do not call each other: `tkl-build-lxc` checks that the setup has been done and,
if not, stops and asks you to run `tkl-build-lxc-setup`. `tkl-build-clean` is independent of the other two.

## 2. One-time setup

It creates buildtasks' `config` (from `config.example`), the `/mnt/isos` and `/mnt/builds` folders, and fixes
`generate-signature`: in the 18.x and 19.x branches of buildtasks it only accepts `turnkey*.iso` and
`bootstrap-*.tar.gz` names, so `bt-container` would end with
`FATAL [generate-signature]: Unexpected filename` on the container's `.tar.gz`.

```bash
tkl-build-lxc-setup
```

Check only, without changing anything (exit 1 if something is missing):

```bash
tkl-build-lxc-setup -c
```

The fix is a local modification to the buildtasks repository. To update buildtasks, revert it,
update and reapply it:

```bash
git -C /turnkey/buildtasks checkout bin/generate-signature && git -C /turnkey/buildtasks pull && tkl-build-lxc-setup
```

## 3. Building an appliance

```bash
tkl-build-lxc turnkey-moodle moodle53-beta
```

First parameter: the repository (`turnkey-moodle`). Second: the branch (`moodle53-beta`,
`moodle45up-beta`, `moodle42up-beta`, `moodle41up-beta`, `moodle405up-beta`).

The script runs 8 steps and stops at the first error, reporting step, line and command:

1. checks: root, tools, buildtasks, one-time setup, valid branch name;
2. clone of the repository into `/turnkey/fab/products/turnkey-moodle` the first time, then `fetch` and
   checkout of the branch brought to the remote state (it stops on uncommitted changes, diverged or
   nonexistent branch, different `origin`);
3. check that the changelog names the appliance like the branch;
4. `make clean && make`;
5. `iso-release` → `/mnt/isos/<name>.iso` and `.hash`;
6. `bt-container` → `/mnt/builds/container/*.tar.gz` and `.hash`;
7. archiving in `/turnkey/fab/products/mybuilds` of ISO, template and hashes with the UTC timestamp
   of the ISO's creation;
8. verification of the archived hashes.

Rebuild from the current state, without `make clean`:

```bash
tkl-build-lxc -n turnkey-moodle moodle53-beta
```

All five in sequence (if one fails it moves on to the next and lists the failed ones at the end):

```bash
F=""; for B in moodle53-beta moodle45up-beta moodle42up-beta moodle41up-beta moodle405up-beta; do tkl-build-lxc turnkey-moodle "$B" || F="$F $B"; done; echo "Fallite:${F:- nessuna}"; ls -lh /turnkey/fab/products/mybuilds/
```

Paths and repository can be changed with environment variables (`GIT_BASE_URL`, `PRODUCTS_DIR`,
`ARCHIVE_DIR`, `BT`), for example:

```bash
GIT_BASE_URL=https://github.com/UncleDan ARCHIVE_DIR=/root/archivio tkl-build-lxc turnkey-moodle moodle53-beta
```

Result in `/turnkey/fab/products/mybuilds`, for example:

```text
turnkey-moodle53-beta-19.0-trixie-amd64_2026-10-10_16-50UTC.iso
turnkey-moodle53-beta-19.0-trixie-amd64_2026-10-10_16-50UTC.iso.hash
debian-13-turnkey-moodle53-beta_19.0-1_amd64_2026-10-10_16-50UTC.tar.gz
debian-13-turnkey-moodle53-beta_19.0-1_amd64_2026-10-10_16-50UTC.tar.gz.hash
```

The same timestamp on ISO and template means they come from the same build.

## 4. Verifying the archived hashes

```bash
(cd /turnkey/fab/products/mybuilds && for h in *.hash; do sha256sum -c "$h" 2>/dev/null; done)
```

## 5. Uploading to Proxmox

Copy the `.tar.gz` to `/var/lib/vz/template/cache/` on the Proxmox node (replace `<proxmox>`):

```bash
scp /turnkey/fab/products/mybuilds/debian-13-turnkey-moodle53-beta_19.0-1_amd64_*.tar.gz root@<proxmox>:/var/lib/vz/template/cache/
```

Alternatively from the web interface: `local` storage → **CT Templates** → **Upload**.

## 6. Problems and solutions

### 6.1 Out of disk space

Symptom, usually during `bt-container` (step 6):

```text
gzip: stdout: No space left on device
FATAL [tkl-build-lxc]: step "6/8 build LXC template (bt-container)" failed ...
```

During the conversion, the ISO, the extracted ISO (`.rootfs` and `.cdroot`, a few GB) and the
`.tar.gz` must all fit on the disk at the same time: to be safe you need 5–6 GB free at that
moment. When `bt-container` fails it does not reach its final cleanup and leaves the extracted
ISO and the incomplete `.tar.gz` on the disk.

Where the space went:

```bash
df -h / /mnt /turnkey; du -sh /mnt/isos /mnt/builds/container/* /turnkey/fab/products/* /turnkey/fab/products/turnkey-moodle/build 2>/dev/null | sort -h | tail -15
```

Before deleting the leftovers, check that there are no mounts left behind (it must print nothing):

```bash
mount | grep /mnt/builds
```

Delete the leftovers of the failed attempts (with the script, successful templates have already been
moved to the archive, so only incomplete ones remain in `/mnt/builds/container`):

```bash
rm -rf /mnt/builds/container/*.rootfs /mnt/builds/container/*.cdroot /mnt/builds/container/debian-*.tar.gz*; df -h /mnt
```

Resume without redoing the build: with `-n` the script skips `make clean`, so `make` finishes immediately
and only `iso-release`, `bt-container` and archiving are repeated (replace the branch):

```bash
tkl-build-lxc -n turnkey-moodle moodle405up-beta
```

To free everything in one go, after listing what will be deleted, see 6.6 (`tkl-build-clean`).

To avoid filling the disk again:

- move the contents of `/turnkey/fab/products/mybuilds` elsewhere (Proxmox, NAS), since it grows by
  one ISO and one template with every build, and then delete it from TKLDev;
- `/mnt/isos` holds the ISO of the last non-archived build and can be emptied;
- the repository's `build/` folder stays until the next `make clean`;
- if TKLDev is a VM, the simplest solution is to enlarge its disk.

### 6.2 Missing bootstrap

Symptom, at the start of `make` (step 4):

```text
/usr/share/fab/product.mk:48: *** bootstrap /turnkey/fab/bootstraps/trixie-amd64 not found - download or build it first.  Stop.
```

The bootstrap is the minimal Debian base from which `make` builds every appliance. It is downloaded again
from the TurnKey mirror with the same procedure as buildtasks' `bt-iso` (download, GPG signature
verification with the TurnKey trixie key, unpacking) plus the sha256 check:

```bash
mkdir -p /turnkey/fab/bootstraps && cd /turnkey/fab/bootstraps && B=bootstrap-trixie-amd64 && U=http://mirror.turnkeylinux.org/turnkeylinux/images/bootstrap && wget -nc "$U/$B.tar.gz" "$U/$B.tar.gz.hash" && K=$(mktemp -d)/k.gpg && gpg --no-default-keyring --keyring "$K" --import /turnkey/fab/common/keys/tkl-trixie-images.asc && gpg --no-default-keyring --keyring "$K" --verify "$B.tar.gz.hash" && grep -q "$(sha256sum "$B.tar.gz" | cut -d' ' -f1)" "$B.tar.gz.hash" && mkdir trixie-amd64 && tar -zxf "$B.tar.gz" -C trixie-amd64 && ls trixie-amd64 && cd -
```

After extraction the `.tar.gz` can be deleted to recover space; the `.hash`, instead, must
stay, because `generate-buildenv` reads it (called by `iso-release`, step 5):

```bash
rm -f /turnkey/fab/bootstraps/bootstrap-trixie-amd64.tar.gz
```

Then run the build again (with `-n` if the repository's `build/` folder is still there):

```bash
tkl-build-lxc turnkey-moodle moodle405up-beta
```

### 6.3 Moodle clone refused by git.moodle.org

Symptom, during `make` (step 4):

```text
fatal: remote error: git.moodle.org is retiring anonymous git:// access to moodle.git. Please clone https://github.com/moodle/moodle.git instead.: /moodle.git
```

In the branch's `conf.d/main` the clone must be done from `https://github.com/moodle/moodle.git` instead of
`git://git.moodle.org/moodle.git` (already fixed in `moodle53-beta` and `moodle405up-beta`).
To see which branches of the repository still use `git://`:

```bash
cd /turnkey/fab/products/turnkey-moodle && git fetch -q origin && for b in $(git for-each-ref --format='%(refname:short)' refs/remotes/origin | grep -v '/HEAD$'); do git grep -n 'git://git.moodle.org' "$b" -- conf.d/main; done; cd -
```

### 6.4 What not to delete to free space

Under `/turnkey/fab`, do not touch `bootstraps` (see 6.2), `common` and, if present, `cdroots`:
they are needed by every build. The same goes for `/turnkey/buildtasks` and for the bootstrap's `.hash`.
The folders that grow and can be emptied are `/turnkey/fab/products/mybuilds` (after moving its
contents elsewhere), `/mnt/isos` and the leftovers in `/mnt/builds/container` (see 6.1).

### 6.5 GPG key cannot be downloaded (signature-verify)

Symptom, during `bt-container` (step 6):

```text
FATAL [signature-verify]: failed to download GPG key DEE90D9970AE35D55B7C6C2DC6D254B76A9D9430.
FATAL [iso-verify]: turnkey-moodle405up-beta-19.0-trixie-amd64.iso.hash verification failed
FATAL [tkl-build-lxc]: step "6/8 build LXC template (bt-container)" failed ...
```

`bt-container` calls `iso-verify`, which calls `signature-verify`: this requires the `BT_GPGKEY` key (set in
buildtasks' `config`) to be in root's keyring. If it is missing, it downloads it from `keyserver.ubuntu.com` and, as
an alternative, from `www.turnkeylinux.org`; if both fail it stops. A missing signature on the hash of a local
build is not a problem (just a warning): the blocker is the key download.

Note: when it fails, `iso-verify` renames the ISO to `.iso.corrupt` and deletes its `.hash`. The ISO is not
actually corrupt, but the `.corrupt` file takes up space and must be removed before resuming.

Check which key is configured and whether it matches the one in TKLDev's files (fingerprint):

```bash
grep -rn BT_GPGKEY /turnkey/buildtasks/config/; gpg --show-keys --with-fingerprint /turnkey/fab/common/keys/tkl-trixie-images.asc
```

If the fingerprint matches, import the key without network access and verify:

```bash
gpg --import /turnkey/fab/common/keys/tkl-trixie-images.asc && gpg --list-keys DEE90D9970AE35D55B7C6C2DC6D254B76A9D9430
```

If it does not match, try the keyserver by hand to see the real error (replace the key with the one read from `config`):

```bash
gpg --keyserver hkps://keyserver.ubuntu.com --recv-keys DEE90D9970AE35D55B7C6C2DC6D254B76A9D9430
```

If it fails, check TKLDev's network and restart dirmngr:

```bash
curl -sI https://keyserver.ubuntu.com | head -1; gpgconf --kill dirmngr
```

Then remove the `.corrupt` file and resume without redoing `make` (replace the branch):

```bash
rm -f /mnt/isos/*.corrupt; tkl-build-lxc -n turnkey-moodle moodle405up-beta
```

### 6.6 Cleaning all builds with tkl-build-clean

To free space without restoring the virtual machine, `tkl-build-clean` lists all existing builds with their
sizes and then deletes them, after confirmation:

| What                                              | Default path                          |
|---------------------------------------------------|---------------------------------------|
| ISOs, `.hash`, `.corrupt`                         | `/mnt/isos/*`                         |
| templates, `.rootfs`, `.cdroot`, bt-container leftovers | `/mnt/builds/container/*`       |
| `make` output                                     | `/turnkey/fab/products/*/build`       |
| archive of ISOs and templates with timestamp      | `/turnkey/fab/products/mybuilds/*`    |

List only, without deleting anything:

```bash
tkl-build-clean -l
```

List and delete with confirmation (you must type `si`, Italian for "yes"):

```bash
tkl-build-clean
```

Options: `-y` skips the confirmation, `-k` keeps the `mybuilds` archive, `-h` help. Paths can be changed with the
`PRODUCTS_DIR`, `ARCHIVE_DIR`, `ISOS_DIR`, `CONTAINER_DIR` variables.

By default it also deletes the `mybuilds` archive: if it contains ISOs or templates you still need, move them
elsewhere first (see 5) or use `-k`. The script refuses to delete if it finds active mounts under the paths to
clean (`umount` first). It never touches `bootstraps`, `common`, `cdroots`, `/turnkey/buildtasks`, the cloned
repositories and their code (see 6.4). After cleaning, the next build starts from scratch (`make` redoes everything).

## Appendix: manual procedure (without scripts)

The same steps by hand, one command per line.

### A.1 One-time

```bash
[ -d /turnkey/buildtasks/config ] || cp -R /turnkey/buildtasks/config.example /turnkey/buildtasks/config; mkdir -p /mnt/isos /mnt/builds
```

```bash
F=/turnkey/buildtasks/bin/generate-signature; grep -q 'debian-\*-turnkey-\*.tar.gz' $F || sed -i '/^elif \[\[ "\$filename" == "turnkey"\*"\.iso" \]\]; then$/i elif [[ "$filename" == debian-*-turnkey-*.tar.gz ]]; then\n    BRANCH="$(get_tkl_ver_no "$CODENAME")"' $F; git -C /turnkey/buildtasks diff --stat
```

### A.2 Repository and branch

```bash
[ -d /turnkey/fab/products/turnkey-moodle ] || git clone https://github.com/turnkeylinux-apps-test/turnkey-moodle.git /turnkey/fab/products/turnkey-moodle; cd /turnkey/fab/products/turnkey-moodle && git fetch --prune origin && git checkout moodle53-beta && git merge --ff-only origin/moodle53-beta && head -1 changelog
```

### A.3 ISO

```bash
make clean && make && ls -lh build/product.iso
```

### A.4 Publishing the ISO to /mnt/isos

```bash
/turnkey/buildtasks/bin/iso-release --force --no-screens /mnt/isos && ls -lh /mnt/isos/$(turnkey-version build/root.sandbox).iso*
```

### A.5 LXC template

```bash
/turnkey/buildtasks/bt-container "$(turnkey-version build/root.sandbox | sed 's/^turnkey-//')" && ls -lh /mnt/builds/container/
```

### A.6 Archiving with the ISO's UTC timestamp

```bash
mkdir -p ../mybuilds && N=$(turnkey-version build/root.sandbox) && A=${N#turnkey-} && A=${A%-*-*-*} && T=$(date -u -r build/product.iso +%Y-%m-%d_%H-%MUTC) && for f in /mnt/isos/$N.iso /mnt/builds/container/debian-*-turnkey-${A}_*.tar.gz; do [ -f "$f" ] || continue; b=$(basename "$f"); case $b in *.iso) n=${b%.iso}_$T.iso;; *) n=${b%.tar.gz}_$T.tar.gz;; esac; mv "$f" "../mybuilds/$n" && if [ -f "$f.hash" ]; then sed "s|$b|$n|g" "$f.hash" > "../mybuilds/$n.hash" && rm "$f.hash"; else (cd ../mybuilds && sha256sum "$n" > "$n.hash"); fi; done
```

## Notes

- The warning about the missing GPG signature is normal for a local build: the hash is verified anyway.
- If you rebuild the ISO of an already published appliance, `--force` overwrites the files in `/mnt/isos`;
  the script, instead, stops if a file with the same name already exists in the archive.
- `bt-container` sets `ON_MEMORY=0.33` only when the name is exactly `moodle`; with the beta
  names it uses the default value `0.25`.
