# Handheld receiver lab ranking

An interactive, single-page explorer for ARRL Lab receiver measurements of VHF/UHF handheld radios. Plot every radio on a strong-signal map, filter by performance, and compare up to four radios side by side on 2 m or 70 cm.

Everything lives in one file, `index.html`. There is no build step, no framework and no server.

## Quick start

Open `index.html` in any modern browser. To serve it locally instead:

```sh
python3 -m http.server 8000
# then visit http://localhost:8000
```

The page loads two fonts (Bricolage Grotesque and Instrument Sans) from Google Fonts. Offline, it falls back to system fonts and works the same.

## What it shows

**Strong-signal map.** A scatter plot of narrow-spacing third-order dynamic range (x, 20 kHz) against wide-spacing third-order dynamic range (y, 10 MHz). Dot size shows adjacent-channel rejection and dot colour shows the manufacturer. Up and to the right is better.

**Compare panel.** Select up to four radios (from the map or the table) to see sensitivity, wide, narrow, adjacent and total score as bars. Selecting a fifth radio drops the oldest selection.

**Table.** Sortable by any column, with a bar behind each number showing where it sits in the range. It is collapsed by default; expand "Table data" to open it.

### Controls

| Control | What it does |
| --- | --- |
| **2 m / 70 cm** | Switches the band used by the chart, table and compare panel. |
| **Search** | Filters by make or model. |
| **All / Complete data / Added** | Shows every radio, only radios with all three dB figures for the chosen band, or only radios added from newer QST reviews. |
| **High perf** | Click to cycle: narrow ≥ 71 dB, wide ≥ 81 dB, both, then off. Shaded on the chart. |
| **Better** | Click to cycle: narrow, wide, combo, then off. Keeps radios at least as good as the most recently selected radio on that measure. Select a radio first. |
| **Make chips** | Filter by one or more manufacturers. |

The page is keyboard accessible (map dots and table rows take Enter or Space), respects `prefers-reduced-motion`, and follows the system light or dark theme. You can force a theme by setting `data-theme="light"` or `data-theme="dark"` on the `<html>` element.

## Reading the numbers

- **Wide-spacing third-order dynamic range** (10 MHz): resistance to intermodulation from strong, distant signals such as pagers, broadcast towers and mountaintop sites. Usually the number that matters most in cities.
- **Narrow-spacing third-order dynamic range** (20 kHz): the same idea for nearby channels.
- **Adjacent rejection** (20 kHz): how well the radio ignores a strong signal on the next channel.
- **Sensitivity**: signal for 12 dB SINAD in µV. Lower is better.
- **Score**: wide + narrow + adjacent, in dB, for the selected band.

Older radios were never tested at wide spacing. Their score is a partial sum, marked **†**, and understates them. Use **Complete data** to compare like with like. On the chart these radios are plotted at an assumed 55 dB wide-spacing and ringed in pink. Radios with no narrow-spacing result appear only in the table.

## Data

The dataset is the `D` array in the `<script>` block. Each row is:

```
[make, model, s2, w2, n2, a2, s7, w7, n7, a7, src, v]
```

| Field | Meaning |
| --- | --- |
| `make`, `model` | Manufacturer and model name. |
| `s2`, `w2`, `n2`, `a2` | 2 m sensitivity (µV), wide, narrow and adjacent (dB). |
| `s7`, `w7`, `n7`, `a7` | The same four figures for 70 cm. |
| `src` | `0` for original rows, or `1`–`4` for rows added later. The number is a key into the `SRC` object, which holds the QST issue, review URL and a caveat. |
| `v` | Verification level: `0` not independently checked, `1` matches the QRP Blog compilation of QST data, `2` checked against the ARRL review. |

Use `null` for any value that was not measured.

### Adding a radio

1. Append a row to `D` in the format above.
2. If it comes from a review you want to cite, add an entry to `SRC` (`m` radio name, `t` issue, `u` URL, `c` caveats) and use its key as `src`.
3. For a per-radio footnote, add the model to the `NOTE` object. Models in the `NG` set show an ⓘ icon; other noted models show ⚠.

### Provenance and verification

The original table follows the QRP Blog compilation of QST lab results. Every row was cross-checked against that compilation. One discrepancy was corrected: the Kenwood TH-D74A's 2 m wide-spacing figure is **79 dB** (Band A, the VHF/UHF receiver) rather than the 87 dB Band B result in the supplied file, and the TH-D75A row was switched to Band A so the two are compared on the same basis.

Fourteen radios were checked directly against their ARRL reviews. The rest match the compilation, which is not the same as being traced to the original QST issue. Four radios (IC-T7A, IC-W32A, DJ-G5TH, FT-50R) have unusually high 70 cm wide-spacing values that could not be traced and carry a ⚠.

Other choices to be aware of:

- Dual-receiver radios use Receiver A, except the FT-5DR and FT-3DR, which use Receiver B as in the ARRL summary.
- The ID-52A figures use FM-N (narrow) results and the AT-D878UV uses wide FM, so they are not on exactly the same basis as most rows.
- Added rows are single-unit ARRL Lab results in FM mode. Mobiles, base stations and wideband scanners were left out. The IC-R15 is a receive-only handheld included as a reference point.

The full notes are in the "Data verification" and "Method and caveats" sections at the bottom of the page.

## Sources

- ARRL Lab measurements as published in *QST* product reviews.
- QRP Blog compilation of QST lab results.
- ARRL's members-only comparison tool (compare.arrl.org) has newer entries that were not incorporated.

If you rely on a number for a purchasing or engineering decision, confirm it in the original QST review.

## Project structure

```
index.html   the whole app: markup, styles, data and script
README.md    this file
```
