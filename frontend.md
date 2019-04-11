# ACT Gui

The ACT GUI requires npm and yarn as specified in the [requirements](requirements.md) section.

## su

change to a non privilegded user.

```bash
su - act
```

## Checkout software

Checkout software from git to src/act-platform.

```bash
mkdir -p ~/src
cd ~/src
git clone https://github.com/mnemonic-no/act-frontend
```

## Configure

Configuration is currently done in src/config.js prior to bulding the frontend. The defaults are ok to leave for the basic installation on a single host, but if the platform is installed on another host, you will need to update `apiUrl` to the full URL of the Service, e.g. `http://act-api.internal.org:8888`.


## Build

Install requirements and build.

```bash
cd ~/src/act-frontend
yarn --silent install
yarn --silent build
```

## nginx

We will need a webserver to host the GUI. We have chosen nginx here, and we also use nginx to proxy requests to the [platform](platform.md).

As root:

```bash
curl --silent https://raw.githubusercontent.com/mnemonic-no/act/master/example-config/nginx.conf > /etc/nginx/nginx.conf
curl --silent https://raw.githubusercontent.com/mnemonic-no/act/master/example-config/nginx-act.conf > /etc/nginx/conf.d/nginx-act.conf
```

Create directory where frontend should be stored:

```bash
mkdir -p /usr/share/nginx/frontend
chown -R act:act /usr/share/nginx/frontend
```

Change back to the `act` user.

```bash
rsync -r --delete ~/src/act-frontend/build/ /usr/share/nginx/frontend
chmod go+rX /usr/share/nginx/frontend
```
