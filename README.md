Ansible role: sshd
===================

Installs OpenSSH server and allows configuration of `sshd_config` and `sshd_config.d/*.conf`.

Role Variables
--------------

```
ENTRY POINT: main - Install and configure OpenSSH server

        Installs OpenSSH server and allows configuration of
        sshd_config and sshd_config.d/*.conf.

OPTIONS (= is mandatory):

- sshd_config
        Contents of the sshd_config configuration file, or empty
        string to leave file as is
        default: ''
        type: str

- sshd_config_d
        List of extra sshd_config.d configuration files to create
        default: []
        elements: dict
        type: list

        OPTIONS:

        = config
            Contents of the sshd_config.d configuration file
            type: str

        = name
            Name of the sshd_config.d configuration file
            type: str
```

Installation
------------

This role can either be installed manually with the ansible-galaxy CLI tool:

    ansible-galaxy install git+https://github.com/wandansible/sshd,main,wandansible.sshd
     
Or, by adding the following to `requirements.yml`:

    - name: wandansible.sshd
      src: https://github.com/wandansible/sshd

Roles listed in `requirements.yml` can be installed with the following ansible-galaxy command:

    ansible-galaxy install -r requirements.yml

Example Playbook
----------------

    - hosts: all
      roles:
         - role: wandansible.sshd
           become: true
           vars:
             sshd_config: |
               Include /etc/ssh/sshd_config.d/*.conf

               # Supported HostKey algorithms by order of preference.
               HostKey /etc/ssh/ssh_host_ed25519_key
               HostKey /etc/ssh/ssh_host_rsa_key
               HostKey /etc/ssh/ssh_host_ecdsa_key

               KexAlgorithms curve25519-sha256@libssh.org,ecdh-sha2-nistp521,ecdh-sha2-nistp384,ecdh-sha2-nistp256,diffie-hellman-group-exchange-sha256

               Ciphers chacha20-poly1305@openssh.com,aes256-gcm@openssh.com,aes128-gcm@openssh.com,aes256-ctr,aes192-ctr,aes128-ctr

               MACs hmac-sha2-512-etm@openssh.com,hmac-sha2-256-etm@openssh.com,umac-128-etm@openssh.com,hmac-sha2-512,hmac-sha2-256,umac-128@openssh.com

               # Password based logins are disabled - only public key based logins are allowed.
               AuthenticationMethods publickey

               # LogLevel VERBOSE logs user's key fingerprint on login. Needed to have a clear audit track of
               # which key was using to log in.
               LogLevel VERBOSE

               # Log sftp level file access (read/write/etc.) that would not be easily logged otherwise.
               Subsystem sftp  /usr/lib/ssh/sftp-server -f AUTHPRIV -l INFO

               PermitRootLogin prohibit-password

               UsePAM yes

               PrintMotd no

               PrintLastLog yes
     
             sshd_config_d:
               - name: change-port
                 config:
                   Port 2222
