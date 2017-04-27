```shell
mdfind "kMDItemCFBundleIdentifier == 'com.github.atom'" | grep -v ShipIt | head -1 | xargs -0 dirname
```
