# Verifier Instructions

At any point, a verifier can run a script to verify each incoming PR and verify the hashes of the targets files.

Pre-requisites:

* [ ] A local Git installation and a Go development setup with support for the Go version [here](https://github.com/sigstore/root-signing/blob/1d4462a5deaffbe3055b5e3fe3c53d1918594159/go.mod#L3)
* SSH authentication for GitHub (see [here](https://docs.github.com/en/authentication/connecting-to-github-with-ssh))

0. **Verifiers** should fork [this](https://github.com/sigstore/root-signing) git repository by clicking the "fork" button on GitHub.

1. To verify a PR, run the script with the pull request ID to verify, where `YOUR_GITHUB_USERNAME` is your GitHub username.

```bash
GITHUB_USER=${YOUR_GITHUB_USERNAME} ./scripts/verify.sh ${PULL_REQUEST_ID}
```

This will download the Yubico root CA. For each key added, it will verify:

* That the hardware key is authentic and came from the manufacturer (using the device cert)
* That the signing key was generated on the device (using the key attestation)
* That the directory where they keys were added match the serial number from the cert (preventing a keyholder from using their key multiple times)

If there is any repository data added in the PR, it will also check signatures in each top-level role.

2. To verify the state of a ceremony branch, add the environment variable `BRANCH` to indicate the ceremony branch.

```bash
GITHUB_USER=${YOUR_GITHUB_USERNAME} BRANCH=${CEREMONY_BRANCH} ./scripts/verify.sh
```

3. Other verifications:

* Verify the targets signed and their SHAs. You may choose to retrieve an independent local copy of the targets (Fulcio Root CA certificate, SigStore signing key, Rekor public key, CTFE key) and verify that the SHA-512 matches the sha in `targets.json`.

* Manual hardware key verification using OpenSSL: the verification script will also output commands for OpenSSL certificate text output and verification for each key it sees. You may choose to run those manually. It will verify the key certificate using the device certificate intermediate and the Yubikey Root CA. It will also extract the public key from the key certificate to compare with the public key in the repository.
