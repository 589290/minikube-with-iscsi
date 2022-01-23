# minikube-with-iscsi

This is the `minikube` _version 1.25.1_ virtual machine compiled with an additional patch that enables iscsi support.  
  
In order to deploy [Longhorn](https://longhorn.io/) on a minikube cluster, the iscsi initiator must be present in the host (minikube VM) OS. Unfortunately, `minikube` currently does not have the iscsi kernel module and associated system apps present in its distribution. Fortunately, anyone can [compile the `minikube` VM.iso on their own](https://minikube.sigs.k8s.io/docs/contrib/building/iso/). Therefore, the purpose of [this patch](iscsi.patch) and [custom VM.iso build](https://github.com/589290/minikube-with-iscsi/raw/main/minikube-iscsi.iso) is to facilitate the usage of Longhorn on a local `minikube` cluster.  
  
To launch a `minikube` cluster with iscsi support, download [minikube-iscsi.iso](https://github.com/589290/minikube-with-iscsi/raw/main/minikube-iscsi.iso) and then start `minikube` with a command similar to below (works with any driver, kubernetes version, or runtime) specifying the usage of the custom .iso file:  

```
minikube start \
  --iso-url=file://$(pwd)/minikube-iscsi.iso \
  --driver=hyperkit \
  --container-runtime=docker \
  --kubernetes-version=1.19.16
```

SSH'ing into the patched VM, the iscsi kernel mod and associated iscsi system apps are present:  

![](./img/minikube-iscsi.jpg)

Without the patched VM, for comparison, output of the same commands:

![](./img/minikube.jpg)

...and of course, the goal... a working Longhorn deployment on `minikube` utilizing the patched VM! ([deploying Longhorn](https://longhorn.io/docs/1.2.3/deploy/install))

![](./img/longhorn.jpg)

As a comparison, the patched VM is about 2.5 megabytes larger in size:

![](./img/iso-size.jpg)

If you would like to perform the custom build on your own, you should have already [followed these instructions setting up an environment](https://minikube.sigs.k8s.io/docs/contrib/building/iso/) to compile the new custom VM iso. To begin, checkout the `minikube` source:

```
git clone https://github.com/kubernetes/minikube.git
cd minikube
git checkout tags/v1.25.1
```

Download the [iscsi.patch](iscsi.patch) and save it inside the `minikube` folder. Then apply the patch:

```
wget https://raw.githubusercontent.com/589290/minikube-with-iscsi/main/iscsi.patch
git apply iscsi.patch
```

With the patch now applied, build the new `minikube` VM iso with iscsi support:

```
make buildroot-image
make out/minikube.iso
```

The [minikube-iscsi.iso](https://github.com/589290/minikube-with-iscsi/raw/main/minikube-iscsi.iso) in this repo was built using an Ubuntu 20.04 + docker virtual machine inside of VirtualBox.  
  
If you run into any trouble with the build using a normal user (lib64 errors) try building with the root user instead.  
  
Be aware that the build takes a LONG time -- on my 2019 Macbook Pro with 16 threads and 64 gigs of RAM, it took about 3 hours!  
  
Happy sailing! ⛵
