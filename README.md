### Kube Installer Sketch

Sketch of a Kubernetes installer.

It's Kubespray with a few minor configurations and alterations.

Layout:
```
├── ansible.cfg
├── configure
├── create-dashboard
├── get-token
├── kubespray
├── log.txt.prod-deployment-20190613
├── README
├── requirements.txt
└── venv
```
### Changes:

#### Turn off override_system_hostname

Not necessary or [helpful](https://github.com/kubernetes-sigs/kubespray/issues/4787) for kubespray to change hostnames.

#### Populating inventory to etc/hosts

This messes up /etc/hosts by putting stuff in the wrong order. Really don't want that.
```
populate_inventory_to_hosts_file: false
```
in kubespray/roles/kubernetes/preinstall/defaults/main.yml

### Other Details of the Install

#### Remote Tmp

Set remote temp path in ansible.conf (maybe only needed because of iii)
```
remote_tmp = /tmp/.ansible-${USER}/tmp
```

#### Home Dir Permissions

This has nothing to do with Kubespray. But an authentication or permission failure was fixed by:
```
for x in {0..8}; do ssh stars-k$x.edc.renci.org "sudo chown -R $USER ~"; done
````

### After the Install

#### The Dashboard

After the install ....
```
kubectl create serviceaccount kubernetes-dashboard
kubectl create -f /projects/stars/stack/k8s/dashboard-cluster-role-binding.yaml 
kubectl get serviceaccounts kubernetes-dashboard -o yaml
kubectl get secret kubernetes-dashboard-token-tcmqh -o yaml
kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')
```

#### A Note on Calico and iptables

iptables v1.4.21 vs min required == 1.4.7
Also, maximum known working version is 1.6


#### Helm

```
kubectl --kubeconfig=renci-translator create serviceaccount --namespace kube-system tiller
kubectl --kubeconfig=renci-translator create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller
kubectl --kubeconfig=renci-translator patch deploy --namespace kube-system tiller-deploy -p '{"spec":{"template":{"spec":{"serviceAccount":"tiller"}}}}'
```
