---
title: Créer une entrée HTTPS avec le cluster Azure Kubernetes Service (AKS)
description: Découvrez comment installer et configurer un contrôleur d’entrée NGINX qui utilise Let’s Encrypt pour la génération automatique de certificats TLS dans un cluster Azure Kubernetes Service (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: mlearned
ms.openlocfilehash: 30f25ad9152bc722b54a834ef0ed037ac1666014
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67615286"
---
# <a name="create-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>Créer un contrôleur d’entrée HTTPS dans Azure Kubernetes Service (AKS)

Un contrôleur d’entrée est un logiciel qui fournit un proxy inversé, un routage du trafic configurable et un arrêt TLS pour les services Kubernetes. Des ressources d’entrée Kubernetes sont utilisées pour configurer les règles d’entrée et les itinéraires des services Kubernetes individuels. En utilisant un contrôleur d’entrée et des règles d’entrée, une seule adresse IP peut être utilisée pour router le trafic vers plusieurs services dans un cluster Kubernetes.

Cet article explique comment déployer le [Contrôleur d’entrée NGINX][nginx-ingress] in an Azure Kubernetes Service (AKS) cluster. The [cert-manager][cert-manager] est utilisé pour générer et configurer automatiquement des certificats [Let’s Encrypt][lets-encrypt]. Enfin, plusieurs applications sont exécutées dans le cluster AKS, chacune étant accessible via une seule adresse IP. Vous pouvez également :

[Créer un contrôleur d’entrée dans Azure Kubernetes Service (AKS)][aks-ingress-basic]

- [Routage d’applications HTTP][aks-http-app-routing]
- [Create an ingress controller to an internal virtual network in Azure Kubernetes Service (AKS)][aks-ingress-internal] (Créer un contrôleur d’entrée pour un réseau virtuel interne dans Azure Kubernetes Service (AKS))
- [Créer un contrôleur d’entrée qui utilise vos propres certificats TLS][aks-ingress-own-tls]
- [Créer un contrôleur d’entrée qui utilise Let’s Encrypt pour générer automatiquement des certificats TLS avec une adresse IP publique statique][aks-ingress-static-tls]
- Avant de commencer

## <a name="before-you-begin"></a>Cet article suppose que vous avez un cluster AKS existant.

Si vous avez besoin d’un cluster AKS, consultez le guide de démarrage rapide d’AKS [avec Azure CLI][aks-quickstart-cli] or [using the Azure portal][aks-quickstart-portal]. Cet article utilise Helm pour installer le contrôleur d’entrée NGINX, cert-manager et un exemple d’application web.

Helm doit être initialisé dans votre cluster AKS et utiliser un compte de service pour Tiller. Vérifiez que vous utilisez la dernière version de Helm. Pour obtenir des instructions de mise à niveau, consultez la [documentation d’installation de Helm][helm-install]. For more information on configuring and using Helm, see [Install applications with Helm in Azure Kubernetes Service (AKS)][use-helm]. Pour les besoins de cet article, vous devez également exécuter Azure CLI version 2.0.64 ou ultérieure.

Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI][azure-cli-install]. Créer un contrôleur d’entrée

## <a name="create-an-ingress-controller"></a>Pour créer le contrôleur d’entrée, utilisez `Helm` pour installer *nginx-ingress*.

Pour renforcer la redondance, deux réplicas des contrôleurs d’entrée NGINX sont déployés avec le paramètre `--set controller.replicaCount`. Pour tirer pleinement parti de l’exécution de réplicas des contrôleurs d’entrée, vérifiez que votre cluster AKS comprend plusieurs nœuds. Le contrôleur d’entrée doit également être planifié sur un nœud Linux.

Les nœuds Windows Server (actuellement en préversion dans AKS) ne doivent pas exécuter le contrôleur d’entrée. Un sélecteur de nœud est spécifié en utilisant le paramètre `--set nodeSelector` pour que le planificateur Kubernetes exécute le contrôleur d’entrée NGINX sur un nœud Linux. L’exemple suivant crée un espace de noms Kubernetes pour les ressources d’entrée *ingress-basic*.

> [!TIP]
> Spécifiez un espace de noms de votre propre environnement, si besoin. Si le contrôle d’accès en fonction du rôle (RBAC) n’est pas activé sur votre cluster AKS, ajoutez `--set rbac.create=false` aux commandes Helm. Si vous souhaitez activer la [préservation de l’adresse IP source du client][client-source-ip] pour les requêtes aux conteneurs de votre cluster, ajoutez `--set controller.service.externalTrafficPolicy=Local` à la commande d’installation Helm.

