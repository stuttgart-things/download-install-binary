stuttgart-things/download-install-binary 
=======================

This role includes the following tasks:
- check whether the binary has been installed, and whether it locates in the ```bin_dir```
- Install (when ```wanted_state="present"```, default) or delete (when ```wanted_state="absent"```) the binary file. The parameter ```wanted_state``` is usually overwriten by a config-file, e.g. cherry file.
- If binary installed and version check required, the role checks binary version. When the wanted version does not match the installed version, it proceeds with the installation, otherwise the installation tasks are skipped to save time.

The process of installing binary:
- unarchive -> download and unarchive the zip-file for installation (also works for tar.gz and binary)
- copy -> copy the binary to bin-folder
- remove -> remove the downloaded directory.


<details><summary>ROLE INSTALLATION</summary>

```bash
cat <<EOF > ./requirements.yaml
roles:
- src: https://github.com/stuttgart-things/download-install-binary.git
  scm: git

collections:
- name: community.general
  version: 8.5.0
EOF

ansible-galaxy install -r ./requirements.yaml --force
ansible-galaxy collection install -r ./requirements.yaml -f
```

</details>

<details><summary>ROLE VARIABLES</summary>

The role takes in a python dictionary with the following values: 
* bin_name: binary name
* bin_version: binary version
* source_url: the link to the installation file
* bin_to_copy: path of the binary, which will be copied to bin-folder
* to_remove: path of the downloaded and unpacked installation file, which will be removed
* bin_dir: directory of the bin-folder, usually "/usr/bin/" or "/usr/local/bin/".
* version_cmd: command to get current binary version
* target_version: binary version value to compare with current binary version
* md5_checksum: hash of current binary version

</details>

<details><summary>EXAMPLE INVENTORY</summary>

```bash
cat <<EOF > inv
[appserver]
1.2.3.4 ansible_user=sthings
EOF
```

</details>

<details><summary>EXAMPLE PLAYBOOK</summary>

```yaml
cat <<EOF > download-install-binary.yaml
- hosts: all
  become: true
  vars:
    download_dir: "/tmp/downloads" # dir will be created if it doesent exists
    cli:
      kubectl:
        bin_name: "kubectl"
        bin_version: "1.29.3"
        check_bin_version_before_installing: true
        source_url: "https://dl.k8s.io/v1.29.3/bin/linux/amd64/kubectl" # just the binary
        bin_to_copy: "kubectl"
        to_remove: "kubectl"
        bin_dir: "/usr/local/bin"
        version_cmd: " version --client"
        target_version: v1.29.3
        md5_checksum: "07b43208389cbc779941b94a05cf89bc" # md5 checksum given
      packer:
        bin_name: "packer"
        bin_version: "1.10.2"
        check_bin_version_before_installing: true
        source_url: "https://releases.hashicorp.com/packer/1.10.2/packer_1.10.2_linux_amd64.zip" # zipped binary
        bin_to_copy: "packer"
        to_remove: "packer"
        bin_dir: "/usr/local/bin"
        version_cmd: " --version"
        target_version: v1.10.2
        # md5 not checksum given
      velero:
        bin_name: "velero"
        bin_version: "1.13.1"
        check_bin_version_before_installing: true
        source_url: "https://github.com/vmware-tanzu/velero/releases/download/v1.13.1/velero-v1.13.1-linux-amd64.tar.gz" # tar.gz binary
        bin_to_copy: "velero-v1.13.1-linux-amd64/velero"
        to_remove: "velero-v1.13.1-linux-amd64"
        bin_dir: "/usr/local/bin"
        version_cmd: " version --client-only"
        target_version: v1.13.1
        md5_checksum: "30ab57f9520ae2318ab28eefbc81728c" # md5 checksum given

  tasks:
    - name: Start download_install_binary
      ansible.builtin.include_role:
        name: download-install-binary
EOF
```

</details>

<details><summary>EXAMPLE EXECUTION</summary>

```bash
# INSTALL BINARIES
ansible-playbook -i inv download-install-binary.yaml -vv

# UNINSTALL BINARIES
ansible-playbook -i inv download-install-binary.yaml -vv -e wanted_state=absent
```

</details>


ROLE HISTORY
----------------
| date  | who | changelog |
|---|---|---|
|2020-03-30  | Xiaomin Lai | intial commit for this role in codehub
|2020-04-10  | Patrick Hermann | added ability to download non zip and tar files
|2020-10-23   | Christian Mueller | Updated for using of ansible collections, fixed role structure

License
-------

BSD

Author Information
------------------

Xiaomin Lai, 03/2020, xiaomin.lai@sva.de, Stuttgart-Things

Patrick Hermann, 03/2020, patrick.hermann@sva.de, Stuttgart-Things
