+++
title = "OpenPGP"
description = "My OpenPGP public key, fingerprint, and instructions for verifying signed software releases and other published files."
+++

I use OpenPGP to sign release artefacts and, occassionally, other files I publish.

My public key is available for download here:

[https://ethanplant.ca/ethanplant.asc](/ethanplant.asc)

The fingerprint is: 

```
55F495BFB563CC48A4ED1A0240C0FDB11DB42537
```

You can inspect the ky after downloading it:
```sh
gpg --show-keys --fingerprint ethanplant.asc
```

And import it with
```sh
gpg --import ethanplant.asc
```

## Verifying a release

Some of my software releases include detached signatures alongside their source archives. For example, given:
```
elsewhere-0.1.1.tar.gz
elsewhere-0.1.1.tar.gz.asc
```

you can verify the signature with:
```sh
gpg --verify elsewhere-0.1.1.tar.gz.asc elsewhere-0.1.1.tar.gz
```

GPG should report that the signature was made by my key. You should still compare the reported fingerprint against the fingerprint published on this page.

## Why publish the key here?

Keyservers are useful distribution infrastructure. They are not where I want the canonical statement of my identity to live. This website is mine. If I am asking you to trust a signature on software published under my name, it seems reasonable that the key used to verify it should also be obtainable from my own domain.

The downloadable key is therefore a convenience. The fingerprint above is the important part. If the fingerprint in a downloaded key does not match the one published here, do not trust it.
