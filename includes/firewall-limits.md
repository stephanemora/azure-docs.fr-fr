---
title: Fichier include
description: Fichier include
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 06/22/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: b317c2a43352a750d4700fad56d5f7d741b2cc7d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85806011"
---
| Ressource | Limite |
| --- | --- |
| Débit de données |30 Gbits/s<sup>1</sup> |
|Règles|10 000. Combinaison de tous les types de règles.|
|Nombre maximum de règles DNAT|298 pour une seule adresse IP publique.<br>Toutes les adresses IP publiques supplémentaires contribuent aux ports SNAT disponibles, mais réduisent le nombre de règles DNAT disponibles. Par exemple, deux adresses IP publiques autorisent 297 règles DNAT. Si le protocole d’une règle est configuré à la fois pour TCP et pour UDP, elle compte pour deux règles.|
|Taille minimale d’AzureFirewallSubnet |/26|
|Plage de ports dans les règles de réseau et d’application|1 - 65535|
|Adresses IP publiques|250 maximum. Toutes les adresses IP publiques peuvent être utilisées dans des règles DNAT et toutes contribuent aux ports SNAT disponibles.|
|Adresses IP dans les groupes IP|Maximum de 100 groupes d’adresses IP par pare-feu.<br>Maximum de 5 000 adresses IP individuelles ou préfixes IP pour chaque groupe IP.<br><br>Pour plus d’informations, consultez [Groupes IP dans le Pare-feu Azure](../articles/firewall/ip-groups.md#ip-address-limits).
|Table de routage|Par défaut, AzureFirewallSubnet a un itinéraire 0.0.0.0/0 avec la valeur NextHopType définie sur **Internet**.<br><br>Le Pare-feu Azure doit avoir une connectivité Internet directe. Si votre AzureFirewallSubnet prend connaissance d’un itinéraire par défaut pour votre réseau local via le protocole BGP, vous devez le remplacer par un UDR 0.0.0.0/0 avec la valeur **NextHopType** définie sur **Internet** pour garantir une connectivité Internet directe. Par défaut, Pare-feu Azure ne prend en charge le tunneling forcé vers un réseau local.<br><br>Toutefois, si votre configuration nécessite un tunneling forcé vers un réseau local, Microsoft le prendra en charge au cas par cas. Contactez le support technique afin qu’il puisse étudier votre cas. Si votre dossier est accepté, nous autoriserons votre abonnement afin de garantir le maintien de la connectivité Internet du pare-feu.|

<sup>1</sup>Pour augmenter ces limites, contactez le support Azure.
