# tigera-openshift-aws-nstall


1. Go to https://cloud.redhat.com/openshift/install and select AWS infrastructure provider

2. Select installer provisioned infrastructure

3. Download the Openshift installer
```
mkdir ocp-demo
cd ocp-demo/
curl https://mirror.openshift.com/pub/openshift-v4/clients/ocp/4.3.25/openshift-install-linux.tar.gz \
    -o openshift-install-linux.tar.gz
tar zxvf openshift-install-linux.tar.gz
```

4. Copy or download the pull secret for openshift from the RedHat portal
```
vi pull-secret-oc.json

{"auths":{"cloud.openshift.com":{"auth":"b3BlbnNoaWZ0LXJlbGVhc2UtZGV2K3Bib2dhZXJ0czFkcmtyd2xyOHpqcGVwYnkzYjRpYm1mbXVtdDpGV0VTN1YxWkU3SjBEMDE1Tk1ROU1XTTRVWlBXTjIwTlc3TVZETzNFUlJCSFlPMUFFSURDUEIwSlVTT1BPQzZC","email":"phili....gera.io"}}}
```

5. Install the oc linux client
```
curl https://mirror.openshift.com/pub/openshift-v4/clients/ocp/4.3.25/openshift-client-linux.tar.gz -o openshift-client-linux.tar.gz
tar zxfv openshift-client-linux.tar.gz

###maybe also create a SSH keypair // todo
```

6. Create a cluster install file (update with pull-secret-oc.json, ssh key, region and domain)<br>
   - update the pullSecret section with the information of  pull-secret-oc.json<br>
   - update a ssh key section with your public ssh key<br>
   - update baseDomain (Route53 domain) <br> 
   - update zones and regions<br>
   - update name section
```
vi  install-config.yaml

apiVersion: v1
baseDomain: tigera-labs-emea.io
compute:
- hyperthreading: Enabled
  name: worker
  platform:
    aws:
      zones:
      - eu-west-3a
      rootVolume:
        iops: 4000
        size: 500
        type: io1
      type: m5.xlarge
  replicas: 3
controlPlane:
  hyperthreading: Enabled
  name: master
  platform:
    aws:
      zones:
      - eu-west-3a
      rootVolume:
        iops: 4000
        size: 500
        type: io1
      type: m5.xlarge
  replicas: 3
metadata:
  creationTimestamp: null
  name: philippe-oc43-demo
networking:
  clusterNetwork:
  - cidr: 10.48.0.0/16
    hostPrefix: 24
  machineCIDR: 10.0.0.0/16
  networkType: Calico
  serviceNetwork:
  - 10.49.0.0/16
platform:
  aws:
    region: eu-west-3
pullSecret: '{"auths":{"cloud.openshift.com":{"auth":"b3BlbnNoaWZ0LXJlbGVhc2UtZGV2K3Bib2dhZXJ0czFkcmtyd2xyOHpqcGVwYnkzYjRpYm1mbXVtdDpGV0VTN1YxWkU3SjBEMDE1Tk1ROU1XTTRVWlBXTjIwTlc3TVZETzNFUlJCSFlPMUFFSURDUEIwSlVTT1BPQzZC","email":"...tigera.io"}}}'
sshKey: |
  ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAAB...
```

7. Create the manifests
```
./openshift-install create manifests
```

