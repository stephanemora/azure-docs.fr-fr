---
title: Ajout de métriques personnalisées - Azure IoT Edge
description: Enrichir les métriques intégrées avec des métriques propres à des scénarios à partir de modules personnalisés
author: veyalla
manager: philmea
ms.author: veyalla
ms.date: 06/08/2021
ms.topic: conceptual
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 12defc783be6fb1a87b815284b1fbf019d8c8817
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111904384"
---
# <a name="add-custom-metrics-preview"></a>Ajouter des métriques personnalisées (préversion)

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Recueillez des métriques personnalisées à partir de vos modules IoT Edge en plus des métriques intégrées fournies par les modules système. Les [métriques intégrées](how-to-access-built-in-metrics.md) fournissent une excellente visibilité de la base de référence pour l’intégrité de votre déploiement. Toutefois, vous pouvez avoir besoin d’informations supplémentaires provenant des modules personnalisés pour avoir une visibilité complète. Les modules personnalisés peuvent être intégrés à votre solution de supervision à l’aide de la [bibliothèque cliente Prometheus](https://prometheus.io/docs/instrumenting/clientlibs/) appropriée pour émettre des métriques. Ces informations supplémentaires peuvent activer de nouvelles vues ou alertes spécialisées en fonction de vos besoins.

## <a name="sample-modules-repository"></a>Exemple de référentiel de modules

Consultez le [référentiel azure-samples](https://github.com/Azure-Samples/iotedge-module-prom-custom-metrics) pour obtenir des exemples de modules personnalisés instrumentés pour émettre des métriques. Même si un exemple dans le langage de votre choix n’est pas encore disponible, l’approche générale peut vous aider.

## <a name="naming-conventions"></a>Conventions d’affectation de noms

Consultez les [meilleures pratiques](https://prometheus.io/docs/practices/naming/) de la documentation Prometheus pour obtenir des conseils généraux. Les recommandations supplémentaires suivantes peuvent être utiles pour les scénarios IoT Edge.

* Incluez le nom du module au début du nom de la métrique pour préciser le module qui a émis la métrique.

* Incluez le nom du hub IoT, l’ID d’appareil IoT Edge et l’ID de module en tant qu’étiquettes (également appelées *balises*/*dimensions*) dans chaque métrique. Ces informations sont disponibles sous forme de variables d’environnement pour chaque module démarré par l’agent IoT Edge. L’approche est [illustrée](https://github.com/Azure-Samples/iotedge-module-prom-custom-metrics/blob/b6b8501adb484521b76e6f317fefee57128834a6/csharp/Program.cs#L49) dans l’exemple de référentiel. Sans ce contexte, il est impossible d’associer une valeur de métrique donnée à un appareil particulier.

* Incluez un ID d’instance dans les étiquettes. Un ID d’instance peut être n’importe quel ID unique, tel qu’un [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) qui est généré lors du démarrage du module. Les informations d’ID d’instance peuvent aider à rapprocher les redémarrages de module lors du traitement des métriques d’un module dans le backend.

## <a name="configure-the-metrics-collector-to-collect-custom-metrics"></a>Configurer le collecteur de métriques pour collecter des métriques personnalisées

Une fois qu’un module personnalisé émet des métriques, l’étape suivante consiste à configurer le [module metrics-collector](how-to-collect-and-transport-metrics.md#metrics-collector-module) pour collecter et transporter des métriques personnalisées.

La variable d’environnement `MetricsEndpointsCSV` doit être mise à jour pour inclure l’URL du point de terminaison des métriques du module personnalisé. Lors de la mise à jour de la variable d’environnement, veillez à inclure les points de terminaison du module système, comme indiqué dans l’exemple de [configuration du collecteur de métriques](how-to-collect-and-transport-metrics.md#metrics-collector-configuration).

>[!NOTE]
>Par défaut, un point de terminaison de métriques d’un module personnalisé n’a pas besoin d’être mappé à un port d’hôte pour permettre au collecteur de métriques d’y accéder. Sauf en cas de substitution explicite, sur Linux, les deux modules sont démarrés sur un [réseau de pont Docker défini par l’utilisateur](https://docs.docker.com/network/bridge/#differences-between-user-defined-bridges-and-the-default-bridge) nommé *azure-iot-edge*.
>
>Les réseaux Docker définis par l’utilisateur incluent un programme de résolution DNS par défaut qui autorise les communications entre modules à l’aide de noms de module (conteneur). Par exemple, si un module personnalisé nommé *Module1* émet des métriques sur le port HTTP *9600* au chemin */metrics*, le collecteur doit être configuré pour collecter à partir du point de terminaison *http://module1:9600/metrics* .

Exécutez la commande suivante sur l’appareil IoT Edge pour tester si les métriques émises par un module personnalisé sur le port HTTP *9600* à l’emplacement */metrics* sont accessibles :

```bash
sudo docker exec replace-with-metrics-collector-module-name curl http://replace-with-custom-module-name:9600/metrics
```

## <a name="add-custom-visualizations"></a>Ajouter des visualisations personnalisées

Une fois que vous avez reçu des métriques personnalisées dans Log Analytics, vous pouvez créer des visualisations et des alertes personnalisées. Les classeurs de supervision peuvent être enrichis pour ajouter des visualisations soutenues par des requêtes.

Chaque métrique est associée à l’ID de ressource du hub IoT. C’est pourquoi vous pouvez vérifier si vos métriques personnalisées ont été correctement gérées à partir de la page **Journaux** du hub IoT associé et non de l’espace de travail Log Analytics en appui. Utilisez cette requête KQL de base pour effectuer la vérification :

```KQL
InsightsMetrics
| where Name == 'replace-with-custom-metric-name'
```

Une fois que vous avez confirmé l’ingestion, vous pouvez créer un nouveau classeur ou enrichir un classeur existant. Comme guide, utilisez la [documentation sur les classeurs](../azure-monitor/visualize/workbooks-overview.md) ainsi que les requêtes des [classeurs IoT Edge](how-to-explore-curated-visualizations.md).

Lorsque vous êtes satisfait des résultats, vous pouvez [partager le classeur](../azure-monitor/visualize/workbooks-access-control.md) avec votre équipe, ou les [déployer par programme](../azure-monitor/visualize/workbooks-automate.md) dans le cadre des déploiements de ressources de votre organisation.

## <a name="next-steps"></a>Étapes suivantes

Explorez les options de visualisation des métriques supplémentaires avec des [classeurs organisés](how-to-explore-curated-visualizations.md).
