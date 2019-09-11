``` bash
# create role tiller
kubectl apply -f tiller-rbac.yaml

# install tiller
helm init --service-account tiller

# verify it worked
helm version

# install jenkins
helm install --name aks-jenkins-rb stable/jenkins

# get admin password
printf $(kubectl get secret --namespace default aks-jenkins-rb -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo
## OWm3cxPU0G

# Ensure external ip is built
kubectl get svc --namespace default -w aks-jenkins-rb

# get the login url
export SERVICE_IP=$(kubectl get svc --namespace default aks-jenkins-rb --template "{{ range (index .status.loadBalancer.ingress 0) }}{{ . }}{{ end }}")
echo http://$SERVICE_IP:8080/login
## http://13.66.225.183:8080/login

# login to the url using the username: admin
# and the password retrieved in the earlier step. 

# upgrade this release to use blue ocean plugin
helm upgrade -f jenkins_values.yaml aks-jenkins-rb stable/jenkins

# get the admin password again
printf $(kubectl get secret --namespace default aks-jenkins-rb -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo

# see the blue ocean plugin, with errors, fix jenkins_values dependency hell
```