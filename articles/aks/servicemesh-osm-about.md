---
title: Open Service Mesh (préversion)
description: Open Service Mesh (OSM) dans Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 3/12/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
zone_pivot_groups: client-operating-system
ms.openlocfilehash: b0a37400d02ff324e7191bb0b04243d7f16090bd
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877153"
---
# <a name="open-service-mesh-aks-add-on-preview"></a>Module complémentaire Open Service Mesh AKS (préversion)

## <a name="overview"></a>Vue d’ensemble

[Open Service Mesh (OSM)](https://docs.openservicemesh.io/) est un maillage de services natif Cloud léger et extensible qui permet aux utilisateurs de gérer et de sécuriser uniformément les environnements de microservices hautement dynamiques et de bénéficier de fonctionnalités d’observabilité prêtes à l’emploi.

OSM exécute un plan de contrôle basé sur un envoi sur Kubernetes, peut être configuré avec des API [SMI](https://smi-spec.io/) et fonctionne en injectant un proxy Envoy en tant que conteneur side-car à côté de chaque instance de votre application. Le proxy Envoy contient et exécute des règles concernant les stratégies de contrôle d’accès, implémente la configuration de routage et capture les métriques. Le plan de contrôle configure continuellement les proxys pour garantir que les stratégies et les règles d’acheminement sont à jour et que les proxys sont intègres.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="capabilities-and-features"></a>Capacités et fonctionnalités

OSM offre l’ensemble des capacités et fonctionnalités suivantes pour fournir un maillage de services natif Cloud pour vos clusters Azure Kubernetes Service (AKS) :

- Sécurisation des communications entre services grâce à l’activation de mTLS

- Intégration facile des applications dans le maillage grâce à l’injection automatique de side-car du proxy Envoy

- Configurations faciles et transparentes pour le déplacement du trafic sur les déploiements

- Possibilité de définir et d’exécuter des stratégies de contrôle d’accès affinées pour les services

- Possibilité d’observer et de comprendre les métriques des applications pour le débogage et la surveillance des services

- Intégration avec des services/solutions externes de gestion des certificats grâce à une interface enfichable

## <a name="scenarios"></a>Scénarios

OSM peut aider vos déploiements AKS dans les scénarios suivants :

- Mise en place de communications chiffrées entre des points de terminaison de service déployés dans le cluster

- Autorisation du trafic HTTP/HTTPS et TCP dans le maillage

- Configuration de contrôles du trafic pondéré entre deux services ou plus pour les déploiements A/B ou Canary

- Collecte et visualisation des indicateurs de performance clés du trafic d’application

## <a name="osm-service-quotas-and-limits-preview"></a>Quotas et limites de service d’OSM (préversion)

Les limitations de la préversion d’OSM en ce qui concerne les quotas et les limites de service sont disponibles sur la [page Quotas et limites régionales](https://docs.microsoft.com/azure/aks/quotas-skus-regions) d’AKS.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/osm/install-osm-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download and install client binary](includes/servicemesh/osm/install-osm-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/osm/install-osm-binary-windows.md)]

::: zone-end

> [!WARNING]
> N’essayez pas d’installer OSM à partir du fichier binaire avec `osm install`. Cela aboutira à une installation d’OSM qui n’est pas intégrée en tant que module complémentaire pour AKS.

### <a name="register-the-aks-openservicemesh-preview-feature"></a>Inscrire la fonctionnalité d’évaluation `AKS-OpenServiceMesh`

Pour créer un cluster AKS capable d’utiliser le module complémentaire Open Service Mesh, vous devez activer l’indicateur de fonctionnalité `AKS-OpenServiceMesh` sur votre abonnement.

Inscrivez l’indicateur de fonctionnalité `AKS-OpenServiceMesh` à l’aide de la commande [az feature register][az-feature-register], comme indiqué dans l’exemple suivant :

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-OpenServiceMesh"
```

Quelques minutes sont nécessaires pour que l’état s’affiche _Registered_ (Inscrit). Vérifiez l’état de l’inscription à l’aide de la commande [az feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-OpenServiceMesh')].{Name:name,State:properties.state}"
```

Lorsque vous êtes prêt, actualisez l’inscription du fournisseur de ressources _Microsoft.ContainerService_ à l’aide de la commande [az provider register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="install-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-a-new-aks-cluster"></a>Installer le module complémentaire Open Service Mesh (OSM) Azure Kubernetes Service (AKS) pour un nouveau cluster AKS

Pour le scénario de déploiement d’un nouveau cluster AKS, vous commencerez par un tout nouveau déploiement de cluster AKS en activant le module complémentaire OSM lors de l’opération de création du cluster.

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Dans Azure, vous allouez les ressources associées à un groupe de ressources. Créez un groupe de ressources à l’aide de la commande [az group create](/cli/azure/group#az_group_create). L’exemple suivant crée un groupe de ressources nommé _myOsmAksGroup_ à l’emplacement _eastus2_ (région) :

```azurecli-interactive
az group create --name <myosmaksgroup> --location <eastus2>
```

### <a name="deploy-an-aks-cluster-with-the-osm-add-on-enabled"></a>Déployer un cluster AKS avec le module complémentaire OSM activé

Déployez ensuite un nouveau cluster AKS avec le module complémentaire OSM activé.

> [!NOTE]
> Notez que la commande de déploiement AKS suivante utilise des disques de système d’exploitation éphémères. Vous trouverez plus d’informations ici sur [les disques de système d’exploitation éphémères pour AKS](https://docs.microsoft.com/azure/aks/cluster-configuration#ephemeral-os).

```azurecli-interactive
az aks create -n osm-addon-cluster -g <myosmaksgroup> --kubernetes-version 1.19.6 --node-osdisk-type Ephemeral --node-osdisk-size 30 --network-plugin azure --enable-managed-identity -a open-service-mesh
```

#### <a name="get-aks-cluster-access-credentials"></a>Obtenir les informations d’identification d’accès au cluster AKS

Obtenez les informations d’identification d’accès pour le nouveau cluster Kubernetes managé.

```azurecli-interactive
az aks get-credentials -n <myosmakscluster> -g <myosmaksgroup>
```

## <a name="enable-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-an-existing-aks-cluster"></a>Activer le module complémentaire Open Service Mesh (OSM) Azure Kubernetes Service (AKS) pour un cluster AKS existant

Pour un scénario de cluster AKS existant, vous allez activer le module complémentaire OSM sur un cluster AKS qui a déjà été déployé.

### <a name="enable-the-osm-add-on-to-existing-aks-cluster"></a>Activer le module complémentaire OSM sur un cluster AKS existant

Pour activer le module complémentaire AKS OSM, vous devez exécuter la commande `az aks enable-addons --addons` en transmettant le paramètre `open-service-mesh`.

```azurecli-interactive
az aks enable-addons --addons open-service-mesh -g <resource group name> -n <AKS cluster name>
```

Vous devez voir une sortie similaire à la sortie ci-dessous pour confirmer que le module complémentaire AKS OSM a été installé.

```json
{- Finished ..
  "aadProfile": null,
  "addonProfiles": {
    "KubeDashboard": {
      "config": null,
      "enabled": false,
      "identity": null
    },
    "openServiceMesh": {
      "config": {},
      "enabled": true,
      "identity": {
...
```

## <a name="validate-the-aks-osm-add-on-installation"></a>Valider l’installation du module complémentaire AKS OSM

Plusieurs commandes doivent être exécutées pour vérifier que tous les composants du module complémentaire AKS OSM sont activés et en cours d’exécution :

Tout d’abord, nous pouvons interroger les profils de module complémentaire du cluster pour vérifier l’état d’activation des modules complémentaires installés. La commande suivante doit renvoyer la valeur « true ».

```azurecli-interactive
az aks list -g <resource group name> -o json | jq -r '.[].addonProfiles.openServiceMesh.enabled'
```

Les commandes `kubectl` suivantes signalent l’état d’osm-controller.

```azurecli-interactive
kubectl get deployments -n kube-system --selector app=osm-controller
kubectl get pods -n kube-system --selector app=osm-controller
kubectl get services -n kube-system --selector app=osm-controller
```

## <a name="accessing-the-aks-osm-add-on"></a>Accéder au module complémentaire AKS OSM

Actuellement, vous pouvez accéder au contrôleur OSM et le configurer via le configmap. Pour afficher les paramètres de configuration du contrôleur OSM, interrogez le configmap osm-config via `kubectl`.

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

La sortie du configmap OSM doit ressembler à ce qui suit :

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254/32,168.63.129.16/32,<YOUR_API_SERVER_PUBLIC_IP>/32",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

Notez que le paramètre **permissive_traffic_policy_mode** est configuré sur **true**. Le mode de stratégie de trafic permissif dans OSM est un mode dans lequel l’application de la stratégie de trafic [SMI](https://smi-spec.io/) est ignorée. Dans ce mode, OSM découvre automatiquement les services qui font partie du maillage de services et programme des règles de stratégie de trafic sur chaque side-car de proxy Envoy pour pouvoir communiquer avec ces services.

> [!WARNING]
> Avant de continuer, vérifiez que votre mode de stratégie de trafic permissif est défini sur true. Si ce n’est pas le cas, définissez-le sur **true** à l’aide de la commande ci-dessous.

```OSM Permissive Mode to True
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"permissive_traffic_policy_mode":"true"}}'
```

## <a name="deploy-a-new-application-to-be-managed-by-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>Déployer une nouvelle application qui sera gérée par le module complémentaire Open Service Mesh (OSM) Azure Kubernetes Service (AKS)

### <a name="before-you-begin"></a>Avant de commencer

Les étapes détaillées dans cette procédure pas à pas supposent que vous avez créé un cluster AKS (Kubernetes `1.19+` et versions ultérieures avec Kubernetes RBAC activé), que vous avez établi une connexion `kubectl` avec le cluster (si vous avez besoin d’aide pour l’un de ces éléments, consultez le [démarrage rapide d’AKS](./kubernetes-walkthrough.md)) et que vous avez installé le module complémentaire AKS OSM.

Les ressources suivantes doivent être installées :

- Azure CLI, version 2.20.0 ou ultérieure
- L’extension `aks-preview` version 0.5.5 ou ultérieure
- OSM, version 0.8.0 ou ultérieure
- apt-get install jq

### <a name="create-namespaces-for-the-application"></a>Créer des espaces de noms pour l’application

Dans cette procédure pas à pas, nous utiliserons l’application bookstore OSM qui dispose des services Kubernetes suivants :

- bookbuyer
- bookthief
- bookstore
- bookwarehouse

Créez des espaces de noms pour chacun de ces composants d’application.

```azurecli-interactive
for i in bookstore bookbuyer bookthief bookwarehouse; do kubectl create ns $i; done
```

Vous devez normalement voir la sortie suivante :

```Output
namespace/bookstore created
namespace/bookbuyer created
namespace/bookthief created
namespace/bookwarehouse created
```

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>Intégrer les espaces de noms à gérer par OSM

En ajoutant les espaces de noms au maillage OSM, vous permettez au contrôleur OSM d’injecter automatiquement votre application dans les conteneurs du proxy side-car Envoy. Exécutez la commande suivante pour intégrer les espaces de noms de l’application bookstore OSM.

```azurecli-interactive
osm namespace add bookstore bookbuyer bookthief bookwarehouse
```

Vous devez normalement voir la sortie suivante :

```Output
Namespace [bookstore] successfully added to mesh [osm]
Namespace [bookbuyer] successfully added to mesh [osm]
Namespace [bookthief] successfully added to mesh [osm]
Namespace [bookwarehouse] successfully added to mesh [osm]
```

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>Déployer l’application Bookstore sur le cluster AKS

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookwarehouse.yaml
```

Toutes les sorties du déploiement sont résumées ci-dessous.

```Output
serviceaccount/bookbuyer created
service/bookbuyer created
deployment.apps/bookbuyer created

serviceaccount/bookthief created
service/bookthief created
deployment.apps/bookthief created

service/bookstore created
serviceaccount/bookstore created
deployment.apps/bookstore created

serviceaccount/bookwarehouse created
service/bookwarehouse created
deployment.apps/bookwarehouse created
```

### <a name="checkpoint-what-got-installed"></a>Point de contrôle : Quels sont les éléments installés ?

L’exemple d’application Bookstore est une application à plusieurs niveaux qui se compose de quatre services : bookbuyer, bookthief, bookstore et bookwarehouse. Le service bookbuyer et bookthief communiquent tous deux avec le service bookstore pour récupérer des livres auprès de ce dernier. Le service bookstore récupère les livres du service bookwarehouse pour approvisionner les composants bookbuyer et bookthief. Il s’agit d’une application simple à plusieurs niveaux qui montre bien l’utilisation d’un maillage de services pour protéger et autoriser les communications entre les services d’applications. Tout au long de la procédure, nous allons activer et désactiver les stratégies SMI (Service Mesh Interface) pour autoriser ou non les services à communiquer via OSM. Vous trouverez ci-dessous un diagramme d’architecture de ce qui a été installé pour l’application bookstore.

![Architecture de l’application de librairie OSM](./media/aks-osm-addon/osm-bookstore-app-arch.png)

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Vérifier l’exécution de l’application Bookstore dans le cluster AKS

Pour l’instant, nous avons déployé l’application multiconteneur bookstore, mais elle n’est accessible qu’à partir du cluster AKS. Les tutoriels ultérieurs vous aideront à exposer l’application en dehors du cluster via un contrôleur d’entrée. Pour le moment, nous allons utiliser la redirection de port pour accéder à l’application bookbuyer à l’intérieur du cluster AKS afin de vérifier qu’elle achète des livres auprès du service bookstore.

Pour vérifier que l’application s’exécute dans le cluster, nous allons utiliser une redirection de port pour afficher l’interface utilisateur des composants bookbuyer et bookthief.

Commençons par récupérer le nom du pod de bookbuyer.

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Le résultat ressemble à ce qui suit. Un nom unique est ajouté à votre pod de bookbuyer.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Une fois que nous avons le nom du pod, nous pouvons utiliser la commande port-forward pour configurer un tunnel entre notre système local et l’application dans le cluster AKS. Exécutez la commande suivante pour configurer la redirection de port pour le port 8080 du système local. Utilisez à nouveau le nom du pod de bookbuyer que vous avez spécifié.

> [!NOTE]
> Pour toutes les commandes de redirection de port, il est préférable d’utiliser un terminal supplémentaire afin de pouvoir continuer à travailler tout au long de cette procédure sans déconnecter le tunnel. Il est également préférable d’établir le tunnel de redirection de port en dehors d’Azure Cloud Shell.

```Bash
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

La sortie doit ressembler à ce qui suit.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

Pendant que la session de redirection de port est en place, accédez à l’URL `http://localhost:8080` à partir d’un navigateur. Vous devriez maintenant être en mesure de voir l’interface utilisateur de l’application Bookbuyer dans le navigateur, comme dans l’image ci-dessous.

![Image de l’interface utilisateur de l’application bookbuyer OSM](./media/aks-osm-addon/osm-bookbuyer-service-ui.png)

Vous remarquerez également que le nombre total de livres achetés continue de s’incrémenter au service bookstore v1. Le service bookstore v2 n’a pas encore été déployé. Nous déploierons le service bookstore v2 lorsque nous présenterons les stratégies de division du trafic SMI.

Vous pouvez également faire de même pour le service bookthief.

```azurecli-interactive
kubectl get pod -n bookthief
```

Le résultat ressemble à ce qui suit. Un nom unique est ajouté à votre pod de bookthief.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookthief-59549fb69c-cr8vl   2/2     Running   0          15m54s
```

Redirigez le port vers le pod de bookthief.

```Bash
kubectl port-forward bookthief-59549fb69c-cr8vl -n bookthief 8080:14001
```

Accédez à l’URL `http://localhost:8080` à partir d’un navigateur. Vous devez voir que le voleur de livres est en train de voler des livres du service bookstore. Plus tard, nous implémenterons une stratégie de trafic pour arrêter le voleur de livres.

![Image de l’interface utilisateur de l’application bookthief OSM](./media/aks-osm-addon/osm-bookthief-service-ui.png)

### <a name="disable-osm-permissive-traffic-mode-for-the-mesh"></a>Désactiver le mode de trafic permissif d’OSM pour le maillage

Comme indiqué précédemment lors de la visualisation de la configuration du cluster OSM, la configuration OSM active par défaut la stratégie de mode de trafic permissif. Dans ce mode, l’application de la stratégie de trafic est ignorée et OSM découvre automatiquement les services qui font partie du maillage de services et programme des règles de stratégie de trafic sur chaque side-car de proxy Envoy pour pouvoir communiquer avec ces services.

Nous allons maintenant désactiver la stratégie de mode de trafic permissif et OSM aura besoin de stratégies [SMI](https://smi-spec.io/) explicites déployées sur le cluster pour autoriser les communications dans le maillage à partir de chaque service. Pour désactiver le mode de trafic permissif, exécutez la commande suivante pour mettre à jour la propriété du configmap en remplaçant la valeur `true` par `false`.

```azurecli-interactive
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"permissive_traffic_policy_mode":"false"}}'
```

Le résultat ressemble à ce qui suit. Un nom unique est ajouté à votre pod de bookthief.

```Output
configmap/osm-config patched
```

Pour vérifier que le mode de trafic permissif a été désactivé, redirigez à nouveau le port vers le pod de bookbuyer ou de bookthief pour afficher leur interface utilisateur dans le navigateur et voir si les livres achetés ou volés ne s’incrémentent plus. Veillez à actualiser le navigateur. Si l’incrémentation s’est arrêtée, la stratégie a été appliquée correctement. Vous avez réussi à empêcher le bookthief de voler des livres, mais le bookbuyer ne peut pas acheter de livres auprès du bookstore et le bookstore ne peut pas récupérer de livres du bookwarehouse. Ensuite, nous allons implémenter des stratégies [SMI](https://smi-spec.io/) pour permettre uniquement aux services du maillage de votre choix de communiquer.

### <a name="apply-service-mesh-interface-smi-traffic-access-policies"></a>Appliquer des stratégies d’accès au trafic SMI (Service Mesh Interface)

Maintenant que nous avons désactivé toutes les communications dans le maillage, autorisons notre service bookbuyer à communiquer avec notre service bookstore pour acheter des livres, et autorisons notre service bookstore à communiquer avec notre service bookwarehouse pour récupérer des livres à vendre.

Déployez les stratégies [SMI](https://smi-spec.io/) suivantes.

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
---
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookstore-service-routes
  namespace: bookstore
spec:
  matches:
  - name: books-bought
    pathRegex: /books-bought
    methods:
    - GET
    headers:
    - "user-agent": ".*-http-client/*.*"
    - "client-app": "bookbuyer"
  - name: buy-a-book
    pathRegex: ".*a-book.*new"
    methods:
    - GET
  - name: update-books-bought
    pathRegex: /update-books-bought
    methods:
    - POST
---
kind: TrafficTarget
apiVersion: access.smi-spec.io/v1alpha3
metadata:
  name: bookstore-access-bookwarehouse
  namespace: bookwarehouse
spec:
  destination:
    kind: ServiceAccount
    name: bookwarehouse
    namespace: bookwarehouse
  rules:
  - kind: HTTPRouteGroup
    name: bookwarehouse-service-routes
    matches:
    - restock-books
  sources:
  - kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  - kind: ServiceAccount
    name: bookstore-v2
    namespace: bookstore
---
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookwarehouse-service-routes
  namespace: bookwarehouse
spec:
  matches:
    - name: restock-books
      methods:
      - POST
      headers:
      - host: bookwarehouse.bookwarehouse
EOF
```

Le résultat ressemble à ce qui suit.

```Output
traffictarget.access.smi-spec.io/bookbuyer-access-bookstore-v1 created
httproutegroup.specs.smi-spec.io/bookstore-service-routes created
traffictarget.access.smi-spec.io/bookstore-access-bookwarehouse created
httproutegroup.specs.smi-spec.io/bookwarehouse-service-routes created
```

Vous pouvez maintenant configurer une session de redirection de port sur les pods de bookbuyer ou bookstore et constater que les métriques des livres achetés et vendus s’incrémentent à nouveau. Vous pouvez également faire de même pour le pod de bookthief afin de vérifier qu’il n’est toujours plus en mesure de voler des livres.

### <a name="apply-service-mesh-interface-smi-traffic-split-policies"></a>Appliquer des stratégies de division du trafic SMI (Service Mesh Interface)

Pour notre dernière démonstration, nous allons créer une stratégie de division du trafic [SMI](https://smi-spec.io/) pour configurer le poids des communications d’un service vers plusieurs services en tant que serveur principal. La fonctionnalité de division du trafic vous permet de déplacer progressivement les connexions d’un service vers un autre en pondérant le trafic sur une échelle de 0 à 100.

Le graphique ci-dessous est un diagramme de la stratégie de division du trafic [SMI](https://smi-spec.io/) à déployer. Nous allons déployer une 2e version de Bookstore, puis diviser le trafic entrant à partir du bookbuyer, en pondérant 25 % du trafic vers le service bookstore v1 et 75 % vers le service bookstore v2.

![Diagramme de division du trafic de bookbuyer OSM](./media/aks-osm-addon/osm-bookbuyer-traffic-split-diagram.png)

Déployez le service bookstore v2.

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: v1
kind: Service
metadata:
  name: bookstore-v2
  namespace: bookstore
  labels:
    app: bookstore-v2
spec:
  ports:
  - port: 14001
    name: bookstore-port
  selector:
    app: bookstore-v2
---
# Deploy bookstore-v2 Service Account
apiVersion: v1
kind: ServiceAccount
metadata:
  name: bookstore-v2
  namespace: bookstore
---
# Deploy bookstore-v2 Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: bookstore-v2
  namespace: bookstore
spec:
  replicas: 1
  selector:
    matchLabels:
      app: bookstore-v2
  template:
    metadata:
      labels:
        app: bookstore-v2
    spec:
      serviceAccountName: bookstore-v2
      containers:
        - name: bookstore
          image: openservicemesh/bookstore:v0.8.0
          imagePullPolicy: Always
          ports:
            - containerPort: 14001
              name: web
          command: ["/bookstore"]
          args: ["--path", "./", "--port", "14001"]
          env:
            - name: BOOKWAREHOUSE_NAMESPACE
              value: bookwarehouse
            - name: IDENTITY
              value: bookstore-v2
---
kind: TrafficTarget
apiVersion: access.smi-spec.io/v1alpha3
metadata:
  name: bookbuyer-access-bookstore-v2
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore-v2
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
EOF
```

Vous devez voir la sortie suivante.

```Output
service/bookstore-v2 configured
serviceaccount/bookstore-v2 created
deployment.apps/bookstore-v2 created
traffictarget.access.smi-spec.io/bookstore-v2 created
```

À présent, déployez la stratégie de division du trafic pour répartir le trafic de bookbuyer entre les deux services bookstore v1 et v2.

```azurecli-interactive
kubectl apply -f - <<EOF
apiVersion: split.smi-spec.io/v1alpha2
kind: TrafficSplit
metadata:
  name: bookstore-split
  namespace: bookstore
spec:
  service: bookstore.bookstore
  backends:
  - service: bookstore
    weight: 25
  - service: bookstore-v2
    weight: 75
EOF
```

Vous devez voir la sortie suivante.

```Output
trafficsplit.split.smi-spec.io/bookstore-split created
```

Configurez un tunnel de redirection de port vers le pod de bookbuyer. Vous devez maintenant voir les livres achetés auprès du service bookstore v2. Si vous continuez à observer l’incrément des achats, vous devriez remarquer un accroissement plus rapide des achats effectués par le service bookstore v2.

![Interface utilisateur des livres achetés de bookbuyer OSM](./media/aks-osm-addon/osm-bookbuyer-traffic-split-ui.png)

## <a name="manage-existing-deployed-applications-to-be-managed-by-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>Gérer les applications déployées qui seront gérées par le module complémentaire Open Service Mesh (OSM) Azure Kubernetes Service (AKS)

### <a name="before-you-begin"></a>Avant de commencer

Les étapes détaillées dans cette procédure pas à pas supposent que vous avez déjà activé le module complémentaire OSM AKS pour votre cluster AKS. Si ce n’est pas le cas, consultez la section [Activer le module complémentaire Open Service Mesh (OSM) Azure Kubernetes Service (AKS) pour un cluster AKS existant](#enable-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-an-existing-aks-cluster) avant de poursuivre. En outre, votre cluster AKS doit avoir la version `1.19+` ou une version ultérieure de Kubernetes avec Kubernetes RBAC activé, une connexion `kubectl` doit être établie avec le cluster (si vous avez besoin d’aide pour l’un de ces éléments, consultez le [démarrage rapide d’AKS](./kubernetes-walkthrough.md)) et le module complémentaire AKS OSM doit être installé.

Les ressources suivantes doivent être installées :

- Azure CLI, version 2.20.0 ou ultérieure
- L’extension `aks-preview` version 0.5.5 ou ultérieure
- OSM, version 0.8.0 ou ultérieure
- apt-get install jq

### <a name="verify-the-open-service-mesh-osm-permissive-traffic-mode-policy"></a>Vérifier la stratégie de mode de trafic permissif d’Open Service Mesh (OSM)

Le mode de stratégie de trafic permissif d’OSM est un mode dans lequel l’application de la stratégie de trafic [SMI](https://smi-spec.io/) est ignorée. Dans ce mode, OSM découvre automatiquement les services qui font partie du maillage de services et programme des règles de stratégie de trafic sur chaque side-car de proxy Envoy pour pouvoir communiquer avec ces services.

Pour vérifier le mode de trafic permissif actuel d’OSM pour votre cluster, exécutez la commande suivante :

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

La sortie du configmap OSM doit ressembler à ce qui suit :

```Output
{
  "egress": "true",
  "enable_debug_server": "true",
  "envoy_log_level": "error",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

Si **permissive_traffic_policy_mode** est configuré sur **true**, vous pouvez intégrer vos espaces de noms en toute sécurité sans interrompre vos communications de service à service. Si **permissive_traffic_policy_mode** est configuré sur **false**, vous devez vous assurer que les bons manifestes de stratégie d’accès au trafic [SMI](https://smi-spec.io/) sont déployés et que vous avez un compte de service représentant chaque service déployé dans l’espace de noms. Suivez les instructions de la section [Intégrer des applications déployées à la stratégie de trafic permissif d’Open Service Mesh (OSM) configurée sur False](#onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false).

### <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-true"></a>Intégrer des applications déployées à la stratégie de trafic permissif d’Open Service Mesh (OSM) configurée sur True

La première chose que nous allons faire est d’ajouter les espaces de noms des applications déployées à OSM pour qu’il les gère.

```azurecli-interactive
osm namespace add bookstore
```

Vous devez normalement voir la sortie suivante :

```Output
Namespace [bookstore] successfully added to mesh [osm]
```

Ensuite, nous examinerons le déploiement actuel des pods dans l’espace de noms. Exécutez la commande suivante pour afficher les pods dans l’espace de noms désigné.

```azurecli-interactive
kubectl get pod -n bookbuyer
```

La sortie doit normalement ressembler à ce qui suit :

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-78666dcff8-wh6wl   1/1     Running   0          43s
```

Remarquez que la colonne **READY** indique **1/1** : cela signifie que le pod d’application n’a qu’un seul conteneur. Ensuite, nous devons redémarrer les déploiements de votre application afin qu’OSM puisse injecter votre pod d’application dans le conteneur du proxy side-car Envoy. Obtenons une liste des déploiements dans l’espace de noms.

```azurecli-interactive
kubectl get deployment -n bookbuyer
```

Vous devez normalement voir la sortie suivante :

```Output
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
bookbuyer   1/1     1            1           23h
```

À présent, nous allons redémarrer le déploiement pour injecter votre pod d’application dans le conteneur du proxy side-car Envoy. Exécutez la commande suivante :

```azurecli-interactive
kubectl rollout restart deployment bookbuyer -n bookbuyer
```

Vous devez normalement voir la sortie suivante :

```Output
deployment.apps/bookbuyer restarted
```

Si nous examinons à nouveau les pods dans l’espace de noms :

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Vous remarquerez que la colonne **READY** indique maintenant que **2/2** conteneurs sont prêts pour votre pod. Le deuxième conteneur est le proxy side-car Envoy.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-84446dd5bd-j4tlr   2/2     Running   0          3m30s
```

Nous pouvons examiner davantage le pod pour voir le proxy Envoy en exécutant la commande describe afin d’afficher la configuration.

```azurecli-interactive
kubectl describe pod bookbuyer-84446dd5bd-j4tlr -n bookbuyer
```

```Output
Containers:
  bookbuyer:
    Container ID:  containerd://b7503b866f915711002292ea53970bd994e788e33fb718f1c4f8f12cd4a88198
    Image:         openservicemesh/bookbuyer:v0.8.0
    Image ID:      docker.io/openservicemesh/bookbuyer@sha256:813874bd2dc9c5a259b9657995348cf0822b905e29c4e86f21fdefa0ef21dcee
    Port:          <none>
    Host Port:     <none>
    Command:
      /bookbuyer
    State:          Running
      Started:      Tue, 23 Mar 2021 10:52:53 -0400
    Ready:          True
    Restart Count:  0
    Environment:
      BOOKSTORE_NAMESPACE:  bookstore
      BOOKSTORE_SVC:        bookstore
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from bookbuyer-token-zft2r (ro)
  envoy:
    Container ID:  containerd://f5f1cb5db8d5304e23cc984eb08146ea162a3e82d4262c4472c28d5579c25e10
    Image:         envoyproxy/envoy-alpine:v1.17.1
    Image ID:      docker.io/envoyproxy/envoy-alpine@sha256:511e76b9b73fccd98af2fbfb75c34833343d1999469229fdfb191abd2bbe3dfb
    Ports:         15000/TCP, 15003/TCP, 15010/TCP
    Host Ports:    0/TCP, 0/TCP, 0/TCP
```

Vérifiez que votre application est toujours fonctionnelle après l’injection dans le proxy side-car Envoy.

### <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false"></a>Intégrer des applications déployées à la stratégie de trafic permissif d’Open Service Mesh (OSM) configurée sur False

Lorsque la configuration d’OSM pour la stratégie de trafic permissif est définie sur `false`, OSM requiert le déploiement de stratégies d’accès au trafic [SMI](https://smi-spec.io/) explicites pour que la communication de service à service ait lieu au sein de votre cluster. À l’heure actuelle, OSM utilise également des comptes de service Kubernetes dans le cadre de l’autorisation des communications de service à service. Pour vous assurer que vos applications déployées existantes communiquent entre elles lorsqu’elles sont gérées par le maillage OSM, nous devons vérifier l’existence du compte de service à utiliser, mettre à jour le déploiement des applications avec les informations du compte de service et appliquer les stratégies d’accès au trafic [SMI](https://smi-spec.io/).

#### <a name="verify-kubernetes-service-accounts"></a>Vérifier les comptes de service Kubernetes

Vérifiez si vous disposez d’un compte de service Kubernetes dans l’espace de noms sur lequel votre application est déployée.

```azurecli-interactive
kubectl get serviceaccounts -n bookbuyer
```

Dans l’exemple suivant, il existe un compte de service nommé `bookbuyer` dans l’espace de noms bookbuyer.

```Output
NAME        SECRETS   AGE
bookbuyer   1         25h
default     1         25h
```

Si vous ne disposez pas d’un compte de service autre que le compte par défaut, vous devrez en créer un pour votre application. Utilisez la commande suivante comme exemple pour créer un compte de service dans l’espace de noms déployé de l’application.

```azurecli-interactive
kubectl create serviceaccount myserviceaccount -n bookbuyer
```

```Output
serviceaccount/myserviceaccount created
```

#### <a name="view-your-applications-current-deployment-specification"></a>Afficher la spécification de déploiement actuelle de votre application

Si vous avez dû créer un compte de service dans la section précédente, il est probable que le déploiement de votre application n’est pas configuré avec un `serviceAccountName` spécifique dans les spécifications de déploiement. Nous pouvons afficher les spécifications de déploiement de votre application à l’aide des commandes suivantes :

```azurecli-interactive
kubectl get deployment -n bookbuyer
```

Une liste de déploiements est répertoriée dans la sortie.

```Output
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
bookbuyer   1/1     1            1           25h
```

Nous allons maintenant décrire le déploiement afin de vérifier si un compte de service est listé dans la section Pod Template.

```azurecli-interactive
kubectl describe deployment bookbuyer -n bookbuyer
```

Dans ce déploiement particulier, vous pouvez voir qu’un compte de service est associé au déploiement indiqué sous la section Pod Template. Ce déploiement utilise le compte de service bookbuyer. Si vous ne voyez pas la propriété **Serivce Account:** , votre déploiement n’est pas configuré pour utiliser un compte de service.

```Output
Pod Template:
  Labels:           app=bookbuyer
                    version=v1
  Annotations:      kubectl.kubernetes.io/restartedAt: 2021-03-23T10:52:49-04:00
  Service Account:  bookbuyer
  Containers:
   bookbuyer:
    Image:      openservicemesh/bookbuyer:v0.8.0

```

Il existe plusieurs techniques pour mettre à jour votre déploiement afin d’ajouter un compte de service Kubernetes. Consultez la documentation de Kubernetes sur la [mise à jour d’un déploiement](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#updating-a-deployment) en ligne ou [configurez les comptes de service pour les pods](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/). Une fois que vous avez mis à jour votre spécification de déploiement avec le compte de service, redéployez (kubectl apply -f your-deployment.yaml) votre déploiement sur le cluster.

#### <a name="deploy-the-necessary-service-mesh-interface-smi-policies"></a>Déployer les stratégies SMI (Service Mesh Interface) nécessaires

La dernière étape pour permettre au trafic autorisé de circuler dans le maillage consiste à déployer les stratégies d’accès au trafic [SMI](https://smi-spec.io/) nécessaires à votre application. La quantité de configuration que vous pouvez réaliser avec les stratégies d’accès au trafic [SMI](https://smi-spec.io/) dépasse le cadre de cette procédure pas à pas, mais nous allons détailler certains des composants communs de la spécification et expliquer comment configurer des stratégies TrafficTarget et HTTPRouteGroup simples pour permettre la communication de service à service pour votre application.

La spécification [**Contrôle d’accès au trafic**](https://github.com/servicemeshinterface/smi-spec/blob/main/apis/traffic-access/v1alpha3/traffic-access.md#traffic-access-control) [SMI](https://smi-spec.io/) permet aux utilisateurs de définir la stratégie de contrôle d’accès pour leurs applications. Nous allons nous concentrer sur les ressources d’API **TrafficTarget** et **HTTPRouteGroup**.

La ressource TrafficTarget est constituée de trois paramètres de configuration principaux : destination, rules et sources. Un exemple de TrafficTarget est présenté ci-dessous.

```TrafficTarget Example spec
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore-v1
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
```

Dans la spécification TrafficTarget ci-dessus, `destination` désigne le compte de service qui est configuré pour le service source de destination. Rappelez-vous que le compte de service qui a été ajouté au déploiement plus tôt sera utilisé pour autoriser l’accès au déploiement auquel il est attaché. Dans cet exemple particulier, la section `rules` définit le type de trafic HTTP autorisé sur la connexion. Vous pouvez configurer des modèles d’expression régulière affinés pour que les en-têtes HTTP soient spécifiques au trafic autorisé via HTTP. La section `sources` correspond au service à l’origine des communications. Cette spécification indique que bookbuyer doit communiquer avec bookstore.

La ressource HTTPRouteGroup se compose d’une correspondance ou d’un tableau de correspondances d’informations d’en-tête HTTP et est obligatoire pour la spécification TrafficTarget. Dans l’exemple ci-dessous, vous pouvez voir que HTTPRouteGroup autorise trois actions HTTP, à savoir deux GET et une POST.

```HTTPRouteGroup Example Spec
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookstore-service-routes
  namespace: bookstore
spec:
  matches:
  - name: books-bought
    pathRegex: /books-bought
    methods:
    - GET
    headers:
    - "user-agent": ".*-http-client/*.*"
    - "client-app": "bookbuyer"
  - name: buy-a-book
    pathRegex: ".*a-book.*new"
    methods:
    - GET
  - name: update-books-bought
    pathRegex: /update-books-bought
    methods:
    - POST
```

Si vous ne connaissez pas le type de trafic HTTP que votre application frontale envoie aux autres niveaux de l’application, étant donné que la spécification TrafficTarget nécessite une règle, vous pouvez créer l’équivalent d’une règle « allow all » en utilisant la spécification ci-dessous pour HTTPRouteGroup.

```HTTPRouteGroup Allow All Example
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: allow-all
  namespace: yournamespace
spec:
  matches:
  - name: allow-all
    pathRegex: '.*'
    methods: ["GET","PUT","POST","DELETE","PATCH"]
```

Une fois que vous avez configuré vos spécifications TrafficTarget et HTTPRouteGroup, vous pouvez les réunir en un seul YAML et les déployer. Vous trouverez ci-dessous la configuration d’un exemple de bookstore.

```Bookstore Example TrafficTarget and HTTPRouteGroup configuration
kubectl apply -f - <<EOF
---
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore-v1
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
---
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookstore-service-routes
  namespace: bookstore
spec:
  matches:
  - name: books-bought
    pathRegex: /books-bought
    methods:
    - GET
    headers:
    - "user-agent": ".*-http-client/*.*"
    - "client-app": "bookbuyer"
  - name: buy-a-book
    pathRegex: ".*a-book.*new"
    methods:
    - GET
  - name: update-books-bought
    pathRegex: /update-books-bought
    methods:
    - POST
EOF
```

Rendez-vous sur le site de [SMI](https://smi-spec.io/) pour obtenir des informations plus détaillées sur la spécification.

### <a name="manage-the-applications-namespace-with-osm"></a>Gérer l’espace de noms de l’application avec OSM

Ensuite, nous allons configurer OSM pour qu’il gère l’espace de noms et redémarrer les déploiements pour que l’application soit injectée dans le proxy side-car Envoy.

Exécutez la commande suivante pour configurer l’espace de noms `azure-vote` afin qu’il soit géré par OSM.

```azurecli-interactive
osm namespace add azure-vote
```

```Output
Namespace [azure-vote] successfully added to mesh [osm]
```

Ensuite, redémarrez les déploiements `azure-vote-front` et `azure-vote-back` avec les commandes suivantes.

```azurecli-interactive
kubectl rollout restart deployment azure-vote-front -n azure-vote
kubectl rollout restart deployment azure-vote-back -n azure-vote
```

```Output
deployment.apps/azure-vote-front restarted
deployment.apps/azure-vote-back restarted
```

Si nous regardons les pods de l’espace de noms `azure-vote`, nous verrons que l’étape **READY** de `azure-vote-front` et `azure-vote-back` est 2/2, ce qui signifie que l’application a été injectée dans le proxy side-car Envoy.

## <a name="tutorial-deploy-an-application-managed-by-open-service-mesh-osm-with-nginx-ingress"></a>Tutoriel : Déployer une application gérée par Open Service Mesh (OSM) avec entrée NGINX

Open Service Mesh (OSM) est un maillage de services natif Cloud léger et extensible qui permet aux utilisateurs de gérer et de sécuriser uniformément les environnements de microservices hautement dynamiques et de bénéficier de fonctionnalités d’observabilité prêtes à l’emploi.

Ce didacticiel présente les procédures suivantes :

> [!div class="checklist"]
>
> - Afficher la configuration actuelle du cluster OSM
> - Créer les espaces de noms pour qu’OSM puisse gérer les applications déployées dans les espaces de noms
> - Intégrer les espaces de noms à gérer par OSM
> - Déployer l’exemple d’application
> - Vérifier l’exécution de l’application dans le cluster AKS
> - Créer un contrôleur d’entrée NGINX utilisé pour l’application
> - Exposer un service via l’entrée d’Azure Application Gateway sur Internet

### <a name="before-you-begin"></a>Avant de commencer

Les étapes détaillées dans cet article supposent que vous avez créé un cluster AKS (Kubernetes `1.19+` et versions ultérieures avec Kubernetes RBAC activé), que vous avez établi une connexion `kubectl` avec le cluster (si vous avez besoin d’aide pour l’un de ces éléments, consultez le [démarrage rapide d’AKS](./kubernetes-walkthrough.md)) et que vous avez installé le module complémentaire AKS OSM.

Les ressources suivantes doivent être installées :

- Azure CLI, version 2.20.0 ou ultérieure
- L’extension `aks-preview` version 0.5.5 ou ultérieure
- OSM, version 0.8.0 ou ultérieure
- apt-get install jq

### <a name="view-and-verify-the-current-osm-cluster-configuration"></a>Afficher et vérifier la configuration actuelle du cluster OSM

Une fois que le module complémentaire OSM pour AKS a été activé sur le cluster AKS, vous pouvez afficher les paramètres de configuration actuels dans le ConfigMap Kubernetes osm-config. Exécutez la commande suivante pour afficher les propriétés du ConfigMap :

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

La sortie indique la configuration actuelle d’OSM pour le cluster.

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254,168.63.129.16,20.193.57.43",
  "permissive_traffic_policy_mode": "false",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

Notez que le paramètre **permissive_traffic_policy_mode** est configuré sur **true**. Le mode de stratégie de trafic permissif dans OSM est un mode dans lequel l’application de la stratégie de trafic [SMI](https://smi-spec.io/) est ignorée. Dans ce mode, OSM découvre automatiquement les services qui font partie du maillage de services et programme des règles de stratégie de trafic sur chaque side-car de proxy Envoy pour pouvoir communiquer avec ces services.

### <a name="create-namespaces-for-the-application"></a>Créer des espaces de noms pour l’application

Dans ce tutoriel, nous allons utiliser l’application bookstore OSM qui comporte les composants d’application suivants :

- bookbuyer
- bookthief
- bookstore
- bookwarehouse

Créez des espaces de noms pour chacun de ces composants d’application.

```azurecli-interactive
for i in bookstore bookbuyer bookthief bookwarehouse; do kubectl create ns $i; done
```

Vous devez normalement voir la sortie suivante :

```Output
namespace/bookstore created
namespace/bookbuyer created
namespace/bookthief created
namespace/bookwarehouse created
```

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>Intégrer les espaces de noms à gérer par OSM

L’ajout des espaces de noms au maillage OSM permettra au contrôleur OSM d’injecter automatiquement votre application dans les conteneurs du proxy side-car Envoy. Exécutez la commande suivante pour intégrer les espaces de noms de l’application bookstore OSM.

```azurecli-interactive
osm namespace add bookstore bookbuyer bookthief bookwarehouse
```

Vous devez normalement voir la sortie suivante :

```Output
Namespace [bookstore] successfully added to mesh [osm]
Namespace [bookbuyer] successfully added to mesh [osm]
Namespace [bookthief] successfully added to mesh [osm]
Namespace [bookwarehouse] successfully added to mesh [osm]
```

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>Déployer l’application Bookstore sur le cluster AKS

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookwarehouse.yaml
```

Toutes les sorties du déploiement sont résumées ci-dessous.

```Output
serviceaccount/bookbuyer created
service/bookbuyer created
deployment.apps/bookbuyer created

serviceaccount/bookthief created
service/bookthief created
deployment.apps/bookthief created

service/bookstore created
serviceaccount/bookstore created
deployment.apps/bookstore created

serviceaccount/bookwarehouse created
service/bookwarehouse created
deployment.apps/bookwarehouse created
```

### <a name="update-the-bookbuyer-service"></a>Mettre à jour le service Bookbuyer

Mettez à jour le service bookbuyer avec la configuration correcte du port entrant à l’aide du manifeste de service suivant.

```azurecli-interactive
kubectl apply -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  name: bookbuyer
  namespace: bookbuyer
  labels:
    app: bookbuyer
spec:
  ports:
  - port: 14001
    name: inbound-port
  selector:
    app: bookbuyer
EOF
```

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Vérifier l’exécution de l’application Bookstore dans le cluster AKS

Pour l’instant, nous avons déployé l’application multiconteneur bookstore, mais elle n’est accessible qu’à partir du cluster AKS. Plus tard, nous ajouterons le contrôleur d’entrée d’Azure Application Gateway pour exposer l’application en dehors du cluster AKS. Pour vérifier que l’application s’exécute dans le cluster, nous allons utiliser une redirection de port pour afficher l’interface utilisateur du composant bookbuyer.

Commençons par récupérer le nom du pod de bookbuyer.

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Le résultat ressemble à ce qui suit. Un nom unique est ajouté à votre pod de bookbuyer.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Une fois que nous avons le nom du pod, nous pouvons utiliser la commande port-forward pour configurer un tunnel entre notre système local et l’application dans le cluster AKS. Exécutez la commande suivante pour configurer la redirection de port pour le port 8080 du système local. Utilisez à nouveau le nom du pod de bookbuyer que vous avez spécifié.

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

La sortie doit ressembler à ce qui suit.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

Pendant que la session de redirection de port est en place, accédez à l’URL `http://localhost:8080` à partir d’un navigateur. Vous devriez maintenant être en mesure de voir l’interface utilisateur de l’application Bookbuyer dans le navigateur, comme dans l’image ci-dessous.

![Image de l’interface utilisateur de l’application bookbuyer OSM pour NGINX](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

### <a name="create-an-nginx-ingress-controller-in-azure-kubernetes-service-aks"></a>Créer un contrôleur d’entrée NGINX dans Azure Kubernetes Service (AKS)

Un contrôleur d’entrée est un logiciel qui fournit un proxy inversé, un routage du trafic configurable et un arrêt TLS pour les services Kubernetes. Des ressources d’entrée Kubernetes sont utilisées pour configurer les règles d’entrée et les itinéraires des services Kubernetes individuels. En utilisant un contrôleur d’entrée et des règles d’entrée, une seule adresse IP peut être utilisée pour router le trafic vers plusieurs services dans un cluster Kubernetes.

Nous utiliserons le contrôleur d’entrée pour exposer l’application gérée par OSM à Internet. Pour créer le contrôleur d’entrée, utilisez Helm pour installer nginx-ingress. Pour renforcer la redondance, deux réplicas des contrôleurs d’entrée NGINX sont déployés avec le paramètre `--set controller.replicaCount`. Pour tirer pleinement parti de l’exécution de réplicas des contrôleurs d’entrée, vérifiez que votre cluster AKS comprend plusieurs nœuds.

Le contrôleur d’entrée doit également être planifié sur un nœud Linux. Les nœuds Windows Server ne doivent pas exécuter le contrôleur d’entrée. Un sélecteur de nœud est spécifié en utilisant le paramètre `--set nodeSelector` pour que le planificateur Kubernetes exécute le contrôleur d’entrée NGINX sur un nœud Linux.

> [!TIP]
> L’exemple suivant crée un espace de noms Kubernetes pour les ressources d’entrée _ingress-basic_. Spécifiez un espace de noms de votre propre environnement, si besoin.

```azurecli-interactive
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Update the helm repo(s)
helm repo update

# Use Helm to deploy an NGINX ingress controller in the ingress-basic namespace
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    --set controller.replicaCount=1 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Lorsque le service équilibreur de charge Kubernetes est créé pour le contrôleur d’entrée NGINX, votre adresse IP publique dynamique est affectée, comme indiqué dans l’exemple de sortie suivant :

```Output
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Aucune règle d’entrée n’a encore été créée. Par conséquent, la page 404 par défaut du contrôleur d’entrée NGINX s’affiche si vous accédez à l’adresse IP interne. Les étapes suivantes permettent de configurer les règles d’entrée.

### <a name="expose-the-bookbuyer-service-to-the-internet"></a>Exposer le service bookbuyer à Internet

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: bookbuyer-ingress
  namespace: bookbuyer
  annotations:
    kubernetes.io/ingress.class: nginx

spec:

  rules:
    - host: bookbuyer.contoso.com
      http:
        paths:
        - path: /
          backend:
            serviceName: bookbuyer
            servicePort: 14001

  backend:
    serviceName: bookbuyer
    servicePort: 14001
EOF
```

Vous devez normalement voir la sortie suivante :

```Output
Warning: extensions/v1beta1 Ingress is deprecated in v1.14+, unavailable in v1.22+; use networking.k8s.io/v1 Ingress
ingress.extensions/bookbuyer-ingress created
```

### <a name="view-the-nginx-logs"></a>Afficher les journaux NGINX

```azurecli-interactive
POD=$(kubectl get pods -n ingress-basic | grep 'nginx-ingress' | awk '{print $1}')

kubectl logs $POD -n ingress-basic -f
```

La sortie indique l’état du contrôleur d’entrée NGINX quand la règle d’entrée a bien été appliquée :

```Output
I0321 <date>       6 event.go:282] Event(v1.ObjectReference{Kind:"Pod", Namespace:"ingress-basic", Name:"nginx-ingress-ingress-nginx-controller-54cf6c8bf4-jdvrw", UID:"3ebbe5e5-50ef-481d-954d-4b82a499ebe1", APIVersion:"v1", ResourceVersion:"3272", FieldPath:""}): type: 'Normal' reason: 'RELOAD' NGINX reload triggered due to a change in configuration
I0321 <date>        6 event.go:282] Event(v1.ObjectReference{Kind:"Ingress", Namespace:"bookbuyer", Name:"bookbuyer-ingress", UID:"e1018efc-8116-493c-9999-294b4566819e", APIVersion:"networking.k8s.io/v1beta1", ResourceVersion:"5460", FieldPath:""}): type: 'Normal' reason: 'Sync' Scheduled for sync
I0321 <date>        6 controller.go:146] "Configuration changes detected, backend reload required"
I0321 <date>        6 controller.go:163] "Backend successfully reloaded"
I0321 <date>        6 event.go:282] Event(v1.ObjectReference{Kind:"Pod", Namespace:"ingress-basic", Name:"nginx-ingress-ingress-nginx-controller-54cf6c8bf4-jdvrw", UID:"3ebbe5e5-50ef-481d-954d-4b82a499ebe1", APIVersion:"v1", ResourceVersion:"3272", FieldPath:""}): type: 'Normal' reason: 'RELOAD' NGINX reload triggered due to a change in configuration
```

### <a name="view-the-nginx-services-and-bookbuyer-service-externally"></a>Afficher les services NGINX et le service bookbuyer en externe

```azurecli-interactive
kubectl get services -n ingress-basic
```

```Output
NAME                                               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
nginx-ingress-ingress-nginx-controller             LoadBalancer   10.0.100.23   20.193.1.74   80:31742/TCP,443:32683/TCP   4m15s
nginx-ingress-ingress-nginx-controller-admission   ClusterIP      10.0.163.98   <none>        443/TCP                      4m15s
```

Puisque le nom d’hôte dans le manifeste d’entrée est un nom fictif utilisé à des fins de test, le nom DNS n’est pas disponible sur Internet. Nous pouvons également utiliser le programme curl et transmettre l’en-tête de l’hôte à l’IP publique de NGINX et recevoir un code 200 qui nous connecte correctement au service bookbuyer.

```azurecli-interactive
curl -H 'Host: bookbuyer.contoso.com' http://EXTERNAL-IP/
```

Vous devez normalement voir la sortie suivante :

```Output
<!doctype html>
<html itemscope="" itemtype="http://schema.org/WebPage" lang="en">
  <head>
      <meta content="Bookbuyer" name="description">
      <meta content="text/html; charset=UTF-8" http-equiv="Content-Type">
      <title>Bookbuyer</title>
      <style>
        #navbar {
            width: 100%;
            height: 50px;
            display: table;
            border-spacing: 0;
            white-space: nowrap;
            line-height: normal;
            background-color: #0078D4;
            background-position: left top;
            background-repeat-x: repeat;
            background-image: none;
            color: white;
            font: 2.2em "Fira Sans", sans-serif;
        }
        #main {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.8em "Fira Sans", sans-serif;
        }
        li {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.2em "Consolas", sans-serif;
        }
      </style>
      <script>
        setTimeout(function(){window.location.reload(1);}, 1500);
      </script>
  </head>
  <body bgcolor="#fff">
    <div id="navbar">
      &#128214; Bookbuyer
    </div>
    <div id="main">
      <ul>
        <li>Total books bought: <strong>1833</strong>
          <ul>
            <li>from bookstore V1: <strong>277</strong>
            <li>from bookstore V2: <strong>1556</strong>
          </ul>
        </li>
      </ul>
    </div>

    <br/><br/><br/><br/>
    <br/><br/><br/><br/>
    <br/><br/><br/><br/>

    Current Time: <strong>Fri, 26 Mar 2021 15:02:53 UTC</strong>
  </body>
</html>
```

## <a name="tutorial-deploy-an-application-managed-by-open-service-mesh-osm-using-azure-application-gateway-ingress-aks-add-on"></a>Tutoriel : Déployer une application gérée par Open Service Mesh (OSM) à l’aide du module complémentaire AKS d’entrée Azure Application Gateway

Open Service Mesh (OSM) est un maillage de services natif Cloud léger et extensible qui permet aux utilisateurs de gérer et de sécuriser uniformément les environnements de microservices hautement dynamiques et de bénéficier de fonctionnalités d’observabilité prêtes à l’emploi.

Ce didacticiel présente les procédures suivantes :

> [!div class="checklist"]
>
> - Afficher la configuration actuelle du cluster OSM
> - Créer les espaces de noms pour qu’OSM puisse gérer les applications déployées dans les espaces de noms
> - Intégrer les espaces de noms à gérer par OSM
> - Déployer l’exemple d’application
> - Vérifier l’exécution de l’application dans le cluster AKS
> - Créer une instance d’Azure Application Gateway à utiliser comme contrôleur d’entrée pour l’application
> - Exposer un service via l’entrée d’Azure Application Gateway sur Internet

### <a name="before-you-begin"></a>Avant de commencer

Les étapes détaillées dans cet article supposent que vous avez créé un cluster AKS (Kubernetes `1.19+` et versions ultérieures avec Kubernetes RBAC activé), que vous avez établi une connexion `kubectl` avec le cluster (si vous avez besoin d’aide pour l’un de ces éléments, consultez le [démarrage rapide d’AKS](./kubernetes-walkthrough.md)), que vous avez installé le module complémentaire AKS OSM et que vous allez créer une nouvelle instance d’Azure Application Gateway pour l’entrée.

Les ressources suivantes doivent être installées :

- Azure CLI, version 2.20.0 ou ultérieure
- L’extension `aks-preview` version 0.5.5 ou ultérieure
- Un cluster AKS version 1.19 ou ultérieure utilisant la mise en réseau d’Azure CNI (attachée à un réseau virtuel Azure)
- OSM, version 0.8.0 ou ultérieure
- apt-get install jq

### <a name="view-and-verify-the-current-osm-cluster-configuration"></a>Afficher et vérifier la configuration actuelle du cluster OSM

Une fois que le module complémentaire OSM pour AKS a été activé sur le cluster AKS, vous pouvez afficher les paramètres de configuration actuels dans le ConfigMap Kubernetes osm-config. Exécutez la commande suivante pour afficher les propriétés du ConfigMap :

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

La sortie indique la configuration actuelle d’OSM pour le cluster.

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254,168.63.129.16,20.193.57.43",
  "permissive_traffic_policy_mode": "false",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

Notez que le paramètre **permissive_traffic_policy_mode** est configuré sur **true**. Le mode de stratégie de trafic permissif dans OSM est un mode dans lequel l’application de la stratégie de trafic [SMI](https://smi-spec.io/) est ignorée. Dans ce mode, OSM découvre automatiquement les services qui font partie du maillage de services et programme des règles de stratégie de trafic sur chaque side-car de proxy Envoy pour pouvoir communiquer avec ces services.

### <a name="create-namespaces-for-the-application"></a>Créer des espaces de noms pour l’application

Dans ce tutoriel, nous allons utiliser l’application bookstore OSM qui comporte les composants d’application suivants :

- bookbuyer
- bookthief
- bookstore
- bookwarehouse

Créez des espaces de noms pour chacun de ces composants d’application.

```azurecli-interactive
for i in bookstore bookbuyer bookthief bookwarehouse; do kubectl create ns $i; done
```

Vous devez normalement voir la sortie suivante :

```Output
namespace/bookstore created
namespace/bookbuyer created
namespace/bookthief created
namespace/bookwarehouse created
```

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>Intégrer les espaces de noms à gérer par OSM

En ajoutant les espaces de noms au maillage OSM, vous permettez au contrôleur OSM d’injecter automatiquement votre application dans les conteneurs du proxy side-car Envoy. Exécutez la commande suivante pour intégrer les espaces de noms de l’application bookstore OSM.

```azurecli-interactive
osm namespace add bookstore bookbuyer bookthief bookwarehouse
```

Vous devez normalement voir la sortie suivante :

```Output
Namespace [bookstore] successfully added to mesh [osm]
Namespace [bookbuyer] successfully added to mesh [osm]
Namespace [bookthief] successfully added to mesh [osm]
Namespace [bookwarehouse] successfully added to mesh [osm]
```

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>Déployer l’application Bookstore sur le cluster AKS

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookwarehouse.yaml
```

Toutes les sorties du déploiement sont résumées ci-dessous.

```Output
serviceaccount/bookbuyer created
service/bookbuyer created
deployment.apps/bookbuyer created

serviceaccount/bookthief created
service/bookthief created
deployment.apps/bookthief created

service/bookstore created
serviceaccount/bookstore created
deployment.apps/bookstore created

serviceaccount/bookwarehouse created
service/bookwarehouse created
deployment.apps/bookwarehouse created
```

### <a name="update-the-bookbuyer-service"></a>Mettre à jour le service Bookbuyer

Mettez à jour le service bookbuyer avec la configuration correcte du port entrant à l’aide du manifeste de service suivant.

```azurecli-interactive
kubectl apply -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  name: bookbuyer
  namespace: bookbuyer
  labels:
    app: bookbuyer
spec:
  ports:
  - port: 14001
    name: inbound-port
  selector:
    app: bookbuyer
EOF
```

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Vérifier l’exécution de l’application Bookstore dans le cluster AKS

Pour l’instant, nous avons déployé l’application multiconteneur bookstore, mais elle n’est accessible qu’à partir du cluster AKS. Plus tard, nous ajouterons le contrôleur d’entrée d’Azure Application Gateway pour exposer l’application en dehors du cluster AKS. Pour vérifier que l’application s’exécute dans le cluster, nous allons utiliser une redirection de port pour afficher l’interface utilisateur du composant bookbuyer.

Commençons par récupérer le nom du pod de bookbuyer.

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Le résultat ressemble à ce qui suit. Un nom unique est ajouté à votre pod de bookbuyer.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Une fois que nous avons le nom du pod, nous pouvons utiliser la commande port-forward pour configurer un tunnel entre notre système local et l’application dans le cluster AKS. Exécutez la commande suivante pour configurer la redirection de port pour le port 8080 du système local. Utilisez à nouveau le nom du pod de bookbuyer que vous avez spécifié.

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

La sortie doit ressembler à ce qui suit.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

Pendant que la session de redirection de port est en place, accédez à l’URL `http://localhost:8080` à partir d’un navigateur. Vous devriez maintenant être en mesure de voir l’interface utilisateur de l’application Bookbuyer dans le navigateur, comme dans l’image ci-dessous.

![Image de l’interface utilisateur de l’application bookbuyer OSM pour App Gateway](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

### <a name="create-an-azure-application-gateway-to-expose-the-bookbuyer-application-outside-the-aks-cluster"></a>Créer une instance d’Azure Application Gateway pour exposer l’application bookbuyer en dehors du cluster AKS

> [!NOTE]
> Les instructions suivantes permettent de créer une nouvelle instance d’Azure Application Gateway qui sera utilisée pour l’entrée. Si vous avez déjà une instance d’Azure Application Gateway que vous souhaitez utiliser, passez directement à la section relative à l’activation du module complémentaire Application Gateway Ingress Controller.

#### <a name="deploy-a-new-application-gateway"></a>Déployer une nouvelle passerelle Application Gateway

> [!NOTE]
> Nous faisons référence à la documentation existante pour activer le module complémentaire Application Gateway Ingress Controller pour un cluster AKS existant. Certaines modifications ont été apportées pour s’adapter à la documentation d’OSM. Vous trouverez une documentation plus détaillée sur le sujet [ici](https://docs.microsoft.com/azure/application-gateway/tutorial-ingress-controller-add-on-existing).

Vous allez maintenant déployer une nouvelle passerelle Application Gateway afin de simuler la présence d’une passerelle Application Gateway existante que vous comptez ensuite utiliser pour équilibrer la charge du trafic vers votre cluster AKS, _myCluster_. La passerelle Application Gateway s’appellera _myApplicationGateway_, mais vous devez d’abord créer une ressource IP publique, nommée _myPublicIp_ ainsi qu’un autre réseau virtuel appelé _myVnet_ avec l’espace d’adressage 11.0.0.0/8 et un sous-réseau avec l’espace d’adressage 11.1.0.0/16 appelé _mySubnet_. Vous devez aussi déployer votre passerelle Application Gateway dans _mySubnet_ avec _myPublicIp_.

Quand vous utilisez un cluster AKS et une passerelle Application Gateway dans des réseaux virtuels distincts, les espaces d’adressage de ces deux réseaux ne doivent pas se chevaucher. L’espace d’adressage par défaut dans lequel un cluster AKS est déployé est 10.0.0.0/8. Nous définissons donc le préfixe d’adresse du réseau virtuel Application Gateway sur 11.0.0.0/8.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2
az network public-ip create -n myPublicIp -g MyResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16
az network application-gateway create -n myApplicationGateway -l eastus2 -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> Le complément Application Gateway Ingress Controller (AGIC) prend en charge **uniquement** les références SKU Application Gateway v2 (Standard et WAF), **pas** les références SKU Application Gateway v1.

#### <a name="enable-the-agic-add-on-for-an-existing-aks-cluster-through-azure-cli"></a>Activer le module complémentaire AGIC pour un cluster AKS existant via Azure CLI

Si vous souhaitez continuer à utiliser Azure CLI, vous pouvez continuer à activer le module complémentaire AGIC dans le cluster AKS que vous avez créé, _myCluster_, et spécifier le module complémentaire AGIC pour qu’il utilise la passerelle applicative existante que vous avez créée, _myApplicationGateway_.

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id")
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

Vous pouvez vérifier que le module complémentaire Azure Application Gateway AKS a été activé à l’aide de la commande suivante.

```azurecli-interactive
az aks list -g osm-aks-rg -o json | jq -r .[].addonProfiles.ingressApplicationGateway.enabled
```

La sortie de cette commande doit être `true`.

#### <a name="peer-the-two-virtual-networks-together"></a>Appairer les deux réseaux virtuels

Étant donné que nous avons déployé le cluster AKS dans son propre réseau virtuel et la passerelle Application Gateway dans un autre réseau virtuel, vous devez appairer les deux réseaux virtuels afin de rendre possible le trafic entre la passerelle Application Gateway et les pods dans le cluster. L’appairage des deux réseaux virtuels s’effectue en exécutant la commande Azure CLI deux fois pour garantir une connexion bidirectionnelle. La première commande crée une connexion d’appairage du réseau virtuel Application Gateway vers le réseau virtuel AKS. La seconde commande crée une connexion d’appairage dans l’autre direction.

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query "nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```

### <a name="expose-the-bookbuyer-service-to-the-internet"></a>Exposer le service bookbuyer à Internet

Appliquez le manifeste d’entrée suivant au cluster AKS pour exposer le service bookbuyer à Internet par le biais d’Azure Application Gateway.

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: bookbuyer-ingress
  namespace: bookbuyer
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway

spec:

  rules:
    - host: bookbuyer.contoso.com
      http:
        paths:
        - path: /
          backend:
            serviceName: bookbuyer
            servicePort: 14001

  backend:
    serviceName: bookbuyer
    servicePort: 14001
EOF
```

Vous devez voir la sortie suivante

```Output
Warning: extensions/v1beta1 Ingress is deprecated in v1.14+, unavailable in v1.22+; use networking.k8s.io/v1 Ingress
ingress.extensions/bookbuyer-ingress created
```

Puisque le nom d’hôte dans le manifeste d’entrée est un nom fictif utilisé à des fins de test, le nom DNS n’est pas disponible sur Internet. Nous pouvons également utiliser le programme curl et transmettre l’en-tête de l’hôte à l’IP publique d’Azure Application Gateway et recevoir un code 200 qui nous connecte correctement au service bookbuyer.

```azurecli-interactive
appGWPIP=$(az network public-ip show -g MyResourceGroup -n myPublicIp -o tsv --query "ipAddress")
curl -H 'Host: bookbuyer.contoso.com' http://$appGWPIP/
```

Vous devez voir la sortie suivante

```Output
<!doctype html>
<html itemscope="" itemtype="http://schema.org/WebPage" lang="en">
  <head>
      <meta content="Bookbuyer" name="description">
      <meta content="text/html; charset=UTF-8" http-equiv="Content-Type">
      <title>Bookbuyer</title>
      <style>
        #navbar {
            width: 100%;
            height: 50px;
            display: table;
            border-spacing: 0;
            white-space: nowrap;
            line-height: normal;
            background-color: #0078D4;
            background-position: left top;
            background-repeat-x: repeat;
            background-image: none;
            color: white;
            font: 2.2em "Fira Sans", sans-serif;
        }
        #main {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.8em "Fira Sans", sans-serif;
        }
        li {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.2em "Consolas", sans-serif;
        }
      </style>
      <script>
        setTimeout(function(){window.location.reload(1);}, 1500);
      </script>
  </head>
  <body bgcolor="#fff">
    <div id="navbar">
      &#128214; Bookbuyer
    </div>
    <div id="main">
      <ul>
        <li>Total books bought: <strong>5969</strong>
          <ul>
            <li>from bookstore V1: <strong>277</strong>
            <li>from bookstore V2: <strong>5692</strong>
          </ul>
        </li>
      </ul>
    </div>

    <br/><br/><br/><br/>
    <br/><br/><br/><br/>
    <br/><br/><br/><br/>

    Current Time: <strong>Fri, 26 Mar 2021 16:34:30 UTC</strong>
  </body>
</html>
```

### <a name="troubleshooting"></a>Résolution des problèmes

- [Documentation de résolution des problèmes relatifs à AGIC](https://docs.microsoft.com/azure/application-gateway/ingress-controller-troubleshoot)
- [Des outils de dépannage supplémentaires sont disponibles sur le référentiel GitHub d’AGIC.](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/docs/troubleshootings/troubleshooting-installing-a-simple-application.md)

## <a name="open-service-mesh-osm-monitoring-and-observability-using-azure-monitor-and-applications-insights"></a>Surveillance et observabilité d’Open Service Mesh (OSM) à l’aide d’Azure Monitor et Applications Insights

Azure Monitor et Azure Application Insights vous aident à optimiser la disponibilité et les performances de vos applications et services en fournissant une solution complète pour collecter, analyser et utiliser la télémétrie de vos environnements cloud et locaux.

Le module complémentaire OSM AKS sera profondément intégré à ces deux services Azure et fournira une expérience Azure transparente permettant de visualiser les indicateurs clés de performance essentiels fournis par les métriques d’OSM et d’y répondre. Pour plus d’informations sur l’activation et la configuration de ces services pour le module complémentaire OSM AKS, consultez la page [Azure Monitor pour OSM](https://aka.ms/azmon/osmpreview).

## <a name="tutorial-manually-deploy-prometheus-grafana-and-jaeger-to-view-open-service-mesh-osm-metrics-for-observability"></a>Tutoriel : Déployer manuellement Prometheus, Grafana et Jaeger afin d’afficher les métriques d’Open Service Mesh (OSM) pour l’observabilité

> [!WARNING]
> L’installation de Prometheus, Grafana et Jaeger est fournie à titre d’aide générale pour montrer la façon dont ces outils peuvent être utilisés pour visualiser les données des métriques d’OSM. L’aide à l’installation ne doit pas être utilisée pour une configuration de production. Reportez-vous à la documentation de chaque outil pour savoir comment paramétrer au mieux leur installation selon vos besoins. Le plus remarquable est l’absence de stockage persistant, ce qui signifie que toutes les données sont perdues une fois que des pods Prometheus, Grafana ou Jaeger sont terminés.

Open Service Mesh (OSM) génère des métriques détaillées relatives à l’ensemble du trafic au sein du maillage. Ces métriques donnent un aperçu du comportement des applications dans le maillage, ce qui aide les utilisateurs à dépanner, à gérer et à analyser leurs applications.

À l’heure actuelle, OSM collecte les métriques directement à partir des proxys side-car (Envoy). OSM fournit de nombreuses métriques du trafic entrant et sortant pour tous les services du maillage. Grâce à ces métriques, l’utilisateur peut obtenir des informations sur le volume global du trafic, les erreurs dans le trafic et le temps de réponse aux demandes.

OSM utilise Prometheus pour recueillir et stocker des métriques et des statistiques cohérentes sur le trafic pour toutes les applications exécutées dans le maillage. Prometheus est une boîte à outils de surveillance et d’alerte open source qui est couramment utilisée, notamment sur les environnements Kubernetes et de maillage de services.

Chaque application faisant partie du maillage s’exécute dans un pod contenant un side-car Envoy qui expose les métriques (métriques du proxy) au format Prometheus. En outre, chaque pod faisant partie du maillage possède des annotations Prometheus, ce qui permet au serveur Prometheus de scrapper l’application de manière dynamique. Ce mécanisme active automatiquement le scraping des métriques chaque fois qu’un nouvel espace de noms/pod/service est ajouté au maillage.

Les métriques d’OSM peuvent être visualisées à l’aide de Grafana, un logiciel de visualisation et d’analyse open source. Ce logiciel vous permet d’interroger, de visualiser, d’alerter et d’explorer vos métriques.

Ce didacticiel présente les procédures suivantes :

> [!div class="checklist"]
>
> - Créer et déployer une instance Prometheus
> - Configurer OSM pour permettre le scraping de Prometheus
> - Mettre à jour le Configmap Prometheus
> - Créer et déployer une instance Grafana
> - Configurer Grafana avec la source de données Prometheus
> - Importer le tableau de bord OSM pour Grafana
> - Créer et déployer une instance Jaeger
> - Configurer le traçage de Jaeger pour OSM

### <a name="deploy-and-configure-a-prometheus-instance-for-osm"></a>Déployer et configurer une instance Prometheus pour OSM

Nous allons utiliser Helm pour déployer l’instance Prometheus. Exécutez les commandes suivantes pour installer Prometheus via Helm :

```azurecli-interactive
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install stable prometheus-community/prometheus
```

Si l’installation s’est déroulée avec succès, vous devriez voir un résultat similaire à celui ci-dessous. Notez le port du serveur Prometheus et le nom DNS du cluster. Ces informations seront utilisées ultérieurement pour configurer Prometheus comme source de données pour Grafana.

```Output
NAME: stable
LAST DEPLOYED: Fri Mar 26 13:34:51 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The Prometheus server can be accessed via port 80 on the following DNS name from within your cluster:
stable-prometheus-server.default.svc.cluster.local


Get the Prometheus server URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9090


The Prometheus alertmanager can be accessed via port 80 on the following DNS name from within your cluster:
stable-prometheus-alertmanager.default.svc.cluster.local


Get the Alertmanager URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=alertmanager" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9093
#################################################################################
######   WARNING: Pod Security Policy has been moved to a global property.  #####
######            use .Values.podSecurityPolicy.enabled with pod-based      #####
######            annotations                                               #####
######            (e.g. .Values.nodeExporter.podSecurityPolicy.annotations) #####
#################################################################################


The Prometheus PushGateway can be accessed via port 9091 on the following DNS name from within your cluster:
stable-prometheus-pushgateway.default.svc.cluster.local


Get the PushGateway URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=pushgateway" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9091

For more information on running Prometheus, visit:
https://prometheus.io/
```

#### <a name="configure-osm-to-allow-prometheus-scraping"></a>Configurer OSM pour permettre le scraping de Prometheus

Pour vous assurer que les composants d’OSM sont configurés pour les scrapes de Prometheus, nous voulons vérifier la configuration de **prometheus_scraping** située dans le fichier config osm-config. Visualisez la configuration avec la commande suivante :

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data.prometheus_scraping'
```

La sortie de la commande précédente doit renvoyer `true` si OSM est configuré pour le scraping de Prometheus. Si la valeur renvoyée est `false`, vous devez mettre à jour la configuration pour qu’elle soit `true`. Exécutez la commande suivante pour **activer** le scraping de Prometheus par OSM :

```azurecli-interactive
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"prometheus_scraping":"true"}}'
```

Vous devez voir la sortie suivante.

```Output
configmap/osm-config patched
```

#### <a name="update-the-prometheus-configmap"></a>Mettre à jour le Configmap Prometheus

L’installation par défaut de Prometheus contiendra deux configmaps Kubernetes. Vous pouvez afficher la liste des configmaps de Prometheus à l’aide de la commande suivante.

```azurecli-interactive
kubectl get configmap | grep prometheus
```

```Output
stable-prometheus-alertmanager   1      4h34m
stable-prometheus-server         5      4h34m
```

Nous devrons remplacer la configuration prometheus.yml située dans le configmap **stable-prometheus-server** par la configuration OSM suivante. Il existe plusieurs techniques de modification de fichiers pour accomplir cette tâche. Une méthode simple et sûre consiste à exporter le configmap, à en créer une copie pour la sauvegarde, puis à le modifier à l’aide d’un éditeur tel que Visual Studio Code.

> [!NOTE]
> Si Visual Studio Code n’est pas installé, vous pouvez le télécharger [ici](https://code.visualstudio.com/Download) et l’installer.

Exportons d’abord le configmap **stable-prometheus-server** et faisons-en une copie pour la sauvegarde.

```azurecli-interactive
kubectl get configmap stable-prometheus-server -o yaml > cm-stable-prometheus-server.yml
cp cm-stable-prometheus-server.yml cm-stable-prometheus-server.yml.copy
```

Ensuite, ouvrons le fichier à l’aide de Visual Studio Code pour le modifier.

```azurecli-interactive
code cm-stable-prometheus-server.yml
```

Une fois que vous avez ouvert le configmap dans l’éditeur Visual Studio Code, remplacez le fichier prometheus.yml par la configuration OSM ci-dessous, puis enregistrez le fichier.

> [!WARNING]
> Il est extrêmement important de veiller à conserver la structure de mise en retrait du fichier YAML. Toute modification apportée à la structure du fichier YAML peut rendre impossible d’appliquer à nouveau le configmap.

```OSM Prometheus Configmap Configuration
prometheus.yml: |
    global:
      scrape_interval: 10s
      scrape_timeout: 10s
      evaluation_interval: 1m

    scrape_configs:
      - job_name: 'kubernetes-apiservers'
        kubernetes_sd_configs:
        - role: endpoints
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
          # TODO need to remove this when the CA and SAN match
          insecure_skip_verify: true
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(apiserver_watch_events_total|apiserver_admission_webhook_rejection_count)'
          action: keep
        relabel_configs:
        - source_labels: [__meta_kubernetes_namespace, __meta_kubernetes_service_name, __meta_kubernetes_endpoint_port_name]
          action: keep
          regex: default;kubernetes;https

      - job_name: 'kubernetes-nodes'
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        kubernetes_sd_configs:
        - role: node
        relabel_configs:
        - action: labelmap
          regex: __meta_kubernetes_node_label_(.+)
        - target_label: __address__
          replacement: kubernetes.default.svc:443
        - source_labels: [__meta_kubernetes_node_name]
          regex: (.+)
          target_label: __metrics_path__
          replacement: /api/v1/nodes/${1}/proxy/metrics

      - job_name: 'kubernetes-pods'
        kubernetes_sd_configs:
        - role: pod
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(envoy_server_live|envoy_cluster_upstream_rq_xx|envoy_cluster_upstream_cx_active|envoy_cluster_upstream_cx_tx_bytes_total|envoy_cluster_upstream_cx_rx_bytes_total|envoy_cluster_upstream_cx_destroy_remote_with_active_rq|envoy_cluster_upstream_cx_connect_timeout|envoy_cluster_upstream_cx_destroy_local_with_active_rq|envoy_cluster_upstream_rq_pending_failure_eject|envoy_cluster_upstream_rq_pending_overflow|envoy_cluster_upstream_rq_timeout|envoy_cluster_upstream_rq_rx_reset|^osm.*)'
          action: keep
        relabel_configs:
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
          action: keep
          regex: true
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
          action: replace
          target_label: __metrics_path__
          regex: (.+)
        - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
          action: replace
          regex: ([^:]+)(?::\d+)?;(\d+)
          replacement: $1:$2
          target_label: __address__
        - source_labels: [__meta_kubernetes_namespace]
          action: replace
          target_label: source_namespace
        - source_labels: [__meta_kubernetes_pod_name]
          action: replace
          target_label: source_pod_name
        - regex: '(__meta_kubernetes_pod_label_app)'
          action: labelmap
          replacement: source_service
        - regex: '(__meta_kubernetes_pod_label_osm_envoy_uid|__meta_kubernetes_pod_label_pod_template_hash|__meta_kubernetes_pod_label_version)'
          action: drop
        # for non-ReplicaSets (DaemonSet, StatefulSet)
        # __meta_kubernetes_pod_controller_kind=DaemonSet
        # __meta_kubernetes_pod_controller_name=foo
        # =>
        # workload_kind=DaemonSet
        # workload_name=foo
        - source_labels: [__meta_kubernetes_pod_controller_kind]
          action: replace
          target_label: source_workload_kind
        - source_labels: [__meta_kubernetes_pod_controller_name]
          action: replace
          target_label: source_workload_name
        # for ReplicaSets
        # __meta_kubernetes_pod_controller_kind=ReplicaSet
        # __meta_kubernetes_pod_controller_name=foo-bar-123
        # =>
        # workload_kind=Deployment
        # workload_name=foo-bar
        # deplyment=foo
        - source_labels: [__meta_kubernetes_pod_controller_kind]
          action: replace
          regex: ^ReplicaSet$
          target_label: source_workload_kind
          replacement: Deployment
        - source_labels:
          - __meta_kubernetes_pod_controller_kind
          - __meta_kubernetes_pod_controller_name
          action: replace
          regex: ^ReplicaSet;(.*)-[^-]+$
          target_label: source_workload_name

      - job_name: 'smi-metrics'
        kubernetes_sd_configs:
        - role: pod
        relabel_configs:
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
          action: keep
          regex: true
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
          action: replace
          target_label: __metrics_path__
          regex: (.+)
        - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
          action: replace
          regex: ([^:]+)(?::\d+)?;(\d+)
          replacement: $1:$2
          target_label: __address__
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: 'envoy_.*osm_request_(total|duration_ms_(bucket|count|sum))'
          action: keep
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_(\d{3})_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: response_code
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_(.*)_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_(.*)_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_(.*)_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_(.*)_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_pod
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_(.*)_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: destination_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_(.*)_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: destination_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_(.*)_destination_pod_.*_osm_request_total
          target_label: destination_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_(.*)_osm_request_total
          target_label: destination_pod
        - source_labels: [__name__]
          action: replace
          regex: .*(osm_request_total)
          target_label: __name__

        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_(.*)_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_(.*)_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_(.*)_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_(.*)_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_pod
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_(.*)_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_(.*)_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_(.*)_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_(.*)_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_pod
        - source_labels: [__name__]
          action: replace
          regex: .*(osm_request_duration_ms_(bucket|sum|count))
          target_label: __name__

      - job_name: 'kubernetes-cadvisor'
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        kubernetes_sd_configs:
        - role: node
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(container_cpu_usage_seconds_total|container_memory_rss)'
          action: keep
        relabel_configs:
        - action: labelmap
          regex: __meta_kubernetes_node_label_(.+)
        - target_label: __address__
          replacement: kubernetes.default.svc:443
        - source_labels: [__meta_kubernetes_node_name]
          regex: (.+)
          target_label: __metrics_path__
          replacement: /api/v1/nodes/${1}/proxy/metrics/cadvisor
```

Appliquez le fichier YAML mis à jour du configmap à l’aide de la commande suivante.

```azurecli-interactive
kubectl apply -f cm-stable-prometheus-server.yml
```

```Output
configmap/stable-prometheus-server configured
```

> [!NOTE]
> Vous pouvez recevoir un message concernant l’absence d’une annotation Kubernetes nécessaire. Ce message peut être ignoré pour le moment.

#### <a name="verify-prometheus-is-configured-to-scrape-the-osm-mesh-and-api-endpoints"></a>Vérifier que Prometheus est configuré pour scraper le maillage OSM et les points de terminaison d’API

Pour vérifier que Prometheus est correctement configuré pour scraper le maillage OSM et les points de terminaison d’API, nous allons rediriger le port vers le pod Prometheus et afficher la configuration cible. Exécutez les commandes suivantes :

```azurecli-interactive
PROM_POD_NAME=$(kubectl get pods -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
kubectl --namespace <promNamespace> port-forward $PROM_POD_NAME 9090
```

Ouvrez un navigateur et accédez à `http://localhost:9090/targets`.

Si vous faites défiler la page vers le bas, vous devriez voir tous les points de terminaison des métriques SMI à l’état **UP**, ainsi que d’autres métriques OSM définies comme illustré ci-dessous.

![Image de l’interface utilisateur, métriques cibles de Prometheus OSM](./media/aks-osm-addon/osm-prometheus-smi-metrics-target-scrape.png)

### <a name="deploy-and-configure-a-grafana-instance-for-osm&quot;></a>Déployer et configurer une instance Grafana pour OSM

Nous allons utiliser Helm pour déployer l’instance Grafana. Exécutez les commandes suivantes pour installer Grafana via Helm :

```
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm install osm-grafana grafana/grafana
```

Nous allons ensuite récupérer le mot de passe Grafana par défaut pour nous connecter au site de Grafana.

```azurecli-interactive
kubectl get secret --namespace default osm-grafana -o jsonpath=&quot;{.data.admin-password}&quot; | base64 --decode ; echo
```

Notez le mot de passe Grafana.

Nous allons ensuite récupérer le pod Grafana afin de rediriger le port vers le tableau de bord Grafana pour la connexion.

```azurecli-interactive
GRAF_POD_NAME=$(kubectl get pods -l &quot;app.kubernetes.io/name=grafana&quot; -o jsonpath=&quot;{.items[0].metadata.name}")
kubectl port-forward $GRAF_POD_NAME 3000
```

Ouvrez un navigateur et accédez à `http://localhost:3000`.

Sur l’écran de connexion illustré ci-dessous, entrez **admin** comme nom d’utilisateur et utilisez le mot de passe Grafana capturé précédemment.

![Image de l’interface utilisateur, page de connexion à Grafana OSM](./media/aks-osm-addon/osm-grafana-ui-login.png)

#### <a name="configure-the-grafana-prometheus-data-source"></a>Configurer la source de données Prometheus pour Grafana

Une fois que vous êtes correctement connecté à Grafana, l’étape suivante consiste à ajouter Prometheus comme sources de données pour Grafana. Pour ce faire, accédez à l’icône de configuration dans le menu de gauche et sélectionnez Data Sources (Sources de données) comme indiqué ci-dessous.

![Image de l’interface utilisateur, page Sources de données de Grafana OSM](./media/aks-osm-addon/osm-grafana-ui-datasources.png)

Cliquez sur le bouton **Add data source** (Ajouter une source de données) et sélectionnez Prometheus sous « Time series databases » (Bases de données de série chronologique).

![Image de l’interface utilisateur, page Sélection des sources de données de Grafana OSM](./media/aks-osm-addon/osm-grafana-ui-datasources-select-prometheus.png)

Dans la page **Configure your Prometheus data source below** (Configurez votre source de données Prometheus ci-dessous), entrez le nom de domaine complet du cluster Kubernetes pour le service Prometheus pour le paramètre URL HTTP. Le nom de domaine complet par défaut doit être `stable-prometheus-server.default.svc.cluster.local`. Une fois que vous avez entré ce point de terminaison de service Prometheus, faites défiler la page vers le bas et sélectionnez **Save & Test** (Enregistrer et tester). Vous devez recevoir une coche verte indiquant que la source de données fonctionne.

#### <a name="importing-osm-dashboards"></a>Importer des tableaux de bord OSM

Les tableaux de bord OSM sont disponibles à la fois via :

- [notre référentiel](https://github.com/grafana/grafana), et peuvent être importés sous forme de blobs json via le portail d’administration web ; ou
- [en ligne à l’adresse grafana.com](https://grafana.com/grafana/dashboards/14145).

Pour importer un tableau de bord, recherchez le signe `+` dans le menu de gauche et sélectionnez `import`.
Vous pouvez importer directement les tableaux de bord par leur ID sur `Grafana.com`. Par exemple, notre tableau de bord `OSM Mesh Details` utilise l’ID `14145`. Vous pouvez utiliser l’ID directement sur le formulaire et sélectionner `import` :

![Image de l’interface utilisateur, page Importer un tableau de bord Grafana OSM](./media/aks-osm-addon/osm-grafana-dashboard-import.png)

Dès que vous sélectionnez l’option d’importation, le tableau de bord importé s’affiche automatiquement.

![Image de l’interface utilisateur, page Détails du maillage du tableau de bord Grafana OSM](./media/aks-osm-addon/osm-grafana-mesh-dashboard-details.png)

### <a name="deploy-and-configure-a-jaeger-operator-on-kubernetes-for-osm"></a>Déployer et configurer un opérateur Jaeger sur Kubernetes pour OSM

[Jaeger](https://www.jaegertracing.io/) est un système de traçage open source utilisé pour la surveillance et la résolution des problèmes des systèmes distribués. Il peut être déployé avec OSM comme nouvelle instance, ou vous pouvez apporter votre propre instance. Les instructions suivantes déploient une nouvelle instance de Jaeger dans l’espace de noms `jaeger` sur le cluster AKS.

#### <a name="deploy-jaeger-to-the-aks-cluster"></a>Déployer Jaeger sur le cluster AKS

Appliquez le manifeste suivant pour installer Jaeger :

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: jaeger
  namespace: jaeger
  labels:
    app: jaeger
spec:
  replicas: 1
  selector:
    matchLabels:
      app: jaeger
  template:
    metadata:
      labels:
        app: jaeger
    spec:
      containers:
        - name: jaeger
          image: jaegertracing/all-in-one
          args:
          - --collector.zipkin.host-port=9411
          imagePullPolicy: IfNotPresent
          ports:
          - containerPort: 9411
          resources:
            limits:
              cpu: 500m
              memory: 512M
            requests:
              cpu: 100m
              memory: 256M
---
kind: Service
apiVersion: v1
metadata:
  name: jaeger
  namespace: jaeger
  labels:
    app: jaeger
spec:
  selector:
    app: jaeger
  ports:
  - protocol: TCP
    # Service port and target port are the same
    port: 9411
  type: ClusterIP
EOF
```

```Output
deployment.apps/jaeger created
service/jaeger created
```

#### <a name="enable-tracing-for-the-osm-add-on"></a>Activer le traçage pour le module complémentaire OSM

Ensuite, nous devons activer le traçage pour le module complémentaire OSM.

> [!NOTE]
> Pour l’instant, les propriétés de traçage ne sont pas visibles dans le configmap osm-config. Elles le seront dans une nouvelle version du module complémentaire OSM AKS.

Exécutez la commande suivante pour activer le traçage pour le module complémentaire OSM :

```azurecli-interactive
kubectl patch configmap osm-config -n kube-system -p '{"data":{"tracing_enable":"true", "tracing_address":"jaeger.jaeger.svc.cluster.local", "tracing_port":"9411", "tracing_endpoint":"/api/v2/spans"}}' --type=merge
```

```Output
configmap/osm-config patched
```

#### <a name="view-the-jaeger-ui-with-port-forwarding"></a>Afficher l’interface utilisateur Jaeger avec la redirection de port

L’interface utilisateur de Jaeger s’exécute sur le port 16686. Pour afficher l’interface utilisateur web, vous pouvez utiliser « kubectl port-forward » :

```azurecli-interactive
JAEGER_POD=$(kubectl get pods -n jaeger --no-headers  --selector app=jaeger | awk 'NR==1{print $1}')
kubectl port-forward -n jaeger $JAEGER_POD  16686:16686
http://localhost:16686/
```

Dans le navigateur, vous devriez voir une liste déroulante Service, qui vous permet de choisir parmi les différentes applications déployées par la version de démonstration de bookstore. Sélectionnez un service pour afficher toutes les durées de celui-ci. Par exemple, si vous sélectionnez bookbuyer avec un Lookback (Recherche arrière) d’une heure, vous pouvez voir ses interactions avec bookstore-v1 et bookstore-v2 triées par heure.

![Image de l’interface utilisateur, page Traçage Jaeger OSM](./media/aks-osm-addon/osm-jaeger-trace-view-ui.png)

Sélectionnez un élément pour l’afficher plus en détail. Sélectionnez plusieurs éléments pour comparer les traces. Par exemple, vous pouvez comparer les interactions de bookbuyer avec bookstore et bookstore-v2 à un moment donné.

Vous pouvez également sélectionner l’onglet System Architecture (Architecture du système) pour afficher un graphique illustrant la manière dont les différentes applications interagissent/communiquent. Cela vous donne une idée de la façon dont le trafic circule entre les applications.

![Image de l’interface utilisateur, architecture du système Jaeger OSM](./media/aks-osm-addon/osm-jaeger-sys-arc-view-ui.png)

## <a name="open-service-mesh-osm-aks-add-on-troubleshooting-guides"></a>Guides de résolution des problèmes du module complémentaire Open Service Mesh (OSM) AKS

Lorsque vous déployez le module complémentaire OSM AKS, vous pouvez parfois rencontrer un problème. Les guides suivants vous aideront à dépanner les erreurs et à résoudre les problèmes courants.

### <a name="verifying-and-troubleshooting-osm-components"></a>Vérification et résolution des problèmes des composants OSM

#### <a name="check-osm-controller-deployment"></a>Vérifier le déploiement du contrôleur OSM

```azurecli-interactive
kubectl get deployment -n kube-system --selector app=osm-controller
```

Un contrôleur OSM sain ressemble à ceci :

```Output
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
osm-controller   1/1     1            1           59m
```

#### <a name="check-the-osm-controller-pod"></a>Vérifier le pod du contrôleur OSM

```azurecli-interactive
kubectl get pods -n kube-system --selector app=osm-controller
```

Un pod OSM sain ressemble à ceci :

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-controller-b5bd66db-wglzl   0/1     Evicted   0          61m
osm-controller-b5bd66db-wvl9w   1/1     Running   0          31m
```

Même si un contrôleur a été expulsé à un moment donné, un autre indique READY 1/1 et Running avec 0 redémarrage. Si la colonne READY indique un résultat différent de 1/1, le maillage de services est en panne.
La colonne READY avec 0/1 indique que le conteneur du plan de contrôle rencontre un incident. Nous devons donc récupérer les journaux. Consultez la section Obtenir les journaux du contrôleur OSM du Centre de support Azure ci-dessous. La colonne READY avec un nombre supérieur à 1 après le signe « / » indique que des side-cars sont installés. Le contrôleur OSM ne fonctionnera probablement pas si des side-cars lui sont attachés.

> [!NOTE]
> À partir de la version v0.8.2, le contrôleur OSM n’est pas en mode HA et s’exécute dans un déploiement avec un nombre de réplicas d’un (1) pod. Le pod dispose de sondes d’intégrité et sera redémarré par le kubelet si nécessaire.

#### <a name="check-osm-controller-service"></a>Vérifier le service du contrôleur OSM

```azurecli-interactive
kubectl get service -n kube-system osm-controller
```

Un service de contrôleur OSM sain ressemble à ceci :

```Output
NAME             TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)              AGE
osm-controller   ClusterIP   10.0.31.254   <none>        15128/TCP,9092/TCP   67m
```

> [!NOTE]
> La valeur pour CLUSTER-IP sera différente. Les propriétés NAME et PORT(S) du service doivent être les mêmes que dans l’exemple ci-dessus.

#### <a name="check-osm-controller-endpoints"></a>Vérifier les points de terminaison du contrôleur OSM

```azurecli-interactive
kubectl get endpoints -n kube-system osm-controller
```

Un point de terminaison de contrôleur OSM sain ressemble à ceci :

```Output
NAME             ENDPOINTS                              AGE
osm-controller   10.240.1.115:9092,10.240.1.115:15128   69m
```

#### <a name="check-osm-injector-deployment"></a>Vérifier le déploiement de l’injecteur OSM

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

Un déploiement d’injecteur OSM sain ressemble à ceci :

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

#### <a name="check-osm-injector-pod"></a>Vérifier le pod de l’injecteur OSM

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

Un pod d’injecteur OSM sain ressemble à ceci :

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

#### <a name="check-osm-injector-service"></a>Vérifier le service de l’injecteur OSM

```azurecli-interactive
kubectl get service -n kube-system osm-injector
```

Un service d’injecteur OSM sain ressemble à ceci :

```Output
NAME           TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
osm-injector   ClusterIP   10.0.39.54   <none>        9090/TCP   75m
```

#### <a name="check-osm-endpoints"></a>Vérifier les points de terminaison OSM

```azurecli-interactive
kubectl get endpoints -n kube-system osm-injector
```

Un point de terminaison OSM sain ressemble à ceci :

```Output
NAME           ENDPOINTS           AGE
osm-injector   10.240.1.172:9090   75m
```

#### <a name="check-validating-and-mutating-webhooks"></a>Vérifier les webhooks Validating et Mutating

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration --selector app=osm-controller
```

Un webhook Validating OSM sain ressemble à ceci :

```Output
NAME              WEBHOOKS   AGE
aks-osm-webhook-osm   1      81m
```

```azurecli-interactive
kubectl get MutatingWebhookConfiguration --selector app=osm-injector
```

Un webhook Mutating OSM sain ressemble à ceci :

```Output
NAME              WEBHOOKS   AGE
aks-osm-webhook-osm   1      102m
```

#### <a name="check-for-the-service-and-the-ca-bundle-of-the-validating-webhook"></a>Rechercher le service et le pack de l’autorité de certification du webhook Validating

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration aks-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

Un webhook Validating bien configuré doit ressembler à ceci :

```json
{
  "name": "osm-config-validator",
  "namespace": "kube-system",
  "path": "/validate-webhook",
  "port": 9093
}
```

#### <a name="check-for-the-service-and-the-ca-bundle-of-the-mutating-webhook"></a>Rechercher le service et le pack de l’autorité de certification du webhook Mutating

```azurecli-interactive
kubectl get MutatingWebhookConfiguration aks-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

Un webhook Mutating bien configuré doit ressembler à ceci :

```json
{
  "name": "osm-injector",
  "namespace": "kube-system",
  "path": "/mutate-pod-creation",
  "port": 9090
}
```

#### <a name="check-whether-osm-controller-has-given-the-validating-or-mutating-webhook-a-ca-bundle"></a>Vérifiez si le contrôleur OSM a donné au webhook Validating (or Mutating) un pack d’autorité de certification.

> [!NOTE]
> À partir de la version 0.8.2, il est important de savoir qu’AKS RP installe le webhook Validating, que l’outil de rapprochement AKS s’assure de son existence, mais que le contrôleur OSM est celui qui remplit le pack de l’autorité de certification.

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration aks-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```azurecli-interactive
kubectl get MutatingWebhookConfiguration aks-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```Example Output
1845
```

Ce nombre indique le nombre d’octets ou la taille du pack de l’autorité de certification. S’il est vide, égal à 0 ou inférieur à 1000, cela signifie que le pack de l’autorité de certification n’est pas correctement approvisionné. Sans un pack d’autorité de certification correct, le webhook Validating émettrait une erreur et interdirait à l’utilisateur d’apporter des modifications au ConfigMap osm-config dans l’espace de noms kube-system.

Exemple d’erreur lorsque le pack de l’autorité de certification est incorrect :

- Tentative de modification du ConfigMap osm-config :

```azurecli-interactive
kubectl patch ConfigMap osm-config -n kube-system --type merge --patch '{"data":{"config_resync_interval":"2m"}}'
```

- Erreur :

```
Error from server (InternalError): Internal error occurred: failed calling webhook "osm-config-webhook.k8s.io": Post https://osm-config-validator.kube-system.svc:9093/validate-webhook?timeout=30s: x509: certificate signed by unknown authority
```

Solution de contournement lorsque la configuration du webhook **Validating** a un certificat incorrect :

- Option 1 : Redémarrer le contrôleur OSM. Cette option permet de redémarrer le contrôleur OSM. Au démarrage, il remplacera le pack de l’autorité de certification des webhooks Mutating et Validating.

```azurecli-interactive
kubectl rollout restart deployment -n kube-system osm-controller
```

- Option 2 : Supprimer le webhook Validating. La suppression du webhook Validating invalide les mutations du ConfigMap `osm-config`. Tous les patchs seront concernés. L’outil de rapprochement AKS s’assurera à un moment donné que le webhook Validating existe et le recréera. Il peut être nécessaire de redémarrer le contrôleur OSM pour réécrire rapidement le pack de l’autorité de certification.

```azurecli-interactive
kubectl delete ValidatingWebhookConfiguration aks-osm-webhook-osm
```

- Option 3 : Supprimer et corriger. La commande suivante permet de supprimer le webhook Validating, ce qui nous permet d’ajouter des valeurs et de tenter immédiatement d’appliquer un patch. Il est fort probable que l’outil de rapprochement AKS n’ait pas suffisamment de temps pour rapprocher et restaurer le webhook Validating, ce qui nous permet d’appliquer une modification en dernier recours :

```azurecli-interactive
kubectl delete ValidatingWebhookConfiguration aks-osm-webhook-osm; kubectl patch ConfigMap osm-config -n kube-system --type merge --patch '{"data":{"config_resync_interval":"15s"}}'
```

#### <a name="check-the-osm-config-configmap"></a>Vérifier le **ConfigMap** `osm-config`

> [!NOTE]
> Le contrôleur OSM ne requiert pas que le ConfigMap `osm-config` soit présent dans l’espace de noms kube-system. Le contrôleur a des valeurs par défaut raisonnables pour la configuration et peut fonctionner sans lui.

Vérifiez son existence :

```azurecli-interactive
kubectl get ConfigMap -n kube-system osm-config
```

Vérifiez le contenu du ConfigMap osm-config :

```azurecli-interactive
kubectl get ConfigMap -n kube-system osm-config -o json | jq '.data'
```

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254,168.63.129.16,20.193.20.233",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

Valeurs du ConfigMap `osm-config` :

| Clé                              | Type   | Valeurs autorisées                                          | Valeur par défaut                          | Fonction                                                                                                                                                                                                                                |
| -------------------------------- | ------ | ------------------------------------------------------- | -------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| egress                           | bool   | true, false                                             | `"false"`                              | Active la sortie dans le maillage.                                                                                                                                                                                                             |
| enable_debug_server              | bool   | true, false                                             | `"true"`                               | Permet à un point de terminaison de débogage sur le pod osm-controller de répertorier les informations relatives au maillage telles que les connexions proxy, les certificats et les stratégies SMI.                                                                                    |
| enable_privileged_init_container | bool   | true, false                                             | `"false"`                              | Active les conteneurs init privilégiés pour les pods dans le maillage. Quand la valeur est false, les conteneurs init n’ont que NET_ADMIN.                                                                                                                                   |
| envoy_log_level                  | string | trace, débogage, info, avertissement, alerte, erreur, critique, désactivé | `"error"`                              | Définit la verbosité de la journalisation du side-car du proxy Envoy, applicable uniquement aux pods nouvellement créés qui rejoignent le maillage. Pour mettre à jour le niveau de journalisation pour les pods existants, redémarrez le déploiement avec `kubectl rollout restart`.                            |
| outbound_ip_range_exclusion_list | string | liste de plages d’adresses IP séparées par des virgules, au format a.b.c.d/x | `-`                                    | Liste globale des plages d’adresses IP à exclure de l’interception du trafic sortant par le proxy side-car.                                                                                                                                    |
| permissive_traffic_policy_mode   | bool   | true, false                                             | `"false"`                              | Si elle est définie sur `true`, elle active le mode allow-all dans le maillage, c’est-à-dire qu’aucune stratégie de trafic n’est appliquée dans le maillage. Si elle est définie sur `false`, elle active la stratégie de trafic deny-all dans le maillage, c’est-à-dire qu’une valeur `SMI Traffic Target` est nécessaire pour que les services puissent communiquer. |
| prometheus_scraping              | bool   | true, false                                             | `"true"`                               | Active le scraping des métriques Prometheus sur les proxys side-car.                                                                                                                                                                                 |
| service_cert_validity_duration   | string | 24 h, 1 h 30 m (n’importe quelle durée)                          | `"24h"`                                | Définit la durée de validité du certificat de service, représentée par une séquence de nombres décimaux, chacun avec une fraction facultative et un suffixe d’unité.                                                                                             |
| tracing_enable                   | bool   | true, false                                             | `"false"`                              | Active le traçage Jaeger pour le maillage.                                                                                                                                                                                                    |
| tracing_address                  | string | jaeger.mesh-namespace.svc.cluster.local                 | `jaeger.kube-system.svc.cluster.local` | Adresse du déploiement Jaeger, si le traçage est activé.                                                                                                                                                                                |
| tracing_endpoint                 | string | /api/v2/spans                                           | /api/v2/spans                          | Point de terminaison pour le traçage des données, si le traçage est activé.                                                                                                                                                                                          |
| tracing_port                     | int    | toute valeur entière différente de zéro                              | `"9411"`                               | Port sur lequel le traçage est activé.                                                                                                                                                                                                       |
| use_https_ingress                | bool   | true, false                                             | `"false"`                              | Active l’entrée HTTPS sur le maillage.                                                                                                                                                                                                      |
| config_resync_interval           | string | si moins d’une minute, à désactiver                            | 0 (désactivé)                           | Quand une valeur supérieure à 1 m (60 s) est fournie, le contrôleur OSM envoie toutes les configurations disponibles à chaque Envoy connecté à l’intervalle donné.                                                                                                    |

#### <a name="check-namespaces"></a>Vérifier les espaces de noms

> [!NOTE]
> L’espace de noms kube-system ne fait jamais partie d’un maillage de services et n’est jamais étiqueté ou annoté avec les clés/valeurs ci-dessous.

Nous utilisons la commande `osm namespace add` pour joindre des espaces de noms à un maillage de services donné.
Lorsqu’un espace de noms k8s fait partie du maillage (ou pour qu’il fasse partie du maillage), les conditions suivantes doivent être remplies :

Affichez les annotations à l’aide de la commande suivante :

```azurecli-interactive
kubectl get namespace bookbuyer -o json | jq '.metadata.annotations'
```

L’annotation suivante doit être présente :

```Output
{
  "openservicemesh.io/sidecar-injection": "enabled"
}
```

Affichez les étiquettes à l’aide de la commande suivante :

```azurecli-interactive
kubectl get namespace bookbuyer -o json | jq '.metadata.labels'
```

L’étiquette suivante doit être présente :

```Output
{
  "openservicemesh.io/monitored-by": "osm"
}
```

Si un espace de noms n’est pas annoté avec `"openservicemesh.io/sidecar-injection": "enabled"` ou n’a pas l’étiquette `"openservicemesh.io/monitored-by": "osm"`, l’injecteur OSM n’ajoutera pas les side-cars Envoy.

> Remarque : Une fois que `osm namespace add` est appelé, seuls les **nouveaux** pods sont injectés avec un side-car Envoy. Les pods existants doivent être redémarrés à l’aide de la commande `kubectl rollout restart deployment ...`.

#### <a name="verify-the-smi-crds"></a>Vérifier les CRD SMI :

Vérifiez si le cluster possède les CRD requis :

```azurecli-interactive
kubectl get crds
```

Les éléments suivants doivent être installés sur le cluster :

- httproutegroups.specs.smi-spec.io
- tcproutes.specs.smi-spec.io
- trafficsplits.split.smi-spec.io
- traffictargets.access.smi-spec.io
- udproutes.specs.smi-spec.io

Récupérez les versions des CRD installés avec cette commande :

```azurecli-interactive
for x in $(kubectl get crds --no-headers | awk '{print $1}' | grep 'smi-spec.io'); do
    kubectl get crd $x -o json | jq -r '(.metadata.name, "----" , .spec.versions[].name, "\n")'
done
```

Sortie attendue :

```Output
httproutegroups.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1


tcproutes.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1


trafficsplits.split.smi-spec.io
----
v1alpha2


traffictargets.access.smi-spec.io
----
v1alpha3
v1alpha2
v1alpha1


udproutes.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1
```

Le contrôleur OSM v0.8.2 requiert les versions suivantes :

- traffictargets.access.smi-spec.io : [v1alpha3](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-access/v1alpha3/traffic-access.md)
- httproutegroups.specs.smi-spec.io : [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#httproutegroup)
- tcproutes.specs.smi-spec.io : [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#tcproute)
- udproutes.specs.smi-spec.io : Non pris en charge
- trafficsplits.split.smi-spec.io : [v1alpha2](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-split/v1alpha2/traffic-split.md)
- \*.metrics.smi-spec.io : [v1alpha1](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-metrics/v1alpha1/traffic-metrics.md)

Si des CRD sont manquants, utilisez les commandes suivantes pour les installer sur le cluster :

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/access.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/specs.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/split.yaml
```

## <a name="disable-open-service-mesh-osm-add-on-for-your-aks-cluster"></a>Désactiver le module complémentaire Open Service Mesh (OSM) pour votre cluster AKS

Pour désactiver le module complémentaire OSM, exécutez la commande suivante :

```azurecli-interactive
az aks disable-addons -n <AKS-cluster-name> -g <AKS-resource-group-name> -a open-service-mesh
```

<!-- LINKS - internal -->

[kubernetes-service]: concepts-network.md#services
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest&preserve-view=true#az_provider_register
