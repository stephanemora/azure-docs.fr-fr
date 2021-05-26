---
title: Quotas et limitations d’Azure Video Analyzer – Azure
description: Cet article décrit les quotas et les limitations d’Azure Video Analyzer.
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: ca70737fd9bcf571dd4f0ca729bbe50b540de90e
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110386092"
---
# <a name="video-analyzer-quotas-and-limitations"></a>Quotas et limitations de Video Analyzer

Cet article décrit les quotas et les limitations d’Azure Video Analyzer.

## <a name="quotas-and-limitations---edge-module"></a>Quotas et limitations – Module Edge

Cette section énumère les quotas et limitations du module Edge Video Analyzer.

### <a name="maximum-period-of-disconnected-use"></a>Période maximale d’utilisation déconnectée

Le module de périphérie peut supporter une perte temporaire de la connectivité Internet. Si le module reste déconnecté pendant plus de 36 heures, il désactive tous les pipelines en direct en cours d’exécution. Tous les appels de méthode directe qui suivent sont bloqués.

Pour restaurer le module Edge dans un état opérationnel, vous devez rétablir la connectivité Internet de sorte que le module puisse communiquer correctement avec le compte Azure Video Analyzer.

### <a name="maximum-number-of-live-pipelines"></a>Nombre maximal de pipelines en direct

Au maximum 1 000 pipelines en direct par module (créés via `livePipelineSet`) sont pris en charge.

### <a name="maximum-number-of-pipeline-topologies"></a>Nombre maximal de topologies de pipeline

Au maximum 50 topologies de pipeline par module (créées via `pipelineTopologySet`) sont prises en charge.

### <a name="limitations-on-pipeline-topologies"></a>Limitations concernant les topologies de pipeline

Voici les limitations relatives à la façon dont différents nœuds peuvent être interconnectés dans une topologie de pipeline.

* Source RTSP
   * Une seule source RTSP est autorisée par topologie de pipeline.
* Processeur de détection de mouvement
   * Doit être immédiatement en aval de la source RTSP.
   * Ne peut pas être utilisé en aval d’un processeur d’extension HTTP ou gRPC.
* Processeur de porte de signal
   * Doit être immédiatement en aval de la source RTSP.
* Processeur de suivi d’objet
   * Doit être immédiatement en aval d’un processeur d’extension HTTP ou gRPC. Le processeur d’extension ne doit pas appliquer un modèle IA à chaque image de la vidéo en direct.
* Processeur de franchissement de ligne
   * Doit être immédiatement en aval d’un processeur de suivi d’objet.
* Récepteur vidéo 
   * Doit être immédiatement en aval de la source RTSP ou du processeur de porte de signal.
* Récepteur de fichiers
   * Doit être immédiatement en aval du processeur de porte de signal.
   * Ne peut pas être immédiatement en aval d’un processeur d’extension HTTP ou gRPC, ou d’un processeur de détection de mouvement.
* Récepteur IoT Hub
   * Ne peut pas être immédiatement en aval d’une source IoT Hub.

### <a name="supported-cameras"></a>Caméras prises en charge
Vous ne pouvez utiliser que des caméras IP qui prennent en charge le protocole RTSP. Vous trouverez les caméras IP qui prennent RTSP en charge dans la page des [produits conformes ONVIF](https://www.onvif.org/conformant-products). Recherchez les appareils conformes aux profils G, S ou T.

En outre, vous devez configurer ces caméras pour utiliser la vidéo H.264 et l’audio AAC. Les autres codecs ne sont actuellement pas pris en charge.

### <a name="support-for-video-ai"></a>Prise en charge de l’intelligence artificielle vidéo
Les processeurs d’extension HTTP ou gRPC prennent uniquement en charge l’envoi de données d’image ou de vidéo avec un module d’IA externe. Ainsi, l’exécution de l’inférence sur des données audio n’est pas prise en charge. Par conséquent, les nœuds de processeur dans les topologies de pipeline qui ont un nœud source RTSP parmi leur `inputs` utilisent également une propriété `outputSelectors` pour s’assurer que seules des vidéos sont transmises au processeur. Pour un exemple, regardez cette [topologie](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/evr-grpcExtension-video-sink/topology.json).

## <a name="quotas-and-limitations---service"></a>Quotas et limitations – Service

Cette section énumère les quotas et limitations du compte Video Analyzer.

### <a name="limitations-on-service-operations-at-preview"></a>Limitations relatives aux opérations de service en préversion

Au moment de mettre en production la préversion, le service Video Analyzer ne prend pas en charge les fonctionnalités suivantes :

* possibilité de migrer le compte Media Services d’un abonnement à un autre sans interruption ;
* possibilité d’utiliser plusieurs comptes de stockage avec le compte ;
* possibilité d’utiliser un compte de stockage dans une région différente de celle du compte Video Analyzer.

### <a name="limits-on-video-resources"></a>Limites des ressources vidéo
Au moment de mettre en production la préversion, chaque compte Video Analyzer peut avoir au maximum 10 000 ressources vidéo. Si vous avez besoin d’une limite supérieure, ouvrez un ticket de support dans le portail Azure.

### <a name="limits-on-access-policies"></a>Limites des stratégies d’accès
Au moment de mettre en production la préversion, chaque compte Video Analyzer peut avoir au maximum 20 stratégies d’accès.

### <a name="limits-on-registered-edge-modules"></a>Limites des modules Edge inscrits
Au moment de mettre en production la préversion, chaque compte Video Analyzer peut avoir au maximum 1 000 modules Edge inscrits. Si vous avez besoin d’une limite supérieure, ouvrez un ticket de support dans le portail Azure.

### <a name="limits-on-client-api-calls"></a>Limites des appels d’API client
Les limites suivantes s’appliquent aux API client au moment de mettre en production la préversion :

* Jusqu’à 50 demandes en 10 secondes
* Jusqu’à 600 demandes par heure

## <a name="limitations---video-analyzer-player-widgets"></a>Limitations – Widgets de lecteur Video Analyzer

Au moment de mettre en production la préversion, la lecture sur des appareils iOS n’est pas prise en charge.

## <a name="next-steps"></a>Étapes suivantes

[Vue d'ensemble](overview.md)
