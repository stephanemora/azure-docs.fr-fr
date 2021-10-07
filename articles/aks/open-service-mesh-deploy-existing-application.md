---
title: Gérer une application existante à l’aide d’Open Service Mesh
description: Gestion d’une application existante à l’aide d’Open Service Mesh
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 092e42a5f9c1779fc5968b9fc733d260d405a90a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128625902"
---
# <a name="manage-an-existing-application-with-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>Gérer une application existante à l’aide du module complémentaire Open Service Mesh (OSM) Azure Kubernetes Service (AKS)

## <a name="before-you-begin"></a>Avant de commencer

Les étapes détaillées dans cette procédure pas à pas supposent que vous avez déjà activé le module complémentaire OSM AKS pour votre cluster AKS. Si ce n’est pas le cas, consultez l’article [Déployer le module complémentaire OSM AKS](./open-service-mesh-deploy-addon-az-cli.md) avant de poursuivre. En outre, votre cluster AKS doit avoir la version `1.19+` ou une version ultérieure de Kubernetes avec Kubernetes RBAC activé, une connexion `kubectl` doit être établie avec le cluster (si vous avez besoin d’aide pour l’un de ces éléments, consultez le [démarrage rapide d’AKS](./kubernetes-walkthrough.md)) et le module complémentaire AKS OSM doit être installé.

Les ressources suivantes doivent être installées :

- Azure CLI, version 2.20.0 ou ultérieure
- L’extension `aks-preview` version 0.5.5 ou ultérieure
- OSM, version 0.8.0 ou ultérieure
- Processeur JSON « jq » version 1.6+

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="verify-the-open-service-mesh-osm-permissive-traffic-mode-policy"></a>Vérifier la stratégie de mode de trafic permissif d’Open Service Mesh (OSM)