8. Goto https://docs.tigera.io/getting-started/openshift/installation/ (maybe update folowing code block)
```
curl https://docs.tigera.io/manifests/ocp/crds/01-crd-alertmanager.yaml -o manifests/01-crd-alertmanager.yaml
curl https://docs.tigera.io/manifests/ocp/crds/01-crd-apiserver.yaml -o manifests/01-crd-apiserver.yaml
curl https://docs.tigera.io/manifests/ocp/crds/01-crd-compliance.yaml -o manifests/01-crd-compliance.yaml
curl https://docs.tigera.io/manifests/ocp/crds/01-crd-manager.yaml -o manifests/01-crd-manager.yaml
curl https://docs.tigera.io/manifests/ocp/crds/01-crd-eck-apmserver.yaml -o manifests/01-crd-eck-apmserver.yaml
curl https://docs.tigera.io/manifests/ocp/crds/01-crd-eck-elasticsearch.yaml -o manifests/01-crd-eck-elasticsearch.yaml
curl https://docs.tigera.io/manifests/ocp/crds/01-crd-eck-kibana.yaml -o manifests/01-crd-eck-kibana.yaml
curl https://docs.tigera.io/manifests/ocp/crds/01-crd-eck-trustrelationship.yaml -o manifests/01-crd-eck-trustrelationship.yaml
curl https://docs.tigera.io/manifests/ocp/crds/01-crd-installation.yaml -o manifests/01-crd-installation.yaml
curl https://docs.tigera.io/manifests/ocp/crds/01-crd-intrusiondetection.yaml -o manifests/01-crd-intrusiondetection.yaml
curl https://docs.tigera.io/manifests/ocp/crds/01-crd-logstorage.yaml -o manifests/01-crd-logstorage.yaml
curl https://docs.tigera.io/manifests/ocp/crds/01-crd-logcollector.yaml -o manifests/01-crd-logcollector.yaml
curl https://docs.tigera.io/manifests/ocp/crds/01-crd-prometheusrule.yaml -o manifests/01-crd-prometheusrule.yaml
curl https://docs.tigera.io/manifests/ocp/crds/01-crd-thanosrulers.yaml -o manifests/01-crd-thanosrulers.yaml
curl https://docs.tigera.io/manifests/ocp/crds/01-crd-prometheus.yaml -o manifests/01-crd-prometheus.yaml
curl https://docs.tigera.io/manifests/ocp/crds/01-crd-servicemonitor.yaml -o manifests/01-crd-servicemonitor.yaml
curl https://docs.tigera.io/manifests/ocp/crds/01-crd-tigerastatus.yaml -o manifests/01-crd-tigerastatus.yaml
curl https://docs.tigera.io/manifests/ocp/crds/01-crd-managementclusterconnection.yaml -o manifests/01-crd-managementclusterconnection.yaml
curl https://docs.tigera.io/manifests/ocp/crds/calico/ee-kdd/02-crd-globalalert.yaml -o manifests/02-crd-globalalert.yaml
curl https://docs.tigera.io/manifests/ocp/crds/calico/ee-kdd/02-crd-globalalerttemplate.yaml -o manifests/02-crd-globalalerttemplate.yaml
curl https://docs.tigera.io/manifests/ocp/crds/calico/ee-kdd/02-crd-globalreport.yaml -o manifests/02-crd-globalreport.yaml
curl https://docs.tigera.io/manifests/ocp/crds/calico/ee-kdd/02-crd-globalreporttype.yaml -o manifests/02-crd-globalreporttype.yaml
curl https://docs.tigera.io/manifests/ocp/crds/calico/ee-kdd/02-crd-globalthreatfeed.yaml -o manifests/02-crd-globalthreatfeed.yaml
curl https://docs.tigera.io/manifests/ocp/crds/calico/ee-kdd/02-crd-licensekey.yaml -o manifests/02-crd-licensekey.yaml
curl https://docs.tigera.io/manifests/ocp/crds/calico/ee-kdd/02-crd-managedcluster.yaml -o manifests/02-crd-managedcluster.yaml
curl https://docs.tigera.io/manifests/ocp/crds/calico/ee-kdd/02-crd-remoteclusterconfiguration.yaml -o manifests/02-crd-remoteclusterconfiguration.yaml
curl https://docs.tigera.io/manifests/ocp/crds/calico/ee-kdd/02-crd-stagedglobalnetworkpolicy.yaml -o manifests/02-crd-stagedglobalnetworkpolicy.yaml
curl https://docs.tigera.io/manifests/ocp/crds/calico/ee-kdd/02-crd-stagedkubernetesnetworkpolicy.yaml -o manifests/02-crd-stagedkubernetesnetworkpolicy.yaml
curl https://docs.tigera.io/manifests/ocp/crds/calico/ee-kdd/02-crd-stagednetworkpolicy.yaml -o manifests/02-crd-stagednetworkpolicy.yaml
curl https://docs.tigera.io/manifests/ocp/crds/calico/ee-kdd/02-crd-tier.yaml -o manifests/02-crd-tier.yaml
curl https://docs.tigera.io/manifests/ocp/crds/calico/kdd/02-crd-bgpconfiguration.yaml -o manifests/02-crd-bgpconfiguration.yaml
curl https://docs.tigera.io/manifests/ocp/crds/calico/kdd/02-crd-bgppeer.yaml -o manifests/02-crd-bgppeer.yaml
curl https://docs.tigera.io/manifests/ocp/crds/calico/kdd/02-crd-blockaffinity.yaml -o manifests/02-crd-blockaffinity.yaml
curl https://docs.tigera.io/manifests/ocp/crds/calico/kdd/02-crd-clusterinformation.yaml -o manifests/02-crd-clusterinformation.yaml
curl https://docs.tigera.io/manifests/ocp/crds/calico/kdd/02-crd-felixconfiguration.yaml -o manifests/02-crd-felixconfiguration.yaml
curl https://docs.tigera.io/manifests/ocp/crds/calico/kdd/02-crd-globalnetworkpolicy.yaml -o manifests/02-crd-globalnetworkpolicy.yaml
curl https://docs.tigera.io/manifests/ocp/crds/calico/kdd/02-crd-globalnetworkset.yaml -o manifests/02-crd-globalnetworkset.yaml
curl https://docs.tigera.io/manifests/ocp/crds/calico/kdd/02-crd-hostendpoint.yaml -o manifests/02-crd-hostendpoint.yaml
curl https://docs.tigera.io/manifests/ocp/crds/calico/kdd/02-crd-ipamblock.yaml -o manifests/02-crd-ipamblock.yaml
curl https://docs.tigera.io/manifests/ocp/crds/calico/kdd/02-crd-ipamconfig.yaml -o manifests/02-crd-ipamconfig.yaml
curl https://docs.tigera.io/manifests/ocp/crds/calico/kdd/02-crd-ipamhandle.yaml -o manifests/02-crd-ipamhandle.yaml
curl https://docs.tigera.io/manifests/ocp/crds/calico/kdd/02-crd-ippool.yaml -o manifests/02-crd-ippool.yaml
curl https://docs.tigera.io/manifests/ocp/crds/calico/kdd/02-crd-kubecontrollersconfiguration.yaml -o manifests/02-crd-kubecontrollersconfiguration.yaml
curl https://docs.tigera.io/manifests/ocp/crds/calico/kdd/02-crd-networkpolicy.yaml -o manifests/02-crd-networkpolicy.yaml
curl https://docs.tigera.io/manifests/ocp/crds/calico/kdd/02-crd-networkset.yaml -o manifests/02-crd-networkset.yaml
curl https://docs.tigera.io/manifests/ocp/tigera-operator/00-namespace-tigera-operator.yaml -o manifests/00-namespace-tigera-operator.yaml
curl https://docs.tigera.io/manifests/ocp/tigera-operator/02-rolebinding-tigera-operator.yaml -o manifests/02-rolebinding-tigera-operator.yaml
curl https://docs.tigera.io/manifests/ocp/tigera-operator/02-role-tigera-operator.yaml -o manifests/02-role-tigera-operator.yaml
curl https://docs.tigera.io/manifests/ocp/tigera-operator/02-serviceaccount-tigera-operator.yaml -o manifests/02-serviceaccount-tigera-operator.yaml
curl https://docs.tigera.io/manifests/ocp/tigera-operator/02-configmap-calico-resources.yaml -o manifests/02-configmap-calico-resources.yaml
curl https://docs.tigera.io/manifests/ocp/tigera-operator/02-configmap-tigera-install-script.yaml -o manifests/02-configmap-tigera-install-script.yaml
curl https://docs.tigera.io/manifests/ocp/tigera-operator/02-tigera-operator.yaml -o manifests/02-tigera-operator.yaml
curl https://docs.tigera.io/manifests/ocp/misc/00-namespace-tigera-prometheus.yaml -o manifests/00-namespace-tigera-prometheus.yaml
curl https://docs.tigera.io/manifests/ocp/prometheus-operator/04-clusterrolebinding-prometheus-operator.yaml -o manifests/04-clusterrolebinding-prometheus-operator.yaml
curl https://docs.tigera.io/manifests/ocp/prometheus-operator/04-clusterrolebinding-prometheus.yaml -o manifests/04-clusterrolebinding-prometheus.yaml
curl https://docs.tigera.io/manifests/ocp/prometheus-operator/04-clusterrole-prometheus-operator.yaml -o manifests/04-clusterrole-prometheus-operator.yaml
curl https://docs.tigera.io/manifests/ocp/prometheus-operator/04-clusterrole-prometheus.yaml -o manifests/04-clusterrole-prometheus.yaml
curl https://docs.tigera.io/manifests/ocp/prometheus-operator/04-deployment-prometheus-operator.yaml -o manifests/04-deployment-prometheus-operator.yaml
curl https://docs.tigera.io/manifests/ocp/prometheus-operator/04-serviceaccount-prometheus-operator.yaml -o manifests/04-serviceaccount-prometheus-operator.yaml
curl https://docs.tigera.io/manifests/ocp/prometheus-operator/04-serviceaccount-prometheus.yaml -o manifests/04-serviceaccount-prometheus.yaml
curl https://docs.tigera.io/manifests/ocp/misc/99-alertmanager-secret.yaml -o manifests/99-alertmanager-secret.yaml
curl https://docs.tigera.io/manifests/ocp/misc/99-alertmanager-service.yaml -o manifests/99-alertmanager-service.yaml
curl https://docs.tigera.io/manifests/ocp/misc/99-prometheus-service.yaml -o manifests/99-prometheus-service.yaml
```

