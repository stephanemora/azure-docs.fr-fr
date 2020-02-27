---
title: Créer un contrôleur d’entrée HTTP avec une adresse IP statique dans Azure Kubernetes Service (AKS)
description: Découvrez comment installer et configurer un contrôleur d’entrée NGINX avec une adresse IP statique dans un cluster Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: a72312e2921b4721a4a5944cf62241b513da1e0a
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77595516"
---
# <a name="create-an-ingress-controller-with-a-static-public-ip-address-in-azure-kubernetes-service-aks"></a>Créer un contrôleur d’entrée avec une adresse IP publique statique dans Azure Kubernetes Service (AKS)

Un contrôleur d’entrée est un logiciel qui fournit un proxy inversé, un routage du trafic configurable et un arrêt TLS pour les services Kubernetes. Des ressources d’entrée Kubernetes sont utilisées pour configurer les règles d’entrée et les itinéraires des services Kubernetes individuels. En utilisant un contrôleur d’entrée et des règles d’entrée, une seule adresse IP peut être utilisée pour router le trafic vers plusieurs services dans un cluster Kubernetes.

Cet article montre comment déployer le [contrôleur d’entrée NGINX][nginx-ingress] dans un cluster Azure Kubernetes Service (AKS). Le contrôleur d’entrée est configuré avec une adresse IP publique statique. Le projet [cert-manager][cert-manager] est utilisé pour générer et configurer automatiquement les certificats [Let’s Encrypt][lets-encrypt]. Enfin, plusieurs applications sont exécutées dans le cluster AKS, chacune étant accessible via une seule adresse IP.

Vous pouvez également :

- [Créer un contrôleur d’entrée dans Azure Kubernetes Service (AKS)][aks-ingress-basic]
- [Routage d’applications HTTP][aks-http-app-routing]
- [Créer un contrôleur d’entrée qui utilise vos propres certificats TLS][aks-ingress-own-tls]
- [Créer un contrôleur d’entrée qui utilise Let’s Encrypt pour générer automatiquement des certificats TLS avec une adresse IP publique dynamique][aks-ingress-tls]

## <a name="before-you-begin"></a>Avant de commencer

Cet article suppose que vous avez un cluster AKS existant. Si vous avez besoin d’un cluster AKS, consultez le guide de démarrage rapide d’AKS [avec Azure CLI][aks-quickstart-cli]ou avec le [Portail Azure][aks-quickstart-portal].

Cet article utilise Helm pour installer le contrôleur d’entrée NGINX, cert-manager et un exemple d’application web. Helm doit être initialisé dans votre cluster AKS et utiliser un compte de service pour Tiller. Assurez-vous d’utiliser la dernière version de Helm 3. Pour des instructions de mise à niveau, consultez la [documentation d’installation de Helm][helm-install]. Pour plus d’informations sur la configuration et l’utilisation de Helm, consultez [Installer des applications avec Helm dans Azure Kubernetes Service (AKS)][use-helm].

Pour les besoins de cet article, vous devez également exécuter Azure CLI version 2.0.64 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Créer un contrôleur d’entrée

Par défaut, un contrôleur d’entrée NGINX est créé avec une nouvelle affectation d’adresse IP publique. Cette adresse IP publique est seulement statique pendant la durée de vie du contrôleur d’entrée, et est perdue si le contrôleur est supprimé et recréé. La spécification de configuration de fournir une adresse IP publique statique existante au contrôleur d’entrée NGINX est courante. L’adresse IP publique statique est conservée si le contrôleur d’entrée est supprimé. Cette approche vous permet d’utiliser des enregistrements DNS et des configurations réseau existants d’une façon cohérente tout au long du cycle de vie de vos applications.

Si vous avez besoin de créer une adresse IP publique statique, commencez par obtenir le nom du groupe de ressources du cluster AKS avec la commande [az aks show][az-aks-show] :

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

