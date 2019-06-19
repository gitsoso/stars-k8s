
This is an installer for Kubernetes.

It is Kubespray with a few minor configurations and alterations.

Directory layout:
```
├── ansible.cfg
├── archive
├── configure
├── create-dashboard
├── get-token
├── kubespray
├── log.txt.prod-deployment-20190613
├── README
├── requirements.txt
└── venv
```
Changes:

In ansible.cfg, we set the remote temp dir to be rooted in /tmp. This is likely unnecessary. A permissions issue addressed elsewhere may have been causing the problem that drove this change.

remote_tmp = /tmp/.ansible-${USER}/tmp

