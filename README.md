# k8s-litespeed


### 1. Install LS Ingress 

- Adding a Licence

```
kubectl create secret generic -n ls-k8s-webadc ls-k8s-webadc --from-file=trial=./trial.key
```

- Installing the Chart

```
helm repo add ls-k8s-webadc https://litespeedtech.github.io/helm-chart/
helm repo update
helm install ls-k8s-webadc ls-k8s-webadc/ls-k8s-webadc -n ls-k8s-webadc
kubectl get --namespace ls-k8s-webadc svc -w ls-k8s-webadc
```

### 2. Cert Manager

```
helm repo add jetstack https://charts.jetstack.io
helm repo update
helm install \
cert-manager jetstack/cert-manager \
 --namespace cert-manager \
 --create-namespace \
 --version v1.8.0 \
 --set installCRDs=true

kubectl get pods --namespace cert-manager
```


# Ols


sudo apt update

sudo wget -O - https://rpms.litespeedtech.com/debian/enable_lst_debian_repo.sh | sudo bash

sudo apt install openlitespeed lsphp82 lsphp82 lsphp82-mysql lsphp82-common lsphp82-opcache lsphp82-intl lsphp82-curl lsphp82-imap lsphp82-ldap lsphp82-redis lsphp82-memcached lsphp81-ioncube 

sudo /usr/local/lsws/admin/misc/admpass.sh



admin:$1$SfykdwIo$iCowJhD9L06l4NT7oBM3O.


/usr/local/lsws/conf/vhosts/

/usr/local/lsws/admin/conf/htpasswd

/usr/local/lsws/lsphp82/etc/php/8.2/litespeed/php.ini