Ensuite, créez une adresse IP publique avec la méthode d’allocation *statique* à l’aide de la commande [az network public-ip create][az-network-public-ip-create]. L’exemple suivant crée une adresse IP publique nommée *myAKSPublicIP* dans le groupe de ressources de cluster AKS obtenu à l’étape précédente :

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --sku Standard --allocation-method static --query publicIp.ipAddress -o tsv
```

À présent, déployez le graphique *nginx-ingress* avec Helm. Ajoutez le paramètre `--set controller.service.loadBalancerIP` et spécifiez votre propre adresse IP publique créée à l’étape précédente. Pour renforcer la redondance, deux réplicas des contrôleurs d’entrée NGINX sont déployés avec le paramètre `--set controller.replicaCount`. Pour tirer pleinement parti de l’exécution de réplicas des contrôleurs d’entrée, vérifiez que votre cluster AKS comprend plusieurs nœuds.

Le contrôleur d’entrée doit également être planifié sur un nœud Linux. Les nœuds Windows Server (actuellement en préversion dans AKS) ne doivent pas exécuter le contrôleur d’entrée. Un sélecteur de nœud est spécifié en utilisant le paramètre `--set nodeSelector` pour que le planificateur Kubernetes exécute le contrôleur d’entrée NGINX sur un nœud Linux.

> [!TIP]
> L’exemple suivant crée un espace de noms Kubernetes pour les ressources d’entrée *ingress-basic*. Spécifiez un espace de noms de votre propre environnement, si besoin. Si le contrôle d’accès en fonction du rôle (RBAC) n’est pas activé sur votre cluster AKS, ajoutez `--set rbac.create=false` aux commandes Helm.

> [!TIP]
> Si vous souhaitez activer la [préservation de l’adresse IP source du client][client-source-ip] pour les requêtes aux conteneurs de votre cluster, ajoutez `--set controller.service.externalTrafficPolicy=Local` à la commande d’installation Helm. L’IP source du client est stockée dans l’en-tête de la requête sous *X-Forwarded-For*. Lors de l’utilisation d’un contrôleur d’entrée pour lequel la conservation de l’adresse IP source du client est activée, le protocole SSL direct ne fonctionnera pas.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.service.loadBalancerIP="40.121.63.72"
```

Lorsque le service équilibreur de charge Kubernetes est créé pour le contrôleur d’entrée NGINX, votre adresse IP statique est affectée, comme indiqué dans l’exemple de sortie suivant :

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                        TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
nginx-ingress-controller                    LoadBalancer   10.0.232.56   40.121.63.72   80:31978/TCP,443:32037/TCP   3m
nginx-ingress-default-backend               ClusterIP      10.0.95.248   <none>         80/TCP                       3m
```

Aucune règle d’entrée n’a encore été créée. Par conséquent, la page 404 par défaut du contrôleur d’entrée NGINX s’affiche si vous accédez à l’adresse IP publique. Les étapes suivantes permettent de configurer les règles d’entrée.

## <a name="configure-a-dns-name"></a>Configurer un nom DNS

Pour que les certificats HTTPS fonctionnent correctement, configurez un nom de domaine complet pour l’adresse IP du contrôleur d’entrée. Mettez à jour le script suivant avec l’adresse IP du contrôleur d’entrée et le nom unique à utiliser dans le nom de domaine complet :

```azurecli-interactive
#!/bin/bash

# Public IP address of your ingress controller
IP="40.121.63.72"

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
# Install the CustomResourceDefinition resources separately
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.12/deploy/manifests/00-crds.yaml

# Create the namespace for cert-manager
kubectl create namespace cert-manager

# Label the cert-manager namespace to disable resource validation
kubectl label namespace cert-manager cert-manager.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install \
  cert-manager \
  --namespace cert-manager \
  --version v0.12.0 \
  jetstack/cert-manager
```

Pour plus d’informations sur la configuration cert-manager, voir le [projet cert-manager][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>Créer un émetteur de cluster d’autorité de certification

Cert-manager a besoin d’une ressource [Issuer][cert-manager-issuer] ou [ClusterIssuer][cert-manager-cluster-issuer] pour pouvoir émettre des certificats. Ces ressources Kubernetes sont identiques du point de vue des fonctionnalités, mais `Issuer` s’applique à un seul espace de noms, tandis que `ClusterIssuer` couvre tous les espaces de noms. Pour plus d’informations, voir la documentation de [l’émetteur cert-manager][cert-manager-issuer].

Créez un émetteur de cluster, tel que `cluster-issuer.yaml`, avec l’exemple de manifeste suivant. Remplacez l’adresse e-mail par une adresse valide de votre organisation :

```yaml
apiVersion: cert-manager.io/v1alpha2
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
    solvers:
    - http01:
        ingress:
          class: nginx
