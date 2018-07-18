---
title: Configurer l’entrée avec le cluster Azure Kubernetes Service (AKS)
description: Installez et configurez un contrôleur d’entrée NGINX dans un cluster Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/25/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 9bee80ebbaf0568706428d673d584819b1daa143
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096986"
---
# <a name="https-ingress-on-azure-kubernetes-service-aks"></a>Entrée HTTPS sur Azure Kubernetes Service (AKS)

Un contrôleur d’entrée est un logiciel qui fournit un proxy inversé, un routage du trafic configurable et un arrêt TLS pour les services Kubernetes. Des ressources d’entrée Kubernetes sont utilisées pour configurer les règles d’entrée et les itinéraires des services Kubernetes individuels. En utilisant un contrôleur d’entrée et des règles d’entrée, une seule adresse externe peut être utilisée pour acheminer le trafic vers plusieurs services dans un cluster Kubernetes.

Ce document décrit un exemple de déploiement du [contrôleur d’entrée NGINX][nginx-ingress] dans un cluster Azure Kubernetes Service (AKS). De plus, le projet [cert-manager][cert-manager] est utilisé pour générer et configurer automatiquement des certificats [Let’s Encrypt][lets-encrypt]. Enfin, plusieurs applications sont exécutées dans le cluster AKS, chacune étant accessible par le biais d’une adresse unique.

## <a name="install-an-ingress-controller"></a>Installer un contrôleur d’entrée

Utilisez Helm pour installer le contrôleur d’entrée NGINX. Consultez la [documentation][nginx-ingress] du contrôleur d’entrée NGINX pour plus d’informations sur le déploiement.

Cet exemple installe le contrôleur dans l’espace de noms `kube-system`. Vous pouvez le modifier en définissant l’espace de noms de votre choix. Si le contrôle d’accès en fonction du rôle (RBAC) n’est pas activé sur votre cluster AKS, ajoutez `--set rbac.create=false` à la commande. Pour plus d’informations, voir [nginx-ingress chart][nginx-ingress].

```bash
helm install stable/nginx-ingress --namespace kube-system
```

Pendant l’installation, une adresse IP publique Azure est créée pour le contrôleur d’entrée. Pour obtenir l’adresse IP publique, utilisez la commande kubectl get service. L’assignation de l’adresse IP au service peut prendre un certain temps.

```console
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                       TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
eager-crab-nginx-ingress-controller        LoadBalancer   10.0.182.160   51.145.155.210  80:30920/TCP,443:30426/TCP   20m
eager-crab-nginx-ingress-default-backend   ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

Étant donné qu’aucune règle d’entrée n’a été créée, si vous accédez à l’adresse IP publique, vous êtes redirigé vers la page 404 par défaut des contrôleurs d’entrée NGINX.

![Backend NGINX par défaut](media/ingress/default-back-end.png)

## <a name="configure-dns-name"></a>Configurer un nom DNS

Étant donné que des certificats HTTPS sont utilisés, vous devez configurer un nom de domaine complet pour l’adresse IP des contrôleurs d’entrée. Pour cet exemple, un nom de domaine complet Azure est créé avec Azure CLI. Mettez à jour le script avec l’adresse IP du contrôleur d’entrée et le nom à utiliser dans le nom de domaine complet.

```bash
#!/bin/bash

# Public IP address
IP="51.145.155.210"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get the resource-id of the public ip
PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)

# Update public ip address with dns name
az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
```

Le contrôleur d’entrée doit désormais être accessible par le biais du nom de domaine complet.

## <a name="install-cert-manager"></a>Installer cert-manager

Le contrôleur d’entrée NGINX prend en charge l’arrêt TLS. Parmi les différentes façons de récupérer et de configurer des certificats pour le protocole HTTPS, ce document décrit [cert-manager][cert-manager], qui fournit la fonctionnalité de génération et de gestion automatiques de certificats [Let’s Encrypt][lets-encrypt].

Pour installer le contrôleur cert-manager, utilisez la commande d’installation Helm suivante.

```bash
helm install stable/cert-manager --set ingressShim.defaultIssuerName=letsencrypt-prod --set ingressShim.defaultIssuerKind=ClusterIssuer
```

Si RBAC n’est pas activé sur votre cluster, utilisez cette commande.

```bash
helm install stable/cert-manager \
  --set ingressShim.defaultIssuerName=letsencrypt-prod \
  --set ingressShim.defaultIssuerKind=ClusterIssuer \
  --set rbac.create=false \
  --set serviceAccount.create=false
