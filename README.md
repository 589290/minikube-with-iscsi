# minikube-with-iscsi

This is the `minikube` VM compiled with an additional patch that enables iscsi support.  
  
In order to deploy [Longhorn](https://longhorn.io/) on minikube, the iscsi initiator must be present in the host (minikube VM) OS. Unfortunately, `minikube` currently does not have the iscsi kernel modules and associated system apps installed in the current distribution. Fortunately, anyone can compile the VM.iso on their own [as is documented here](https://minikube.sigs.k8s.io/docs/contrib/building/iso/). Hence the need for this patch and custom VM build to facilitate the usage of Longhorn on a local cluster.  
  
To deploy `minikube` with this custom .iso, [download it](https://github.com/589290/minikube-with-iscsi/raw/main/minikube-iscsi.iso) locally and then launch with a similar command:  

```
minikube start \
  --iso-url=file://$(pwd)/minikube-iscsi.iso \
  --driver=hyperkit \
  --container-runtime=docker \
  --kubernetes-version=1.19.16
```
