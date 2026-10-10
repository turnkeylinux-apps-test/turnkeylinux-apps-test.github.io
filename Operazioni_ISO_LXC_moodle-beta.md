# Da buildcode a template LXC — appliance Moodle beta

Versione 1.4 — 2026-10-10 21:30 (Europe/Rome). Ambiente: TKLDev 19 (Debian 13 Trixie).

## Appliance

Il buildcode sta nel repository `https://github.com/turnkeylinux-apps-test/turnkey-moodle.git`,
un branch per appliance:

| Branch / nome      | Moodle                       | Nome ISO                                 | Template LXC                                      |
|--------------------|------------------------------|------------------------------------------|---------------------------------------------------|
| `moodle53-beta`    | ramo 5.3 (MOODLE_503_STABLE) | `turnkey-moodle53-beta-19.0-trixie-amd64`    | `debian-13-turnkey-moodle53-beta_19.0-1_amd64.tar.gz`    |
| `moodle45up-beta`  | ramo 4.5 (MOODLE_405_STABLE) | `turnkey-moodle45up-beta-19.0-trixie-amd64`  | `debian-13-turnkey-moodle45up-beta_19.0-1_amd64.tar.gz`  |
| `moodle42up-beta`  | ramo 4.2 (MOODLE_402_STABLE) | `turnkey-moodle42up-beta-19.0-trixie-amd64`  | `debian-13-turnkey-moodle42up-beta_19.0-1_amd64.tar.gz`  |
| `moodle41up-beta`  | ramo 4.1 (MOODLE_401_STABLE) | `turnkey-moodle41up-beta-19.0-trixie-amd64`  | `debian-13-turnkey-moodle41up-beta_19.0-1_amd64.tar.gz`  |
| `moodle405up-beta` | 4.0.5 (tag v4.0.5)           | `turnkey-moodle405up-beta-19.0-trixie-amd64` | `debian-13-turnkey-moodle405up-beta_19.0-1_amd64.tar.gz` |

Il nome viene dalla prima riga del `changelog` del branch (`turnkey-<nome>-19.0`) e deve coincidere
con il nome del branch. buildtasks accetta solo minuscole, numeri e trattini: niente maiuscole,
underscore o punti.

Tutti i comandi della guida sono su una sola riga e vanno eseguiti come root su TKLDev.

## 1. Installazione degli script

Dalla cartella in cui hai scaricato i due file:

```bash
install -m 755 tkl-build-lxc_v1.0_20261010-2018 /usr/local/bin/tkl-build-lxc && install -m 755 tkl-build-lxc-setup_v1.0_20261010-2018 /usr/local/bin/tkl-build-lxc-setup
```

| Script                | Cosa fa                                                                                   |
|-----------------------|-------------------------------------------------------------------------------------------|
| `tkl-build-lxc-setup` | passaggi una tantum di buildtasks; fa solo quelli che mancano                             |
| `tkl-build-lxc`       | da un branch del repository alla ISO e al template LXC archiviati; si ferma al primo errore |

I due script non si richiamano a vicenda: `tkl-build-lxc` controlla che il setup sia stato fatto e,
se manca, si ferma e chiede di lanciare `tkl-build-lxc-setup`.

## 2. Setup una tantum

Crea `config` di buildtasks (da `config.example`), le cartelle `/mnt/isos` e `/mnt/builds`, e corregge
`generate-signature`: nei branch 18.x e 19.x di buildtasks accetta solo nomi `turnkey*.iso` e
`bootstrap-*.tar.gz`, quindi `bt-container` finirebbe con
`FATAL [generate-signature]: Unexpected filename` sul `.tar.gz` del container.

```bash
tkl-build-lxc-setup
```

Solo controllo, senza modificare nulla (exit 1 se manca qualcosa):

```bash
tkl-build-lxc-setup -c
```

La correzione è una modifica locale al repository di buildtasks. Per aggiornare buildtasks annullala,
aggiorna e riapplicala:

```bash
git -C /turnkey/buildtasks checkout bin/generate-signature && git -C /turnkey/buildtasks pull && tkl-build-lxc-setup
```

## 3. Build di un'appliance

```bash
tkl-build-lxc turnkey-moodle moodle53-beta
```

Primo parametro: il repository (`turnkey-moodle`). Secondo: il branch (`moodle53-beta`,
`moodle45up-beta`, `moodle42up-beta`, `moodle41up-beta`, `moodle405up-beta`).

Lo script esegue 8 passi e si ferma al primo errore, indicando passo, riga e comando:

1. controlli: root, strumenti, buildtasks, setup una tantum, nome del branch valido;
2. clone del repository in `/turnkey/fab/products/turnkey-moodle` la prima volta, poi `fetch` e
   checkout del branch portato allo stato remoto (si ferma con modifiche non committate, branch
   divergente o inesistente, `origin` diverso);
3. controllo che il changelog chiami l'appliance come il branch;
4. `make clean && make`;
5. `iso-release` → `/mnt/isos/<nome>.iso` e `.hash`;
6. `bt-container` → `/mnt/builds/container/*.tar.gz` e `.hash`;
7. archiviazione in `/turnkey/fab/products/mybuilds` di ISO, template e hash con il timestamp UTC
   di creazione della ISO;
8. verifica degli hash archiviati.

Ricostruire dallo stato attuale, senza `make clean`:

