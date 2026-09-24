# tvOS 26.4 manual validation

Run this procedure only against a disposable tvOS simulator. The validated
tvOS catalog covers telemetry, web, cloud-sync, Calendar/Contacts, Family, and
low-interaction app metadata. It deliberately excludes TV home-screen,
playback, remote-input, search, and Siri services.

1. Find the installed runtime and create a fresh Apple TV simulator. Substitute
   the exact runtime identifier printed on the host if it differs.

   ```sh
   xcrun simctl list runtimes | rg tvOS
   xcrun simctl create "SimSlim tvOS validation" "Apple TV 4K (3rd generation)" "com.apple.CoreSimulator.SimRuntime.tvOS-26-4"
   export SIMSLIM_TV_UDID="<the returned UDID>"
   ```

2. Boot the stock simulator, capture its starting state, and confirm Settings
   launches.

   ```sh
   xcrun simctl boot "$SIMSLIM_TV_UDID"
   xcrun simctl bootstatus "$SIMSLIM_TV_UDID" -b
   simslim status "$SIMSLIM_TV_UDID"
   xcrun simctl launch "$SIMSLIM_TV_UDID" com.apple.TVSettings
   xcrun simctl io "$SIMSLIM_TV_UDID" screenshot /tmp/simslim-tvos-stock.png
   ```

3. Apply the conservative profile, reboot-persistence check, and verification.

   ```sh
   simslim on "$SIMSLIM_TV_UDID"
   simslim verify "$SIMSLIM_TV_UDID"
   xcrun simctl shutdown "$SIMSLIM_TV_UDID"
   xcrun simctl boot "$SIMSLIM_TV_UDID"
   xcrun simctl bootstatus "$SIMSLIM_TV_UDID" -b
   simslim verify "$SIMSLIM_TV_UDID"
   ```

4. Confirm the UI remains usable and Settings plus Preview Shell still launch,
   then capture the post-slim screen.

   ```sh
   xcrun simctl launch "$SIMSLIM_TV_UDID" com.apple.TVSettings
   xcrun simctl launch "$SIMSLIM_TV_UDID" com.apple.PreviewShell
   xcrun simctl io "$SIMSLIM_TV_UDID" screenshot /tmp/simslim-tvos-slim.png
   ```

5. Restore stock state, reboot once more, and verify no managed service remains
   disabled. Delete the disposable simulator when finished.

   ```sh
   simslim off "$SIMSLIM_TV_UDID"
   xcrun simctl shutdown "$SIMSLIM_TV_UDID"
   xcrun simctl boot "$SIMSLIM_TV_UDID"
   xcrun simctl bootstatus "$SIMSLIM_TV_UDID" -b
   simslim status "$SIMSLIM_TV_UDID"
   xcrun simctl delete "$SIMSLIM_TV_UDID"
   ```

Keep screenshots and any recording outside the repository. Do not claim tvOS
memory savings from this procedure: its purpose is safety and functional
regression coverage, not a memory benchmark.
