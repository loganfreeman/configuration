PIP_DOWNLOAD_CACHE
---
Added support for an environmental variable `$PIP_DOWNLOAD_CACHE` which will cache package downloads, so future installations won’t require large downloads. Network access is still required, but just some downloads will be avoided when using this.

To take advantage of this, I've added the following to my ~/.bash_profile:

`export PIP_DOWNLOAD_CACHE=$HOME/.pip_download_cache`
```shell
# install our common Python packages in a temporary virtual env so that they'll get cached
if [[ ! -e /home/vagrant/.pip_download_cache ]]; then
    su - vagrant -c "mkdir -p /home/vagrant/.pip_download_cache && \
        virtualenv /home/vagrant/yayforcaching && \
        PIP_DOWNLOAD_CACHE=/home/vagrant/.pip_download_cache /home/vagrant/yayforcaching/bin/pip install -r /vagrant_data/common_requirements.txt && \
        rm -rf /home/vagrant/yayforcaching"
fi
```
Install setuptools
---
```shell
curl https://bitbucket.org/pypa/setuptools/raw/bootstrap/ez_setup.py | sudo python -
```
