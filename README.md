download_binary
=========

This is a generic role which includes the following tasks:
- unarchive -> download and unarchive the zip-file for installation
- copy -> copy the binary to bin-folder
- remove -> remove the downloaded directory.

Requirements
------------

- unzip
- curl or wget

Role Variables
--------------

The role takes in a python dictionary with the following values: 
- source_link:   the link to the installation file
- bin_to_copy:   directory of the binary, which will be copied to bin-folder
- to_remove: directory of the downloaded and unpacked installation file, which will be removed
- bin_dir:       directory of the bin-folder, usually "/usr/bin/" or "/usr/local/bin/".

Dependencies
------------

None.

Example Playbook
----------------

```
- hosts: myserver
  vars:
    download_dir: "/tmp/downloads" # dir will be created if it doesent exists
    cli:
      terraform:
        source_url: "https://releases.hashicorp.com/terraform/0.12.24/terraform_0.12.24_linux_amd64.zip"
        bin_to_copy: "{{ download_dir }}/terraform"
        bin_dir: "/usr/local/bin/"
        to_remove: "{{ download_dir }}/terraform"

      terraform-inventory:
        source_url: "https://github.com/adammck/terraform-inventory/releases/download/v0.9/terraform-inventory_0.9_linux_amd64.zip"
        bin_to_copy: "{{ download_dir }}/terraform-inventory"
        bin_dir: "/usr/local/bin/"
        to_remove: "{{ download_dir }}/terraform-inventory"

  tasks:
    - name: call role download_binary in a loop
      include_role:
        name: download_binary
      loop: "{{ lookup('dict', cli, wantlist=True) }}"
```

License
-------

BSD

Author Information
------------------

Xiaomin Lai, 03/2020, xiaomin.lai@sva.de
Patrick Hermann, 03/2020, patrick.hermann@sva.de