Le mode de stratégie de trafic permissif d’OSM est un mode dans lequel l’application de la stratégie de trafic [SMI](https://smi-spec.io/) est ignorée. Dans ce mode, OSM découvre automatiquement les services qui font partie du maillage de services et programme des règles de stratégie de trafic sur chaque side-car de proxy Envoy pour pouvoir communiquer avec ces services.

Pour vérifier le mode de trafic permissif actuel d’OSM pour votre cluster, exécutez la commande suivante :

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system -o yaml
```

La sortie de la MeshConfig OSM doit ressembler à ce qui suit :

```Output
apiVersion: config.openservicemesh.io/v1alpha1
kind: MeshConfig
metadata:
  creationTimestamp: "0000-00-00A00:00:00A"
  generation: 1
  name: osm-mesh-config
  namespace: kube-system
  resourceVersion: "2494"
  uid: 6c4d67f3-c241-4aeb-bf4f-b029b08faa31
spec:
  certificate:
    serviceCertValidityDuration: 24h
  featureFlags:
    enableEgressPolicy: true
    enableMulticlusterMode: false
    enableWASMStats: true
  observability:
    enableDebugServer: true
    osmLogLevel: info
    tracing:
      address: jaeger.osm-system.svc.cluster.local
      enable: false
      endpoint: /api/v2/spans
      port: 9411
  sidecar:
    configResyncInterval: 0s
    enablePrivilegedInitContainer: false
    envoyImage: mcr.microsoft.com/oss/envoyproxy/envoy:v1.18.3
    initContainerImage: mcr.microsoft.com/oss/openservicemesh/init:v0.9.1
    logLevel: error
    maxDataPlaneConnections: 0
    resources: {}
  traffic:
    enableEgress: true
    enablePermissiveTrafficPolicyMode: true
    inboundExternalAuthorization:
      enable: false
      failureModeAllow: false
      statPrefix: inboundExtAuthz
      timeout: 1s
    useHTTPSIngress: false
```

Si **enablePermissiveTrafficPolicyMode** est configuré sur **true**, vous pouvez intégrer vos espaces de noms en toute sécurité sans interrompre vos communications service à service. Si **enablePermissiveTrafficPolicyMode** est configuré sur **false**, vous devez vous assurer d’avoir déployé les manifestes de stratégie d’accès au trafic [SMI](https://smi-spec.io/) corrects. Vous devez également vous assurer que vous disposez d’un compte de service représentant chaque service déployé dans l’espace de noms. Pour plus d’informations sur le mode de trafic permissif, consultez et lisez l’article [Mode de stratégie de trafic permissif](https://docs.openservicemesh.io/docs/guides/traffic_management/permissive_mode/).

## <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-true"></a>Intégrer des applications déployées à la stratégie de trafic permissif d’Open Service Mesh (OSM) configurée sur True

La première chose que nous allons faire est d’ajouter les espaces de noms des applications déployées à OSM pour qu’il les gère. L’exemple ci-dessous intégrera l’espace de noms **bookstore** à OSM.

```azurecli-interactive
osm namespace add bookstore
```

Vous devez normalement voir la sortie suivante.

```Output
Namespace [bookstore] successfully added to mesh [osm]
```

Ensuite, nous examinerons le déploiement actuel des pods dans l’espace de noms. Exécutez la commande suivante pour afficher les pods dans l’espace de noms `bookbuyer`.

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

## <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false"></a>Intégrer des applications déployées à la stratégie de trafic permissif d’Open Service Mesh (OSM) configurée sur False

Lorsque la configuration d’OSM pour la stratégie de trafic permissif est définie sur `false`, OSM requiert le déploiement de stratégies d’accès au trafic [SMI](https://smi-spec.io/) explicites pour que la communication de service à service ait lieu au sein de votre cluster. À l’heure actuelle, OSM utilise également des comptes de service Kubernetes dans le cadre de l’autorisation des communications de service à service. Pour vous assurer que vos applications déployées existantes communiquent entre elles lorsqu’elles sont gérées par le maillage d’OSM, nous allons maintenant vérifier l’existence d’un compte de service à utiliser, mettre à jour le déploiement de l’application avec les informations du compte de service et appliquer les stratégies d’accès au trafic [SMI](https://smi-spec.io/).

### <a name="verify-kubernetes-service-accounts"></a>Vérifier les comptes de service Kubernetes

Vérifiez si vous disposez d’un compte de service Kubernetes dans l’espace de noms sur lequel votre application est déployée.

```azurecli-interactive
kubectl get serviceaccounts -n bookbuyer
```

Dans l’exemple suivant, il existe un compte de service nommé `bookbuyer` dans l’espace de noms `bookbuyer`.

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

### <a name="view-your-applications-current-deployment-specification"></a>Afficher la spécification de déploiement actuelle de votre application

Si vous avez dû créer un compte de service dans la section précédente, il est probable que le déploiement de votre application n’est pas configuré avec un `serviceAccountName` spécifique dans les spécifications de déploiement. Nous pouvons afficher les spécifications de déploiement de votre application à l’aide des commandes suivantes :

```azurecli-interactive
kubectl get deployment -n bookbuyer
```

Une liste de déploiements est répertoriée dans la sortie.

```Output
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
bookbuyer   1/1     1            1           25h
```

Nous allons décrire le déploiement afin de vérifier si un compte de service est listé dans la section Pod Template.

```azurecli-interactive
kubectl describe deployment bookbuyer -n bookbuyer
```

Dans ce déploiement particulier, vous pouvez voir qu’un compte de service est associé au déploiement indiqué sous la section Pod Template. Ce déploiement utilise le compte de service `bookbuyer`. Si vous ne voyez pas la propriété **Service Account:** , votre déploiement n’est pas configuré pour utiliser un compte de service.

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

### <a name="deploy-the-necessary-service-mesh-interface-smi-policies"></a>Déployer les stratégies SMI (Service Mesh Interface) nécessaires

La dernière étape pour permettre au trafic autorisé de circuler dans le maillage consiste à déployer les stratégies d’accès au trafic [SMI](https://smi-spec.io/) nécessaires à votre application. La quantité de configuration que vous pouvez réaliser avec les stratégies d’accès au trafic [SMI](https://smi-spec.io/) dépasse le cadre de cette procédure pas à pas, mais nous allons détailler certains des composants communs de la spécification et expliquer comment configurer des stratégies TrafficTarget et HTTPRouteGroup simples pour permettre la communication service à service pour votre application.

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

Dans la spécification TrafficTarget ci-dessus, `destination` désigne le compte de service qui est configuré pour le service source de destination. Rappelez-vous que le compte de service qui a été ajouté au déploiement plus tôt sera utilisé pour autoriser l’accès au déploiement auquel il est attaché. Dans cet exemple particulier, la section `rules` définit le type de trafic HTTP autorisé sur la connexion. Vous pouvez configurer des modèles d’expression régulière affinés pour que les en-têtes HTTP soient spécifiques au trafic autorisé via HTTP. La section `sources` correspond au service à l’origine des communications. Cette spécification indique que `bookbuyer` doit communiquer avec l’espace de noms bookstore.

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

## <a name="manage-the-applications-namespace-with-osm"></a>Gérer l’espace de noms de l’application avec OSM

Ensuite, nous allons veiller à ce qu’OSM gère l’espace de noms et redémarrer les déploiements pour que l’application soit injectée dans le proxy side-car Envoy.

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
