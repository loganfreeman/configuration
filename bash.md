Application Environment Setup Using /etc/profile.d/*
---
> When a user logs in, environment variables are set from various places.  That includes `/etc/profile` (for all users).
> Then all the files in the `/etc/profile.d` directory.
> Then `~/.bash_profile`, then `~/.bashrc`.
```shell
echo "Adding composer's vendor directory to system PATH"
cat >/etc/profile.d/composer-bin-root.sh <<EOF
#!/usr/bin/env bash

pathmunge /home/vagrant/.composer/vendor/bin after
pathmunge /root/.composer/vendor/bin after
EOF
```