```bash
tkl-build-lxc -n turnkey-moodle moodle53-beta
```

Tutte e cinque in sequenza (se una fallisce passa alla successiva e alla fine elenca le fallite):

```bash
F=""; for B in moodle53-beta moodle45up-beta moodle42up-beta moodle41up-beta moodle405up-beta; do tkl-build-lxc turnkey-moodle "$B" || F="$F $B"; done; echo "Fallite:${F:- nessuna}"; ls -lh /turnkey/fab/products/mybuilds/
```

Percorsi e repository si possono cambiare con variabili d'ambiente (`GIT_BASE_URL`, `PRODUCTS_DIR`,
`ARCHIVE_DIR`, `BT`), ad esempio:

```bash
GIT_BASE_URL=https://github.com/UncleDan ARCHIVE_DIR=/root/archivio tkl-build-lxc turnkey-moodle moodle53-beta
```

Risultato in `/turnkey/fab/products/mybuilds`, ad esempio:

```text
turnkey-moodle53-beta-19.0-trixie-amd64_2026-10-10_16-50UTC.iso
turnkey-moodle53-beta-19.0-trixie-amd64_2026-10-10_16-50UTC.iso.hash
debian-13-turnkey-moodle53-beta_19.0-1_amd64_2026-10-10_16-50UTC.tar.gz
debian-13-turnkey-moodle53-beta_19.0-1_amd64_2026-10-10_16-50UTC.tar.gz.hash
```

Lo stesso timestamp su ISO e template indica che vengono dalla stessa build.

## 4. Verifica degli hash archiviati

```bash
(cd /turnkey/fab/products/mybuilds && for h in *.hash; do sha256sum -c "$h" 2>/dev/null; done)
```

## 5. Caricamento su Proxmox

Copia il `.tar.gz` in `/var/lib/vz/template/cache/` sul nodo Proxmox (sostituisci `<proxmox>`):

```bash
scp /turnkey/fab/products/mybuilds/debian-13-turnkey-moodle53-beta_19.0-1_amd64_*.tar.gz root@<proxmox>:/var/lib/vz/template/cache/
```

In alternativa dall'interfaccia web: storage `local` → **CT Templates** → **Upload**.

## Appendice: procedura manuale (senza script)

Gli stessi passi a mano, un comando per riga.

### A.1 Una tantum

```bash
[ -d /turnkey/buildtasks/config ] || cp -R /turnkey/buildtasks/config.example /turnkey/buildtasks/config; mkdir -p /mnt/isos /mnt/builds
```

```bash
F=/turnkey/buildtasks/bin/generate-signature; grep -q 'debian-\*-turnkey-\*.tar.gz' $F || sed -i '/^elif \[\[ "\$filename" == "turnkey"\*"\.iso" \]\]; then$/i elif [[ "$filename" == debian-*-turnkey-*.tar.gz ]]; then\n    BRANCH="$(get_tkl_ver_no "$CODENAME")"' $F; git -C /turnkey/buildtasks diff --stat
```

### A.2 Repository e branch

```bash
[ -d /turnkey/fab/products/turnkey-moodle ] || git clone https://github.com/turnkeylinux-apps-test/turnkey-moodle.git /turnkey/fab/products/turnkey-moodle; cd /turnkey/fab/products/turnkey-moodle && git fetch --prune origin && git checkout moodle53-beta && git merge --ff-only origin/moodle53-beta && head -1 changelog
```

### A.3 ISO

```bash
make clean && make && ls -lh build/product.iso
```

### A.4 Pubblicazione della ISO in /mnt/isos

```bash
/turnkey/buildtasks/bin/iso-release --force --no-screens /mnt/isos && ls -lh /mnt/isos/$(turnkey-version build/root.sandbox).iso*
```

### A.5 Template LXC

```bash
/turnkey/buildtasks/bt-container "$(turnkey-version build/root.sandbox | sed 's/^turnkey-//')" && ls -lh /mnt/builds/container/
```

### A.6 Archiviazione con il timestamp UTC della ISO

```bash
mkdir -p ../mybuilds && N=$(turnkey-version build/root.sandbox) && A=${N#turnkey-} && A=${A%-*-*-*} && T=$(date -u -r build/product.iso +%Y-%m-%d_%H-%MUTC) && for f in /mnt/isos/$N.iso /mnt/builds/container/debian-*-turnkey-${A}_*.tar.gz; do [ -f "$f" ] || continue; b=$(basename "$f"); case $b in *.iso) n=${b%.iso}_$T.iso;; *) n=${b%.tar.gz}_$T.tar.gz;; esac; mv "$f" "../mybuilds/$n" && if [ -f "$f.hash" ]; then sed "s|$b|$n|g" "$f.hash" > "../mybuilds/$n.hash" && rm "$f.hash"; else (cd ../mybuilds && sha256sum "$n" > "$n.hash"); fi; done
```

## Note

- Il warning sulla firma GPG mancante è normale per una build locale: l'hash viene verificato comunque.
- Se rifai la ISO di un'appliance già pubblicata, `--force` sovrascrive i file in `/mnt/isos`;
  lo script si ferma invece se nell'archivio esiste già un file con lo stesso nome.
- `bt-container` imposta `ON_MEMORY=0.33` solo quando il nome è esattamente `moodle`; con i nomi
  beta usa il valore predefinito `0.25`.
