# Kubernetes Nginx Ingress Controller WAF with ModSecurity

We will install Nginx Ingress Contoller with the values from custom-values.yml, next command deploy the ingress service in ours cluster automatically:
```
helm install nginx-ingress stable/nginx-ingress -f custom-values.yml
```
we already have nginx worked, now we will install a example application to test that Modsecurity works then, we will deploy Jenkins but you can deploy other application.
```
helm install jenkins stable/jenkins
```
Next step we will a ingress manifest to expose Jenkins outside the Kubernetes cluster, in this case I using "jorge.guerrero.com" like host but you can use other domains for example foo.bar.com, we will edit the host file in next step to configure this, you can run this command to create it:
```
kubectl apply -f jenkinsingress.yml
```

We will need the Nginx Ingress Service IP to access to ours service we can get this with the command, we copy this IP, I'm using a load balancer service from Kubernetes service hosted in Google Cloud but you can use other service you only need access to Ingress service.

```
Kubectl get svc
```
I'm using Ubuntu and my host file is in /etc/hots, you should edit your host file depend for you operative system. We will add a new line with the Nginx ingress controller service IP and the domain, in my case jorge.guerrero.com

```
/etc/hosts
23.236.63.214 jorge.guerrero.com
```

Finally we check that Modsecurity works correctly, we will simulate a malicious request:
```
curl -s "http://jorge.guerrero.com/?username='%20or%20'1'%20=%20'"
```
voilà! We WAF works perfectly, Modsecurity block the malicious request and return a 403 error.
```
<html>
<head><title>403 Forbidden</title></head>
<body>
<center><h1>403 Forbidden</h1></center>
<hr><center>nginx</center>
</body>
</html>
```

