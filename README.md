{
  "builders": [
    {
      "boot_command": [
        "<tab> text ks=http://{{ .HTTPIP }}:{{ .HTTPPort }}/vagrant.ks<enter><wait>"
      ],
      "boot_wait": "10s",
      "disk_size": "10240",
      "export_opts": [
        "--manifest",
        "--vsys",
        "0",
        "--description",
        "{{user `artifact_description`}}",
        "--version",
        "{{user `artifact_version`}}"
      ],
 в мануале указан неправильная ссылка centos
+ неверный параметр iso_cheksum

      "http_directory": "http",
      "iso_checksum": "sha256:659691c28a0e672558b003d223f83938f254b39875ee7559d1a4a14c79173193",
      "iso_url": "http://mirror.yandex.ru/centos/7.8.2003/isos/x86_64/CentOS-7-x86_64-Minimal-2003.iso",
      "name": "{{user `image_name`}}",
      "output_directory": "builds",
      "shutdown_command": "sudo -S /sbin/halt -h -p",
      "shutdown_timeout": "5m",
      "ssh_password": "vagrant",
      "ssh_port": 22,
      "ssh_pty": true,
      "ssh_timeout": "20m",
      "ssh_username": "vagrant",
      "type": "virtualbox-iso",
      "vboxmanage": [
        [
          "modifyvm",
          "{{.Name}}",
          "--memory",
          "1024"
        ],
        [
          "modifyvm",
          "{{.Name}}",
          "--cpus",
          "2"
        ]
      ],
      "vm_name": "packer-centos-vm"
    }
  ],
  "post-processors": [
    {
      "compression_level": "7",
      "output": "centos-{{user `artifact_version`}}-kernel-5-x86_64-Minimal.box",
      "type": "vagrant"
    }
  ],
  "provisioners": [
    {
      "execute_command": "{{.Vars}} sudo -S -E bash '{{.Path}}'",
      "expect_disconnect": true,
      "override": {
        "{{user `image_name`}}": {
          "scripts": [
            "scripts/stage-1-kernel-update.sh",
            "scripts/stage-2-clean.sh"
          ]
        }
      },
      "pause_before": "20s",
      "start_retry_timeout": "1m",
      "type": "shell"
    }
  ],
  "variables": {
    "artifact_description": "CentOS 7.8 with kernel 5.x",
    "artifact_version": "7.8.2003",
    "image_name": "centos-7-8"
  }
}



packer build centos.json

vagrant box add --name centos-7-8 centos-7.8.2003-kernel-5-x86_64-Minimal.box

vagrant box list
centos-7-8            (virtualbox, 0)

vagrant cloud auth login
Vagrant Cloud username or email: <user_email>
Password (will be hidden): 
You are now logged in.


vagrant cloud publish --release <username>/centos-7-8 1.0 virtualbox centos-7.8.2003-kernel-5-x86_64-Minimal.box


Загрузить через CLI так и не удалось, заливал через веб интерфейс.
и то с 20 попытки.