```
curl https://docs.tigera.io/manifests/ocp/01-cr-installation.yaml -o manifests/01-cr-installation.yaml
curl https://docs.tigera.io/manifests/ocp/01-cr-apiserver.yaml -o manifests/01-cr-apiserver.yaml
curl https://docs.tigera.io/manifests/ocp/01-cr-manager.yaml -o manifests/01-cr-manager.yaml
curl https://docs.tigera.io/manifests/ocp/01-cr-compliance.yaml -o manifests/01-cr-compliance.yaml
curl https://docs.tigera.io/manifests/ocp/01-cr-intrusiondetection.yaml -o manifests/01-cr-intrusiondetection.yaml
curl https://docs.tigera.io/manifests/ocp/01-cr-alertmanager.yaml -o manifests/01-cr-alertmanager.yaml
curl https://docs.tigera.io/manifests/ocp/01-cr-logstorage.yaml -o manifests/01-cr-logstorage.yaml
curl https://docs.tigera.io/manifests/ocp/01-cr-logcollector.yaml -o manifests/01-cr-logcollector.yaml
curl https://docs.tigera.io/manifests/ocp/01-cr-prometheus.yaml -o manifests/01-cr-prometheus.yaml
curl https://docs.tigera.io/manifests/ocp/01-cr-prometheusrule.yaml -o manifests/01-cr-prometheusrule.yaml
curl https://docs.tigera.io/manifests/ocp/01-cr-servicemonitor.yaml -o manifests/01-cr-servicemonitor.yaml
```

