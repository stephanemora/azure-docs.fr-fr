---
title: Créer une entrée HTTPS avec le cluster Azure Kubernetes Service (AKS)
description: Découvrez comment installer et configurer un contrôleur d’entrée NGINX qui utilise Let’s Encrypt pour la génération automatique de certificats SSL dans un cluster Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/30/2018
ms.author: iainfou
ms.openlocfilehash: cb441aeab8f6f2cfbaa099ee17a3af9e767fc218
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56235697"
---
# <a name="create-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>Créer un contrôleur d’entrée HTTPS dans Azure Kubernetes Service (AKS)

Un contrôleur d’entrée est un logiciel qui fournit un proxy inversé, un routage du trafic configurable et un arrêt TLS pour les services Kubernetes. Des ressources d’entrée Kubernetes sont utilisées pour configurer les règles d’entrée et les itinéraires des services Kubernetes individuels. En utilisant un contrôleur d’entrée et des règles d’entrée, une seule adresse IP peut être utilisée pour router le trafic vers plusieurs services dans un cluster Kubernetes.

Cet article montre comment déployer le [contrôleur d’entrée NGINX][nginx-ingress] dans un cluster Azure Kubernetes Service (AKS). Le projet [cert-manager][cert-manager] est utilisé pour générer et configurer automatiquement les certificats [Let’s Encrypt][lets-encrypt]. Enfin, plusieurs applications sont exécutées dans le cluster AKS, chacune étant accessible via une seule adresse IP.

Vous pouvez également :

- [Créer un contrôleur d’entrée de base avec une connectivité réseau externe][aks-ingress-basic]
- [Activer le module complémentaire de routage d’application HTTP][aks-http-app-routing]
- [Créer un contrôleur d’entrée qui utilise un réseau privé interne et une adresse IP][aks-ingress-internal]
- [Créer un contrôleur d’entrée qui utilise vos propres certificats TLS][aks-ingress-own-tls]
- [Créer un contrôleur d’entrée qui utilise Let’s Encrypt pour générer automatiquement des certificats TLS avec une adresse IP publique statique][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Avant de commencer

Cet article utilise Helm pour installer le contrôleur d’entrée NGINX, cert-manager et un exemple d’application web. Helm doit être initialisé dans votre cluster AKS et utiliser un compte de service pour Tiller. Vérifiez que vous utilisez la dernière version de Helm. Pour obtenir des instructions de mise à niveau, consultez la [documentation d’installation de Helm][helm-install]. Pour plus d’informations sur la configuration et l’utilisation de Helm, consultez [Installer des applications avec Helm dans Azure Kubernetes Service (AKS)][use-helm].

Cet article nécessite également que vous exécutiez Azure CLI version 2.0.41 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installer Azure CLI 2.0][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Créer un contrôleur d’entrée

Pour créer le contrôleur d’entrée, utilisez `Helm` pour installer *nginx-ingress*. Pour renforcer la redondance, deux réplicas des contrôleurs d’entrée NGINX sont déployés avec le paramètre `--set controller.replicaCount`. Pour tirer pleinement parti de l’exécution de réplicas des contrôleurs d’entrée, vérifiez que votre cluster AKS comprend plusieurs nœuds.

> [!TIP]
> L’exemple suivant installe le contrôleur d’entrée dans l’espace de noms `kube-system`. Si vous le souhaitez, vous pouvez spécifier un espace de noms différent pour votre propre environnement. Si le contrôle d’accès en fonction du rôle (RBAC) n’est pas activé sur votre cluster AKS, ajoutez `--set rbac.create=false` aux commandes.

```console
helm install stable/nginx-ingress --namespace kube-system --set controller.replicaCount=2
```

Pendant l’installation, une adresse IP publique Azure est créée pour le contrôleur d’entrée. Cette adresse IP publique est statique pour la durée de vie du contrôleur d’entrée. Si vous supprimez le contrôleur d’entrée, l’attribution d’adresse IP publique est perdue. Si vous créez ensuite un contrôleur d’entrée supplémentaires, une nouvelle adresse IP publique est attribuée. Si vous souhaitez conserver l’utilisation de l’adresse IP publique, vous pouvez au lieu de cela [créer un contrôleur d’entrée avec une adresse IP publique statique][aks-ingress-static-tls].

Pour obtenir l’adresse IP publique, utilisez la commande `kubectl get service`. L’affectation de l’adresse IP au service peut prendre quelques minutes.

```
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                       TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
eager-crab-nginx-ingress-controller        LoadBalancer   10.0.182.160   51.145.155.210  80:30920/TCP,443:30426/TCP   20m
eager-crab-nginx-ingress-default-backend   ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

Aucune règle d’entrée n’a encore été créée. Si vous accédez à l’adresse IP publique, la page 404 par défaut du contrôleur d’entrée NGINX s’affiche.

## <a name="configure-a-dns-name"></a>Configurer un nom DNS

Pour que les certificats HTTPS fonctionnent correctement, configurez un nom de domaine complet pour l’adresse IP du contrôleur d’entrée. Mettez à jour le script suivant avec l’adresse IP du contrôleur d’entrée et le nom unique à utiliser dans le nom de domaine complet :

```console
#!/bin/bash

# Public IP address of your ingress controller
IP="51.145.155.210"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get the resource-id of the public ip
PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)

