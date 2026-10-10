# Da buildcode a template LXC — appliance Moodle beta

Versione 1.3 — 2026-10-10 20:01 (Europe/Rome). Ambiente: TKLDev 19 (Debian 13 Trixie).

## Appliance

| Cartella / nome    | Moodle                       | Nome ISO                                 | Template LXC                                      |
|--------------------|------------------------------|------------------------------------------|---------------------------------------------------|
| `moodle53-beta`    | ramo 5.3 (MOODLE_503_STABLE) | `turnkey-moodle53-beta-19.0-trixie-amd64`    | `debian-13-turnkey-moodle53-beta_19.0-1_amd64.tar.gz`    |
| `moodle45up-beta`  | ramo 4.5 (MOODLE_405_STABLE) | `turnkey-moodle45up-beta-19.0-trixie-amd64`  | `debian-13-turnkey-moodle45up-beta_19.0-1_amd64.tar.gz`  |
| `moodle42up-beta`  | ramo 4.2 (MOODLE_402_STABLE) | `turnkey-moodle42up-beta-19.0-trixie-amd64`  | `debian-13-turnkey-moodle42up-beta_19.0-1_amd64.tar.gz`  |
| `moodle41up-beta`  | ramo 4.1 (MOODLE_401_STABLE) | `turnkey-moodle41up-beta-19.0-trixie-amd64`  | `debian-13-turnkey-moodle41up-beta_19.0-1_amd64.tar.gz`  |
| `moodle405up-beta` | 4.0.5 (tag v4.0.5)           | `turnkey-moodle405up-beta-19.0-trixie-amd64` | `debian-13-turnkey-moodle405up-beta_19.0-1_amd64.tar.gz` |

Il nome viene dalla prima riga del `changelog` (`turnkey-<nome>-19.0`). buildtasks accetta solo
minuscole, numeri e trattini: non usare maiuscole, underscore o punti nel nome.

## 1. Una tantum: configurazione di buildtasks

Crea la configurazione di buildtasks (se manca) e la cartella delle ISO:

```bash
[ -d /turnkey/buildtasks/config ] || cp -R /turnkey/buildtasks/config.example /turnkey/buildtasks/config; mkdir -p /mnt/isos
```

