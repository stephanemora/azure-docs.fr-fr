---
title: Fichier Include
description: Fichier Include
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 5/3/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 8709d4d903bd31ff94d04ec61e226857e4190407
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65150340"
---
| Ressource | Limite par défaut |
| --- | --- |
| Débit de données |30 Gbits/s<sup>1</sup> |
|Règles|10 000, tous les règles des types associés.|
|Taille minimale d’AzureFirewallSubnet |/26|
|Plage de ports dans les règles de réseau et d’application|0-64,000. Nous nous efforçons actuellement d’assouplir cette limitation.|
|Table de routage|Par défaut, AzureFirewallSubnet dispose d’un itinéraire 0.0.0.0/0 avec la valeur de type de tronçon suivant définie sur **Internet**.<br><br>Pare-feu Azure doit avoir une connectivité Internet directe. Si votre AzureFirewallSubnet prend connaissance d’un itinéraire par défaut pour votre réseau local via le protocole BGP, vous devez la remplacer par un UDR 0.0.0.0/0 avec le **NextHopType** valeur définie en tant que **Internet** pour mettre à jour directe Connexion Internet. Par défaut, les pare-feu Azure ne prend en charge le tunneling forcé à un réseau local.<br><br>Toutefois, si votre configuration nécessite le tunneling forcé à un réseau local, Microsoft prendra en charge il cas par cas. Contactez le Support technique afin que nous pouvons examiner votre cas. Si accepté, nous liste verte votre abonnement et garantir que la conservation de la connectivité Internet de pare-feu nécessaires.|

<sup>1</sup>si vous avez besoin d’augmenter ces limites, contactez le Support Azure.