> [!TIP]
> L’IP source du client est stockée dans l’en-tête de la requête sous *X-Forwarded-For*. Lors de l’utilisation d’un contrôleur d’entrée pour lequel la conservation de l’adresse IP source du client est activée, le protocole SSL direct ne fonctionnera pas. Pendant l’installation, une adresse IP publique Azure est créée pour le contrôleur d’entrée.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Cette adresse IP publique est statique pour la durée de vie du contrôleur d’entrée. Si vous supprimez le contrôleur d’entrée, l’attribution d’adresse IP publique est perdue. Si vous créez ensuite un contrôleur d’entrée supplémentaires, une nouvelle adresse IP publique est attribuée. Si vous souhaitez conserver l’utilisation de l’adresse IP publique, vous pouvez au lieu de cela [créer un contrôleur d’entrée avec une adresse IP publique statique][aks-ingress-static-tls]. Pour obtenir l’adresse IP publique, utilisez la commande `kubectl get service`.

L’affectation de l’adresse IP au service peut prendre quelques minutes. Aucune règle d’entrée n’a encore été créée.

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                             TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
billowing-kitten-nginx-ingress-controller        LoadBalancer   10.0.182.160   51.145.155.210  80:30920/TCP,443:30426/TCP   20m
billowing-kitten-nginx-ingress-default-backend   ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

Si vous accédez à l’adresse IP publique, la page 404 par défaut du contrôleur d’entrée NGINX s’affiche. Configurer un nom DNS

## <a name="configure-a-dns-name"></a>Pour que les certificats HTTPS fonctionnent correctement, configurez un nom de domaine complet pour l’adresse IP du contrôleur d’entrée.

Mettez à jour le script suivant avec l’adresse IP du contrôleur d’entrée et le nom unique à utiliser dans le nom de domaine complet : Le contrôleur d’entrée est désormais accessible par le biais du nom de domaine complet.

```azurecli-interactive
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

Installer cert-manager

## <a name="install-cert-manager"></a>Le contrôleur d’entrée NGINX prend en charge l’arrêt TLS.

Il existe plusieurs façons de récupérer et de configurer des certificats pour le protocole HTTPS. Cet article montre comment utiliser [cert-manager][cert-manager], which provides automatic [Lets Encrypt][lets-encrypt], qui permet de générer et de gérer des certificats. Cet article utilise l’environnement `staging` pour Let’s Encrypt.

> [!NOTE]
> Dans les déploiements de production, utilisez `letsencrypt-prod` et `https://acme-v02.api.letsencrypt.org/directory` dans les définitions de ressources et lorsque vous installez le graphique Helm. Pour installer le contrôleur cert-manager dans un cluster où RBAC est activé, utilisez la commande `helm install` suivante :

Pour plus d’informations sur la configuration cert-manager, voir le [projet cert-manager][cert-manager].

```console
# Install the CustomResourceDefinition resources separately
kubectl apply -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.8/deploy/manifests/00-crds.yaml

# Create the namespace for cert-manager
kubectl create namespace cert-manager

# Label the cert-manager namespace to disable resource validation
kubectl label namespace cert-manager certmanager.k8s.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install \
  --name cert-manager \
  --namespace cert-manager \
  --version v0.8.0 \
  jetstack/cert-manager
```

Créer un émetteur de cluster d’autorité de certification

## <a name="create-a-ca-cluster-issuer"></a>Cert-manager a besoin d’une ressource [Issuer][cert-manager-issuer] or [ClusterIssuer][cert-manager-cluster-issuer] pour pouvoir émettre des certificats.

Ces ressources Kubernetes sont identiques du point de vue des fonctionnalités, mais `Issuer` s’applique à un seul espace de noms, tandis que `ClusterIssuer` couvre tous les espaces de noms. Pour plus d’informations, voir la documentation de [l’émetteur cert-manager][cert-manager-issuer]. Créez un émetteur de cluster, tel que `cluster-issuer.yaml`, avec l’exemple de manifeste suivant.

Remplacez l’adresse e-mail par une adresse valide de votre organisation : Pour créer l’émetteur, utilisez la commande `kubectl apply -f cluster-issuer.yaml`.

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
  namespace: ingress-basic
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-staging
    http01: {}
```

Exécuter des applications de démonstration

```
$ kubectl apply -f cluster-issuer.yaml

