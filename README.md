# terraform-release-skeleton

A collection of files and instructions used to reduce the amount of time needed to publish net-new providers to Hashicorp's Terraform Registry.  Nothing contained in here differs dramatically from the instructions from Hashi (found [here](https://www.terraform.io/docs/registry/providers/publishing.html)), however, this repository contains some initial files to assist in the scaffolding of the new repository.

Most of the root skeleton files are pulled from [Hashicorp's repository](https://github.com/hashicorp/terraform-provider-scaffolding) covering this topic.  However, in anecdotal experience, this repository can be overwhelming to those new to the process.  This repository is not meant as a replacement to the official repository, but meant as a barebones set of files and process to expedite the publishing process of new providers. 

The main steps of the process of publishing a Terraform Provider via Cisco DevNet looks like this:

1. Write, properly document, verify and test the Terraform Provider;
2. Migrate the repository to CiscoDevNet (if not already there);
3. Create GPG signing keys and securely share the public key with CiscoDevNet admin (most likely a Developer Advocate (DA));
4. Add the appropriate GoReleaser files (can be found in this repository and [here](https://github.com/hashicorp/terraform-provider-scaffolding);
5. Notify the CiscoDevNet admin you are working for, after which it can be published to the Terraform Registry;
6. Share the Terraform Provider with the world! Have you created DevNet Learning Labs already? What about a Cisco Developer blog post? 

## Included or Required Files

This repository contains several files to assist with the building of the provider when a release is generated.

### GPG Key

An ASCII-armored GPG key is required.  Raw keys are generally kept within each publishing Cisco Business Entirity (BE), with the ASCII-armored export being added to the Terrform Registry under the CiscoDevNet organization.  You may need to work with your DA in order to add the key to the appropriate namespace. If you don't have an existing GPG key, you can [generate a new GPG key](https://docs.github.com/en/github/authenticating-to-github/managing-commit-signature-verification/generating-a-new-gpg-key) to use for signing commits and tags.

From the actual GPG key, the armored version can be exported using:

```bash
gpg --armor --export "{KEY_ID}"
```

This key will need to be added to the GitHub repository under *Settings > Secrets*.  It should be added with the environment variable `GPG_PRIVATE_KEY` with any passphrase used added under the `PASSPHRASE` variable.

> Note: These environment variables can be changed, however, care must be taken to change the use of these variables in the associated files used within the GitHub action.

### goreleaser.yml

Use the included `goreleaser.yml` file into the root of the provider repository.  This file is used by the `goreleaser` container within the action to generate the packaged output files for each OS that can leverage the provider.  If the above ENV vars are changed, this file can remain in its current state

### release.yml

This file comprises the actions to be taken to generate the new files for each revision of the provider automatically, and will run with each new release generated from the GitHub web UI.  This file should be placed in the `.github/workflows/` directory of the provider repository.  If any of the environment variable names mentioned above are changed, these changes will need to be made in this file.

## Usage

Once the files are in place within the repository, a release can be created from the files within the repository.  This should kick off the action, which will take some time to run and the status can be viewed from the *Actions* tab of the GitHub web UI for that repository.  If a run is successfully completed, each of the packages for each OS will built.  This can be seen under the *Run GoReleaser* part of the action and the output assets can be viewed by clicking on the *Release*.  These files may take some time to populate within the release after running the action.  When all of the packages are listed under the release, the provider can then be published to Terraform Registry.

> Note: If the action completed successfully, but the OS specific builds and assets are not present in the release, the publish action within Terraform Registry will fail indicating that no assets are available.  If you encounter this, please wait until all files are present within the release and retry the publish action.

## Publishing the provider
Once all of the steps have been completed, the provider can then be published to Terraform Registry. For this to work an admin of the CiscoDevNet GitHub organization needs to log in to the Terraform Registry and publish. Please work with your DA or DevNet contact to do this and finalize the publishing process. 