```

Pour créer l’émetteur, utilisez la commande `kubectl apply -f cluster-issuer.yaml`.

```
$ kubectl apply -f cluster-issuer.yaml

clusterissuer.cert-manager.io/letsencrypt-staging created
```

## <a name="run-demo-applications"></a>Exécuter des applications de démonstration

Un contrôleur d’entrée et une solution de gestion de certificats ont été configurés. Maintenant, nous allons exécuter deux applications de démonstration dans le cluster AKS. Pour cet exemple, Helm est utilisé pour déployer deux instances d’une simple application « Hello World ».

Avant d’installer les exemples de graphiques Helm, ajoutez le référentiel d’exemples Azure à votre environnement Helm :

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Créez la première application de démonstration à partir d’un graphique Helm avec la commande suivante :

```console
helm install aks-helloworld azure-samples/aks-helloworld --namespace ingress-basic
```

Installez maintenant une deuxième instance de l’application de démonstration. Pour la deuxième instance, spécifiez un nouveau titre de manière à pouvoir distinguer visuellement les deux applications. Vous devez également spécifier un nom de service unique :

```console
helm install aks-helloworld-2 azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
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
    cert-manager.io/cluster-issuer: letsencrypt-staging
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

Créez la ressource d’entrée avec la commande `kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic`.

```
$ kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic

ingress.extensions/hello-world-ingress created
```

## <a name="create-a-certificate-object"></a>Créer un objet certificat

Il faut ensuite créer une ressource de certificat. Elle définit le certificat X.509 souhaité. Pour plus d’informations, consultez [Certificats cert-manager][cert-manager-certificates].

Cert-manager a probablement créé automatiquement un objet de certificat à l’aide d’ingress-shim ; le certificat est automatiquement déployé avec cert-manager depuis la version 0.2.2. Pour plus d’informations, consultez la [documentation d’ingress-shim][ingress-shim].

Pour vérifier que le certificat a bien été créé, utilisez la commande `kubectl describe certificate tls-secret --namespace ingress-basic`.

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
apiVersion: cert-manager.io/v1alpha2
kind: Certificate
metadata:
  name: tls-secret
  namespace: ingress-basic
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

certificate.cert-manager.io/tls-secret created
```

## <a name="test-the-ingress-configuration"></a>Tester la configuration d’entrée

Ouvrez un navigateur web pour accéder au nom de domaine complet de votre contrôleur d’entrée Kubernetes, par exemple, *https://demo-aks-ingress.eastus.cloudapp.azure.com* .

Comme ces exemples utilisent `letsencrypt-staging`, le certificat SSL émis n’est pas approuvé par le navigateur. Acceptez l’avertissement pour accéder à votre application. Les informations de certificat montrent que le certificat *Fake LE Intermediate X1* est émis par Let’s Encrypt. Ce faux certificat indique que `cert-manager` a correctement traité la demande et a reçu un certificat du fournisseur :

![Certificat intermédiaire Let’s Encrypt](media/ingress/staging-certificate.png)

Lorsque vous modifiez Let’s Encrypt pour utiliser `prod` plutôt que `staging`, un certificat approuvé émis par Let’s Encrypt est utilisé, comme indiqué dans l’exemple suivant :

![Certificat Let’s Encrypt](media/ingress/certificate.png)

L’application de démonstration s’affiche dans le navigateur web :

![Exemple d’application numéro un](media/ingress/app-one.png)

Maintenant, ajoutez le chemin */hello-world-two* au nom de domaine complet, par exemple, *https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two* . La deuxième application de démonstration portant le titre personnalisé s’affiche :

![Exemple d’application numéro deux](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Cet article vous a montré comment utiliser Helm pour installer les composants d’entrée, les certificats et les exemples d’applications. Quand vous déployez un graphique Helm, une série de ressources Kubernetes est créée. Ces ressources incluent des pods, des déploiements et des services. Pour nettoyer ces ressources, vous pouvez supprimer l’espace de noms exemple en entier ou des ressources individuelles.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Supprimer l’espace de noms exemple et toutes les ressources

Pour supprimer l’espace de noms exemple en entier, utilisez la commande `kubectl delete` et spécifiez le nom de votre espace de noms. Toutes les ressources de l’espace de noms sont supprimées.

```console
kubectl delete namespace ingress-basic
```

Ensuite, supprimez le référentiel Helm pour l’application AKS « hello world » :

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Supprimer les ressources individuellement

Sinon, une approche plus précise consiste à supprimer les ressources individuelles créées. Commencez par supprimer les ressources de certificat :

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

À présent, listez les versions de Helm avec la commande `helm list`. Recherchez les graphiques nommés *nginx-ingress*, *cert-manager* et *aks-helloworld*, comme illustré dans l’exemple de sortie suivant :

```
$ helm list --all-namespaces

