---
title: Fichier Include
description: Fichier Include
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 11/26/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 8b236bc1f0089b89aca90e7c69e9b445b01a374b
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52440120"
---
| Ressource | Limite par défaut |
| --- | --- |
| Données traitées |1000 To/pare-feu/mois <sup>1</sup> |
|Règles|10k : combinaison de tous les types de règles|
|Homologation globale|Non pris en charge. Vous devez avoir au moins un déploiement de pare-feu par région.|
|Nombre maximal de ports dans une règle de réseau|15<br>Une plage de ports (par exemple : 2 - 10) compte pour deux.
|Taille minimale d’AzureFirewallSubnet |/26


<sup>1</sup> Pour relever ces limites, contactez le support technique Azure.
