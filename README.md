# ☀️ Lila's Sunny Walks

A standalone web app that recommends two sunny 30-minute dog walks per day. It was built for Lila in Bodrum, Türkiye.
It compares the ICON-EU and ECMWF IFS (9 km) forecasts from [Open-Meteo](https://open-meteo.com/). There is no backend, no API key and no AI dependency.

**Live app:** https://ececakar.github.io/lila-sunny-walks/

## Files
- `index.html`: the whole app. Settings are in the `CONFIG` block near the top of the script.
- `manifest.webmanifest`, `icon-180.png`, `icon-512.png`: the home-screen icon and app metadata.

## Hosting your own copy
The app must be served over HTTPS. GitHub Pages is the lowest-maintenance option:
1. Fork this repository, or create a new public repository and upload the four files above.
2. In `index.html`, set your coordinates, timezone and place name in `CONFIG`. Two decimal places are enough (about 1 km), because the models use 7–9 km grids.
3. Open **Settings → Pages**, set Source to "Deploy from a branch", then choose branch `main` and folder `/ (root)`.
4. After about a minute, the app is live at `https://<username>.github.io/<repository>/`.

## Installing on a device
- **iPhone or iPad:** open the app in Safari, then choose **Share → Add to Home Screen**. It opens full-screen, like a native app.
- **Mac:** open the app in Safari, then choose **File → Add to Dock**.

## How updates work
- Every time you open the app, it fetches fresh forecasts if the data is more than 45 minutes old. Tap ↻ to refresh at any time.
- The last good forecast is stored on the device, so the app still shows it when you are offline.
- ICON-EU updates every 3 hours and ECMWF every 6 hours, so opening the app in the morning always gives the latest runs.
- Optional morning prompt: in Shortcuts, create an automation for a time of day (for example 07:30) with the action "Open URLs" and your app address, and set it to "Run Immediately".

## How the recommendation works
For each daylight hour and each model, the app builds a sun score (0–100%) from three inputs:
- **Sunshine duration (35%):** seconds of sunshine in the hour, using the WMO definition (direct beam above 120 W/m²).
- **Direct radiation (35%):** compared with the clear-sky value for that sun elevation, so the score means the same in winter and summer.
- **Cloud cover (30%):** full credit at 15% or less, falling to zero at 80%.

Each hour's ranking is then adjusted:
- **Disagreement:** the ranking uses the mean sun score minus 30% of the gap between the two models, so windows that both models call sunny come first.
- **Safety and comfort:**
  - Excluded: thunderstorms, gusts of 65 km/h or more, and a feels-like temperature of 34°C or more, which risks heat stress for dogs.
  - Penalised: rain, strong gusts, heat and cold.
- **Low sun:** below 8° elevation, hills and buildings often block the sun. These hours are penalised and hatched in the timeline.

The two walks are taken one before and one after solar noon, at least 4 hours apart, to maximise the combined score. Up to three alternatives are listed.

Confidence labels show how far the two models' sun scores differ:
- **Models agree:** less than 15 points apart.
- **Some disagreement:** 15 to 35 points apart.
- **Models disagree:** more than 35 points apart.

Open **Hourly model detail** to see the raw numbers from each model.

## Maintenance
- **Model IDs:** Open-Meteo is moving DWD models to native grids from 30 November 2026. The app tries model IDs in order:
  - ICON: `icon_eu`, then `dwd_icon_eu`, `dwd_icon_eu_native` and `icon_seamless`.
  - ECMWF: `ecmwf_ifs`, then `ecmwf_ifs025`.

  The footer shows which ID is in use. If one model fails completely, the app continues with the other and shows a banner.
- **Coverage:** ICON-EU covers Europe, North Africa and the Middle East. Elsewhere, replace the ICON candidates with `icon_global`.
- **Tuning:** edit `CONFIG` in `index.html`, then upload the file again. You can change:
  - `cloudIdeal`: the cloud threshold that counts as clear sky
  - `lowSunDeg`: the low-sun angle
  - `minGapHours`: the minimum gap between the two walks
- **Limits:** forecasts describe 7–9 km grid cells. Coastal fog and hillside shade can differ at street level.

## Licence
The code is available under the [MIT License](LICENSE). Weather data is from [Open-Meteo.com](https://open-meteo.com/) under CC BY 4.0 and is free for non-commercial use; attribution is shown in the app footer.
