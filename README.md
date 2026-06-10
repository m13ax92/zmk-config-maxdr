# maxdr — ZMK Firmware (Wireless Split, nice!nano v2)

Komplette ZMK-Konfiguration für die maxdr-Split-Tastatur.
- Controller: nice!nano v2 (pro_micro-kompatibel)
- 24 Tasten pro Hälfte, Matrix 6 Spalten x 5 Reihen
- Linke Hälfte = central (verbindet zum PC), rechte = peripheral

## Pin-Belegung (aus dem PCB abgeleitet)
- Spalten (col-gpios): P21 P20 P19 P18 P15 P14
- Reihen (row-gpios):  P4  P5  P6  P7  P8 (P8 = Daumenreihe)
- diode-direction: col2row

## Firmware bauen (ohne lokale Toolchain, via GitHub Actions)
1. Neues **öffentliches** GitHub-Repo anlegen, den kompletten Inhalt dieses
   Ordners (`zmk-config/`) ins Repo-Wurzelverzeichnis hochladen — inklusive des
   versteckten Ordners `.github/` (enthält den Build-Workflow!).
2. Build-Workflow (`.github/workflows/build.yml`) und Bau-Matrix (`build.yaml`)
   sind bereits eingerichtet.
3. Nach dem Push baut Actions automatisch. Unter "Actions" -> letzter Run ->
   "Artifacts" lädst du `firmware.zip` herunter. Darin:
   - `maxdr_left-nice_nano_v2-zmk.uf2`
   - `maxdr_right-nice_nano_v2-zmk.uf2`
   - `settings_reset-nice_nano_v2-zmk.uf2` (zum Löschen alter BLE-Paarungen)

## Flashen (pro Hälfte)
1. nice!nano per USB anstecken, **2x Reset** (deinen B1-Taster) drücken
   -> es erscheint ein USB-Laufwerk "NICENANO".
2. Die passende .uf2 (left bzw. right) draufkopieren -> Nano flasht & startet neu.
3. Beide Hälften einzeln flashen.

## Keymap
`config/boards/shields/maxdr/maxdr.keymap` enthält eine Platzhalter-Belegung
(alle Tasten = "0"). Dort deine echten Tasten eintragen
(siehe https://zmk.dev/docs/keymaps).

## WICHTIG / zu prüfen auf Hardware
- **diode-direction:** steht auf `col2row`. Falls Tasten gar nicht oder falsch
  reagieren -> in `maxdr.dtsi` auf `row2col` ändern (einzige Zeile) und neu bauen.
- **Links/Rechts-Spiegelung:** Die rechte Hälfte ist das umgedrehte PCB, daher ist
  ihr Spaltenblock in der Matrix-Map (`maxdr.dtsi`) bewusst umgekehrt (11→6).
  Beim Bring-up zuerst testen: Drückst du auf der rechten Hand die linke obere Taste,
  muss "Z" kommen (nicht BSPC). Kommt es seitenverkehrt -> rechten Block in der Map
  wieder auf natürliche Reihenfolge 6→11 stellen.
- **Welche Hälfte ist central:** Aktuell = LINKE (`Kconfig.defconfig`). Die linke
  verbindet sich per USB/BLE mit dem PC; die rechte ist peripheral. Beide trotzdem
  einzeln flashen.
- **Laden:** Power-Schalter (T1) muss AN sein, sonst lädt der Akku nicht.
- **JST-Polarität** vor erstem Einstecken pro Hälfte mit Multimeter prüfen
  (dreht sich, weil die rechte Platine gespiegelt ist!).
