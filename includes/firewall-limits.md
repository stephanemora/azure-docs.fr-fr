---
title: Fichier Include
description: Fichier Include
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 12/14/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 7d905550114bb76a0a091146b3972bab4a652022
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53429962"
---
| Ressource | Limite par défaut |
| --- | --- |
| Données traitées |1000 To/pare-feu/mois <sup>1</sup> |
|Règles|10k : combinaison de tous les types de règles|
|Homologation globale|Non pris en charge. Vous devez avoir au moins un déploiement de pare-feu par région.|
|Nombre maximal de ports dans une règle de réseau|15<br>Une plage de ports (par exemple : 2 - 10) compte pour deux.
|Taille minimale d’AzureFirewallSubnet |/26|
|Plage de ports dans les règles de réseau et d’application|0-64,000. Nous nous efforçons actuellement d’assouplir cette limitation.|
|


<sup>1</sup> Pour relever ces limites, contactez le support technique Azure.
