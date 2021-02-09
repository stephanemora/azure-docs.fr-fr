---
title: Notes de publication pour Live Video Analytics sur IoT Edge – Azure
description: Cette rubrique fournit des notes de publication sur les versions, les améliorations, les correctifs de bogues et les problèmes connus de Live Video Analytics sur IoT Edge.
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: f130b93b8d799c371a640f2b29c22c0d77834cba
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954393"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>Notes de publication pour Live Video Analytics sur IoT Edge

>Soyez notifié de la disponibilité des mises à jour sur cette page en faisant un copier-coller de cette URL : `https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us` dans votre lecteur de flux RSS.

Cet article vous fournit des informations sur :

* Versions les plus récentes
* Problèmes connus
* Résolution des bogues
* Fonctionnalités dépréciées

<hr width=100%>

## <a name="january-12-2021"></a>12 janvier 2021

La balise de version correspondant à l’actualisation de janvier 2021 du module est la suivante :

```
mcr.microsoft.com/media/live-video-analytics:2.0.1
```

> [!NOTE]
> Dans les démarrages rapides et les tutoriels, les manifestes de déploiement utilisent une balise 2 (live-video-analytics:2). Par conséquent, le simple redéploiement de tels manifestes doit mettre à jour le module sur votre application Edge > Appareils.
### <a name="bug-fixes"></a>Résolution des bogues 

* Les champs `ActivationSignalOffset`, `MinimumActivationTime` et `MaximumActivationTime` dans les processeurs de la porte de signal n’ont pas été correctement définis comme propriétés requises. Ils correspondent désormais à des propriétés **facultatives**.
* Correction d’un bogue d’utilisation qui entraîne l’arrêt du module Live Video Analytics sur IoT Edge lors de son déploiement dans certaines régions.

<hr width=100%>

## <a name="december-14-2020"></a>14 décembre 2020
Cette version est la préversion publique d’actualisation de Live Video Analytics sur IoT Edge. La balise de version est