clusterissuer.certmanager.k8s.io/letsencrypt-staging created
```

## <a name="run-demo-applications"></a>Un contrôleur d’entrée et une solution de gestion de certificats ont été configurés.

Maintenant, nous allons exécuter deux applications de démonstration dans le cluster AKS. Pour cet exemple, Helm est utilisé pour déployer deux instances d’une simple application « Hello World ». Avant d’installer les exemples de graphiques Helm, ajoutez le référentiel d’exemples Azure à votre environnement Helm :

Créez la première application de démonstration à partir d’un graphique Helm avec la commande suivante :

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Installez maintenant une deuxième instance de l’application de démonstration.

```console
helm install azure-samples/aks-helloworld --namespace ingress-basic
```

Pour la deuxième instance, spécifiez un nouveau titre de manière à pouvoir distinguer visuellement les deux applications. Vous devez également spécifier un nom de service unique : Créer un itinéraire d’entrée

```console
helm install azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Les deux applications sont maintenant exécutées dans votre cluster Kubernetes. Toutefois, elles ont été configurées avec un service de type `ClusterIP`.

Ainsi, ces applications ne sont pas accessibles à partir d’Internet. Pour les rendre disponibles publiquement, créez une ressource d’entrée Kubernetes. La ressource d’entrée configure les règles qui acheminent le trafic vers l’une des deux applications. Dans l’exemple suivant, le trafic vers l’adresse `https://demo-aks-ingress.eastus.cloudapp.azure.com/` est acheminé vers le service nommé `aks-helloworld`.

Le trafic vers l’adresse `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` est acheminé vers le service `ingress-demo`. Mettez à jour les valeurs de *hosts* et de *host* avec le nom DNS que vous avez créé à l’étape précédente. Créez un fichier nommé `hello-world-ingress.yaml` et copiez-y l’exemple de code YAML suivant.

Créez la ressource d’entrée avec la commande `kubectl apply -f hello-world-ingress.yaml`.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    certmanager.k8s.io/cluster-issuer: letsencrypt-staging
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  tls:
  - hosts:
    - demo-aks-ingress.eastus.cloudapp.azure.com
    secretName: tls-secret
  rules:
  - host: demo-aks-ingress.eastus.cloudapp.azure.com
    http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
      - backend:
          serviceName: ingress-demo
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
```

Créer un objet certificat

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="create-a-certificate-object"></a>Il faut ensuite créer une ressource de certificat.

Elle définit le certificat X.509 souhaité. Pour plus d’informations, consultez [Certificats cert-manager][cert-manager-certificates]. Cert-manager a probablement créé automatiquement un objet de certificat à l’aide d’ingress-shim ; le certificat est automatiquement déployé avec cert-manager depuis la version 0.2.2.

Pour plus d’informations, consultez la [documentation d’ingress-shim][ingress-shim]. Pour vérifier que le certificat a bien été créé, utilisez la commande `kubectl describe certificate tls-secret --namespace ingress-basic`.

Si le certificat a été émis, vous obtenez une sortie similaire à celle-ci :

Si vous devez créer une ressource de certificat supplémentaire, vous pouvez le faire avec l’exemple de manifeste suivant.
```
Type    Reason          Age   From          Message
----    ------          ----  ----          -------
  Normal  CreateOrder     11m   cert-manager  Created new ACME order, attempting validation...
  Normal  DomainVerified  10m   cert-manager  Domain "demo-aks-ingress.eastus.cloudapp.azure.com" verified with "http-01" validation
  Normal  IssueCert       10m   cert-manager  Issuing certificate...
  Normal  CertObtained    10m   cert-manager  Obtained certificate from ACME server
  Normal  CertIssued      10m   cert-manager  Certificate issued successfully
```

Mettez à jour les valeurs de *dnsNames* et de *domains* avec le nom DNS que vous avez créé à l’étape précédente. Si vous utilisez un contrôleur d’entrée interne uniquement, spécifiez le nom DNS interne pour votre service. Pour créer la ressource de certificat, utilisez la commande `kubectl apply -f certificates.yaml`.

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: Certificate
metadata:
  name: tls-secret
  namespace: ingress-basic
spec:
  secretName: tls-secret-staging
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

Tester la configuration d’entrée

```
$ kubectl apply -f certificates.yaml

