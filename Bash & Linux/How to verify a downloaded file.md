# How to verify a downloaded file?

These are the steps one could take to verify a downloaded file. Vagrant is used as an example.

1. Download gpg public key, for example:

    ```bash
    curl -O https://keybase.io/hashicorp/key.asc
    ```

    The above public key will be used to verify that the checksum’s digital signature was created by the same person who made the key (in this case HashiCorp), this confirms the checksum itself has not been tampered with.

2. you can rename the key now (you can choose any name) and add it to your key ring:

    ```bash
    mv key.asc vagrant_1.8.6_public_key.asc
    gpg --import vagrant_1.8.6_public_key.asc
    ```

3. Now download the checksum:

    ```bash
    curl -O https://releases.hashicorp.com/vagrant/1.8.6/vagrant_1.8.6_SHA256SUMS
    ```

4. Download checksum’s signature (note that not everyone provides a digital signature for the checksum, so if not provided then you skip straight to the last step):

    ```bash
    curl -O https://releases.hashicorp.com/vagrant/1.8.6/vagrant_1.8.6_SHA256SUMS.sig
    ```

5. Use the public key from the keyring to verify that the checksum (vagrant_1.8.6_SHA256SUMS in our case) is genuine, for this use the downloaded checksum’s signature. Note that here the .sig file was created by the owner using private key (via `gpg --sign` ...), so we decrypt it is using public key which was downloaded earlier:

    ```bash
    gpg --verify vagrant_1.8.6_SHA256SUMS.sig vagrant_1.8.6_SHA256SUMS
    ```

6. make sure you get the following (note "Good signature"), any other warnings can be ignored

    ```bash
    gpg: Good signature from "HashiCorp Security <security@hashicorp.com>"
    ```

7. Now when you know that the checksum is fine you can verify if the `SHASUM` of the downloaded .deb file matches the one in the checksum, for this make sure that the checksum txt file and the downloaded .deb files are in the same directory, also note that the name of the downloaded .deb file matches one of the names listed in the checksum text file. Note that the checksum in our case is just a text file that contains some hashed value next to a file name you earlier downloaded, for example in our case it is vagrant_1.8.6_x86_64.deb, so you basically check that those hash values are genuine.

Now run:

```bash
shasum --algorithm 256 --check vagrant_1.8.6_SHA256SUMS
```

Note that the checksum text file contains a number of lines you just need to make sure that there's OK against your .deb file