# Update public ip address with DNS name
az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
```

Le contrôleur d’entrée est désormais accessible par le biais du nom de domaine complet.

## <a name="install-cert-manager"></a>Installer cert-manager

Le contrôleur d’entrée NGINX prend en charge l’arrêt TLS. Il existe plusieurs façons de récupérer et de configurer des certificats pour le protocole HTTPS. Cet article montre comment utiliser [cert-manager][cert-manager], qui permet de générer et de gérer automatiquement des certificats [Let’s Encrypt][lets-encrypt].

> [!NOTE]
> Cet article utilise l’environnement `staging` pour Let’s Encrypt. Dans les déploiements de production, utilisez `letsencrypt-prod` et `https://acme-v02.api.letsencrypt.org/directory` dans les définitions de ressources et lorsque vous installez le graphique Helm.

Pour installer le contrôleur cert-manager dans un cluster où RBAC est activé, utilisez la commande `helm install` suivante :

```console
kubectl label namespace kube-system certmanager.k8s.io/disable-validation=true

kubectl apply \
    -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.6/deploy/manifests/00-crds.yaml
    
helm install stable/cert-manager \
    --namespace kube-system \
    --set ingressShim.defaultIssuerName=letsencrypt-staging \
    --set ingressShim.defaultIssuerKind=ClusterIssuer \
    --version v0.6.0
```

Si RBAC n’est pas activé dans votre cluster, utilisez la commande suivante :

```console
kubectl label namespace kube-system certmanager.k8s.io/disable-validation=true

kubectl apply \
    -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.6/deploy/manifests/00-crds.yaml
    
helm install stable/cert-manager \
    --namespace kube-system \
    --set ingressShim.defaultIssuerName=letsencrypt-staging \
    --set ingressShim.defaultIssuerKind=ClusterIssuer \
    --set rbac.create=false \
    --set serviceAccount.create=false \
    --version v0.6.0
```

