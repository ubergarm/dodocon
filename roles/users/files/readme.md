Each username gets three files:

1. Password hash: `openssl passwd -1 PASSWORD > username.passwd`
2. SSH Pubkey: username.pub
3. specify specific usernames (useful for NFS) e.g. 2000 in: username.puid
3. chmod 600 *.p*
