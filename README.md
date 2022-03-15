# jenkins-deployment-command-run-on--kubernetes

URL https://computingforgeeks.com/how-to-add-multiple-kubernetes-clusters-to-jenkins/
~~~
root@master:~# cat admin.yaml
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: kube-admin
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: kube-admin
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: kube-admin
    namespace: kube-system

~~~


~~~
pipeline{
agent any
stages {
    stage("List Pods"){
        steps{

withKubeCredentials(kubectlCredentials: [[caCertificate: '', clusterName: 'kubernetes', contextName: 'kubernetes-admin@kubernetes', credentialsId: 'kubernetes', namespace: 'default', serverUrl: 'https://10.10.10.15:6443']]) {
     sh 'curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl"'
        sh 'chmod u+x ./kubectl' 
        sh './kubectl get pod -A'
        sh './kubectl create -f deployment.yaml'
}
}
}
}
}
~~~
