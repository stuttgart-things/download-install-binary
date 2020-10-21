download-install-binary 
=======================

This role includes the following tasks:
- check whether the binary has been installed, and whether it locates in the ```bin_dir```
- Install (when ```wanted_state="present"```, default) or delete (when ```wanted_state="absent"```) the binary file. The parameter ```wanted_state``` is usually overwriten by a config-file, e.g. cherry file.
- If binary installed and version check required, the role checks binary version. When the wanted version does not match the installed version, it proceeds with the installation, otherwise the installation tasks are skipped to save time.

The process of installing binary:
- unarchive -> download and unarchive the zip-file for installation
- copy -> copy the binary to bin-folder
- remove -> remove the downloaded directory.

### Role installation:
<details><summary><b>Install this role on your ansible host (klick here)</b></summary>
stable version: 

```
cat <<EOF > ./requirements.yaml
roles:
- src: git@codehub.sva.de:Lab/stuttgart-things/supporting-roles/download-install-binary.git
  scm: git
  version: stable

collections:
- name: community.general
EOF
ansible-galaxy install -r ./requirements.yaml --force && ansible-galaxy collection install -r ./requirements.yaml -f
```
latest version: (may unstable)

```
cat <<EOF > ./requirements.yaml
roles:
- src: git@codehub.sva.de:Lab/stuttgart-things/supporting-roles/download-install-binary.git
  scm: git

collections:
- name: community.general
EOF
ansible-galaxy install -r ./requirements.yaml --force && ansible-galaxy collection install -r ./requirements.yaml -f
```
</details>

Requirements
------------

- unzip
- curl or wget

Role Variables
--------------

The role takes in a python dictionary with the following values: 
- bin_name: binary name
- bin_version: binary version
- source_url: the link to the installation file
- bin_to_copy: path of the binary, which will be copied to bin-folder
- to_remove: path of the downloaded and unpacked installation file, which will be removed
- bin_dir: directory of the bin-folder, usually "/usr/bin/" or "/usr/local/bin/".

Dependencies
------------

None.

Example Playbook
----------------

```
- hosts: myserver
  vars:
    download_dir: "/tmp/downloads" # dir will be created if it doesent exists
    bin:
      terraform:
        bin_name: 'terraform'
        bin_version: '0.12.24'
        source_url: "https://releases.hashicorp.com/terraform/0.12.24/terraform_0.12.24_linux_amd64.zip"
        bin_to_copy: "terraform"
        bin_dir: "/usr/local/bin"
        to_remove: "terraform"
        check_bin_version_before_installing: true

      velero:
        bin_name: "velero"
        bin_version: "1.3.1"
        check_bin_version_before_installing: true
        source_url: "https://github.com/vmware-tanzu/velero/releases/download/v1.3.1/velero-v1.3.1-linux-amd64.tar.gz"
        bin_to_copy: "velero-v1.3.1-linux-amd64/velero"
        to_remove: "velero-v1.3.1-linux-amd64"
        bin_dir: "/usr/local/bin"

  tasks:
    - name: call role download_binary in a loop
      include_role:
        name: download_binary
      loop: "{{ lookup('dict', bin, wantlist=True) }}"
```

License
-------

BSD

Author Information
------------------

Xiaomin Lai, 03/2020, xiaomin.lai@sva.de
Patrick Hermann, 03/2020, patrick.hermann@sva.de
