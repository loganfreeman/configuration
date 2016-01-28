Homebrew: Could not symlink, /usr/local/bin is not writable
---
```shell
sudo chown -R `whoami`:admin /usr/local
```
Homebrew library path
---
```ruby
HOMEBREW_LIBRARY_PATH = Pathname.new(__FILE__).realpath.parent.join("Homebrew")
$:.unshift(HOMEBREW_LIBRARY_PATH.to_s)
```
