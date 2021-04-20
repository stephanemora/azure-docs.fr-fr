---
title: Fichier include
description: Fichier include
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 04/07/2021
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: c4c36c0e099ed7474a5d27f6edcbd4b3ac435f4f
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107030818"
---
| Ressource | Limite |
| --- | --- |
| Débit de données |30 Gbits/s<sup>1</sup> |
|Limites de règle|10 000 sources/destinations uniques dans les règles de réseau|
|Nombre maximum de règles DNAT|298 pour une seule adresse IP publique.<br>Toutes les adresses IP publiques supplémentaires contribuent aux ports SNAT disponibles, mais réduisent le nombre de règles DNAT disponibles. Par exemple, deux adresses IP publiques autorisent 297 règles DNAT. Si le protocole d’une règle est configuré à la fois pour TCP et pour UDP, elle compte pour deux règles.|
|Taille minimale d’AzureFirewallSubnet |/26|
|Plage de ports dans les règles de réseau et d’application|1 - 65535|
|Adresses IP publiques|250 maximum. Toutes les adresses IP publiques peuvent être utilisées dans des règles DNAT et toutes contribuent aux ports SNAT disponibles.|
|Adresses IP dans les groupes IP|Maximum de 100 groupes d’adresses IP par pare-feu.<br>Maximum de 5 000 adresses IP individuelles ou préfixes IP pour chaque groupe IP.
|Table de routage|Par défaut, AzureFirewallSubnet a un itinéraire 0.0.0.0/0 avec la valeur NextHopType définie sur **Internet**.<br><br>Le Pare-feu Azure doit avoir une connectivité Internet directe. Si votre AzureFirewallSubnet prend connaissance d’un itinéraire par défaut pour votre réseau local via le protocole BGP, vous devez le remplacer par un UDR 0.0.0.0/0 avec la valeur **NextHopType** définie sur **Internet** pour garantir une connectivité Internet directe. Par défaut, Pare-feu Azure ne prend en charge le tunneling forcé vers un réseau local.<br><br>Toutefois, si votre configuration nécessite un tunneling forcé vers un réseau local, Microsoft le prendra en charge au cas par cas. Contactez le support technique afin qu’il puisse étudier votre cas. Si votre dossier est accepté, nous autoriserons votre abonnement afin de garantir le maintien de la connectivité Internet du pare-feu.|
|Noms FQDN dans les règles réseau|Pour de bonnes performances, ne dépassez pas plus de 1 000 noms FQDN dans toutes les règles réseau par pare-feu.|

<sup>1</sup>Pour augmenter ces limites, contactez le support Azure.
