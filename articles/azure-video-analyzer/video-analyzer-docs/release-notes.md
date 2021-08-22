---
title: Notes de publication d’Azure Video Analyzer - Azure
description: Cette rubrique fournit des notes de publication sur les versions, les améliorations, les correctifs de bogues et les problèmes connus d’Azure Video Analyzer.
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: f7189e6bda42ad77cb4bb6f426bf73209f46c363
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2021
ms.locfileid: "114601923"
---
# <a name="azure-video-analyzer-release-notes"></a>Notes de publication d’Azure Video Analyzer

>Soyez notifié de la disponibilité des mises à jour sur cette page en faisant un copier-coller de cette URL : `https://docs.microsoft.com/api/search/rss?search=%22Azure+Video+Analyzer+on+IoT+Edge+release+notes%22&locale=en-us` dans votre lecteur de flux RSS.

Cet article vous fournit des informations sur :

* Versions les plus récentes
* Problèmes connus
* Résolution des bogues
* Fonctionnalités dépréciées

<hr width=100%>

## <a name="june-3-2021"></a>3 juin 2021

Cette balise de version pour l’actualisation de juillet 2021 du module est

```
     mcr.microsoft.com/media/video-analyzer:1.0.1
```
> [!NOTE]
> Dans les démarrages rapides et les tutoriels, les manifestes de déploiement utilisent une balise 1 (video-analyzer:1). Par conséquent, le simple redéploiement de tels manifestes doit mettre à jour le module sur vos appareils périphériques quand de nouvelles balises sont publiées.

### <a name="module-updates"></a>Mises à jour de module
* Prend en charge les caractères unicode dans les informations d’identification pour la connexion à une caméra RTSP
* Mises à jour pour activer les journaux détaillés en mode débogage

<hr width=100%>

## <a name="may-25-2021"></a>25 mai 2021

Cette version est la première préversion publique d’Azure Video Analyzer. La balise de version est

```
mcr.microsoft.com/media/video-analyzer:1.0.0
```

> [!NOTE]
> Dans les démarrages rapides et les tutoriels, les manifestes de déploiement utilisent une balise 1 (video-analyzer:1). Par conséquent, le simple redéploiement de tels manifestes doit mettre à jour le module sur vos appareils périphériques quand de nouvelles balises sont publiées.

### <a name="supported-functionalities"></a>Fonctionnalités prises en charge

* Analyser les flux vidéo en direct à l’aide des modules d’intelligence artificielle de votre choix et enregistrer éventuellement une vidéo sur le périphérique ou dans le cloud
* Enregistrer une vidéo avec des métadonnées d’inférence dans le cloud
* Déclencher des événements lorsque des objets franchissent une ligne à l’aide de votre propre modèle de détection d’objets
* Suivre les objets détectés avec votre propre modèle de détection 
* Utiliser les widgets du lecteur Video Analyzer (composants web) pour lire la vidéo et les métadonnées d’inférence enregistrées
* Déployer et configurer le module via le hub IoT à l’aide de Portail Azure ou Visual Studio Code
* Gérer les [topologies de pipeline](pipeline.md#pipeline-topologies) à distance ou localement à l’aide d’appels de [méthode directe](direct-methods.md)

## <a name="next-steps"></a>Étapes suivantes

[Vue d'ensemble](overview.md)
