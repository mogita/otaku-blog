---
title: Minikube With Podman on Apple Silicon
slug: minikube-podman-on-m1-apple-silicon
date: 2022-08-29T11:38:55+08:00
category: tech
tags: [minikube, k8s]
---

[Minikube](https://minikube.sigs.k8s.io/docs/) has been a go-to environment for running a single-node cluster to test out the Kubernetes features on a local platform. For macOS computers with an Apple Silicon, a.k.a. M1 or M2, minikube provides an ARM64 version and can be installed with [Homebrew](https://formulae.brew.sh/formula/minikube).

However when it comes to start minikube after successfully installed it, it would require Docker as the default driver. Here comes my problem. As a matter of fact, Docker on macOS has long been released under the name of "Docker Desktop", which contains a huge pile of Electron based applications and UI. It starts slow too. I don't ever want to install such a **monstrous** thing in my system. So I have to give up the default driver and look for alternatives.

At the time of writing, minikube supports various kinds of drivers, such as Docker, Hyperkit, Hyper-V, KVM, Parallels, Podman, VirtualBox, and VMware Fusion/Workstation. After an hour of struggling and testing, Podman stood out. It's backed by a QEMU virtual machine, supports Docker API and Docker-based tools, and can be intsalled in a very smooth fasion.

So I'm going to record the steps to initialize and maintain a minikube cluster with Podman.

# Installation

With the help of the [Get Started](https://minikube.sigs.k8s.io/docs/start/) documentation for minikube, just pick an installation option that works. Mine was `brew install minikube`.

Then the Podman installation done with `brew install podman`.

Simple enough.

# Start Up

Create a Podman machine with the following command, giving it 2 CPU cores and 8Gi memory.

```
podman machine init --cpus 2 --memory 8192 --disk-size 80
podman machine start
podman system connection default podman-machine-default-root
```

Now Podman should be running in the background. One can check out the information with `podman info` command, or find out its running stats with `podman stats` command.

To start minikube, follow the step below. By default, minikube uses Docker as the driver. One should pass a `--driver` argument to change that.

```
minikube start --driver=podman --container-runtime=cri-o
```

One should see the output similar to the following logs:

```
❯ minikube start --driver=podman --container-runtime=cri-o

😄  minikube v1.26.1 on Darwin 12.5.1 (arm64)
✨  Using the podman (experimental) driver based on existing profile
👍  Starting control plane node minikube in cluster minikube
🚜  Pulling base image ...
E0829 11:34:04.822031   11181 cache.go:203] Error downloading kic artifacts:  not yet implemented, see issue #8426
🔄  Restarting existing podman container for "minikube" ...
🎁  Preparing Kubernetes v1.24.3 on CRI-O 1.24.1 ...
E0829 11:34:09.704438   11181 start.go:129] Unable to get host IP: RoutableHostIPFromInside is currently only implemented for linux
🔗  Configuring CNI (Container Networking Interface) ...
🔎  Verifying Kubernetes components...
    ▪ Using image gcr.io/k8s-minikube/storage-provisioner:v5
    ▪ Using image kubernetesui/dashboard:v2.6.0
    ▪ Using image kubernetesui/metrics-scraper:v1.0.8
🌟  Enabled addons: storage-provisioner, default-storageclass, dashboard
🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
```

Alternative to explicitly passing the `--driver` argument, one can change the defautl driver with this command:

```
minikube config set driver podman
```

# Stop / Restart

To stop the minikube + Podman system, follow these commands:

```
minikube stop
podman machine stop
```

To restart the system, simply run `start` respectively (note the sequence, `podman` should go first as `minikube` depends on it):

```shell
podman machine start
minikube start --driver=podman --container-runtime=cri-o
# or with the default driver changed to podman:
minikube start
```

# Not Working?

**Q**: If there are multiple clusters to manage, including but not limited to GKE (by Google Cloud) and EKS (by AWS) etc., how to manage and switch from these clusters?

**A**: This can be configured through `~/.kube/config` easily as Kubernetes has the concept of "Context", which basically means different clusters. Upon minikube successfully starting, a new context called `minikube` shall be inserted automatically to this config file. One can list and switch to a certain context using the following commands (switching to minikube for example):

```
kubectl config get-contexts
kubectl config set current-context minikube
```