certificate.certmanager.k8s.io/tls-secret-staging created
```

## <a name="test-the-ingress-configuration"></a>Ouvrez un navigateur web pour accéder au nom de domaine complet de votre contrôleur d’entrée Kubernetes, par exemple, *https://demo-aks-ingress.eastus.cloudapp.azure.com* .

Comme ces exemples utilisent `letsencrypt-staging`, le certificat SSL émis n’est pas approuvé par le navigateur.

Acceptez l’avertissement pour accéder à votre application. Les informations de certificat montrent que le certificat *Fake LE Intermediate X1* est émis par Let’s Encrypt. Ce faux certificat indique que `cert-manager` a correctement traité la demande et a reçu un certificat du fournisseur : Certificat intermédiaire Let’s Encrypt

![Lorsque vous modifiez Let’s Encrypt pour utiliser `prod` plutôt que `staging`, un certificat approuvé émis par Let’s Encrypt est utilisé, comme indiqué dans l’exemple suivant :](media/ingress/staging-certificate.png)

Certificat Let’s Encrypt

![L’application de démonstration s’affiche dans le navigateur web :](media/ingress/certificate.png)

Exemple d’application numéro un

![Maintenant, ajoutez le chemin */hello-world-two* au nom de domaine complet, par exemple, *https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two* .](media/ingress/app-one.png)

La deuxième application de démonstration portant le titre personnalisé s’affiche : Exemple d’application numéro deux

![Supprimer des ressources](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>Cet article vous a montré comment utiliser Helm pour installer les composants d’entrée, les certificats et les exemples d’applications.

Quand vous déployez un graphique Helm, une série de ressources Kubernetes est créée. Ces ressources incluent des pods, des déploiements et des services. Pour nettoyer ces ressources, vous pouvez supprimer l’espace de noms exemple en entier ou des ressources individuelles. Supprimer l’espace de noms exemple et toutes les ressources

### <a name="delete-the-sample-namespace-and-all-resources"></a>Pour supprimer l’espace de noms exemple en entier, utilisez la commande `kubectl delete` et spécifiez le nom de votre espace de noms.

Toutes les ressources de l’espace de noms sont supprimées. Ensuite, supprimez le référentiel Helm pour l’application AKS « hello world » :

```console
kubectl delete namespace ingress-basic
```

Supprimer les ressources individuellement

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Sinon, une approche plus précise consiste à supprimer les ressources individuelles créées.

Commencez par supprimer les ressources de certificat : À présent, listez les versions de Helm avec la commande `helm list`.

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

Recherchez les graphiques nommés *nginx-ingress*, *cert-manager* et *aks-helloworld*, comme illustré dans l’exemple de sortie suivant : Supprimez les versions avec la commande `helm delete`.

```
$ helm list

NAME                    REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
billowing-kitten        1           Wed Mar  6 19:37:43 2019    DEPLOYED    nginx-ingress-1.3.1     0.22.0      kube-system
loitering-waterbuffalo  1           Wed Mar  6 20:25:01 2019    DEPLOYED    cert-manager-v0.6.6     v0.6.2      kube-system
flabby-deer             1           Wed Mar  6 20:27:54 2019    DEPLOYED    aks-helloworld-0.1.0                default
linting-echidna         1           Wed Mar  6 20:27:59 2019    DEPLOYED    aks-helloworld-0.1.0                default
```

L’exemple suivant supprime le déploiement d’entrée NGINX, le gestionnaire de certificats et les deux exemples d’applications AKS « hello world ». Ensuite, supprimez le référentiel Helm pour l’application AKS « hello world » :

```
$ helm delete billowing-kitten loitering-waterbuffalo flabby-deer linting-echidna

release "billowing-kitten" deleted
release "loitering-waterbuffalo" deleted
release "flabby-deer" deleted
release "linting-echidna" deleted
```

Supprimez l’espace de noms lui-même.

```console
helm repo remove azure-samples
```

Utilisez la commande `kubectl delete` et spécifiez le nom de votre espace de noms : Enfin, supprimez la route d’entrée qui a dirigé le trafic vers les exemples d’applications :

```console
kubectl delete namespace ingress-basic
```

Étapes suivantes

```console
kubectl delete -f hello-world-ingress.yaml
```

## <a name="next-steps"></a>Cet article a mentionné certains composants qui n’appartiennent pas à AKS.

Pour plus d’informations sur ces composants, consultez les pages projet suivantes : [Interface CLI Helm][helm-cli]

- [Contrôleur d’entrée NGINX][nginx-ingress]
- [cert-manager][cert-manager]
- Vous pouvez également :

[Créer un contrôleur d’entrée dans Azure Kubernetes Service (AKS)][aks-ingress-basic]

- [Routage d’applications HTTP][aks-http-app-routing]
- [Create an ingress controller to an internal virtual network in Azure Kubernetes Service (AKS)][aks-ingress-internal] (Créer un contrôleur d’entrée pour un réseau virtuel interne dans Azure Kubernetes Service (AKS))
- [Créer un contrôleur d’entrée qui utilise vos propres certificats TLS][aks-ingress-own-tls]
- [Créer un contrôleur d’entrée qui utilise Let’s Encrypt pour générer automatiquement des certificats TLS avec une adresse IP publique statique][aks-ingress-static-tls]
- <bpt id="p1">[</bpt>Create an ingress controller that uses Let's Encrypt to automatically generate TLS certificates with a static public IP address<ept id="p1">][aks-ingress-static-tls]</ept>

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html
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
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli