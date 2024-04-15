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

### Role installation:
<details><summary><b>Install this role on your ansible host (expand here)</b></summary>
stable version: 

```
cat <<EOF > ./requirements.yaml
roles:
- src: https://github.com/stuttgart-things/download-install-binary.git
  scm: git

collections:
- name: community.general
EOF

ansible-galaxy install -r ./requirements.yaml --force && ansible-galaxy collection install -r ./requirements.yaml -f
```
</details>

Role Variables
--------------

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

Example Playbook for binary-file
----------------

```
- hosts: myserver
  become: true
  vars:
    download_dir: "/tmp/downloads" # dir will be created if it doesent exists
    cli:
      kubectl:
        bin_name: "kubectl"
        bin_version: "1.29.3"
        check_bin_version_before_installing: true
        source_url: "https://dl.k8s.io/v1.29.3/bin/linux/amd64/kubectl"
        bin_to_copy: "kubectl"
        to_remove: "kubectl"
        bin_dir: "/usr/local/bin"
        version_cmd: " version --client"
        target_version: v1.29.3
        md5_checksum: "07b43208389cbc779941b94a05cf89bc"

  tasks:
    - name: Start download_install_binary
      ansible.builtin.include_role:
        name: download-install-binary
```

Example Playbook for zip-file
----------------

```
- hosts: myserver
  become: true
  vars:
    download_dir: "/tmp/downloads" # dir will be created if it doesent exists
    cli:
      packer:
        bin_name: "packer"
        bin_version: "1.10.2"
        check_bin_version_before_installing: true
        source_url: "https://releases.hashicorp.com/packer/1.10.2/packer_1.10.2_linux_amd64.zip"
        bin_to_copy: "packer"
        to_remove: "packer"
        bin_dir: "/usr/local/bin"
        version_cmd: " --version"
        target_version: v1.10.2
        md5_checksum: "374f22185f1f8cb25bc53187a2154ef0"

  tasks:
    - name: Start download_install_binary
      ansible.builtin.include_role:
        name: download-install-binary
```

Example Playbook for tar.gz-file
----------------

```
- hosts: myserver
  become: true
  vars:
    download_dir: "/tmp/downloads" # dir will be created if it doesent exists
    cli:
      velero:
        bin_name: "velero"
        bin_version: "1.13.1"
        check_bin_version_before_installing: true
        source_url: "https://github.com/vmware-tanzu/velero/releases/download/v1.13.1/velero-v1.13.1-linux-amd64.tar.gz"
        bin_to_copy: "velero-v1.13.1-linux-amd64/velero"
        to_remove: "velero-v1.13.1-linux-amd64"
        bin_dir: "/usr/local/bin"
        version_cmd: " version --client-only"
        target_version: v1.13.1
        md5_checksum: "30ab57f9520ae2318ab28eefbc81728c"

  tasks:
    - name: Start download_install_binary
      ansible.builtin.include_role:
        name: download-install-binary
```

### Run playbook command:
<details><summary><b>Example command to run the playbook (expand here)</b></summary>
```
ansible-playbook -vvv -i ~/path/to/file/download-install-binary/tests/inventory ~/path/to/file/download-install-binary/tests/test.yml
```
</details>

Role history
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
