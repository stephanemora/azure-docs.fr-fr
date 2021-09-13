---
title: Open Service Mesh avec Azure Arc (préversion)
description: Extension Open Service Mesh (OSM) sur le cluster Kubernetes avec Arc activé
services: azure-arc
ms.service: azure-arc
ms.date: 07/23/2021
ms.topic: article
author: mayurigupta13
ms.author: mayg
ms.openlocfilehash: c8a10873f420b5aba75596a4377bfa4f0b37d4f7
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122606892"
---
# <a name="azure-arc-enabled-open-service-mesh-preview"></a>Open Service Mesh avec Azure Arc (préversion)

[Open Service Mesh (OSM)](https://docs.openservicemesh.io/) est un maillage de services natif Cloud léger et extensible qui permet aux utilisateurs de gérer et de sécuriser uniformément les environnements de microservices hautement dynamiques et de bénéficier de fonctionnalités d’observabilité prêtes à l’emploi.

OSM exécute un plan de contrôle basé sur un envoi sur Kubernetes, peut être configuré avec des API [SMI](https://smi-spec.io/) et fonctionne en injectant un proxy Envoy en tant que conteneur side-car à côté de chaque instance de votre application. [Découvrez-en plus](https://docs.openservicemesh.io/#features) sur les scénarios de maillage de service activés par Open Service Mesh.

### <a name="support-limitations-for-arc-enabled-open-service-mesh"></a>Limitations de prise en charge pour Arc avec Open Service Mesh

- Une seule instance d’Open Service Mesh peut être déployée sur un cluster Kubernetes connecté avec Arc
- La préversion publique est disponible pour Open Servie Mesh version v0.8.4 et versions ultérieures. Découvrez la version la plus récente [ici](https://github.com/Azure/osm-azure/releases). Les versions de publication prises en charge sont ajoutées aux notes. Ignorez les étiquettes associées aux versions intermédiaires. 
- Les distributions Kubernetes prises en charge sont les suivantes
    - Moteur AKS
    - AKS sur HCI
    - Cluster API Azure
    - Google Kubernetes Engine
    - Canonical Kubernetes Distribution
    - Rancher Kubernetes Engine
    - OpenShift Kubernetes Distribution
    - Amazon Elastic Kubernetes Service
- L’intégration Azure Monitor avec Open Service Mesh avec Azure Arc est disponible avec une [prise en charge limitée](https://github.com/microsoft/Docker-Provider/blob/ci_dev/Documentation/OSMPrivatePreview/ReadMe.md).


[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

### <a name="prerequisites"></a>Configuration requise

- Vérifiez que vous respectez toutes les conditions préalables courantes pour les extensions de cluster répertoriées [ici](extensions.md#prerequisites).
- Utiliser az k8s-extension CLI version >= v0.4.0

## <a name="install-arc-enabled-open-service-mesh-osm-on-an-arc-enabled-kubernetes-cluster"></a>Installer Open Service Mesh (OSM) sur un cluster Kubernetes avec Arc

Les étapes suivantes supposent que vous disposez déjà d’un cluster avec une distribution Kubernetes prise en charge connectée à Azure Arc.

### <a name="install-a-specific-version-of-osm"></a>Installer une version spécifique d’OSM

Assurez-vous que votre variable d’environnement KUBECONFIG pointe vers le kubeconfig du cluster Kubernetes sur lequel vous souhaitez installer l’extension OSM.

Définissez les variables d’environnement :

```azurecli-interactive
export VERSION=0.8.4
export CLUSTER_NAME=<arc-cluster-name>
export RESOURCE_GROUP=<resource-group-name>
```

Lorsque l’Open Service Mesh avec Arc est en préversion, la commande `az k8s-extension create` accepte uniquement `pilot` pour l'indicateur `--release-train`. `--auto-upgrade-minor-version` a toujours la valeur `false` et une version doit être fournie. Si vous disposez d’un cluster OpenShift, suivez les étapes de la [section](#install-a-specific-version-of-osm-on-openshift-cluster).

```azurecli-interactive
az k8s-extension create --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP --cluster-type connectedClusters --extension-type Microsoft.openservicemesh --scope cluster --release-train pilot --name osm --version $VERSION
```

Vous devriez voir une sortie similaire à celle ci-dessous. Le déploiement du graphique OSM Helm réel sur le cluster peut prendre 3-5 minutes. Tant que ce déploiement ne se produit pas, vous continuez à voir installState comme étant En attente.

```json
{
  "autoUpgradeMinorVersion": false,
  "configurationSettings": {},
  "creationTime": "2021-04-29T17:50:11.4116524+00:00",
  "errorInfo": {
    "code": null,
    "message": null
  },
  "extensionType": "microsoft.openservicemesh",
  "id": "/subscriptions/<subscription-id>/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Kubernetes/connectedClusters/$CLUSTER_NAME/providers/Microsoft.KubernetesConfiguration/extensions/osm",
  "identity": null,
  "installState": "Pending",
  "lastModifiedTime": "2021-04-29T17:50:11.4116525+00:00",
  "lastStatusTime": null,
  "location": null,
  "name": "osm",
  "releaseTrain": "pilot",
  "resourceGroup": "$RESOURCE_GROUP",
  "scope": {
    "cluster": {
      "releaseNamespace": "arc-osm-system"
    },
    "namespace": null
  },
  "statuses": [],
  "type": "Microsoft.KubernetesConfiguration/extensions",
  "version": "0.8.4"
}
```

### <a name="install-a-specific-version-of-osm-on-openshift-cluster"></a>Installer une version spécifique de OSM sur un cluster OpenShift

1. Copiez et enregistrez le contenu suivant dans un fichier JSON. Si vous avez déjà créé un fichier de paramètres de configuration, ajoutez la ligne suivante au fichier existant pour conserver les modifications précédentes.
   ```json
   {
       "osm.OpenServiceMesh.enablePrivilegedInitContainer": "true"
   }
   ```

   Définissez le chemin d’accès du fichier en tant que variable d’environnement :
   ```azurecli-interactive
   export SETTINGS_FILE=<json-file-path>
   ```
   
2. Exécutez la commande `az k8s-extension create` utilisée pour créer l’extension OSM et transmettez le fichier de paramètres à l’aide des paramètres de configuration :
   ```azurecli-interactive
   az k8s-extension create --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP --cluster-type connectedClusters --extension-type Microsoft.openservicemesh --scope cluster --release-train pilot --name osm --version $VERSION --configuration-settings-file $SETTINGS_FILE
   ```
   
3. Ajoutez la [contrainte de contexte de sécurité](https://docs.openshift.com/container-platform/4.7/authentication/managing-security-context-constraints.html) privilégié à chaque compte de service pour les applications dans le maillage.
   ```azurecli-interactive
   oc adm policy add-scc-to-user privileged -z <service account name> -n <service account namespace>
   ```

Le déploiement du graphique OSM Helm réel sur le cluster peut prendre 3-5 minutes. Tant que ce déploiement ne se produit pas, vous continuez à voir installState comme étant En attente.

Pour vous assurer que le paramètre de conteneur init privilégié n’est pas rétabli à la valeur par défaut, transmettez le paramètre de configuration « osm.OpenServiceMesh.enablePrivilegedInitContainer » : « true » à toutes les commandes de création de l’extension az k8s-extension ultérieures.

### <a name="install-arc-enabled-osm-using-arm-template"></a>Installer l’OSM avec Arc à l’aide d’un modèle ARM

Après avoir connecté votre cluster à Azure Arc, créez un fichier json avec le format suivant, en vous assurant de mettre à jour la valeur <cluster-name> :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "ConnectedClusterName": {
            "defaultValue": "<cluster-name>",
            "type": "String",
            "metadata": {
                "description": "The Connected Cluster name."
            }
        },
        "ExtensionInstanceName": {
            "defaultValue": "osm",
            "type": "String",
            "metadata": {
                "description": "The extension instance name."
            }
        },
        "ExtensionVersion": {
            "defaultValue": "0.8.4",
            "type": "String",
            "metadata": {
                "description": "The extension type version."
            }
        },
        "ExtensionType": {
            "defaultValue": "Microsoft.openservicemesh",
            "type": "String",
            "metadata": {
                "description": "The extension type."
            }
        },
        "ReleaseTrain": {
            "defaultValue": "Pilot",
            "type": "String",
            "metadata": {
                "description": "The release train."
            }
        }
    },
    "functions": [],
    "resources": [
        {
            "type": "Microsoft.KubernetesConfiguration/extensions",
            "apiVersion": "2020-07-01-preview",
            "name": "[parameters('ExtensionInstanceName')]",
            "properties": {
                "extensionType": "[parameters('ExtensionType')]",
                "releaseTrain": "[parameters('ReleaseTrain')]",
                "version": "[parameters('ExtensionVersion')]"
            },
            "scope": "[concat('Microsoft.Kubernetes/connectedClusters/', parameters('ConnectedClusterName'))]"
        }
    ]
}
```

Définissez maintenant les variable d'environnement :

```azurecli-interactive
export TEMPLATE_FILE_NAME=<template-file-path>
export DEPLOYMENT_NAME=<desired-deployment-name>
```

Enfin, exécutez cette commande pour installer l’extension OSM via az CLI :

```azurecli-interactive
az deployment group create --name $DEPLOYMENT_NAME --resource-group $RESOURCE_GROUP --template-file $TEMPLATE_FILE_NAME
```

À présent, vous devriez être en mesure d’afficher les ressources OSM et d’utiliser l’extension OSM dans votre cluster.

## <a name="validate-the-arc-enabled-open-service-mesh-installation"></a>Valider l’installation Open Service Mesh avec Arc

Exécutez la commande suivante.

```azurecli-interactive
az k8s-extension show --cluster-type connectedClusters --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP --name osm
```

Vous devriez voir une sortie JSON similaire à celle ci-dessous :

```json
{
  "autoUpgradeMinorVersion": false,
  "configurationSettings": {},
  "creationTime": "2021-04-29T19:22:00.7649729+00:00",
  "errorInfo": {
    "code": null,
    "message": null
  },
  "extensionType": "microsoft.openservicemesh",
  "id": "/subscriptions/<subscription-id>/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Kubernetes/connectedClusters/$CLUSTER_NAME/providers/Microsoft.KubernetesConfiguration/extensions/osm",
  "identity": null,
  "installState": "Installed",
  "lastModifiedTime": "2021-04-29T19:22:00.7649731+00:00",
  "lastStatusTime": "2021-04-29T19:23:27.642+00:00",
  "location": null,
  "name": "osm",
  "releaseTrain": "pilot",
  "resourceGroup": "$RESOURCE_GROUP",
  "scope": {
    "cluster": {
      "releaseNamespace": "arc-osm-system"
    },
    "namespace": null
  },
  "statuses": [],
  "type": "Microsoft.KubernetesConfiguration/extensions",
  "version": "0.8.4"
}
```
## <a name="osm-controller-configuration"></a>Configuration du contrôleur OSM
OSM déploie une ressource MeshConfig `osm-mesh-config` dans le cadre de son plan de contrôle dans l’espace de noms arc-osm-system. L’objectif de cette ressource MeshConfig est de fournir à l’opérateur/propriétaire du maillage la possibilité de mettre à jour certaines configurations de maillage en fonction de ses besoins. Pour afficher les valeurs par défaut, utilisez la commande suivante.

```azurecli-interactive
kubectl describe meshconfig osm-mesh-config -n arc-osm-system
```
La sortie affiche les valeurs par défaut :

```azurecli-interactive
Certificate:
    Service Cert Validity Duration:  24h
  Feature Flags:
    Enable Egress Policy:      true
    Enable Multicluster Mode:  false
    Enable WASM Stats:         true
  Observability:
    Enable Debug Server:  false
    Osm Log Level:        info
    Tracing:
      Address:   jaeger.osm-system.svc.cluster.local
      Enable:    false
      Endpoint:  /api/v2/spans
      Port:      9411
  Sidecar:
    Config Resync Interval:            0s
    Enable Privileged Init Container:  false
    Envoy Image:                       mcr.microsoft.com/oss/envoyproxy/envoy:v1.18.3
    Init Container Image:              mcr.microsoft.com/oss/openservicemesh/init:v0.9.1
    Log Level:                         error
    Max Data Plane Connections:        0
    Resources:
  Traffic:
    Enable Egress:                          false
    Enable Permissive Traffic Policy Mode:  true
    Inbound External Authorization:
      Enable:              false
      Failure Mode Allow:  false
      Stat Prefix:         inboundExtAuthz
      Timeout:             1s
    Use HTTPS Ingress:     false
```
Pour plus d’informations, reportez-vous à la [référence de l’API Config](https://docs.openservicemesh.io/docs/api_reference/config/v1alpha1/). Notez que **spec.traffic.enablePermissiveTrafficPolicyMode** a pour valeur **true**. Le mode de stratégie de trafic permissif dans OSM est un mode dans lequel l’application de la stratégie de trafic [SMI](https://smi-spec.io/) est ignorée. Dans ce mode, OSM découvre automatiquement les services qui font partie du maillage de services et programme des règles de stratégie de trafic sur chaque side-car de proxy Envoy pour pouvoir communiquer avec ces services.

### <a name="making-changes-to-osm-controller-configuration"></a>Modification de la configuration du contrôleur OSM

> [!NOTE]
> Les valeurs dans la ressource MeshConfig `osm-mesh-config` sont conservées entre les mises à niveau.

Les modifications apportées à `osm-mesh-config` peuvent être effectuées à l’aide de la commande kubectl patch. Dans l’exemple suivant, le mode de stratégie de trafic permissif est remplacé par false.

```azurecli-interactive
kubectl patch meshconfig osm-mesh-config -n arc-osm-system -p '{"spec":{"traffic":{"enablePermissiveTrafficPolicyMode":false}}}' --type=merge
```

Si une valeur incorrecte est utilisée, les validations sur le CRD MeshConfig empêchent la modification avec un message d’erreur expliquant pourquoi la valeur est non valide. Par exemple, la commande ci-dessous montre ce qui se passe si nous corrigeons enableEgress en une valeur non booléenne.

```azurecli-interactive
kubectl patch meshconfig osm-mesh-config -n arc-osm-system -p '{"spec":{"traffic":{"enableEgress":"no"}}}'  --type=merge

# Validations on the CRD will deny this change
The MeshConfig "osm-mesh-config" is invalid: spec.traffic.enableEgress: Invalid value: "string": spec.traffic.enableEgress in body must be of type boolean: "string"
```

## <a name="osm-controller-configuration-version-v084"></a>Configuration du contrôleur OSM (version v0.8.4)

Actuellement, vous pouvez accéder au contrôleur OSM et le configurer via le ConfigMap. Pour afficher les paramètres de configuration du contrôleur OSM, interrogez le ConfigMap `osm-config` via `kubectl`.

```azurecli-interactive
kubectl get configmap osm-config -n arc-osm-system -o json
```

Sortie :

```json
{
  "egress": "false",
  "enable_debug_server": "false",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "true",
  "service_cert_validity_duration": "24h",
  "tracing_enable": "false",
  "use_https_ingress": "false"
}
```

Lisez la [documentation OSM ConfigMap](https://release-v0-8.docs.openservicemesh.io/docs/osm_config_map/) pour comprendre chacune des configurations disponibles. 

Pour apporter des modifications à l’élément OSM ConfigMap pour la version v0.8.4, suivez les instructions ci-dessous :

1. Copiez et enregistrez les modifications que vous souhaitez apporter dans un fichier JSON. Dans cet exemple, nous allons passer permissive_traffic_policy_mode de true à false. Chaque fois que vous apportez une modification à `osm-config`, vous devrez fournir la liste complète des modifications (par rapport à la valeur `osm-config` par défaut) dans un fichier JSON.
    ```json
    {
        "osm.OpenServiceMesh.enablePermissiveTrafficPolicy" : "false"
    }
    ```
    
    Définissez le chemin d’accès du fichier en tant que variable d’environnement :
    
    ```azurecli-interactive
    export SETTINGS_FILE=<json-file-path>
    ```
    
2. Exécutez la même commande `az k8s-extension create` que celle utilisée pour créer l’extension, mais transmettez maintenant le fichier de paramètres de configuration : 
    ```azurecli-interactive
    az k8s-extension create --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP --cluster-type connectedClusters --extension-type Microsoft.openservicemesh --scope cluster --release-train pilot --name osm --version $VERSION --configuration-settings-file $SETTINGS_FILE
    ```
    
    > [!NOTE]
    > Pour vous assurer que les modifications apportées à élément ConfigMap ne sont pas rétablies à la valeur par défaut, transmettez les mêmes paramètres de configuration à toutes les commandes suivantes de création de az k8s-extension.

## <a name="using-the-arc-enabled-open-service-mesh"></a>Utiliser Open Service Mesh avec Arc

Pour commencer à utiliser les fonctionnalités OSM, vous devez d’abord intégrer les espaces de noms de l’application à la maille du service. Téléchargez l’interface de commande OSM à partir de la [page de versions GitHub d’OSM](https://github.com/openservicemesh/osm/releases/). Une fois les espaces de noms ajoutés à la maille, vous pouvez configurer les stratégies SMI pour obtenir la fonction OSM souhaitée.

### <a name="onboard-namespaces-to-the-service-mesh"></a>Intégration des espaces de noms à la maille de service

Ajoutez les espaces de noms à la maille en exécutant la commande suivante :

```azurecli-interactive
osm namespace add <namespace_name>
```

Vous trouverez [ici](https://docs.openservicemesh.io/docs/guides/app_onboarding/#onboard-services) plus d’informations sur les services d’intégration.

### <a name="configure-osm-with-service-mesh-interface-smi-policies"></a>Configurer OSM avec les stratégies Service Mesh Interface (SMI)

Vous pouvez commencer par une [application de démonstration](https://docs.openservicemesh.io/docs/getting_started/quickstart/manual_demo/#deploy-applications) ou utiliser votre environnement de test pour tester les stratégies SMI.

> [!NOTE] 
> Assurez-vous que la version de l’application Bookstore que vous exécutez correspond à la version de l’extension OSM installée sur votre cluster. Par exemple, si vous utilisez la v0.8.4 de l’extension OSM, utilisez la démonstration Bookstore de la branche release-v0.8 de l’espace de stockage en amont d’OSM.

### <a name="configuring-your-own-jaeger-prometheus-and-grafana-instances"></a>Configuration de vos propres instances Jaeger, Prometheus et Grafana

L’extension OSM n’installe pas les modules complémentaires comme [Jaeger](https://www.jaegertracing.io/docs/getting-started/), [Prometheus](https://prometheus.io/docs/prometheus/latest/installation/) et [Grafana](https://grafana.com/docs/grafana/latest/installation/) afin que les utilisateurs puissent intégrer OSM avec leurs propres instances en cours d’exécution de ces outils à la place. Pour une intégration avec vos propres instances, consultez la documentation suivante :

> [!NOTE]
> Utilisez avec précaution les commandes fournies dans la documentation GitHub sur OSM. Veillez à utiliser le bon nom d’espace de noms « arc-osm-system » lorsque vous apportez des modifications à `osm-mesh-config`.

- [Instance BYO-Jaeger](https://docs.openservicemesh.io/docs/guides/observability/tracing/#byo-bring-your-own)
- [Instance BYO-Prometheus](https://docs.openservicemesh.io/docs/guides/observability/metrics/#byo-prometheus)
- [Tableau de bord BYO-Grafana](https://docs.openservicemesh.io/docs/guides/observability/metrics/#importing-dashboards-on-a-byo-grafana-instance)


## <a name="monitoring-application-using-azure-monitor-and-applications-insights"></a>Surveillance d’application à l’aide d’Azure Monitor et d’Application Insights

Azure Monitor et Azure Application Insights vous aident à optimiser la disponibilité et les performances de vos applications et services en fournissant une solution complète pour collecter, analyser et utiliser la télémétrie de vos environnements cloud et locaux.

Open Service Mesh avec Arc sera profondément intégré à ces deux services Azure et fournira une expérience Azure transparente permettant de visualiser les indicateurs clés de performance essentiels fournis par les métriques d’OSM et d’y répondre. Suivez les étapes ci-dessous pour autoriser Azure Monitor à mettre en rebut les points de terminaison Prometheus pour la collecte des métriques d’application. 

1. Assurez-vous que les espaces de noms d’application que vous souhaitez analyser sont intégrés à la maille. Suivez les instructions [disponibles ici](#onboard-namespaces-to-the-service-mesh).

2. Exposez les points de terminaison Prometheus pour les espaces de noms d’application.
    ```azurecli-interactive
    osm metrics enable --namespace <namespace1>
    osm metrics enable --namespace <namespace2>
    ```
    Pour v0.8.4, veillez à ce que `prometheus_scraping` soit défini sur `true` dans le ConfigMap `osm-config`.

3. Installez l’extension d’Azure Monitor à l’aide des conseils disponibles [ici](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md?toc=/azure/azure-arc/kubernetes/toc.json).

4. Ajoutez les espaces de noms que vous souhaitez analyser dans container-azm-ms-osmconfig de ConfigMap. Téléchargez le ConfigMap [ici](https://github.com/microsoft/Docker-Provider/blob/ci_prod/kubernetes/container-azm-ms-osmconfig.yaml).
    ```azurecli-interactive
    monitor_namespaces = ["namespace1", "namespace2"]
    ```

5. Exécutez la commande kubectl suivante
    ```azurecli-interactive
    kubectl apply -f container-azm-ms-osmconfig.yaml
    ```

L’affichage des métriques dans Log Analytics peut prendre jusqu’à 15 minutes. Vous pouvez essayer d’interroger la table InsightsMetrics.

```azurecli-interactive
InsightsMetrics
| where Name contains "envoy"
| extend t=parse_json(Tags)
| where t.app == "namespace1"
```
En savoir plus sur l’intégration avec Azure Monitor [ici](https://github.com/microsoft/Docker-Provider/blob/ci_dev/Documentation/OSMPrivatePreview/ReadMe.md).

### <a name="navigating-the-osm-dashboard"></a>Navigation dans le tableau de bord OSM

1. Accédez à votre cluster Kubernetes connecté avec Arc à l’aide de ce [lien](https://aka.ms/azmon/osmarcux).
2. Accédez à Azure Monitor et accédez à l’onglet rapports pour accéder au classeur OSM.
3. Sélectionnez l’intervalle de temps et l’espace de noms pour étendre vos services.

![Classeur OSM](./media/tutorial-arc-enabled-open-service-mesh/osm-workbook.jpg)

#### <a name="requests-tab"></a>Onglet Requêtes

- Cet onglet fournit le résumé de toutes les requêtes HTTP envoyées via le service à service dans OSM.
- Vous pouvez afficher tous les services et tous les services auxquels il communique en sélectionnant le service dans la grille.
- Vous pouvez voir le nombre total de requêtes, le taux d’erreur de requête et la latence P90.
- Vous pouvez descendre dans la hiérarchie de la vue de destination et afficher des tendances pour le code d’erreur/de réussite HTTP, le taux de réussite, l’utilisation des ressources pod et les latences à différents centile.

#### <a name="connections-tab"></a>Onglet Connexions

- Cet onglet fournit un résumé de toutes les connexions entre vos services dans Open Service Mesh.
- Connexions sortantes : nombre total de connexions entre les services source et de destination.
- Connexions actives sortantes : dernier nombre de connexions actives entre la source et la destination dans l’intervalle de temps sélectionné.
- Connexions sortantes : nombre total de connexions entre les services source et de destination

## <a name="upgrade-the-osm-extension-instance-to-a-specific-version"></a>Mettre à niveau l’instance de l’extension OSM vers une version spécifique

Pendant les mises à niveau, il peut y avoir un temps d’arrêt du plan de contrôle. Le plan de données n’est affecté que lors des mises à jour de CRD.

### <a name="supported-upgrades"></a>Mises à niveau prises en charge

L’extension OSM peut être mise à niveau jusqu’à la version mineure suivante. Passer à une version antérieure et mettre à niveau vers une version majeures ne sont pas pris en charge pour l’instant.

### <a name="crd-upgrades"></a>Mises à niveau de CRD

L’extension OSM ne peut pas être mise à niveau vers une nouvelle version si cette version contient des mises à jour de version CRD sans supprimer préalablement les CRD existants. Vous pouvez vérifier si une mise à niveau d’OSM comprend également des mises à jour de version de CRD en vérifiant la section mises à jour CRD des [Notes de publication OSM](https://github.com/openservicemesh/osm/releases).

Veillez à sauvegarder vos ressources personnalisées avant de supprimer les CRD afin qu’elles puissent être facilement recréées après la mise à niveau. Ensuite, suivez les instructions de mise à niveau capturées ci-dessous.

> [!NOTE] 
> La mise à niveau des CRD affecte le plan de données, car les stratégies SMI n’existent pas entre le moment où elles sont supprimées et le moment où elles sont à nouveau créées.

### <a name="upgrade-instructions"></a>Instructions de mise à niveau

1. Supprimez les anciennes définitions CRD et ressources personnalisées (exécutées à partir de la racine du [dépôt OSM](https://github.com/openservicemesh/osm)). Veillez à ce que l’étiquette des [définitions CRD OSM](https://github.com/openservicemesh/osm/tree/main/charts/osm/crds) corresponde à la nouvelle version du graphique.
    ```azurecli-interactive
    kubectl delete --ignore-not-found --recursive -f ./charts/osm/crds/

2. Install the updated CRDs.
    ```azurecli-interactive
    kubectl apply -f charts/osm/crds/
    ```

3. Définissez la nouvelle version du graphique en tant que variable d’environnement :
    ```azurecli-interactive
    export VERSION=<chart version>
    ```
    
4. Exécuter az k8s-extension create avec la nouvelle version du graphique
    ```azurecli-interactive
    az k8s-extension create --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP --cluster-type connectedClusters --extension-type Microsoft.openservicemesh --scope cluster --release-train pilot --name osm --version $VERSION --configuration-settings-file $SETTINGS_FILE
    ```

5. Recréer des ressources personnalisées à l’aide des nouvelles définitions CRD

## <a name="uninstall-arc-enabled-open-service-mesh"></a>Désinstaller Open Service Mesh avec Arc

Utilisez la commande suivante :

```azurecli-interactive
az k8s-extension delete --cluster-type connectedClusters --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP --name osm -y
```

Vérifiez que l’instance d’extension a été supprimée :

```azurecli-interactive
az k8s-extension list --cluster-type connectedClusters --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP
```

Cette sortie ne doit pas inclure OSM. Si vous n’avez pas d’autres extensions installées sur votre cluster, il s’agira simplement d’un tableau vide.

Quand vous utilisez la commande az k8s-extension pour supprimer l’extension OSM, l’espace de noms arc-osm-system n’est pas supprimé et les ressources réelles au sein de l’espace de noms (telles que la configuration de webhook et du pod osm-controller) prennent environ 10 minutes à supprimer.

> [!NOTE] 
> Utilisez l’interface de ligne de commande az k8s-extension pour désinstaller les composants gérés par OSM avec Arc. L’utilisation de l’interface de ligne de commande OSM pour la désinstallation n’est pas prise en charge par Arc et peut entraîner un comportement indésirable.

## <a name="troubleshooting"></a>Résolution des problèmes

Reportez-vous au guide de dépannage [disponible ici](troubleshooting.md#arc-enabled-open-service-mesh).

## <a name="next-steps"></a>Étapes suivantes

> **Vous voulez juste essayer ?**  
> Commencez rapidement avec un scénario [Azure Arc JumpStart](https://aka.ms/arc-jumpstart-osm) à l’aide de l’API de Cluster.
