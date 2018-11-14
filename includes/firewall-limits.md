---
title: Fichier Include
description: Fichier Include
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 7/30/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: f1fd32fc02bee47d229093680166e6a13a5ba721
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2018
ms.locfileid: "49458829"
---
| Ressource | Limite par défaut |
| --- | --- |
| Données traitées |1000 To/pare-feu/mois <sup>1</sup> |
|Règles|10k : combinaison de tous les types de règles|
|Homologation de réseaux virtuels|Pour les implémentations de réseau en étoile, un nombre maximal de 50 réseaux virtuels de rayons.|
|Homologation globale|Non pris en charge. Vous devez avoir au moins un déploiement de pare-feu par région.|
|Nombre maximal de ports dans une règle de réseau|15<br>Une plage de ports (par exemple : 2 - 10) compte pour deux.


<sup>1</sup> Pour relever ces limites, contactez le support technique Azure.
