# OCP-Install-Nutanix

1. Creating install-config
openshift-install create install-config --dir /root/workspace/ocpinstall/ --log-level debug
DEBUG OpenShift Installer 4.14.34
DEBUG Built from commit 0363a65f4b5504f77bdb37fba93a3e0f4deadce1
DEBUG Fetching Install Config...
DEBUG Loading Install Config...
DEBUG   Loading SSH Key...
DEBUG   Loading Base Domain...
DEBUG     Loading Platform...
DEBUG   Loading Cluster Name...
DEBUG     Loading Base Domain...
DEBUG     Loading Platform...
DEBUG   Loading Networking...
DEBUG     Loading Platform...
DEBUG   Loading Pull Secret...
DEBUG   Loading Platform...
DEBUG   Fetching SSH Key...
DEBUG   Generating SSH Key...
? SSH Public Key /root/.ssh/id_ed25519.pub
DEBUG   Fetching Base Domain...
DEBUG     Fetching Platform...
DEBUG     Generating Platform...
? Platform nutanix
? Prism Central prism-central-url.example.com
? Port 9440
? Username username
? Password [? for help] **************
INFO Connecting to Prism Central prism-central-url.example.com
? Prism Element prism-element
? Subnet Subnet1
? Virtual IP Address for API 10.10.10.10
? Virtual IP Address for Ingress 10.10.10.11
DEBUG   Generating Base Domain...
? Base Domain example.com
DEBUG   Fetching Cluster Name...
DEBUG     Fetching Base Domain...
DEBUG     Reusing previously-fetched Base Domain
DEBUG     Fetching Platform...
DEBUG     Reusing previously-fetched Platform
DEBUG   Generating Cluster Name...
? Cluster Name cluster-name
DEBUG   Fetching Networking...
DEBUG     Fetching Platform...
DEBUG     Reusing previously-fetched Platform
DEBUG   Generating Networking...
DEBUG   Fetching Pull Secret...
DEBUG   Generating Pull Secret...
? Pull Secret [? for help] *********************************************************************************************************************************************
DEBUG   Fetching Platform...
DEBUG   Reusing previously-fetched Platform
DEBUG Generating Install Config...
INFO Install-Config created in: /root/workspace/ocpinstall

======================================================================
2. Setup proxy environment variables

export http_proxy=http://proxy-ip:8080
export https_proxy=http://proxy-ip:8080

3. Credentials CCOCTL Generation
RELEASE_IMAGE=$(openshift-install version| awk '/release image/ {print $3}')
oc adm release extract --from=$RELEASE_IMAGE --credentials-requests --included --install-config=/root/workspace/ocpinstall/install-config.yaml --to=/root/workspace/ocpinstall/
Extracted release payload created at 2024-08-01T09:40:34Z

ccoctl nutanix create-shared-secrets --credentials-requests-dir=/root/workspace//ocpinstall/ --output-dir=/root/workspace/ocpinstall/ --credentials-source-filepath=/root/workspace/ocpinstall/prism_credentials.yaml
2024/10/16 19:34:59 Saved credentials configuration to: /root/workspace/ocpinstall/manifests/openshift-cloud-controller-manager-nutanix-credentials-credentials.yaml
2024/10/16 19:34:59 Saved credentials configuration to: /root/workspace/ocpinstall/manifests/openshift-machine-api-nutanix-credentials-credentials.yaml

4. Unset proxy variables.
unset https_proxy
unset http_proxy

5. Create manifest

openshift-install create manifests --dir=/root/workspace/ocpinstall/ --log-level debug

6. List the files in manifests folder and copy the credentials files to manifests folder
ls -l manifests/
total 92
-rw-r----- 1 root root   559 Oct 16 19:41 cloud-provider-config.yaml
-rw-r----- 1 root root 10630 Oct 16 19:41 cluster-config.yaml
-rw-r----- 1 root root   195 Oct 16 19:41 cluster-dns-02-config.yml
-rw-r----- 1 root root   955 Oct 16 19:41 cluster-infrastructure-02-config.yml
-rw-r----- 1 root root   231 Oct 16 19:41 cluster-ingress-02-config.yml
-rw-r----- 1 root root  9607 Oct 16 19:41 cluster-network-01-crd.yml
-rw-r----- 1 root root   273 Oct 16 19:41 cluster-network-02-config.yml
-rw-r----- 1 root root   701 Oct 16 19:41 cluster-proxy-01-config.yaml
-rw-r----- 1 root root   171 Oct 16 19:41 cluster-scheduler-02-config.yml
-rw-r----- 1 root root   200 Oct 16 19:41 cvo-overrides.yaml
-rw-r----- 1 root root   118 Oct 16 19:41 kube-cloud-config.yaml
-rw-r----- 1 root root  1304 Oct 16 19:41 kube-system-configmap-root-ca.yaml
-rw-r----- 1 root root  4090 Oct 16 19:41 machine-config-server-tls-secret.yaml
-rw------- 1 root root   316 Oct 16 19:34 openshift-cloud-controller-manager-nutanix-credentials-credentials.yaml
-rw-r----- 1 root root  3953 Oct 16 19:41 openshift-config-secret-pull-secret.yaml
-rw------- 1 root root   303 Oct 16 19:34 openshift-machine-api-nutanix-credentials-credentials.yaml
-rw-r----- 1 root root  8217 Oct 16 19:41 user-ca-bundle-config.yaml


7. Create OCP cluster
openshift-install create cluster --dir=/root/workspace/ocpinstall/ --log-level debug

Once installation completes, all cluster operators are up and running.

8. Output of CO

oc get co
NAME                                       VERSION   AVAILABLE   PROGRESSING   DEGRADED   SINCE   MESSAGE
authentication                             4.14.34   True        False         False      57m
baremetal                                  4.14.34   True        False         False      79m
cloud-controller-manager                   4.14.34   True        False         False      92m
cloud-credential                           4.14.34   True        False         False      80m
cluster-autoscaler                         4.14.34   True        False         False      79m
config-operator                            4.14.34   True        False         False      84m
console                                    4.14.34   True        False         False      64m
control-plane-machine-set                  4.14.34   True        False         False      75m
csi-snapshot-controller                    4.14.34   True        False         False      67m
dns                                        4.14.34   True        False         False      67m
etcd                                       4.14.34   True        False         False      78m
image-registry                             4.14.34   True        False         False      73m
ingress                                    4.14.34   True        False         False      66m
insights                                   4.14.34   True        False         False      76m
kube-apiserver                             4.14.34   True        False         False      78m
kube-controller-manager                    4.14.34   True        False         False      77m
kube-scheduler                             4.14.34   True        False         False      78m
kube-storage-version-migrator              4.14.34   True        False         False      67m
machine-api                                4.14.34   True        False         False      73m
machine-approver                           4.14.34   True        False         False      80m
machine-config                             4.14.34   True        False         False      80m
marketplace                                4.14.34   True        False         False      80m
monitoring                                 4.14.34   True        False         False      59m
network                                    4.14.34   True        False         False      80m
node-tuning                                4.14.34   True        False         False      80m
openshift-apiserver                        4.14.34   True        False         False      67m
openshift-controller-manager               4.14.34   True        False         False      78m
openshift-samples                          4.14.34   True        False         False      76m
operator-lifecycle-manager                 4.14.34   True        False         False      81m
operator-lifecycle-manager-catalog         4.14.34   True        False         False      81m
operator-lifecycle-manager-packageserver   4.14.34   True        False         False      67m
service-ca                                 4.14.34   True        False         False      84m
storage                                    4.14.34   True        False         False      84m
