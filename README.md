# ProtonMail Bridge
Copyright (c) 2020 Proton Technologies AG

This repository holds the ProtonMail Bridge application.
For a detailed build information see [BUILDS](./BUILDS.md).
For licensing information see [COPYING](./COPYING.md).
For contribution policy see [CONTRIBUTING](./CONTRIBUTING.md).

## Patch Notes

**Note: I know nothing about security and I don't know if this is a security nightmare. Use this at your own risk.**

This fork of ProtonMail bridge allows you to run the Bridge as a
IMAP/SMTP server which can then be served to clients on multiple
devices.

### Configuring Certificate

Currently, on-the-fly hostname configuration is not enabled; you'll
have to build the hostname into the application. Hostname
configuration is stored in `pkg/config/tls.go`. All you need to
configure is these four lines:

```go
var CERT_COUNTRY = "";
var CERT_ORGANIZATION = "";
var CERT_ORGANIZATIONAL_UNIT = "";
var CERT_HOSTNAME = "";
```

You can configure the cert to be issued for trusted IPs instead of
hostnames; to do this comment line 60, uncomment line 59, and
configure a list of trusted IPs like so:

```go
	IPAddresses:           []net.IP{net.ParseIP("127.0.0.1")},
```

### Building (for Linux)

After you've configured your hostname/IP for the certificate, run
`make build` in order to create the `proton-bridge` binary,
which will end up in `cmd/Desktop-Bridge/deploy/linux/`.

### Configuring (for Linux/Systemd/tmux)

In order to make the bridge run in the background, I created a
Systemd service to launch the Bridge:

```
[Unit]
Description=IMAP/SMTP interface to ProtonMail
After=network.target

[Service]
Type=simple
User=
Restart=always
RestartSec=1
ExecStart=/usr/bin/tmux new-session -d -s protonmail-bridge /proton-bridge/cmd/Desktop-Bridge/deploy/linux/proton-bridge --cli

[Install]
WantedBy=multi-user.target
```

In order to use this Systemd service, fill in a user in the `User=`
field and make sure they have access to a supported password manager.
If one is not detected, I recommend installing and initializing
`pass`.

## Description
ProtonMail Bridge for e-mail clients.

When launched, Bridge will initialize local IMAP/SMTP servers and render 
its GUI.

To configure an e-mail client, firstly log in using your ProtonMail credentials. 
Open your e-mail client and add a new account using the settings which are 
located in the Bridge GUI. The client will only be able to sync with 
your ProtonMail account when the Bridge is running, thus the option 
to start Bridge on startup is enabled by default.

When the main window is closed, Bridge will continue to run in the
background.

More details [on the public website](https://protonmail.com/bridge).


## Keychain
You need to have a keychain in order to run the ProtonMail Bridge. On Mac or
Windows, Bridge uses native credential managers. On Linux, use
[Gnome keyring](https://wiki.gnome.org/Projects/GnomeKeyring/)
or
[pass](https://www.passwordstore.org/).

## Environment Variables

### Bridge application
- `BRIDGESTRICTMODE`: tells bridge to turn on `bbolt`'s "strict mode" which checks the database after every `Commit`. Set to `1` to enable.

### Dev build or run
- `PROTONMAIL_ENV`: when set to `dev` it is not using Sentry to report crashes
- `VERBOSITY`: set log level used during test time and by the makefile.
- `VERSION`: set the bridge app version used during testing or building.

### Integration testing
- `TEST_ENV`: set which env to use (fake or live)
- `TEST_ACCOUNTS`: set JSON file with configured accounts
- `TAGS`: set build tags for tests
- `FEATURES`: set feature dir, file or scenario to test




