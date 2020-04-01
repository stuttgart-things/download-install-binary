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
- dir_to_copy:   directory of the binary, which will be copied to bin-folder
- dir_to_remove: directory of the downloaded and unpacked installation file, which will be removed
- bin_dir:       directory of the bin-folder, usually "/usr/bin/" or "/usr/local/bin/".

Dependencies
------------

None.

Example Playbook
----------------

```
- hosts: servers
  vars:
    cli:
      velero:
        source_link: "https://github.com/vmware-tanzu/velero/releases/download/v1.3.1/velero-v1.3.1-linux-amd64.tar.gz"
        dir_to_copy: "/tmp/velero-v1.3.1-linux-amd64/velero"
        dir_to_delete: "/tmp/velero-v1.3.1-linux-amd64"
        bin_dir: "/usr/bin/"
      awsprovider:
        source_link: "https://releases.hashicorp.com/terraform-provider-aws/2.27.0/terraform-provider-aws_2.27.0_linux_amd64.zip"
        dir_to_copy: "/tmp/terraform-provider-aws_v2.27.0_x4"
        dir_to_delete: "/tmp/terraform-provider-aws_v2.27.0_x4"
        bin_dir: "/usr/bin/"

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
