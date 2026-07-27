# Monster Remote compatibility profiles

`watch_requests_offsets.json` is the exact profile document bundled into the
Android Helper and iOS app. Its `.sig` sidecar is an Ed25519 signature over the
unchanged JSON bytes.

The Course volume widget also relies on `COURSE_VOLUME_REP_HOOK_OFFSET`.
It must point to the instruction directly after the accepted-repetition
callback in `CounterManagerLogic::_onCount`, never to the set-index setter.

The iOS app loads this signed catalog first, then its last verified cache. The
Helper keeps that remote catalog separately from the APK asset and falls back
to the bundled document when no valid remote catalog is available. Catalog
`revision` is monotonic so a newer signed publication can safely replace or
revoke older profile data without an App Store release.

Publish only with:

```sh
python3 tools/publish_offset_profiles.py \
  --profiles app/src/main/assets/watch_requests_offsets.json \
  --private-key /secure/path/monster_remote_profile_signing_private.pem \
  --output-dir remote
```

Never commit the private key. The app rejects a changed JSON document unless
its signature verifies against the pinned public key.

See [NEW_VERSION_CHECKLIST.md](NEW_VERSION_CHECKLIST.md) before publishing any
new Speediance profile.
