---
title: Notes de publication pour Live Video Analytics sur IoT Edge – Azure
description: Cette rubrique fournit des notes de publication sur les versions, les améliorations, les correctifs de bogues et les problèmes connus de Live Video Analytics sur IoT Edge.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: fd607ee027b46cf386180338440e6530da911302
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84260322"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>Notes de publication pour Live Video Analytics sur IoT Edge

>Soyez notifié de la disponibilité des mises à jour sur cette page en faisant un copier-coller de cette URL : `https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us` dans votre lecteur de flux RSS.

Cet article vous fournit des informations sur :

* Versions les plus récentes
* Problèmes connus
* Résolution des bogues
* Fonctionnalités dépréciées

## <a name="june-1-2020"></a>1er juin 2020

Cette version est la première préversion publique de Live Video Analytics sur IoT Edge. La balise de version est

```
     mcr.microsoft.com/media/live-video-analytics:1.0.0
```

### <a name="supported-functionalities"></a>Fonctionnalités prises en charge
* Analyser les flux vidéo en direct à l’aide des modules d’intelligence artificielle de votre choix et enregistrer éventuellement une vidéo sur le périphérique ou dans le cloud
* Utiliser sur les systèmes d’exploitation Linux AMD64 [pris en charge](https://docs.microsoft.com/azure/iot-edge/support) par IoT Edge
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