Pour plus d’informations sur la configuration cert-manager, voir le [projet cert-manager][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>Créer un émetteur de cluster d’autorité de certification

Cert-manager a besoin d’une ressource [Issuer][cert-manager-issuer] ou [ClusterIssuer][cert-manager-cluster-issuer] pour pouvoir émettre des certificats. Ces ressources Kubernetes sont identiques du point de vue des fonctionnalités, mais `Issuer` s’applique à un seul espace de noms, tandis que `ClusterIssuer` couvre tous les espaces de noms. Pour plus d’informations, voir la documentation de [l’émetteur cert-manager][cert-manager-issuer].

Créez un émetteur de cluster, tel que `cluster-issuer.yaml`, avec l’exemple de manifeste suivant. Remplacez l’adresse e-mail par une adresse valide de votre organisation :

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-staging
    http01: {}
```

Pour créer l’émetteur, utilisez la commande `kubectl apply -f cluster-issuer.yaml`.

```
$ kubectl apply -f cluster-issuer.yaml

clusterissuer.certmanager.k8s.io/letsencrypt-staging created
```

## <a name="run-demo-applications"></a>Exécuter des applications de démonstration

Un contrôleur d’entrée et une solution de gestion de certificats ont été configurés. Maintenant, nous allons exécuter deux applications de démonstration dans le cluster AKS. Pour cet exemple, Helm est utilisé pour déployer deux instances d’une simple application « Hello World ».

Avant d’installer les exemples de graphiques Helm, ajoutez le référentiel d’exemples Azure à votre environnement Helm :

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Créez la première application de démonstration à partir d’un graphique Helm avec la commande suivante :

```console
helm install azure-samples/aks-helloworld
```

Installez maintenant une deuxième instance de l’application de démonstration. Pour la deuxième instance, spécifiez un nouveau titre de manière à pouvoir distinguer visuellement les deux applications. Vous devez également spécifier un nom de service unique :

```console
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Créer un itinéraire d’entrée

Les deux applications sont maintenant exécutées dans votre cluster Kubernetes. Toutefois, elles ont été configurées avec un service de type `ClusterIP`. Ainsi, ces applications ne sont pas accessibles à partir d’Internet. Pour les rendre disponibles publiquement, créez une ressource d’entrée Kubernetes. La ressource d’entrée configure les règles qui acheminent le trafic vers l’une des deux applications.

Dans l’exemple suivant, le trafic vers l’adresse `https://demo-aks-ingress.eastus.cloudapp.azure.com/` est acheminé vers le service nommé `aks-helloworld`. Le trafic vers l’adresse `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` est acheminé vers le service `ingress-demo`. Mettez à jour les valeurs de *hosts* et de *host* avec le nom DNS que vous avez créé à l’étape précédente.

Créez un fichier nommé `hello-world-ingress.yaml` et copiez-y l’exemple de code YAML suivant.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    certmanager.k8s.io/cluster-issuer: letsencrypt-staging
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

Créez la ressource d’entrée avec la commande `kubectl apply -f hello-world-ingress.yaml`.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="create-a-certificate-object"></a>Créer un objet certificat

Il faut ensuite créer une ressource de certificat. Elle définit le certificat X.509 souhaité. Pour plus d’informations, consultez [Certificats cert-manager][cert-manager-certificates].

Cert-manager a probablement créé automatiquement un objet de certificat à l’aide d’ingress-shim ; le certificat est automatiquement déployé avec cert-manager depuis la version 0.2.2. Pour plus d’informations, consultez la [documentation d’ingress-shim][ingress-shim].

Pour vérifier que le certificat a bien été créé, utilisez la commande `kubectl describe certificate tls-secret`.

Si le certificat a été émis, vous obtenez une sortie similaire à celle-ci :
```
Type    Reason          Age   From          Message
----    ------          ----  ----          -------
  Normal  CreateOrder     11m   cert-manager  Created new ACME order, attempting validation...
  Normal  DomainVerified  10m   cert-manager  Domain "demo-aks-ingress.eastus.cloudapp.azure.com" verified with "http-01" validation
  Normal  IssueCert       10m   cert-manager  Issuing certificate...
  Normal  CertObtained    10m   cert-manager  Obtained certificate from ACME server
  Normal  CertIssued      10m   cert-manager  Certificate issued successfully
```

Si vous devez créer une ressource de certificat supplémentaire, vous pouvez le faire avec l’exemple de manifeste suivant. Mettez à jour les valeurs de *dnsNames* et de *domains* avec le nom DNS que vous avez créé à l’étape précédente. Si vous utilisez un contrôleur d’entrée interne uniquement, spécifiez le nom DNS interne pour votre service.

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
    name: letsencrypt-staging
    kind: ClusterIssuer
```

Pour créer la ressource de certificat, utilisez la commande `kubectl apply -f certificates.yaml`.

```
$ kubectl apply -f certificates.yaml

certificate.certmanager.k8s.io/tls-secret created
```

## <a name="test-the-ingress-configuration"></a>Tester la configuration d’entrée

Ouvrez un navigateur web pour accéder au nom de domaine complet de votre contrôleur d’entrée Kubernetes, par exemple, *https://demo-aks-ingress.eastus.cloudapp.azure.com*.

Comme ces exemples utilisent `letsencrypt-staging`, le certificat SSL émis n’est pas approuvé par le navigateur. Acceptez l’avertissement pour accéder à votre application. Les informations de certificat montrent que le certificat *Fake LE Intermediate X1* est émis par Let’s Encrypt. Ce faux certificat indique que `cert-manager` a correctement traité la demande et a reçu un certificat du fournisseur :

![Certificat intermédiaire Let’s Encrypt](media/ingress/staging-certificate.png)

Lorsque vous modifiez Let’s Encrypt pour utiliser `prod` plutôt que `staging`, un certificat approuvé émis par Let’s Encrypt est utilisé, comme indiqué dans l’exemple suivant :

![Certificat Let’s Encrypt](media/ingress/certificate.png)

L’application de démonstration s’affiche dans le navigateur web :

![Exemple d’application numéro un](media/ingress/app-one.png)

Maintenant, ajoutez le chemin */hello-world-two* au nom de domaine complet, par exemple, *https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two*. La deuxième application de démonstration portant le titre personnalisé s’affiche :

![Exemple d’application numéro deux](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Cet article vous a montré comment utiliser Helm pour installer les composants d’entrée, les certificats et les exemples d’applications. Quand vous déployez un graphique Helm, une série de ressources Kubernetes est créée. Ces ressources incluent des pods, des déploiements et des services. Pour effectuer le nettoyage, commencez par supprimer les ressources de certificat :

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

À présent, listez les versions de Helm avec la commande `helm list`. Recherchez les graphiques nommés *nginx-ingress*, *cert-manager* et *aks-helloworld*, comme illustré dans l’exemple de sortie suivant :

```
$ helm list

NAME                    REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
billowing-kitten        1           Tue Oct 16 17:24:05 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
loitering-waterbuffalo  1           Tue Oct 16 17:26:16 2018    DEPLOYED    cert-manager-v0.3.4     v0.3.2      kube-system
flabby-deer             1           Tue Oct 16 17:27:06 2018    DEPLOYED    aks-helloworld-0.1.0                default
linting-echidna         1           Tue Oct 16 17:27:02 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

Supprimez les versions avec la commande `helm delete`. L’exemple suivant supprime le déploiement d’entrée NGINX, le gestionnaire de certificats et les deux exemples d’applications AKS « hello world ».

```
$ helm delete billowing-kitten loitering-waterbuffalo flabby-deer linting-echidna

release "billowing-kitten" deleted
release "loitering-waterbuffalo" deleted
release "flabby-deer" deleted
release "linting-echidna" deleted
```

Ensuite, supprimez le référentiel Helm pour l’application AKS « hello world » :

```console
helm repo remove azure-samples
```

Enfin, supprimez la route d’entrée qui a dirigé le trafic vers les exemples d’applications :

```console
kubectl delete -f hello-world-ingress.yaml
```

## <a name="next-steps"></a>Étapes suivantes

Cet article a mentionné certains composants qui n’appartiennent pas à AKS. Pour plus d’informations sur ces composants, consultez les pages projet suivantes :

- [Interface CLI Helm][helm-cli]
- [Contrôleur d’entrée NGINX][nginx-ingress]
- [cert-manager][cert-manager]

Vous pouvez également :

- [Créer un contrôleur d’entrée de base avec une connectivité réseau externe][aks-ingress-basic]
- [Activer le module complémentaire de routage d’application HTTP][aks-http-app-routing]
- [Créer un contrôleur d’entrée qui utilise un réseau privé interne et une adresse IP][aks-ingress-internal]
- [Créer un contrôleur d’entrée qui utilise vos propres certificats TLS][aks-ingress-own-tls]
- [Créer un contrôleur d’entrée qui utilise Let’s Encrypt pour générer automatiquement des certificats TLS avec une adresse IP publique statique][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[ingress-shim]: http://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
