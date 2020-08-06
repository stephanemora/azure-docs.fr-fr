---
title: Notes de publication pour Live Video Analytics sur IoT Edge – Azure
description: Cette rubrique fournit des notes de publication sur les versions, les améliorations, les correctifs de bogues et les problèmes connus de Live Video Analytics sur IoT Edge.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 28260728532d9db52b8d36488c2e456bd11803ea
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091777"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>Notes de publication pour Live Video Analytics sur IoT Edge

>Soyez notifié de la disponibilité des mises à jour sur cette page en faisant un copier-coller de cette URL : `https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us` dans votre lecteur de flux RSS.

Cet article vous fournit des informations sur :

* Versions les plus récentes
* Problèmes connus
* Résolution des bogues
* Fonctionnalités dépréciées

## <a name="july-13-2020"></a>13 juillet 2020

Cette balise de version pour l’actualisation du module en juillet 2020 est la suivante :

```
     mcr.microsoft.com/media/live-video-analytics:1.0.2
```

> [!NOTE]
> Dans les démarrages rapides et les didacticiels, les manifestes de déploiement utilisent une balise 1 (live-video-analytics:1). Par conséquent, le simple redéploiement de tels manifestes doit mettre à jour le module sur votre application Edge > Appareils.

### <a name="new-features"></a>Nouvelles fonctionnalités
* Vous pouvez désormais créer des topologies de graphiques qui ont un nœud récepteur de ressources, ainsi qu’un nœud récepteur de fichiers en aval d’un nœud processeur de porte de signal. Pour obtenir un exemple, consultez [cette page](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-motion-assets-files).

### <a name="bug-fixes"></a>Résolution des bogues
* Améliorations de la validation des propriétés souhaitées

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
