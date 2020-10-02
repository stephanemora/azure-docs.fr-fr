---
title: Quotas et limitations de Live Video Analytics sur IoT Edge - Azure
description: Cet article décrit les quotas et limitations de Live Video Analytics sur IoT Edge.
ms.topic: conceptual
ms.date: 05/22/2020
ms.openlocfilehash: df1978de4ee1bbbe15d0df3b02a70fb51491e9d2
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90529228"
---
# <a name="quotas-and-limitations"></a>Quotas et limitations

Cet article énumère les quotas et limitations du module Live Video Analytics sur IoT Edge.

## <a name="maximum-period-of-disconnected-use"></a>Période maximale d’utilisation déconnectée

Le module de périphérie peut supporter une perte temporaire de connectivité réseau. Si le module reste déconnecté pendant plus de 36 heures, il désactive toutes les instances de graphe en cours d’exécution. De plus, les appels de méthodes directes qui suivent sont bloqués.

Pour ramener le module de périphérie à un état opérationnel, vous devez restaurer la connectivité réseau. De plus, le module doit pouvoir communiquer correctement avec le compte Azure Media Services.

## <a name="maximum-number-of-graph-instances"></a>Nombre maximal d’instances de graphe

Vous pouvez avoir au maximum 1 000 instances de graphe par module (créées via GraphInstanceSet).

## <a name="maximum-number-of-graph-topologies"></a>Nombre maximal de topologies de graphe

Vous pouvez avoir au maximum 50 topologies de graphe par module (créées via GraphTopologySet).

## <a name="limitations-on-graph-topologies-at-preview"></a>Limitations relatives aux topologies de graphe dans la préversion

Avec la préversion, il existe des limitations sur les différents nœuds qui peuvent être connectés ensemble dans une topologie de graphe multimédia.

* Source RTSP
   * Une seule source RTSP est autorisée par topologie de graphe.
* Processeur de filtre de fréquence d’images
   * Doit être immédiatement en aval de la source RTSP ou du processeur de détection de mouvement.
   * Ne peut pas être utilisé en aval d’un processeur d’extension HTTP ou gRPC.
   * Ne peut pas être en amont d’un processeur de détection de mouvement.
* Processeur d’extension HTTP
   * Il ne peut exister qu’un seul processeur de ce type par topologie de graphe.
* Processeur d’extension gRPC
   * Il ne peut exister qu’un seul processeur de ce type par topologie de graphe.
* Processeur de détection de mouvement
   * Doit être immédiatement en aval de la source RTSP.
   * Il ne peut exister qu’un seul processeur de ce type par topologie de graphe.
   * Ne peut pas être utilisé en aval d’un processeur d’extension HTTP ou gRPC.
* Processeur de porte de signal
   * Doit être immédiatement en aval de la source RTSP.
* Récepteur de ressources 
   * Doit être immédiatement en aval de la source RTSP ou du processeur de porte de signal.
* Récepteur de fichiers
   * Doit être immédiatement en aval du processeur de porte de signal.
   * Ne peut pas être immédiatement en aval du processeur d’extension HTTP ou gRPC, ou d’une processeur de détection de mouvement
* Récepteur IoT Hub
   * Ne peut pas être immédiatement en aval d’une source IoT Hub.

Si les nœuds de processeur de détection de mouvement et de taux de filtrage sont utilisés, ils doivent être dans la même chaîne de nœuds menant au nœud source RTSP.

## <a name="limitations-on-media-service-operations-at-preview"></a>Limitations relatives aux opérations Media Services dans la préversion

Au lancement de la préversion, Live Video Analytics sur IoT Edge ne prend pas en charge les éléments suivants :

* Possibilité de migrer le compte Media Services d’un abonnement à un autre sans interruption.
* Possibilité d’utiliser plusieurs comptes de stockage avec le compte Media Services.
* Possibilité de changer dynamiquement les informations du principal de service dans les propriétés souhaitées du module, sans redémarrage.

Vous ne pouvez utiliser que des caméras IP qui prennent en charge le protocole RTSP. Vous trouverez les caméras IP qui prennent RTSP en charge dans la page des [produits conformes ONVIF](https://www.onvif.org/conformant-products). Recherchez les appareils conformes aux profils G, S ou T.

En outre, vous devez configurer ces caméras pour utiliser la vidéo H.264 et l’audio AAC. Les autres codecs ne sont actuellement pas pris en charge. 

## <a name="next-steps"></a>Étapes suivantes

[Vue d'ensemble](overview.md)