Correggi `generate-signature` (branch 18.x e 19.x di buildtasks): accetta solo nomi
`turnkey*.iso` e `bootstrap-*.tar.gz`, quindi `bt-container` finirebbe con
`FATAL [generate-signature]: Unexpected filename` sul `.tar.gz` del container. Il comando aggiunge
il caso `debian-*-turnkey-*.tar.gz` ed è idempotente (non fa nulla se la correzione c'è già):

```bash
F=/turnkey/buildtasks/bin/generate-signature; grep -q 'debian-\*-turnkey-\*.tar.gz' $F || sed -i '/^elif \[\[ "\$filename" == "turnkey"\*"\.iso" \]\]; then$/i elif [[ "$filename" == debian-*-turnkey-*.tar.gz ]]; then\n    BRANCH="$(get_tkl_ver_no "$CODENAME")"' $F; git -C /turnkey/buildtasks diff --stat
```

La modifica resta locale. Prima di aggiornare buildtasks annullala, poi riapplicala con il comando sopra:

```bash
git -C /turnkey/buildtasks checkout bin/generate-signature && git -C /turnkey/buildtasks pull
```

## 2. Ciclo da ripetere per ogni appliance

Tutti i comandi si lanciano dalla cartella dell'appliance. Ogni riga è indipendente dalle altre.

### 2.1 Entra nella cartella dell'appliance

```bash
cd /turnkey/fab/products/moodle53-beta
```

(oppure `moodle45up-beta`, `moodle42up-beta`, `moodle41up-beta`, `moodle405up-beta`)

### 2.2 Costruisci la ISO

```bash
make clean && make && ls -lh build/product.iso
```

### 2.3 Pubblica la ISO in /mnt/isos (nome ufficiale + .hash)

```bash
/turnkey/buildtasks/bin/iso-release --force --no-screens /mnt/isos && ls -lh /mnt/isos/$(turnkey-version build/root.sandbox).iso*
```

### 2.4 Crea il template LXC

```bash
/turnkey/buildtasks/bt-container "$(turnkey-version build/root.sandbox | sed 's/^turnkey-//')" && ls -lh /mnt/builds/container/
```

Il warning sulla firma GPG mancante è normale per una build locale: l'hash viene verificato comunque.

### 2.5 Rinomina e archivia ISO e template con l'ora di creazione della ISO (UTC)

Sposta in `../mybuilds` la ISO di `/mnt/isos` e il `.tar.gz` del container, aggiungendo a entrambi
lo stesso timestamp UTC preso da `build/product.iso`. Gli `.hash` vengono rinominati e aggiornati
all'interno con il nuovo nome (se uno manca, viene creato con `sha256sum`).

```bash
mkdir -p ../mybuilds && N=$(turnkey-version build/root.sandbox) && A=${N#turnkey-} && A=${A%-*-*-*} && T=$(date -u -r build/product.iso +%Y-%m-%d_%H-%MUTC) && for f in /mnt/isos/$N.iso /mnt/builds/container/debian-*-turnkey-${A}_*.tar.gz; do [ -f "$f" ] || continue; b=$(basename "$f"); case $b in *.iso) n=${b%.iso}_$T.iso;; *) n=${b%.tar.gz}_$T.tar.gz;; esac; mv "$f" "../mybuilds/$n" && if [ -f "$f.hash" ]; then sed "s|$b|$n|g" "$f.hash" > "../mybuilds/$n.hash" && rm "$f.hash"; else (cd ../mybuilds && sha256sum "$n" > "$n.hash"); fi; done
```

Risultato, ad esempio:

```text
turnkey-moodle53-beta-19.0-trixie-amd64_2026-10-10_16-50UTC.iso
turnkey-moodle53-beta-19.0-trixie-amd64_2026-10-10_16-50UTC.iso.hash
debian-13-turnkey-moodle53-beta_19.0-1_amd64_2026-10-10_16-50UTC.tar.gz
debian-13-turnkey-moodle53-beta_19.0-1_amd64_2026-10-10_16-50UTC.tar.gz.hash
```

### 2.6 Verifica gli hash archiviati

```bash
(cd ../mybuilds && for h in *.hash; do sha256sum -c "$h" 2>/dev/null; done)
```

## 3. Facoltativo: tutte e cinque in sequenza

Esegue i passi da 2.1 a 2.5 per ogni appliance e salta quelle la cui build fallisce:

```bash
for APP in moodle53-beta moodle45up-beta moodle42up-beta moodle41up-beta moodle405up-beta; do cd /turnkey/fab/products/$APP || continue; make clean && make || { echo "BUILD FALLITA: $APP"; continue; }; /turnkey/buildtasks/bin/iso-release --force --no-screens /mnt/isos && /turnkey/buildtasks/bt-container "$(turnkey-version build/root.sandbox | sed 's/^turnkey-//')" && { mkdir -p ../mybuilds && N=$(turnkey-version build/root.sandbox) && A=${N#turnkey-} && A=${A%-*-*-*} && T=$(date -u -r build/product.iso +%Y-%m-%d_%H-%MUTC) && for f in /mnt/isos/$N.iso /mnt/builds/container/debian-*-turnkey-${A}_*.tar.gz; do [ -f "$f" ] || continue; b=$(basename "$f"); case $b in *.iso) n=${b%.iso}_$T.iso;; *) n=${b%.tar.gz}_$T.tar.gz;; esac; mv "$f" "../mybuilds/$n" && if [ -f "$f.hash" ]; then sed "s|$b|$n|g" "$f.hash" > "../mybuilds/$n.hash" && rm "$f.hash"; else (cd ../mybuilds && sha256sum "$n" > "$n.hash"); fi; done; }; done; ls -lh /turnkey/fab/products/mybuilds/
```

## 4. Caricamento su Proxmox

Copia il `.tar.gz` in `/var/lib/vz/template/cache/` sul nodo Proxmox (sostituisci `<proxmox>`):

```bash
scp /turnkey/fab/products/mybuilds/debian-13-turnkey-moodle53-beta_19.0-1_amd64_*.tar.gz root@<proxmox>:/var/lib/vz/template/cache/
```

In alternativa dall'interfaccia web: storage `local` → **CT Templates** → **Upload**.

## Note

- Se rifai la ISO di un'appliance già pubblicata, `--force` sovrascrive i file in `/mnt/isos`.
- `bt-container` imposta `ON_MEMORY=0.33` solo quando il nome è esattamente `moodle`; con i nomi
  beta usa il valore predefinito `0.25`.