```
     mcr.microsoft.com/media/live-video-analytics:2.0.0
```
### <a name="module-updates"></a>Mises à jour de module
* Ajout de la prise en charge de plusieurs processeurs d’extension HTTP et gRPC par topologie de graphe.
* Ajout de la prise en charge de la [gestion de l’espace disque pour les nœuds récepteurs](upgrading-lva-module.md#disk-space-management-with-sink-nodes).
* Ajout de la prise en charge par le nœud `MediaGraphGrpcExtension` de la propriété [extensionConfiguration](grpc-extension-protocol.md) permettant d’utiliser plusieurs modèles IA au sein d’un même serveur gRPC.
* Ajout de la prise en charge de la collecte des métriques du module Live Video Analytics au [format Prometheus](https://prometheus.io/docs/practices/naming/). En savoir plus sur la [collecte des métriques et de l’affichage dans Azure Monitor](monitoring-logging.md#azure-monitor-collection-via-telegraf). 
* Ajout de la possibilité de filtrer la sélection de sortie. Vous pouvez transmettre **l’audio uniquement**, la **vidéo uniquement** ou **l’audio et la vidéo** avec `outputSelectors` à n’importe quel nœud de graphe. 
* Processeur de filtre de fréquence d’images **déconseillé**.  
    * Gestion de la fréquence d’images désormais disponible dans les nœuds du processeur d’extension de graphe.

### <a name="visual-studio-code-extension"></a>Extension de Visual Studio Code
* Publication de [Live Video Analytics sur IoT Edge](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.live-video-analytics-edge), une extension Visual Studio Code qui facilite la gestion des graphes multimédias LVA. Cette extension fonctionne avec le **module LVA 2.0** et permet de modifier et de gérer les graphiques multimédias avec une interface graphique élégante et facile à utiliser.
## <a name="september-22-2020"></a>22 septembre 2020

La balise de version correspondant à l’actualisation de septembre 2020 du module est la suivante :

```
mcr.microsoft.com/media/live-video-analytics:1.0.4
```

> [!NOTE]
> Dans les démarrages rapides et les didacticiels, les manifestes de déploiement utilisent une balise 1 (live-video-analytics:1). Par conséquent, le simple redéploiement de tels manifestes doit mettre à jour le module sur votre application Edge > Appareils.

### <a name="module-updates"></a>Mises à jour de module

* Un nouveau nœud d’extension de graphique, [MediaGraphCognitiveServicesVisionExtension](spatial-analysis-tutorial.md), est disponible pour une intégration au module [Analyse spatiale](/legal/cognitive-services/computer-vision/intro-to-spatial-analysis-public-preview) (préversion) à partir de Cognitive Services.
* Ajout de la prise en charge des appareils Linux ARM64 : utilisez les [étapes manuelles](deploy-iot-edge-device.md) pour déployer cette fonctionnalité sur ces appareils.

### <a name="documentation-updates"></a>Mises à jour de la documentation

* [Des instructions](deploy-azure-stack-edge-how-to.md) relatives à l’utilisation de Live Video Analytics sur IoT Edge sur des appareils Azure Stack Edge sont disponibles.
* Nouveau tutoriel sur le développement de modèles de vision par ordinateur spécifiques à un scénario à l’aide du [service Custom Vision](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) et en utilisant ce dernier pour [analyser des vidéos en direct](custom-vision-tutorial.md) en temps réel.

<hr width=100%>

## <a name="august-19-2020"></a>19 août 2020

La balise de version correspondant à l’actualisation d’août 2020 du module est la suivante :

```
mcr.microsoft.com/media/live-video-analytics:1.0.3
```

> [!NOTE]
> Dans les démarrages rapides et les didacticiels, les manifestes de déploiement utilisent une balise 1 (live-video-analytics:1). Par conséquent, le simple redéploiement de tels manifestes doit mettre à jour le module sur votre application Edge > Appareils.

### <a name="module-updates"></a>Mises à jour de module

* Vous pouvez désormais bénéficier de performances de transfert de contenu de données élevées entre Live Video Analytics sur IoT Edge et votre extension personnalisée à l’aide de l’infrastructure gRPC. Pour bien démarrer, consultez [le démarrage rapide](analyze-live-video-use-your-grpc-model-quickstart.md).
* Déploiement régional plus étendu de Live Video Analytics et seul le service cloud a été mis à jour.  
* Live Video Analytics est désormais disponible dans 25 régions supplémentaires dans le monde. Voici la [liste](https://azure.microsoft.com/global-infrastructure/services/?products=media-services) de toutes les régions disponibles.  
* La [configuration](https://aka.ms/lva-edge/setup-resources-for-samples) pour les démarrages rapides a également été mise à jour avec la prise en charge de nouvelles régions.
    * Aucun appel à l’action n’est effectué pour quiconque a déjà configuré des ressources

### <a name="bug-fixes"></a>Résolution des bogues 

* Suppression de l’utilisation d’une extension Azure déconseillée dans le script de configuration.

<hr width=100%>

## <a name="july-13-2020"></a>13 juillet 2020

La balise de version correspondant à l’actualisation de juillet 2020 du module est la suivante :

```
mcr.microsoft.com/media/live-video-analytics:1.0.2
```

> [!NOTE]
> Dans les démarrages rapides et les didacticiels, les manifestes de déploiement utilisent une balise 1 (live-video-analytics:1). Par conséquent, le simple redéploiement de tels manifestes doit mettre à jour le module sur votre application Edge > Appareils.

### <a name="module-updates"></a>Mises à jour de module

* Vous pouvez désormais créer des topologies de graphiques qui ont un nœud récepteur de ressources et un nœud récepteur de fichiers en aval d’un nœud processeur de porte de signal. Pour obtenir un exemple, consultez [la topologie](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-motion-assets-files).

### <a name="bug-fixes"></a>Résolution des bogues

* Améliorations de la validation des propriétés souhaitées

<hr width=100%>

## <a name="june-1-2020"></a>1er juin 2020

Cette version est la première préversion publique de Live Video Analytics sur IoT Edge. La balise de version est

```
     mcr.microsoft.com/media/live-video-analytics:1.0.0
```

### <a name="supported-functionalities"></a>Fonctionnalités prises en charge

* Analyser les flux vidéo en direct à l’aide des modules d’intelligence artificielle de votre choix et enregistrer éventuellement une vidéo sur le périphérique ou dans le cloud
* Utiliser sur les systèmes d’exploitation Linux AMD64 [pris en charge](../../iot-edge/support.md) par IoT Edge
* Déployer et configurer le module via le hub IoT à l’aide de Portail Azure ou Visual Studio Code
* Gérer [les topologies et instances de graphe](media-graph-concept.md#media-graph-topologies-and-instances) à distance ou localement par le biais des appels de méthode directe suivants

    *   GraphTopologyGet
    *   GraphTopologySet
    *   GraphTopologyDelete
    *   GraphTopologyList
    *   GraphInstanceGet
    *   GraphInstanceSet
    *   GraphInstanceDelete
    *   GraphInstanceList

## <a name="next-steps"></a>Étapes suivantes

[Vue d'ensemble](overview.md)
