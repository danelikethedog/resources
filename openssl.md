# Openssl Commands and Concepts

## Useful Links
- [OpenSSL Command Docs](https://wiki.openssl.org/index.php/Command_Line_Utilities#Commands)

## Generating Hashes for Files

In order to generate a hash for a file you can use the following:

```bash
openssl dgst -<sha1><md5> <filename>
```

You can also use the explicit hash alg to generate the hash:

```bash
openssl <sha1><md5> <filename>
```

