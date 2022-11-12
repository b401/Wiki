---
title: GPG
author: i4
date: M11-02-2022
---
# GPG

For more best-practices: [r/GPGpractice](https://www.reddit.com/r/GPGpractice/wiki/index/)

## Commands

### Encrypt message/file
Replace `target@domain` with the intended recipient.  
Be aware that the public key needs to be in your possession 

- Use `--armor` to make the encrypted blob copy/paste friendly.
- Use `-R recipient@domain` to hide the recipient key ids. Which is a limited way to counter traffic analysis
- Use `--no-emit-version` to hide gpg version info

```
gpg --output enc_file.gpg --encrypt file.txt --no-emit-version --sign --armor -R recipient@domain
```

### Decrypt message/file
This command decrypts and verifies in one step.
```
gpg --output decrypted.txt --decrypt <encrypted file>
```

### Sign message
> A digital signature certifies and timestamps a document. If the document is subsequently modified in any way, a verification of the signature will fail. A digital signature can serve the same purpose as a hand-written signature with the additional benefit of being tamper-resistant. The GnuPG source distribution, for example, is signed so that users can verify that the source code has not been modified since it was packaged.
 More information [here](https://www.gnupg.org/gph/en/manual/x135.html)

- Use `--default-key <user-id-or-mail>` that you want to use for signing (If you have multiple).
- Use `gpg --clearsign <message file>'` to include the original message in the output file.
- It's rather uncommon to just `--sign` without also encrypting (with `--encrypt`) the message.
```
gpg -s <message file>
```

If you just need to provide a way to check the authenticity use `--clearsign`
```
$ gpg --clearsign <message file>
$ cat ./<output.file>.asc

-----BEGIN PGP SIGNED MESSAGE-----
Hash: SHA256

test
-----BEGIN PGP SIGNATURE-----

iHUEARYIAB0WIQRz43zRSr2ji3tRkuE5/MviWQWcQgUCY2+VnAAKCRA5/MviWQWc
Qog1AP4hDomHCOB/i8tVoji32MltYYvVwVVgCazj2odPw5Dc7QD9EFUCD+QOtpMu
fnnnUTsWFz/3iGjQTssbTlrnNa7ddwM=
=N1sw
-----END PGP SIGNATURE-----
```

### Verify signature
```
gpg --verify 
```


### List keys
```
gpg --list-keys
```

### Export public key
Replace `username@domain` with the key information (uuid) that you want to export.
```
gpg --output filename.gpg --armor --export username@domain
```

This file can be shared with parties that need to contact you.

### Importing a key
```
gpg --import filename.gpg
```

### Verifying key fingerprint

```
gpg --fingerprint username@domain
```

Check with the owner of the key if the fingerprint is correct and sign it afterwards.

```
gpg --sign-key username@domain
```

### Remove keys
```
gpg --delete-secret-keys {ID}
gpg --delete-keys {PUB ID}
```

### Create quickly a new key

The `--default-new-key-algo` param is used to change the default algorithm to ed25519 instead of using RSA.
```
gpg --default-new-key-algo "ed25519/cert,sign+cv25519/encr" --quick-generate-key "username@domain"
```

### Create a backup
```
gpg --output backupkeys.pgp --armor --export-secret-keys --export-options export-backup user@email
```


