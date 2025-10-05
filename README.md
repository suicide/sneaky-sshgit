# Sneaky SSH Git Config

This is a template that can be applied to a parent directory to setup git, ssh,
and gpg to mimick another identity. Thus, this hides your identity when
interacting with a git remote.

The local git config sets another committer name. The local GPG can sign using
another identity. Git SSH/HTTPS traffic is passed through a proxy. Environment
config is set via direnv, but mise and other tools can do the same.

As this setup requires initial manual configuration, you should copy the files
into your file system and make necessary changes.

It is known to work with Mullvad VPN Wireguard Socks proxies.

## Setup

### 1. Copy the files into the parent directory of your hidden git repositories:

- `.envrc` from `.envrc.example`
- `.ssh/config`
- `.gitconfig`

Change your name in `.gitconfig`

### 2. Enable direnv

```bash
direnv allow
```

### 3. Optional: Create a SSH key and apply it

Use for example:

```bash
ssh-keygen -t ed25519 -a 100
```

Check that the key is in the local `.ssh` directory.

Check in `.envrc` that the name of the key matches. If you do changes you may
have to `direnv allow` them again. Alternatively, you can link the key file in
your `.ssh/config`. Note that the path cannot be configured as flexible as in
the shell.

The public key has to be uploaded to GitHub.

### 4. Optional: Create a GPG key

For example:

```bash
gpg --full-generate-key
gpg --list-secret-keys --keyid-format SHORT
gpg --armor --export <YOUR_SHORT_KEY_ID>
```

The gpg setup is stored in `.gnupg`

Add the short key id to your `.gitconfig`

Upload the armoured public key to GitHub.

### 5. Set the exit node

Depending on whether you use HTTPS or SSH to connect to the remote you have to
configure the socks proxy in:

HTTP: `.gitconfig` (`http`/`https` blocks)

SSH: `.ssh/config` (`ProxyCommand`)

Changing the proxy everywhere might be worthwhile when using `git submodules`.

## Usage

As `direnv` applies the env vars automatically you can use the `git` command
line as usual. However, commits will have to local identity, and network
communication will take place over the proxy
