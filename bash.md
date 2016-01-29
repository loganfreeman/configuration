Application Environment Setup Using /etc/profile.d/*
---
```shell
echo "Adding composer's vendor directory to system PATH"
cat >/etc/profile.d/composer-bin-root.sh <<EOF
#!/usr/bin/env bash

pathmunge /home/vagrant/.composer/vendor/bin after
pathmunge /root/.composer/vendor/bin after
EOF
```
