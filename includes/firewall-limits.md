---
title: Fichier Include
description: Fichier Include
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 12/06/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: a7b6867033e750f476b3d995926f0b670965a5d7
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74875410"
---
| Ressource | Limite par défaut |
| --- | --- |
| Débit de données |30 Gbits/s<sup>1</sup> |
|Règles|10 000. Combinaison de tous les types de règles.|
|Règles DNAT par adresse IP publique|299|
|Taille minimale d’AzureFirewallSubnet |/26|
|Plage de ports dans les règles de réseau et d’application|0-64,000. Nous nous efforçons actuellement d’assouplir cette limitation.|
|Adresses IP publiques|100 maximum (actuellement, les ports SNAT sont ajoutés uniquement pour les cinq premières adresses IP publiques.)|
|Table de routage|Par défaut, AzureFirewallSubnet a un itinéraire 0.0.0.0/0 avec la valeur NextHopType définie sur **Internet**.<br><br>Le Pare-feu Azure doit avoir une connectivité Internet directe. Si votre AzureFirewallSubnet prend connaissance d’un itinéraire par défaut pour votre réseau local via le protocole BGP, vous devez le remplacer par un UDR 0.0.0.0/0 avec la valeur **NextHopType** définie sur **Internet** pour garantir une connectivité Internet directe. Par défaut, Pare-feu Azure ne prend en charge le tunneling forcé vers un réseau local.<br><br>Toutefois, si votre configuration nécessite un tunneling forcé vers un réseau local, Microsoft le prendra en charge au cas par cas. Contactez le support technique afin qu’il puisse étudier votre cas. Si votre dossier est accepté, nous autoriserons votre abonnement afin de garantir le maintien de la connectivité Internet du pare-feu.|

<sup>1</sup>Pour augmenter ces limites, contactez le support Azure.
