#Install Kubernetes on Centos
Documentation to install kubernetes cluster on Centos

## Getting Started

1. Edit Hosts file
`vim /etc/hosts`

`10.0.15.10      k8s-master`
`10.0.15.21      node01`
`10.0.15.22      node02`

1. Disable SELinux
`setenforce 0`
`sed -i --follow-symlinks 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux`

1. Enable br_netfilter Kernel Module
`modprobe br_netfilter`
`echo '1' > /proc/sys/net/bridge/bridge-nf-call-iptables`

1. Disable Swap
`swapoff -a`

1. Edit fstab - comment swap line
`vim /etc/fstab`

1. Install Docker
`yum install -y yum-utils device-mapper-persistent-data lvm2`
`yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo`
`yum install -y docker-ce`

1. Install Kubernetes
``cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg
        https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
EOF``

`yum install -y kubelet kubeadm kubectl`

1. Reboot then startup Docker and Kubernetes
`systemctl start docker && systemctl enable docker`
`systemctl start kubelet && systemctl enable kubelet`

`docker info | grep -i cgroup`

1. Cgroup changes
`sed -i 's/cgroup-driver=systemd/cgroup-driver=cgroupfs/g' /etc/systemd/system/kubelet.service.d/10-kubeadm.conf`

1. Restart daemon and kubelet
`systemctl daemon-reload`
`systemctl restart kubelet`

1. Initialize the Kubernetes cluster - on Master only
`kubeadm init --apiserver-advertise-address=10.0.15.10 --pod-network-cidr=10.244.0.0/16`
