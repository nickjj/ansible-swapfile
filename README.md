## What is ansible-swapfile? [![Build Status](https://secure.travis-ci.org/nickjj/ansible-swapfile.png)](http://travis-ci.org/nickjj/ansible-swapfile)

It is an [Ansible](http://www.ansible.com/home) role to:

- Create a swap file
- Delete a swap file

## Why would you want to use this role?

Having a swap file is useful. It will help protect you from the OOM killer going
berserk and shutting down services, but it also gives the Linux kernel a place
to dump less frequently accessed memory pages, so it can provide more memory to
highly accessed memory pages. Basically it can improve overall system performance.

## Supported platforms

- Ubuntu 16.04 LTS (Xenial)
- Debian 8 (Jessie)
- Debian 9 (Stretch)

## Role variables

```
# Default to double your system's RAM capacity if you have 2GB or less.
swapfile_size: "{{ ((ansible_memtotal_mb | int * 2)
                    if (ansible_memtotal_mb | int <= 2048)
                    else '512') }}"

# Using fallocate is quite a bit faster than dd, so we'll default to using that.
# If your file system does not support fallocate, set this to False.
#
# If you're using one of the supported platforms you have it, so don't worry.
swapfile_fallocate: True

# Where should the swap file be created?
swapfile_path: "/swapfile-{{ swapfile_size }}"

# Settings to configure your swap file. Google them for more details.
swapfile_swappiness: 60
swapfile_vfs_cache_pressure: 100

# Default settings for configuring your swap file, you should not edit this
# directly. Instead use the variables above. This allows you to customize just
# 1 of them without having to bring along the entire dictionary.
swapfile_sysctl:
  "vm.swappiness": "{{ swapfile_swappiness }}"
  "vm.vfs_cache_pressure": "{{ swapfile_vfs_cache_pressure }}"

# When set to True, the swap file will be disabled and deleted.
swapfile_delete: False
```

## Example usage

For the sake of this example let's assume you have a group called **app** and
you have a typical `site.yml` file.

To use this role edit your `site.yml` file to look something like this:

```
---

- name: "Configure app server(s)"
  hosts: "app"
  become: True

  roles:
    - { role: "nickjj.swapfile", tags: "swapfile" }
```

Let's say you want to set a 4GB swap file and customize the swappiness, you can
do this by opening or creating `group_vars/app.yml` which is located relative
to your `inventory` directory and then making it look like this:

```
---

swapfile_size: 4096

swapfile_swappiness: 20

```

Now you would run `ansible-playbook -i inventory/hosts site.yml -t swapfile`.

## Installation

`$ ansible-galaxy install nickjj.swapfile`

## Ansible Galaxy

You can find it on the official
[Ansible Galaxy](https://galaxy.ansible.com/nickjj/swapfile/) if you want to
rate it.

## License

MIT