```

Pour plus d’informations sur la configuration cert-manager, voir le [projet cert-manager][cert-manager].

## <a name="create-ca-cluster-issuer"></a>Créer un émetteur de cluster d’autorité de certification

Cert-manager a besoin d’une ressource [Issuer][cert-manager-issuer] ou [ClusterIssuer][cert-manager-cluster-issuer] pour pouvoir émettre des certificats. Ces ressources sont identiques du point de vue des fonctionnalités, mais `Issuer` s’applique à un seul espace de noms, tandis que `ClusterIssuer` couvre tous les espaces de noms. Pour plus d’informations, voir la documentation de [l’émetteur cert-manager][cert-manager-issuer].

Créez un émetteur de certificat avec le manifeste suivant. Mettez à jour l’adresse e-mail avec une adresse valide de votre organisation.

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-prod
    http01: {}
```

## <a name="create-certificate-object"></a>Créer un objet certificat

Il faut ensuite créer une ressource de certificat. Elle définit le certificat X.509 souhaité. Pour plus d’informations, voir [Certificats cert-manager][cert-manager-certificates].

Créez la ressource de certificat avec le manifeste suivant.

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: Certificate
metadata:
  name: tls-secret
spec:
  secretName: tls-secret
  dnsNames:
  - demo-aks-ingress.eastus.cloudapp.azure.com
  acme:
    config:
    - http01:
        ingressClass: nginx
      domains:
      - demo-aks-ingress.eastus.cloudapp.azure.com
  issuerRef:
    name: letsencrypt-prod
    kind: ClusterIssuer
```

## <a name="run-application"></a>Exécuter l’application

À ce stade, un contrôleur d’entrée et une solution de gestion de certificats ont été configurés. Exécutez maintenant quelques applications dans votre cluster AKS.

Pour cet exemple, Helm est utilisé pour exécuter plusieurs instances d’une simple application Hello World.

Avant d’exécuter l’application, ajoutez le dépôt Helm des exemples Azure sur votre système de développement.

```bash
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Exécutez le graphique AKS Hello World avec la commande suivante :

```bash
helm install azure-samples/aks-helloworld
```

Installez maintenant une deuxième instance de l’application Hello World.

Pour la deuxième instance, spécifiez un nouveau titre afin que les deux applications soient visuellement distinctes. Vous devez également spécifier un nom de service unique. Ces configurations peuvent être consultées dans la commande suivante.

```bash
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
```

## <a name="create-ingress-route"></a>Créer un itinéraire d’entrée

Les deux applications sont maintenant en cours d’exécution sur votre cluster Kubernetes ; toutefois, elles ont été configurées avec un service de type `ClusterIP`. Ainsi, ces applications ne sont pas accessibles à partir d’Internet. Afin de les rendre disponibles, créez une ressource d’entrée Kubernetes. La ressource d’entrée configure les règles qui acheminent le trafic vers l’une des deux applications.

Créez un fichier nommé `hello-world-ingress.yaml` et copiez-y le YAML suivant.

Notez que le trafic vers l’adresse `https://demo-aks-ingress.eastus.cloudapp.azure.com/` est acheminé vers le service nommé `aks-helloworld`. Le trafic vers l’adresse `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` est acheminé vers le service `ingress-demo`.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    certmanager.k8s.io/cluster-issuer: letsencrypt-prod
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  tls:
  - hosts:
    - demo-aks-ingress.eastus.cloudapp.azure.com
    secretName: tls-secret
  rules:
  - host: demo-aks-ingress.eastus.cloudapp.azure.com
    http:
      paths:
      - path: /
        backend:
          serviceName: aks-helloworld
          servicePort: 80
      - path: /hello-world-two
        backend:
          serviceName: ingress-demo
          servicePort: 80
```

Créez la ressource d’entrée avec la commande `kubectl apply`.

```console
kubectl apply -f hello-world-ingress.yaml
```

## <a name="test-the-ingress-configuration"></a>Tester la configuration d’entrée

Recherchez le nom de domaine complet de votre contrôleur d’entrée Kubernetes. Vous devez voir l’application Hello World.

![Exemple d’application numéro un](media/ingress/app-one.png)

Maintenant, accédez au nom de domaine complet du contrôleur d’entrée avec le chemin `/hello-world-two`. Vous devez voir l’application Hello World avec le titre personnalisé.

![Exemple d’application numéro deux](media/ingress/app-two.png)

Notez également que la connexion est chiffrée et qu’un certificat émis par Let’s Encrypt est utilisé.

![Certificat Lets Encrypt](media/ingress/certificate.png)

## <a name="next-steps"></a>Étapes suivantes

Découvrez le logiciel mentionné dans ce document.

- [Interface CLI Helm][helm-cli]
- [Contrôleur d’entrée NGINX][nginx-ingress]
- [cert-manager][cert-manager]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm#install-helm-cli
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
