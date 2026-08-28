# Åpen Hall – statusside

En enkel, gratis statusside for "Åpen Hall" på Bákthárji i Kautokeino, til å lenke til
via QR-kode. To sider:

- `index.html` – offentlig side. Viser kommende fredag, tidspunkt og status (Åpen / Innstilt / Stengt).
- `admin.html` – adminside der dere setter status og evt. avvikende tidspunkt.

Status (og evt. tidspunkt) lagres i `status.json` i dette repoet, og hentes automatisk
av `index.html`. Datoen regnes alltid ut som **kommende fredag** – dere trenger aldri å
oppdatere den selv. Status og tidspunkt nullstilles automatisk hver ny uke: hvis ingen
har satt noe for kommende fredag, viser siden "Åpen" kl. **18:00–23:00** som standard.
Vil dere avvike fra standardtidspunktet én uke, setter dere det i adminsiden – det
gjelder da kun den ene fredagen, og hopper tilbake til 18:00–23:00 uka etter.

## Design

Utformingen følger malen dere sendte: "Poppins" (gratis Google Font, lastes fra
Google Fonts – nærmeste match til fonten i eksempelbildet). Har dere et eksakt
fontnavn dere vil bruke i stedet, si fra, så bytter jeg det i `<link>`-taggen og
`font-family` øverst i `index.html`. Logoene (Kautokeino I.L. og Kautokeino kommune)
ligger i `assets/`-mappa – bytt dem ut med nye filer med samme navn for å oppdatere.

## Oppsett (gjøres én gang)

### 1. Opprett repo og slå på GitHub Pages

1. Last opp alle filene i denne mappen til et nytt GitHub-repo (f.eks. `apen-hall-status`).
   Repoet kan være offentlig eller privat — Pages funker med begge, men et privat repo
   krever GitHub Pro/Team for gratis Pages-hosting. Offentlig er enklest.
2. Gå til repoets **Settings → Pages**.
3. Under "Build and deployment": velg **Deploy from a branch**, branch **main**, mappe **/(root)**.
4. Lagre. Etter ca. ett minutt får dere en lenke som
   `https://<brukernavn>.github.io/<reponavn>/` — dette er lenken dere lager QR-kode av.

### 2. Lag et adminpassord

1. Åpne `generate-hash.html` i nettleseren (last den ned og dobbeltklikk, eller besøk
   `https://<brukernavn>.github.io/<reponavn>/generate-hash.html` etter steg 1).
2. Skriv inn et passord dere blir enige om internt, trykk "Lag hash".
3. Kopier den lange bokstav/tall-strengen som kommer opp.
4. Åpne `admin.html` i en tekstredigerer, finn linjen:
   ```js
   const ADMIN_PASSWORD_HASH = "REPLACE_WITH_YOUR_OWN_SHA256_HASH";
   ```
   og lim inn hashen i stedet for `REPLACE_WITH_YOUR_OWN_SHA256_HASH`.
5. Last opp den oppdaterte `admin.html` til repoet (commit).

Merk: dette er et enkelt passordlag, ikke banksikkerhet — hashen ligger synlig i
kildekoden, så noen med nok teknisk innsikt kan i teorien regne seg fram til svake
passord. Den reelle sikkerheten ligger i GitHub-tokenet (neste steg), som kreves for
faktisk å kunne lagre noe.

### 3. Lag en GitHub-tilgangsnøkkel (Personal Access Token)

1. Gå til GitHub → **Settings** (på kontoen, ikke repoet) → **Developer settings** →
   **Personal access tokens** → **Fine-grained tokens** → **Generate new token**.
2. Gi den et navn, f.eks. "Åpen Hall admin".
3. Under **Repository access**: velg "Only select repositories" og velg dette repoet.
4. Under **Permissions → Repository permissions**: sett **Contents** til **Read and write**.
5. Generer tokenet og kopier det (starter gjerne med `github_pat_...`). Dere ser det
   kun én gang — lagre det trygt (f.eks. i en passordbehandler) i tillegg.

### 4. Fyll inn oppsett i admin-siden

1. Åpne `https://<brukernavn>.github.io/<reponavn>/admin.html`.
2. Skriv inn adminpassordet fra steg 2.
3. Åpne "Oppsett" nederst, fyll inn:
   - **GitHub-brukernavn/organisasjon**: f.eks. `dittbrukernavn`
   - **Repo-navn**: f.eks. `apen-hall-status`
   - **Branch**: `main`
   - **GitHub Personal Access Token**: tokenet fra steg 3
4. Trykk "Lagre oppsett". Dette lagres kun i nettleseren dere bruker.

Gjenta steg 4 i hver nettleser/enhet dere skal administrere status fra (passord +
token må fylles inn på nytt der).

## Bruk i hverdagen

1. Gå til `admin.html`.
2. Skriv inn passordet.
3. Velg status: Åpen / Innstilt (+ årsak) / Stengt (+ årsak).
4. La tidspunktet stå som 18:00–23:00 med mindre denne fredagen avviker.
5. Trykk "Lagre status".

Forsiden (`index.html`) oppdaterer seg automatisk (kan ta noen sekunder).

## Filer i dette repoet

- `index.html` – den offentlige statussiden (den QR-koden skal peke til)
- `admin.html` – adminsiden for å sette status og tidspunkt
- `generate-hash.html` – engangsverktøy for å lage passord-hash (trengs bare ved oppsett)
- `status.json` – selve statusdataen, oppdateres automatisk av admin-siden
- `assets/kautokeino-il.png`, `assets/kautokeino-kommune.png` – logoene
- `README.md` – denne filen

## Vanlige spørsmål

**Kan flere personer administrere status?**
Ja — del passordet og tokenet med de som trenger admintilgang. Alle må gjøre steg 4
i sin egen nettleser.

**Hva om vi glemmer å sette status en uke?**
Da viser siden automatisk "Åpen" — det er standardverdien når ingenting er satt for
kommende fredag.

**Kan vi endre tidspunktet for én enkelt fredag?**
Ja, i admin-siden – standard er 18:00–23:00, og det gjelder alltid med mindre dere
endrer det for den kommende fredagen. Uka etter går det automatisk tilbake til standard.

**Kan vi endre stedsnavnet permanent, eller standard-tidspunktet (18:00–23:00)?**
Ja, rediger teksten/`DEFAULT_TIME_START`/`DEFAULT_TIME_END` direkte i `index.html`
(og standardverdiene i `admin.html`) og last opp på nytt.

**Er dette sikkert nok?**
Det er beskyttet av et passord og et GitHub-token som kreves for å faktisk skrive til
repoet — mer enn nok for en frivillig statusside, men ikke bygget for høy sikkerhet.
Bruk ikke et passord dere bruker andre steder.
