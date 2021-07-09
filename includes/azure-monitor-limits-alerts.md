---
title: Fichier include
description: Fichier include
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 05/03/2021
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 1d9ec2d05374412c17493b47f3bb71524f8cf93f
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110631129"
---
| Ressource | Limite par défaut | Limite maximale |
| --- | --- | --- |
| Alertes de métrique (classiques) |100 règles d’alerte actives par abonnement. | Appelez le support technique |
| Alertes de métrique |5 000 règles d’alerte actives par abonnement dans les clouds Azure public, Azure China 21Vianet et Azure Government. Si vous atteignez cette limite, déterminez si vous pouvez utiliser des [alertes à plusieurs ressources de même type](../articles/azure-monitor/alerts/alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor).<br/>5 000 séries chronologiques de métriques par règle d’alerte. | Appelez le support technique. |
| Alertes de journal d’activité | 100 règles d’alerte actives par abonnement (ne peut pas être augmentée). | Identique à la valeur par défaut |
| Alertes de journal | 1 000 règles d’alerte actives par abonnement. 1 000 règles d’alerte actives par ressource. | Appelez le support technique |
| Longueur de la description des règles d’alerte et des règles d’action| Alertes de recherche dans les journaux avec 4 096 caractères<br/>Tous les 2 048 autres caractères | Identique à la valeur par défaut |

### <a name="alerts-api"></a>API d’alertes
Les alertes Azure Monitor permettent d’établir des limitations pour bloquer les utilisateurs qui effectuent un nombre excessif d’appels. Ce comportement peut en effet surcharger les ressources back-end du système et compromettre la réactivité du service. Les limites suivantes sont conçues pour protéger les clients des interruptions et maintenir le niveau de service. Les limites et limitations appliquées aux utilisateurs sont conçues pour impacter uniquement les cas d’usage extrêmes. Elles ne concernent pas les cas d’usage classiques.

| Ressource | Limite par défaut | Limite maximale |
| --- | --- | --- |
| GET alertsSummary | 50 appels par minute et par abonnement | Identique à la valeur par défaut | 
|   GET alerts (sans spécifier d’ID d’alerte) | 100 appels par minute et par abonnement | Identique à la valeur par défaut | 
|   Tous les autres appels | 1 000 appels par minute et par abonnement | Identique à la valeur par défaut | 

