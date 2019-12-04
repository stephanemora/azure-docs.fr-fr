---
title: Utiliser un contrôleur d’entrée traefik personnalisé et configurer HTTPS
services: azure-dev-spaces
ms.date: 08/13/2019
ms.topic: conceptual
description: Découvrez comment configurer Azure Dev Spaces pour utiliser un contrôleur d’entrée traefik personnalisé et configurer HTTPS à l’aide de ce contrôleur d’entrée
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs, Helm, service Mesh, routage du service Mesh, kubectl, k8s
ms.openlocfilehash: 8ddaa7b3e982cb85428a7faef20b59525a175778
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482545"
---
# <a name="use-a-custom-traefik-ingress-controller-and-configure-https"></a>Utiliser un contrôleur d’entrée traefik personnalisé et configurer HTTPS

Cet article explique comment configurer Azure Dev Spaces pour utiliser un contrôleur d’entrée traefik personnalisé. Cet article vous montre également comment configurer ce contrôleur d’entrée personnalisé pour utiliser le protocole HTTPS.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’en avez pas, vous pouvez créer un [compte gratuit][azure-account-create].
* [Azure CLI][az-cli].
* [Cluster AKS (Azure Kubernetes Service) avec Azure Dev Spaces activé][qs-cli].
* [kubectl][kubectl] installé.
* [Helm 2.13 à 2.16 installé][helm-installed].
* Un [domaine personnalisé][custom-domain] avec une [zone DNS][dns-zone] dans le même groupe de ressources que votre cluster AKS.

## <a name="configure-a-custom-traefik-ingress-controller"></a>Configurer un contrôleur d’entrée traefik personnalisé

Connectez-vous à votre cluster à l’aide de [kubectl][kubectl], le client de ligne de commande Kubernetes. Pour configurer `kubectl` afin de vous connecter à votre cluster Kubernetes, exécutez la commande [az aks get-credentials][az-aks-get-credentials]. Cette commande télécharge les informations d’identification et configure l’interface CLI Kubernetes pour les utiliser.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKS
```

Pour vérifier la connexion à votre cluster, utilisez la commande [kubectl get][kubectl-get] pour retourner une liste des nœuds du cluster.

```console
$ kubectl get nodes
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
```

Créez un espace de noms Kubernetes pour le contrôleur d’entrée traefik et installez-le à l’aide de `helm`.

```console
kubectl create ns traefik
helm init --wait
helm install stable/traefik --name traefik --namespace traefik --set kubernetes.ingressClass=traefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true
```

Récupérez l’adresse IP du service de contrôleur d’entrée traefik avec [kubectl get][kubectl-get].

```console
kubectl get svc -n traefik --watch
```

L’exemple de sortie montre les adresses IP de tous les services de l'espace de noms *traefik*.

```console
NAME      TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
traefik   LoadBalancer   10.0.205.78   <pending>     80:32484/TCP,443:30620/TCP   20s
...
traefik   LoadBalancer   10.0.205.78   MY_EXTERNAL_IP   80:32484/TCP,443:30620/TCP   60s
```

Ajoutez un enregistrement *A* à votre zone DNS avec l’adresse IP externe du service traefik à l' aide de la commande [az network dns record-set a add-record][az-network-dns-record-set-a-add-record].

```console
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address MY_EXTERNAL_IP
```

L’exemple ci-dessus ajoute un enregistrement *A* à la zone *MY_CUSTOM_DOMAIN*.

Dans cet article, vous utilisez l’[exemple d’application de partage de vélos Azure Dev Spaces](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) pour illustrer l’utilisation d’Azure Dev Spaces. Clonez l’application à partir de GitHub, puis accédez à son répertoire :

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/charts
```

Ouvrez [values.yaml][values-yaml] et remplacez toutes les instances de *<REPLACE_ME_WITH_HOST_SUFFIX>* par *traefik.MY_CUSTOM_DOMAIN* avec votre domaine pour *MY_CUSTOM_DOMAIN*. Remplaces également *kubernetes.io/ingress.class: traefik-azds  # Dev Spaces-specific* par *kubernetes.io/ingress.class: traefik  # Custom Ingress*. Voici un exemple de fichier `values.yaml` mis à jour :

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
    hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
    hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
```

Enregistrer vos modifications et fermez le fichier.

Déployez l’exemple d’application avec `helm install`.

```console
helm install -n bikesharing . --dep-up --namespace dev --atomic
```

L’exemple ci-dessus déploie l’exemple d'application dans l’espace de noms *dev*.

Sélectionnez l'espace *dev* avec votre exemple d'application à l’aide de `azds space select` et affichez les URL pour accéder à l’exemple d’application à l’aide de `azds list-uris`.

```console
azds space select -n dev
azds list-uris
```

La sortie ci-dessous montre les exemples d’URL de `azds list-uris`.

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Accédez au service *bikesharingweb* en ouvrant l’URL publique à partir de la commande `azds list-uris`. Dans l’exemple ci-dessus, l’URL publique pour le service *bikesharingweb* est `http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/`.

Utilisez la commande `azds space select` pour créer un espace enfant sous *dev* et répertoriez les URL pour accéder à l’espace de développement enfant.

```console
azds space select -n dev/azureuser1 -y
azds list-uris
```

La sortie ci-dessous montre les exemples d’URL de `azds list-uris` pour accéder à l'exemple d’application dans l’espace de développement enfant *azureuser1*.

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Accédez au service *bikesharingweb* pour l’espace de développement enfant *azureuser1* en ouvrant l’URL publique à partir de la commande `azds list-uris`. Dans l’exemple ci-dessus, l’URL publique pour le service *bikesharingweb* dans l’espace de développement enfant *azureuser1* est `http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/`.

## <a name="configure-the-traefik-ingress-controller-to-use-https"></a>Configurer le contrôleur d’entrée traefik pour utiliser HTTPS

Créez un fichier `dev-spaces/samples/BikeSharingApp/traefik-values.yaml` similaire à l’exemple ci-dessous. Mettez à jour la valeur *e-mail* avec votre propre adresse de messagerie, qui est utilisée pour générer le certificat avec Let's Encrypt.

```yaml
fullnameOverride: traefik
replicas: 1
cpuLimit: 400m
memoryRequest: 200Mi
memoryLimit: 500Mi
externalTrafficPolicy: Local
kubernetes:
  ingressClass: traefik
  ingressEndpoint:
    useDefaultPublishedService: true
