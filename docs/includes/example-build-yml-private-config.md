Create a `build.yml` file in project root with the following structure. Some of the values will be filled out later.

```yaml
apiVersion: automation.cio/v1alpha1
kind: RepositoryConfig
ealImapNumber: 'EAL-007199'
build:
  strategy: simple-pipeline
  pipeline: nodejs-v18-npm-library
  version: v1
  config:
    private-dependencies: 'encrypted:v1:AAABgJiAewfxl9nd7SvYJtzfl/bdGCmJMi01PExSYuBCKCBTBwlcjXfunR14MdPCE8mbB7URd31S79ZaVQjbJQdW10alo1IUahP5frJh41YsgOqZ1eQ7C2o1eG/JDHjJqv38MuMAfpkOAnSgm4Nh0odCLWRgiW9IzxeMBCFwPf4KdbYfgLpZ6mAg5TgoCH9l0BRvCaSEEQ36XydSGCCthEVbklGdvn5DZY27at1mULNxLackHZ2nGWxfxq/B2oFVm8pR++TfzPMv4uWGVDB3Fdhkla/PRjv6IETU8pkwW/G6njcktU3Wp61K0ig4kEp94fpn4W2snCGxumzUMAKkMF6+LlNWnJFqJpH1lEG6oL2W3boZHC9TeDAy9XViA6/YueuLfigxHNTKU9uyer3mviu0'
```
