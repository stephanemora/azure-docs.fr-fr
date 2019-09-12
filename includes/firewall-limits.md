---
title: Fichier Include
description: Fichier Include
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 7/16/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: a42284765a46f4a000dc5b7fcf2867ef17d69570
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2019
ms.locfileid: "68229303"
---
| Ressource | Limite par défaut |
| --- | --- |
| Débit de données |30 Gbits/s<sup>1</sup> |
|Règles|10 000, combinaison de tous les types de règles.|
|Taille minimale d’AzureFirewallSubnet |/26|
|Plage de ports dans les règles de réseau et d’application|0-64,000. Nous nous efforçons actuellement d’assouplir cette limitation.|
|Adresses IP publiques|100 maximum|
|Table de routage|Par défaut, AzureFirewallSubnet a un itinéraire 0.0.0.0/0 avec la valeur NextHopType définie sur **Internet**.<br><br>Le Pare-feu Azure doit avoir une connectivité Internet directe. Si votre AzureFirewallSubnet prend connaissance d’un itinéraire par défaut pour votre réseau local via le protocole BGP, vous devez le remplacer par un UDR 0.0.0.0/0 avec la valeur **NextHopType** définie sur **Internet** pour garantir une connectivité Internet directe. Par défaut, Pare-feu Azure ne prend en charge le tunneling forcé vers un réseau local.<br><br>Toutefois, si votre configuration nécessite un tunneling forcé vers un réseau local, Microsoft le prendra en charge au cas par cas. Contactez le support technique afin qu’il puisse étudier votre cas. Si votre dossier est accepté, nous autoriserons votre abonnement afin de garantir le maintien de la connectivité Internet du pare-feu.|

<sup>1</sup>Pour augmenter ces limites, contactez le support Azure.