dashboard:
  enabled: false
debug:
  enabled: false
accessLogs:
  enabled: true
  fields:
    defaultMode: keep
    headers:
      defaultMode: keep
      names:
        Authorization: redact
acme:
  enabled: true
  email: "someone@example.com"
  staging: false
  challengeType: tls-alpn-01
ssl:
  enabled: true
  enforced: true
  permanentRedirect: true
  tlsMinVersion: VersionTLS12
  cipherSuites:
    - TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
    - TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
    - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
    - TLS_RSA_WITH_AES_128_GCM_SHA256
    - TLS_RSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
```

Mettez à jour votre service *traefik* à l'aide de `helm repo update` et en incluant le fichier *traefik-values.yaml* que vous avez créé.

```console
cd ..
helm upgrade traefik stable/traefik --namespace traefik --values traefik-values.yaml
```

La commande ci-dessus exécute une nouvelle version du service traefik à l’aide des valeurs de *traefik-values.yaml* et supprime le service précédent. Le service traefik crée également un certificat TLS à l’aide du chiffrement et commence à rediriger le trafic Web pour utiliser le protocole HTTPS.

> [!NOTE]
> Le démarrage de la nouvelle version du service traefik peut prendre quelques minutes. Vous pouvez vérifier la progression avec `kubectl get pods --namespace traefik --watch`.

Accédez à l’exemple d’application dans l’espace enfant *dev/azureuser1* et remarquez que vous êtes redirigé pour utiliser le protocole HTTPS. Notez également que la page se charge, mais que le navigateur affiche des erreurs. L’ouverture de la console du navigateur affiche l’erreur liée à une page HTTPS tentant de charger des ressources HTTP. Par exemple :

```console
Mixed Content: The page at 'https://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/devsignin' was loaded over HTTPS, but requested an insecure resource 'http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/api/user/allUsers'. This request has been blocked; the content must be served over HTTPS.
```

Pour corriger cette erreur, mettez à jour [BikeSharingWeb/azds.yaml][azds-yaml] pour utiliser *traefik* pour *kubernetes.io/ingress.class* et votre domaine personnalisé pour *$(hostSuffix)* . Par exemple :

```yaml
...
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik
      hosts:
      # This expands to [space.s.][rootSpace.]bikesharingweb.<random suffix>.<region>.azds.io
      - $(spacePrefix)$(rootSpacePrefix)bikesharingweb.traefik.MY_CUSTOM_DOMAIN
...
```

Mettez à jour [BikeSharingWeb/package.json][package-json] avec une dépendance pour le package *url*.

```json
{
...
    "react-responsive": "^6.0.1",
    "universal-cookie": "^3.0.7",
    "url": "0.11.0"
  },
...
```

Mettez à jour la méthode *getApiHostAsync* dans [BikeSharingWeb/pages/helpers.js][helpers-js] pour utiliser HTTPS :

```javascript
...
    getApiHostAsync: async function() {
        const apiRequest = await fetch('/api/host');
        const data = await apiRequest.json();
        
        var urlapi = require('url');
        var url = urlapi.parse(data.apiHost);

        console.log('apiHost: ' + "https://"+url.host);
        return "https://"+url.host;
    },
...
```

Accédez au répertoire `BikeSharingWeb` et utilisez `azds up` pour exécuter votre service BikeSharingWeb mis à jour.

```console
cd BikeSharingWeb/
azds up
```

Accédez à l’exemple d’application dans l’espace enfant *dev/azureuser1* et remarquez que vous êtes redirigé pour utiliser le protocole HTTPS sans erreur.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment Azure Dev Spaces vous aide à développer des applications plus complexes sur plusieurs conteneurs, et comment vous pouvez simplifier le développement collaboratif en utilisant différentes versions ou branches de votre code dans différents espaces.

> [!div class="nextstepaction"]
> [Développement en équipe dans Azure Dev Spaces][team-development-qs]


[az-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[custom-domain]: ../../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
[dns-zone]: ../../dns/dns-getstarted-cli.md
[qs-cli]: ../quickstart-cli.md
[team-development-qs]: ../quickstart-team-development.md

[azds-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/azds.yaml
[azure-account-create]: https://azure.microsoft.com/free
[helm-installed]: https://v2.helm.sh/docs/using_helm/#installing-helm
[helpers-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/pages/helpers.js#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[package-json]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/package.json
[values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml