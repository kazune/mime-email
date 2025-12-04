# mime-email

constructs a MIME-formatted email message from command-line options and an optional body file.

The resulting message is written to standard output.

This command is written as a BASH script.

## send example

```sh
echo "hello mime-email" \
   | mime-email \
        --from me@example.com \
        --to a@exampel.com \
        --bcc b@exampel.com \
        --bcc c@exampel.com \
        --subject "test mail" \
        --attach README.md \
        --attach somefile.txt \
  | msmtp -v -t
```

## Dependent Commands

- /usr/bin/env
- bash
- base64
- basename
- cat
- date
- file

## install msmtp (reference)

```sh
sudo dnf install epel-release
sudo dnf install msmtp
```

### write in `~/.msmtprc`

```msmtprc
account default
host mail.example.com
port 465
auth on
from me@example.com
user me
password MyPassword123
tls on
tls_starttls off
tls_trust_file /etc/ssl/certs/ca-bundle.crt
```

### stanby

```sh
chmod 600 ~/.msmtprc
```

