---
title: Collecter et transporter des métriques – Azure IoT Edge
description: Utilisez Azure Monitor pour surveiller à distance les métriques intégrées d’IoT Edge
author: veyalla
manager: philmea
ms.author: veyalla
ms.date: 06/09/2021
ms.topic: conceptual
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b7c849bd00991eb243c9620c1b08bede7f239170
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111904388"
---
# <a name="collect-and-transport-metrics-preview"></a>Collecter et transporter des métriques (préversion)

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Vous pouvez surveiller à distance votre flotte IoT Edge à l’aide d’Azure Monitor et des métriques intégrées. Pour activer cette capacité sur votre appareil, ajoutez le module collecteur de métriques à votre déploiement et configurez-le pour collecter et transporter les métriques du module vers Azure Monitor.

## <a name="architecture"></a>Architecture

[![Architecture de surveillance des métriques](./media/how-to-collect-and-transport-metrics/arch.png)](./media/how-to-collect-and-transport-metrics/arch.png#lightbox)

| Notes | Description |
|-|-|
|  1 | Tous les modules doivent émettre des métriques à l’aide du [modèle de données Prometheus](https://prometheus.io/docs/concepts/data_model/). Si les [métriques intégrées](how-to-access-built-in-metrics.md) permettent par défaut une large visibilité de la charge de travail, vous pouvez également utiliser des modules personnalisés pour émettre des métriques spécifiques d’un scénario afin d’améliorer la solution de surveillance. Pour découvrir comment instrumenter des modules personnalisés à l’aide de bibliothèques open source, consultez l’article [Ajouter des métriques personnalisées](how-to-add-custom-metrics.md). |
|  2️ | Le [module collecteur de métriques](https://aka.ms/edgemon-metric-collector) est un module IoT Edge fourni par Microsoft qui collecte des métriques d’un module de charge de travail et les transporte hors de l’appareil. La collecte de métriques utilise un modèle d’*extraction*. Vous pouvez configurer la fréquence de collecte, les points de terminaison et les filtres de manière à contrôler les données sortant du module. Pour plus d’informations, consultez la section [Configuration du collecteur de métriques](#metrics-collector-configuration) plus loin dans cet article. |
|  3️ | Vous avez le choix entre deux options pour envoyer des métriques du module collecteur de métriques vers le cloud. L’*option 1* envoie les métriques à Log Analytics. <sup>1</sup> Les métriques collectées sont ingérées dans l’espace de travail Log Analytics spécifié à l’aide d’une table native fixe appelée `InsightsMetrics`. Le schéma de cette table est compatible avec le modèle de données métriques Prometheus.<br><br> Cette option requiert l’accès à l’espace de travail sur le port de sortie 443. L’ID et la clé de l’espace de travail Log Analytics doivent être spécifiés dans la configuration du module. Pour activer la fonctionnalité dans des réseaux restreints, consultez la section [Activer la fonctionnalité dans des scénarios d’accès réseau restreint](#enable-in-restricted-network-access-scenarios) plus loin dans cet article.
|  4️ | Chaque entrée de métrique contient le `ResourceId` spécifié dans la [configuration du module](#metrics-collector-configuration). Cette association lie automatiquement la métrique à la ressource spécifiée (par exemple, IoT Hub). Par conséquent, les [modèles de classeur IoT Edge organisés](how-to-explore-curated-visualizations.md) peuvent récupérer des métriques en émettant des requêtes sur la ressource. <br><br> Cette approche permet également à plusieurs hubs IoT de partager en toute sécurité un espace de travail Log Analytics en tant que base de données de métriques. |
|  5️ | L’*option 2* envoie les métriques à IoT Hub. <sup>1</sup> Le module du collecteur peut être configuré pour envoyer les métriques collectées sous la forme de [messages appareil-à-cloud](../iot-hub/iot-hub-devguide-messages-d2c.md) JSON codés en UTF-8 via le module `edgeHub`. Cette option a pour effet de déverrouiller la surveillance des appareils IoT Edge verrouillés, dont l’accès externe n’est autorisé qu’au point de terminaison IoT Hub. Elle permet également de surveiller les appareils IoT Edge enfants dans une configuration imbriquée où les appareils enfants ne peuvent accéder qu’à leur appareil parent. |
|  6️ | Lorsque les métriques sont acheminées via IoT Hub, un flux de travail cloud (ponctuel) doit être configuré. Le flux de travail traite les messages du module collecteur de métriques et les envoie à l’espace de travail Log Analytics. Le flux de travail active les [visualisations organisées](how-to-explore-curated-visualizations.md) et les [alertes](how-to-create-alerts.md), même pour les métriques arrivant par ce chemin facultatif. Pour plus de détails sur la configuration de ce flux de travail cloud, consultez la section [Acheminer des métriques via IoT Hub](#route-metrics-through-iot-hub). |

<sup>1</sup> Actuellement, l’utilisation de l’*option 1* pour transporter directement les métriques de l’appareil IoT Edge vers Log Analytics est la voie la plus simple, qui nécessite une configuration minimale. La première option est préférable, sauf si votre scénario spécifique exige l’approche de l’*option 2* afin que l’appareil IoT Edge communique uniquement avec le service IoT Hub.

## <a name="metrics-collector-module"></a>Module collecteur de métriques

Un module collecteur de métriques fourni par Microsoft peut être ajouté à un déploiement IoT Edge pour collecter des métriques et les envoyer à Azure Monitor. Le code du module est open source et disponible dans le [dépôt GitHub IoT Edge](https://github.com/Azure/iotedge/tree/release/1.1/edge-modules/azure-monitor).

Le module collecteur de métriques est fourni sous la forme d’une image de conteneur Docker multi-architecture qui prend en charge Linux x64, ARM32, ARM64 et Windows x64 (version 1809). Il est publiquement disponible à l’adresse **[`mcr.microsoft.com/azureiotedge-metrics-collector`](https://aka.ms/edgemon-metrics-collector)** .

Il est également disponible dans [Place de marché des modules IoT Edge](https://aka.ms/edgemon-module-marketplace).

## <a name="metrics-collector-configuration"></a>Configuration du collecteur de métriques

Toute la configuration du collecteur de métriques est effectuée à l’aide de variables d’environnement. Au minimum, les variables répertoriées dans le tableau ci-dessous et marquées comme **obligatoires** doivent être spécifiées.

| Nom de variable d’environnement | Description |
|-|-|
| `ResourceId` | ID de ressource de l’IoT Hub avec lequel l’appareil communique. Pour connaître les étapes de récupération de l’ID de ressource, consultez la section [ID de ressource](#resource-id).  <br><br>  **Obligatoire** <br><br> Valeur par défaut : *aucune* |
| `UploadTarget` |  Contrôle si les métriques sont envoyées directement à Azure Monitor avec le protocole HTTPS, ou à IoT Hub en tant que messages D2C. Pour plus d’informations, consultez [cible de chargement](#upload-target). <br><br>Il peut s’agir d’**AzureMonitor** ou d’**IoTMessage**  <br><br>  **Non obligatoire** <br><br> Valeur par défaut : *AzureMonitor* |
| `LogAnalyticsWorkspaceId` | [ID d’espace de travail Log Analytics](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key). <br><br>**Obligatoire** uniquement si *UploadTarget* est *AzureMonitor* <br><br>Valeur par défaut : *aucune* |
| `LogAnalyticsSharedKey` | [Clé d’espace de travail Log Analytics](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key). <br><br>**Obligatoire** uniquement si  *UploadTarget*  est  *AzureMonitor*   <br><br> Valeur par défaut : *aucune* |
| `MetricsEndpointsCSV` | Liste séparée par des virgules des points de terminaison à partir desquels collecter les métriques Prometheus. Tous les points de terminaison de module à partir desquels collecter les métriques doivent figurer dans cette liste.<br><br>  Exemple : *http://edgeAgent:9600/metrics , http://edgeHub:9600/metrics, http://MetricsSpewer:9417/metrics* <br><br>  **Non obligatoire** <br><br> Valeur par défaut : *http://edgeHub:9600/metrics , http://edgeAgent:9600/metrics* |
| `AllowedMetrics` | Liste des métriques à collecter, toutes les autres métriques étant ignorées. Définissez une chaîne vide pour désactiver la fonction. Pour plus d’informations, consultez [Autoriser et interdire des listes](#allow-and-disallow-lists). <br><br>Exemple : *metricToScrape{quantile=0.99}[endpoint=http://MetricsSpewer:9417/metrics ]*<br><br>  **Non obligatoire** <br><br> Valeur par défaut : *vide* |
| `BlockedMetrics` | Liste des métriques à ignorer. Remplace *AllowedMetrics*, de sorte qu’une métrique n’est pas rapportée si elle est incluse dans les deux listes. Pour plus d’informations, consultez [Autoriser et interdire des listes](#allow-and-disallow-lists). <br><br>   Exemple : *metricToIgnore{quantile=0.5}[endpoint=http://VeryNoisyModule:9001/metrics ], docker_container_disk_write_bytes*<br><br>  **Non obligatoire**  <br><br>Valeur par défaut : *vide* |
| `CompressForUpload` | Contrôle si une compression doit être utilisée lors du chargement de métriques. S’applique à toutes les cibles de chargement.<br><br>  Exemple : *true* <br><br>    **Non obligatoire** <br><br>  Valeur par défaut : *true* |

### <a name="resource-id"></a>ID de ressource

Le module collecteur de métriques requiert l’ID Azure Resource Manager du hub IoT auquel appartient l’appareil IoT Edge. Fournissez cet ID en tant que valeur de la variable d’environnement **ResourceId**.

L’ID de ressource prend le format suivant :

```input
/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Devices/IoTHubs/<iot hub name>
```

Vous pouvez trouver l’ID de ressource dans la page **Propriétés** du hub IoT dans le portail Azure.

:::image type="content" source="./media/how-to-collect-and-transport-metrics/resource-id.png" alt-text="Récupérez l’ID de ressource dans les propriétés du service IoT Hub.":::

Vous pouvez aussi récupérer l’ID avec la commande [az resource show](/cli/azure/resource#az_resource_show) :

```azurecli-interactive
az resource show -g \<group> -n \<name> --resource-type "Microsoft.Devices/IoTHubs"`
```

### <a name="upload-target"></a>Cible de chargement

L’option de configuration **UploadTarget** contrôle si les métriques sont envoyées directement à Azure Monitor ou au service IoT Hub.

Si vous définissez **UploadTarget** sur **IoTMessage**, les métriques de votre module sont publiées en tant que messages IoT. Ces messages sont émis à partir du point de terminaison sous la forme de fichiers JSON encodés UTF8`/messages/modules/<module name>/outputs/metricOutput`. au format suivant :

```json
[{
    "TimeGeneratedUtc": "<time generated>",
    "Name": "<prometheus metric name>",
    "Value": <decimal value>,
    "Label": {
        "<label name>": "<label value>"
    }
}, {
    "TimeGeneratedUtc": "2020-07-28T20:00:43.2770247Z",
    "Name": "docker_container_disk_write_bytes",
    "Value": 0.0,
    "Label": {
        "name": "AzureMonitorForIotEdgeModule"
    }
}]
```

### <a name="allow-and-disallow-lists"></a>Autoriser et interdire des listes

Les options de configuration `AllowedMetrics` et `BlockedMetrics` prennent des listes de sélecteurs de métriques séparées par des espaces ou des virgules. Une métrique correspondra à la liste et sera incluse ou exclue si elle correspond à une ou plusieurs métriques de l’une ou l’autre liste.

Les sélecteurs métriques utilisent un format similaire à un sous-ensemble du langage de requête [PromQL](https://prometheus.io/docs/prometheus/latest/querying/basics/).

```query
metricToSelect{quantile=0.5,otherLabel=~Re[ge]*|x}[http://VeryNoisyModule:9001/metrics]
```

Les sélecteurs de métriques se composent de trois parties :

Nom de la métrique (`metricToSelect`).

* Les caractères génériques `*` (n’importe quelle chaîne de caractères) et `?` (n’importe quel caractère unique) peuvent être utilisés dans les noms de métriques. Par exemple, `*CPU` correspondrait à `maxCPU` et `minCPU`, mais pas à `CPUMaximum`. `???CPU` correspondrait à `maxCPU` et `minCPU`, mais pas à `maximumCPU`.
* Ce composant est obligatoire dans un sélecteur de métriques.

Sélecteurs basés sur une étiquette (`{quantile=0.5,otherLabel=~Re[ge]*|x}`).

* Plusieurs valeurs métriques peuvent être placées entre accolades. Les valeurs doivent être séparées par des virgules.
* Une correspondance est établie avec une métrique si toutes les étiquettes dans le sélecteur sont présentes et correspondent.
* Comme pour le langage de requête PromQL, les opérateurs de correspondance suivants sont autorisés.
  * `=` correspondance avec les étiquettes qui sont exactement identiques à la chaîne entrée (sensible à la casse).
  * `!=` correspondance avec les étiquettes qui ne sont pas exactement identiques à la chaîne entrée.
  * `=~` correspondance avec les étiquettes contenant une expression régulière. exemple : `label=~CPU|Mem|[0-9]*`
  * `!=` correspondance avec les étiquettes ne contenant pas une expression régulière.
  * L’expression régulière est entièrement ancrée (les signes ^ et $ sont automatiquement ajoutés au début et à la fin de chaque expression régulière)
  * Ce composant est facultatif dans un sélecteur de métriques.

Sélecteur de point de terminaison (`[http://VeryNoisyModule:9001/metrics]`).

* L’URL doit correspondre exactement à une URL figurant dans `MetricsEndpointsCSV`.
* Ce composant est facultatif dans un sélecteur de métriques.

Une métrique doit correspondre à toutes les parties d’un sélecteur donné pour être sélectionnée. Il doit correspondre au nom *et* avoir toutes les mêmes étiquettes avec les valeurs correspondantes *et* provenir du point de terminaison donné. Par exemple, `mem{quantile=0.5,otherLabel=foobar}[http://VeryNoisyModule:9001/metrics]` ne correspond pas au sélecteur `mem{quantile=0.5,otherLabel=~foo|bar}[http://VeryNoisyModule:9001/metrics]`. Vous devez utiliser plusieurs sélecteurs pour créer un comportement d’opérateur « ou » plutôt qu’un comportement d’opérateur « et ».

Par exemple, pour autoriser la métrique `mem` d’un module `module1`, quelle que soit l’étiquette, mais n’autoriser la même métrique de `module2` qu’avec l’étiquette `agg=p99`, vous pouvez ajouter le sélecteur à `AllowedMetrics` :

```query
mem{}[http://module1:9001/metrics] mem{agg="p99"}[http://module2:9001/metrics]
```

Ou, pour autoriser les métriques `mem` et `cpu`, quelles que soient les étiquettes ou le point de terminaison, ajoutez ce qui sui à `AllowedMetrics` :

```query
mem cpu
```

## <a name="enable-in-restricted-network-access-scenarios"></a>Activer la fonctionnalité dans des scénarios d’accès réseau restreint

Si vous envoyez des métriques directement à l’espace de travail Log Analytics, autorisez l’accès sortant aux URL suivantes :

* `https://<LOG_ANALYTICS_WORKSPACE_ID>.ods.opinsights.azure.com/*`
* `https://<LOG_ANALYTICS_WORKSPACE_ID>.oms.opinsights.azure.com/*`

### <a name="proxy-considerations"></a>Considérations relatives au proxy

Le module collecteur de métriques est écrit dans .NET Core. Utilisez donc les mêmes instructions que pour les modules système pour [permettre la communication via un serveur proxy](how-to-configure-proxy-support.md#configure-deployment-manifests).

La collecte de métriques à partir de modules locaux utilise le protocole HTTP. Excluez toute communication locale passant par le serveur proxy en définissant la variable d’environnement `NO_PROXY`.

Définir la valeur `NO_PROXY` sur une liste de noms d’hôte séparés par des virgules à exclure. Utilisez des noms de module pour les noms d’hôte. Par exemple : *edgeHub,edgeAgent,myCustomModule*.

## <a name="route-metrics-through-iot-hub"></a>Acheminer les métriques via le service IoT Hub

Il est parfois nécessaire d’ingérer des métriques via IoT Hub au lieu de les envoyer directement à Log Analytics. Par exemple, lors de la surveillance d’[appareils IoT Edge dans une configuration imbriquée](tutorial-nested-iot-edge.md) où les appareils enfants n’ont accès qu’au hub IoT Edge de leur appareil parent. Un autre exemple est le déploiement d’un appareil IoT Edge avec un accès réseau sortant uniquement vers le service IoT Hub.

Pour permettre la surveillance dans ce scénario, le module collecteur de métriques peut être configuré pour envoyer des métriques sous forme de messages appareil-à-cloud (D2C) via le module edgeHub. Vous pouvez activer cette fonctionnalité en définissant la variable d’environnement `UploadTarget` sur `IoTMessage` dans la [configuration](#metrics-collector-configuration) du collecteur.

>[!TIP]
>N’oubliez pas d’ajouter un itinéraire edgeHub pour la livraison des messages de métriques du module collecteur au service IoT Hub. Il ressemble à `FROM /messages/modules/replace-with-collector-module-name/* INTO $upstream`.

Cette option nécessite une configuration supplémentaire pour livrer les messages de métriques parvenant au service IoT Hub à l’espace de travail Log Analytics. Sans cette configuration, les autres parties de l’intégration, telles que les [visualisations organisées](how-to-explore-curated-visualizations.md) et les [alertes](how-to-create-alerts.md), ne fonctionneront pas.

>[!NOTE]
>Sachez que cette option occasionne des coûts supplémentaires. Les messages de métriques sont comptabilisés par rapport à votre quota de messages IoT Hub. Vous serez également facturé pour les ressources d’ingestion dans Log Analytics et de flux de travail dans le cloud.

### <a name="sample-cloud-workflow"></a>Exemple de flux de travail cloud

Un flux de travail cloud qui fournit des messages de métriques d’IoT Hub à Log Analytics est disponible dans l’[exemple de journalisation et de surveillance IoT Edge](https://github.com/Azure-Samples/iotedge-logging-and-monitoring-solution#monitoring-architecture-reference). Vous pouvez déployer l’exemple sur des ressources cloud existantes ou l’utiliser en tant que référence de déploiement en production.

## <a name="next-steps"></a>Étapes suivantes

Explorez les types de [visualisations organisées](how-to-explore-curated-visualizations.md) qu’Azure Monitor active.
