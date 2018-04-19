---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5e7e365a73c9ba9cfd91d4f93759e7af55a0ad48
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2018
---
Le tableau suivant répertorie la configuration requise pour les passerelles VPN basées sur des stratégies ou des itinéraires. Cette table s’applique aux modèles de déploiement classique et Resource Manager. Pour le modèle classique, les passerelles VPN basées sur des stratégies sont identiques aux passerelles statiques et les passerelles basées sur des itinéraires sont identiques aux passerelles dynamiques.

|  | **Passerelle VPN basée sur des stratégies De base** | **Passerelle VPN basée sur des itinéraires De base** | **Passerelle VPN basée sur des itinéraires Standard** | **Passerelle VPN à hautes performances basée sur des itinéraires** |
| --- | --- | --- | --- | --- |
| **Connectivité de site à site (S2S)** |Configuration de VPN basé sur une stratégie |Configuration de VPN basé sur les itinéraires |Configuration de VPN basé sur les itinéraires |Configuration de VPN basé sur les itinéraires |
| **Connectivité de point à site (P2S)** |Non pris en charge |Prise en charge (peut coexister avec S2S) |Prise en charge (peut coexister avec S2S) |Prise en charge (peut coexister avec S2S) |
| **Méthode d’authentification** |Clé prépartagée |Clé prépartagée pour la connectivité de site à site, certificats pour la connectivité de point à site |Clé prépartagée pour la connectivité de site à site, certificats pour la connectivité de point à site |Clé prépartagée pour la connectivité de site à site, certificats pour la connectivité de point à site |
| **Nombre maximal de connexions de site à site** |1 |10 |10 |30 |
| **Nombre maximal de connexions de point à site** |Non pris en charge |128 |128 |128 |
| **Routage actif (BGP)** (*) |Non pris en charge |Non pris en charge |Prise en charge |Prise en charge |

  (*) Le protocole BGP n’est pas pris en charge avec le modèle de déploiement Classic.