NAME                    NAMESPACE       REVISION        UPDATED                        STATUS          CHART                   APP VERSION
aks-helloworld          ingress-basic   1               2020-01-11 15:02:21.51172346   deployed        aks-helloworld-0.1.0
aks-helloworld-2        ingress-basic   1               2020-01-11 15:03:10.533465598  deployed        aks-helloworld-0.1.0
nginx-ingress           ingress-basic   1               2020-01-11 14:51:03.454165006  deployed        nginx-ingress-1.28.2    0.26.2
cert-manager            cert-manager    1               2020-01-06 21:19:03.866212286  deployed        cert-manager-v0.12.0            v0.12.0
```

Supprimez les versions avec la commande `helm uninstall`. L’exemple suivant supprime le déploiement d’entrée NGINX, le gestionnaire de certificats et les deux exemples d’applications AKS « hello world ».

```
$ helm uninstall aks-helloworld aks-helloworld-2 nginx-ingress -n ingress-basic

release "aks-helloworld" deleted
release "aks-helloworld-2" deleted
release "nginx-ingress" deleted

$ helm uninstall cert-manager -n cert-manager

release "cert-manager" deleted
```

Ensuite, supprimez le référentiel Helm pour l’application AKS « hello world » :

```console
helm repo remove azure-samples
```

Supprimez l’espace de noms lui-même. Utilisez la commande `kubectl delete` et spécifiez le nom de votre espace de noms :

```console
kubectl delete namespace ingress-basic
```

Enfin, supprimez l’adresse IP publique statique créée pour le contrôleur d’entrée. Indiquez le nom du groupe de ressources de cluster *MC_* obtenu à la première étape de cet article, tel que *MC_myResourceGroup_myAKSCluster_eastus* :

```azurecli-interactive
az network public-ip delete --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP
```

## <a name="next-steps"></a>Étapes suivantes

Cet article a mentionné certains composants qui n’appartiennent pas à AKS. Pour plus d’informations sur ces composants, consultez les pages projet suivantes :

- [Interface CLI Helm][helm-cli]
- [Contrôleur d’entrée NGINX][nginx-ingress]
- [cert-manager][cert-manager]

Vous pouvez également :

- [Créer un contrôleur d’entrée dans Azure Kubernetes Service (AKS)][aks-ingress-basic]
- [Routage d’applications HTTP][aks-http-app-routing]
- [Create an ingress controller to an internal virtual network in Azure Kubernetes Service (AKS)][aks-ingress-internal] (Créer un contrôleur d’entrée pour un réseau virtuel interne dans Azure Kubernetes Service (AKS))
- [Créer un contrôleur d’entrée qui utilise vos propres certificats TLS][aks-ingress-own-tls]
- [Create an HTTPS ingress controller on Azure Kubernetes Service (AKS)][aks-ingress-tls] (Créer un contrôleur d’entrée HTTPS dans Azure Kubernetes Service (AKS))

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
