# Monster Remote — new Speediance version checklist

Do not publish a profile until every required item below passes. Keep the GM on
the last supported Speediance release until the profile has passed the complete
matrix.

## 1. Capture the release

- [ ] Download the full APK, not a split/delta package.
- [ ] Record Speediance `versionName`, `versionCode`, APK SHA-256 and MD5.
- [ ] Extract the exact `lib/arm64-v8a/libapp.so` Build-ID.
- [ ] Store APK, Blutter output and the generated report under
      `/Volumes/EXT/blutter/Speediance.<version>*`.

## 2. Generate and review the profile

- [ ] Run `port_watch_requests_profile.py` from the newest verified profile.
- [ ] Resolve every non-exact field from the new Blutter/AArch64 output.
- [ ] Check register and stack ABI at every changed hook. A matching function
      name or nearby offset is not sufficient.
- [ ] Record every reviewed value and evidence in
      `tools/offset_verifications/<version>.json`.
- [ ] Run `finalize_watch_requests_profile.py`.
- [ ] Confirm: no unresolved/unknown fields, exact APK Build-ID, every code
      offset AArch64-aligned and inside the executable segment.
- [ ] Confirm every profile has the identical complete field set.
- [ ] Confirm the catalog `revision` increased when profile data changed.

## 3. Build and install a private test build

- [ ] Compile `watch_requests` with the Android NDK clang on the Mac.
- [ ] Build Android `testDebugUnitTest`, `assembleDebug` and `assemblePayload`.
- [ ] Copy the exact payload APK, helper version JSON and profile JSON into the
      iOS resources.
- [ ] Build the iOS app.
- [ ] Install only on a test GM and verify Helper, watcher and staged-profile
      hashes.
- [ ] Verify `/health` reports the exact installed version and Build-ID as
      compatible.

## 4. Screen and lifecycle matrix

- [ ] `/home`: no training overlays and no stale Rowing/Free Lift data.
- [ ] Free Lift empty, exercise selection, exercise change and return to empty.
- [ ] Free Lift modes: Standard, Eccentric, Chain, Constant Speed, Rowing,
      Skiing and Pilates where available.
- [ ] Regular Course: first exercise, set change, exercise change, rest,
      resume and training report.
- [ ] Rowing Course and Free Lift Rowing.
- [ ] Exit/cancel, watch wake, motor-lock and other native popups: overlays hide
      and reliably return only on the correct training screen.
- [ ] Rest expansion/collapse and upcoming-exercise data.
- [ ] Background video first load, cached load and exercise transition.
- [ ] Update dialog: offered version is detected without localized text. An
      unsupported version shows the red warning; a supported version does not.

## 5. Command matrix

- [ ] Weight on/off, set weight, increase and decrease.
- [ ] Extra/eccentric weight.
- [ ] Mode selection on Free Lift and Course.
- [ ] Barbell, non-barbell and dual load.
- [ ] Spotter off/mode 1/mode 2 and flip screen.
- [ ] Skip set, skip exercise, skip/increase rest.
- [ ] Free Lift rest actions: continue current set, new set, new movement.
- [ ] Course pause/resume and Rowing auto/manual resistance.
- [ ] Safety Start, Unset, Confirm and Cancel on every model that exposes them.
- [ ] Start training and finish-microphone behavior.
- [ ] Run commands both once and in short bursts; execution must remain
      immediate and must not crash/ANR.

## 6. Data and overlay matrix

- [ ] Authentication token capture and correct regional API host.
- [ ] Free Lift/Course exercise JSON and upcoming exercise JSON.
- [ ] Recent, 2-month and 6-month history; immutable responses use cache.
- [ ] Correct kg/lb values, limits and labels from the GM preference.
- [ ] Correct locale date formatting and same-day set numbering only.
- [ ] Heart graph continuity across exercise transitions and missing samples.
- [ ] Rowing speed, power, SPM, resistance and distance live values.
- [ ] Rowing history, averages, PB race indicator and empty-workout handling.
- [ ] All card visibility/options remain mirrored between GM and iOS.

## 7. Stability

- [ ] Leave `/home` idle for at least 15 minutes.
- [ ] Complete a multi-exercise Course including non-weight/stretch exercises.
- [ ] Repeat skip-set/skip-exercise and rapid command bursts.
- [ ] Reboot the GM twice and verify automatic Helper/watcher startup.
- [ ] Inspect crash buffer, ANR traces and `watch_requests.log`.

## 8. Signed publication and fallback

- [ ] Run `tools/publish_offset_profiles.py` using the private Ed25519 key
      outside every repository.
- [ ] Verify the generated signature against the pinned public key.
- [ ] Confirm Android asset, iOS resource and published JSON are byte-identical.
- [ ] Publish JSON and `.sig` to `theLilaQ/monster-remote-profiles`.
- [ ] Verify the raw GitHub URLs return HTTP 200 and the unchanged bytes.
- [ ] Verify iOS fetches the signed online catalog and Helper reports
      `profileSource=signed_remote`.
- [ ] Disable Internet access and verify the last verified cache works.
- [ ] Remove the cache on a test iPhone, remain offline and verify the bundled
      profile works.
- [ ] Serve a modified JSON with an old/invalid signature and verify it is
      rejected without replacing the working profile.

## Rollback

Never lower the catalog revision. Publish the last known-good profiles as a new
higher revision, sign them again, verify the signature, then test online,
cached and bundled behavior before notifying users.
