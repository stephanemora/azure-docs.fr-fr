---
title: Fichier include
description: Fichier include
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 10/01/2020
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 071f2849a877f4ea1e8a84eff6ccfb8343be3ec7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101734034"
---
| Ressource | Limite par défaut | Limite maximale |
| --- | --- | --- |
| Alertes de métrique (classiques) |100 règles d’alerte actives par abonnement. | Appelez le support technique |
| Alertes de métrique |5 000 règles d’alerte actives par abonnement dans les clouds Azure public, Azure China 21Vianet et Azure Government. Si vous atteignez cette limite, déterminez si vous pouvez utiliser des [alertes à plusieurs ressources de même type](../articles/azure-monitor/alerts/alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor).<br/>5 000 séries chronologiques de métriques par règle d’alerte. | Appelez le support technique. |
| Alertes de journal d’activité | 100 règles d’alerte actives par abonnement (ne peut pas être augmentée). | Identique à la valeur par défaut |
| Alertes de journal | 512 règles d’alerte actives par abonnement. 200 règles d’alerte actives par abonnement. | Appelez le support technique |
| Longueur de la description des règles d’alerte et des règles d’action| Alertes de recherche dans les journaux avec 4 096 caractères<br/>Tous les 2 048 autres caractères | Identique à la valeur par défaut |