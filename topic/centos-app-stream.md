---
id: centos-app-stream
aliases: []
tags: []
---

# centos-app-stream

改成 http://vault.centos.org

```sh
sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-*
sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-*
dnf update -y
dnf --disablerepo '*' --enablerepo extras swap centos-linux-repos centos-stream-repos -y
dnf distro-sync -y
```

改成 http://vault.epel.cloud

```sh
sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-Linux-*
sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.epel.cloud|g' /etc/yum.repos.d/CentOS-Linux-*
```
