---
title: Déployer Open Service Mesh (préversion)
description: Déployer Open service Mesh sur Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 8476d6c01c0c388eefad42eb89d65011c57bfa65
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123441137"
---
# <a name="deploy-the-open-service-mesh-aks-add-on-preview"></a>Déployer le module complémentaire AKS Open Service Mesh (préversion)
Cet article explique comment déployer le module complémentaire OSM sur AKS.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Prérequis

- Azure CLI, version 2.20.0 ou ultérieure
- L’extension `aks-preview` version 0.5.5 ou ultérieure
- OSM, version 1.9.0 ou ultérieure
- Processeur JSON « jq » version 1.6+

## <a name="install-the-aks-preview-extension"></a>Installer l’extension aks-preview

Vous aurez besoin de la version 0.5.24 ou d’une version ultérieure de l’extension Azure CLI *aks-preview*. Installez l’extension Azure CLI *aks-preview* à l’aide de la commande [az extension add][az-extension-add]. Ou installez toutes les mises à jour disponibles à l’aide de la commande [az extension add][az-extension-add].

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="register-the-aks-openservicemesh-preview-feature"></a>Inscrire la fonctionnalité d’évaluation `AKS-OpenServiceMesh`

Pour créer un cluster AKS capable d’utiliser le module complémentaire Open Service Mesh, vous devez activer l’indicateur de fonctionnalité `AKS-OpenServiceMesh` sur votre abonnement.

Inscrivez l’indicateur de fonctionnalité `AKS-OpenServiceMesh` à l’aide de la commande [az feature register][az-feature-register], comme indiqué dans l’exemple suivant :

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-OpenServiceMesh"
```

Quelques minutes sont nécessaires pour que l’état s’affiche _Registered_ (Inscrit). Vérifiez l’état de l’inscription à l’aide de la commande [az feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-OpenServiceMesh')].{Name:name,State:properties.state}"
```

Lorsque vous êtes prêt, actualisez l’inscription du fournisseur de ressources _Microsoft.ContainerService_ à l’aide de la commande [az provider register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="install-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-a-new-aks-cluster"></a>Installer le module complémentaire Open Service Mesh (OSM) Azure Kubernetes Service (AKS) pour un nouveau cluster AKS

Pour le scénario de déploiement d’un nouveau cluster AKS, vous commencerez par un tout nouveau déploiement de cluster AKS en activant le module complémentaire OSM lors de l’opération de création du cluster.

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Dans Azure, vous allouez les ressources associées à un groupe de ressources. Créez un groupe de ressources à l’aide de la commande [az group create](/cli/azure/group#az_group_create). L’exemple suivant crée un groupe de ressources nommé _myOsmAksGroup_ à l’emplacement _eastus2_ (région) :

```azurecli-interactive
az group create --name <my-osm-aks-cluster-rg> --location <azure-region>
```

### <a name="deploy-an-aks-cluster-with-the-osm-add-on-enabled"></a>Déployer un cluster AKS avec le module complémentaire OSM activé

Déployez ensuite un nouveau cluster AKS avec le module complémentaire OSM activé.

> [!NOTE]
> Notez que la commande de déploiement AKS suivante utilise des disques de système d’exploitation éphémères, par exemple, un déploiement AKS. Vous trouverez plus d’informations ici sur [les disques de système d’exploitation éphémères pour AKS](./cluster-configuration.md#ephemeral-os).

```azurecli-interactive
az aks create -n <my-osm-aks-cluster-name> -g <my-osm-aks-cluster-rg> --node-osdisk-type Ephemeral --node-osdisk-size 30 --network-plugin azure --enable-managed-identity -a open-service-mesh
```

#### <a name="get-aks-cluster-access-credentials"></a>Obtenir les informations d’identification d’accès au cluster AKS

Obtenez les informations d’identification d’accès pour le nouveau cluster Kubernetes managé.

```azurecli-interactive
az aks get-credentials -n <my-osm-aks-cluster-name> -g <my-osm-aks-cluster-rg>
```

## <a name="enable-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-an-existing-aks-cluster"></a>Activer le module complémentaire Open Service Mesh (OSM) Azure Kubernetes Service (AKS) pour un cluster AKS existant

Pour un scénario de cluster AKS existant, vous allez activer le module complémentaire OSM sur un cluster AKS qui a déjà été déployé.

### <a name="enable-the-osm-add-on-to-existing-aks-cluster"></a>Activer le module complémentaire OSM sur un cluster AKS existant

Pour activer le module complémentaire AKS OSM, vous devez exécuter la commande `az aks enable-addons --addons` en transmettant le paramètre `open-service-mesh`.

```azurecli-interactive
az aks enable-addons --addons open-service-mesh -g <my-osm-aks-cluster-rg> -n <my-osm-aks-cluster-name>
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
az aks list -g <my-osm-aks-cluster-rg> -o json | jq -r '.[].addonProfiles.openServiceMesh.enabled'
```

Les commandes `kubectl` suivantes signalent l’état d’osm-controller.

```azurecli-interactive
kubectl get deployments -n kube-system --selector app=osm-controller
kubectl get pods -n kube-system --selector app=osm-controller
kubectl get services -n kube-system --selector app=osm-controller
```

## <a name="accessing-the-aks-osm-add-on-configuration"></a>Accès à la configuration du module complémentaire AKS OSM

Actuellement, vous pouvez configurer le contrôleur OSM et accéder à sa configuration par le biais de la ressource OSM MeshConfig. Pour voir les paramètres de configuration du contrôleur OSM par le biais de l’interface CLI, utilisez la commande **kubectl** get, comme indiqué ci-dessous.

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system -o yaml
```

La sortie de MeshConfig doit se ressembler à ceci :

```
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

Notez que le paramètre **enablePermissiveTrafficPolicyMode** est configuré sur **true**. Le mode de stratégie de trafic permissif dans OSM est un mode dans lequel l’application de la stratégie de trafic [SMI](https://smi-spec.io/) est ignorée. Dans ce mode, OSM découvre automatiquement les services qui font partie du maillage de services et programme des règles de stratégie de trafic sur chaque side-car de proxy Envoy pour pouvoir communiquer avec ces services. Pour plus d’informations sur le mode de trafic permissif, consultez et lisez l’article [mode de stratégie de trafic permissif](https://docs.openservicemesh.io/docs/guides/traffic_management/permissive_mode/). 

> [!WARNING]
> Avant de continuer, vérifiez que votre mode de stratégie de trafic permissif est défini sur true. Si ce n’est pas le cas, définissez-le sur **true** à l’aide de la commande ci-dessous.

```OSM Permissive Mode to True
kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"enablePermissiveTrafficPolicyMode":true}}}' --type=merge
```