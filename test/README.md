# Testing

For now there's a single test. Execute `test.sh` to run it.

## Details

- `Test Project.xcodeproj` was created using Xcode's default project template
- `Test.xcconfig` was added to set `SDKROOT = iphoneos`, the same as the value set in Xcode's Build Settings GUI. The xcconfig was wired up for project-level settings and the `SDKROOT` setting was cleared for those settings in the GUI.
- `xcbs` was run to obtain the settings lock files for the project in its initial state, which are checked in to source control

### Xcode releases

Each Xcode release introduces changes to its default project settings. Perform these steps before running the tests to work with a new Xcode release, before making other changes to xcbs code.

- open `test/Test\ Project/Test\ Project.xcodeproj` and accept all project migrations
- `rake output_current_settings`
- commit the changes

### Running and evaluating tests

Running `test/test.sh` (`rake test`):

- in `Test.xcconfig`, `SDKROOT` value is changed from `iphoneos` to `macosx10.12`
- `xcbs` is run again, producing different settings lock files

Baseline diff:

- The resulting git diffs from running the two previous steps were written to `baseline.diff`, which includes the change to `Test.xcconfig` and the settings lock files in `Test Project/.xcbs`. `baseline.diff` is checked into source control.

Test comparison:

- On subsequent runs of `test.sh`, a new diff is output to `computed.diff`, which is compared to `baseline.diff` using `diff` itself. If `diff` exits with non-zero status, then the settings lock files or `Test.xcconfig` itself changed.
	- If unexpected, then a bug or regression has been introduced with a new code change
	- If expected, then `baseline.diff` should be overwrittedn with the contents of `computed.diff` and committed (`rake accept_baseline_deltas`).

**Notes:**

- The test relies on the state of the git working directory, so any other changes present will cause a failure. `git stash` is called at the beginning of `test.sh` and `git pop` is called at the end of a successful run.
