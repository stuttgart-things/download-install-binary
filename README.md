stuttgart-things/download-install-binary
=======================

This Role downloads binaries and unarchives them for installation. It can also remove installed binaries.

<details><summary>ROLE DESCRIPTION</summary>

This role includes the following tasks:
- check whether the binary has been installed, and whether it locates in the ```bin_dir```
- Install (when ```wanted_state="present"```, default) or delete (when ```wanted_state="absent"```) the binary file. The parameter ```wanted_state``` is usually overwriten by a config-file, e.g. cherry file.
- If binary installed and version check required, the role checks binary version. When the wanted version does not match the installed version, it proceeds with the installation, otherwise the installation tasks are skipped to save time.

The process of installing binary:
- unarchive -> download and unarchive the zip-file for installation (also works for tar.gz and binary)
- copy -> copy the binary to bin-folder
- remove -> remove the downloaded directory.

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

<details><summary>EXAMPLE INVENTORY</summary>

```bash
cat <<EOF > inv
[appserver]
1.2.3.4 ansible_user=sthings
EOF
```

</details>

<details><summary>EXAMPLE PLAYBOOK - BINARIES INLINE</summary>

```yaml
cat <<EOF > download-install-binary.yaml
- hosts: all
  become: true
  vars:
    download_dir: "/tmp/downloads" # dir will be created if it doesent exists
    bin:
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

<details><summary>EXAMPLE PLAYBOOK - BINARIES FROM VARS FILE</summary>

```yaml
cat <<EOF > binaries.yaml
bin:
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

  terraform:
    bin_name: "terraform"
    bin_version: "1.8.0"
    check_bin_version_before_installing: true
    source_url: "https://releases.hashicorp.com/terraform/1.8.0/terraform_1.8.0_linux_amd64.zip"
    bin_to_copy: "terraform"
    to_remove: "terraform"
    bin_dir: "/usr/local/bin"
    version_cmd: " --version"
    target_version: v1.8.0
    md5_checksum: "2c6638e53cf5474c4d1363c17e8653ef"

  helm:
    bin_name: "helm"
    bin_version: "3.14.3"
    check_bin_version_before_installing: true
    source_url: "https://get.helm.sh/helm-v3.14.3-linux-amd64.tar.gz"
    bin_to_copy: "linux-amd64/helm"
    to_remove: "linux-amd64"
    bin_dir: "/usr/local/bin"
    version_cmd: " version"
    target_version: v3.14.3
    md5_checksum: "8dd2ecdbb70ef4e3a55083e8d5ebf352"

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

  k9s:
    bin_name: "k9s"
    bin_version: "0.32.4"
    check_bin_version_before_installing: true
    source_url: "https://github.com/derailed/k9s/releases/download/v0.32.4/k9s_Linux_amd64.tar.gz"
    bin_to_copy: "k9s"
    to_remove: "k9s"
    bin_dir: "/usr/local/bin"
    version_cmd: " version --short"
    target_version: v0.32.4
    md5_checksum: "04ba6f524a433f8ceb9095c4c8292240"

EOF
```

```yaml
cat <<EOF > download-install-binary.yaml
- hosts: all
  become: true
  vars_files:
    - binaries.yaml

  tasks:
    - name: Start download_install_binary
      ansible.builtin.include_role:
        name: download-install-binary
EOF
```

</details>

<details><summary>GET MD5 FROM INSTALLED BINARY</summary>

```yaml
cat <<EOF > get-md5.yaml
---
- hosts: "{{ target_host | default('all') }}"
  become: true
  vars:
    bin_dir: /usr/local/bin
    binary_name: nerdctl

  tasks:
    - name: "Calculate MD5 Checksum from file {{ item.value.bin_dir }}/{{ item.value.bin_name }}"
      ansible.builtin.stat:
        path: "{{ bin_dir }}/{{ binary_name }}"
        checksum_algorithm: md5
      register: status_checksum

    - name: Output checksum
      ansible.builtin.debug:
        var: status_checksum.stat.checksum
EOF
```

</details>


<details><summary>EXAMPLE EXECUTION</summary>

```bash
# INSTALL BINARIES
ansible-playbook -i inv download-install-binary.yaml -vv

# UNINSTALL BINARIES
ansible-playbook -i inv download-install-binary.yaml -vv -e wanted_state=absent

# GET MD5
ansible-playbook -i inv get-md5.yaml -vv
```

</details>


ROLE HISTORY
----------------
| DATE  | WHO | CHANGELOG |
|---|---|---|
|2024-04-17  | Andre Ebert | Changed role structure and added linter skip rules.
|2020-10-23  | Christian Mueller | Updated for using of ansible collections, fixed role structure
|2020-04-10  | Patrick Hermann | added ability to download non zip and tar files
|2020-03-30  | Xiaomin Lai | intial commit for this role in codehub

## License
<details><summary>LICENSE</summary>

Copyright 2020 patrick hermann.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
</details>

Author Information
------------------

```yaml
Xiaomin Lai, 03/2020, xiaomin.lai@sva.de, Stuttgart-Things
Patrick Hermann, 03/2020, patrick.hermann@sva.de, Stuttgart-Things
Andre Ebert, 04/2024, andre.ebert@sva.de, Stuttgart-Things
```