9. Preparing the tigera pull secret
```
###obtain a pull secret for Tigera and store in ./pull-secret-tigera.json 
curl https://docs.tigera.io/manifests/ocp/02-pull-secret.yaml -o manifests/02-pull-secret.yaml
SECRET=$(cat ./pull-secret-tigera.json | tr -d '\n\r\t ' | base64 -w 0)
sed -i "s/SECRET/${SECRET}/" manifests/02-pull-secret.yaml
```

10. Install the cluster
```
./openshift-install create cluster --log-level debug
```
... take some coffee....
```
export KUBECONFIG=/home/ubuntu/ocp-demo/auth/kubeconfig
./oc get nodes
./kubectl get nodes
```

11. Create a storage class
```
vi storage_class_ebs.yaml


apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: tigera-elasticsearch
provisioner: kubernetes.io/aws-ebs
parameters:
  type: gp2
  fsType: ext4
reclaimPolicy: Retain
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
```
```
kubectl apply -f ./storage_class_ebs.yaml
```

12.  Make sure Tigera components are coming up and apply license
```
watch ./oc get tigerastatus
```
```
./oc create -f ./license.yaml
```
```
watch ./oc get tigerastatus
```
... take some more coffee ...


13. Secure calico EE
```
./oc create -f https://docs.tigera.io/manifests/tigera-policies.yaml
```
14. Expose UI
```
vi ui-lb.yaml

kind: Service
apiVersion: v1
metadata:
  name: tigera-manager-external
  namespace: tigera-manager
spec:
  type: LoadBalancer
  selector:
    k8s-app: tigera-manager
  externalTrafficPolicy: Local
  ports:
  - port: 9443
    targetPort: 9443
    protocol: TCP
```
```
kubectl apply -f ./ui-lb.yaml
kubectl get svc -n tigera-manager
```
... have small shot ...
You should be able to connect to the https://xxxxx:9443

15. Create access token
```
kubectl create sa demo
kubectl create clusterrolebinding demo-access --clusterrole tigera-network-admin --serviceaccount default:demo
kubectl get secret $(kubectl get serviceaccount demo -o jsonpath='{range .secrets[*]}{.name}{"\n"}{end}' | grep token) -o go-template='{{.data.token | base64decode}}' && echo
```

You can now login to Tigera EE
You can check access to Openshift UI https://console-openshift-console.apps.xxxxxxxx-oc43-demo.tigera-labs-emea.io  and find pwd ./auth/kubeadmin-password  and usn kubeadmin




16. Cleanup the cluster
```
./openshift-install destroy cluster --log-level debug
```




