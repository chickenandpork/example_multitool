# example_multitool
An example of rules_multtool for the gotopt2 pre-built binary

This example was written in response to a request for guidance in the Bazel slack channels.


## How does it work?

Really, this is a simplification of the examples in rules_multitool.

1. multitool.lock.json lists the pre-built binary in a downloadable form.  It tracks that the file
    is an archive, and the `gotopt2` is at `filmil/gotopt2` and it's a linux/x86_64 binary.
2. the rule is configured in MODULE.bazel to use that config file
3. the alias() in BUILD.bazel is an example of how you can find the gotopt2 tool.

you *could* `bazel run //:gotopt2` but since it's looking for stdin, you may need to use it as a tool.

You could list it as a toolchain to a rule: I think a genrule() has a toolchain option and you'd be
able to `$(location ..)` that to get it dereferenced to the physical binary.  the toolchain type is
defined as `@@rules_multitool++multitool+multitool//tools/gotopt2:toolchain_type`, so you could
define a custom rule and state that the type of toolchain you need for the "gotopt2" is this
toolchain type, and the binary would be made available when the rule needs it.

If you don't have the corresponding build host architecture (gotopt2 only offers an amd64 and an
i386 release, unsure the difference) you can test the build using:

```
docker run --rm -v $(pwd):/$(basename $(pwd)) -w /$(basename $(pwd)) gcr.io/bazel-public/bazel:latest \
  build \
  --toolchain_resolution_debug='@@rules_multitool++multitool+multitool//tools/gotopt2:toolchain_type' \
  //...
```
