---
title: Fichier Include
description: Fichier Include
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 21f1fca1e8c5cf2b060cee41d1e474996f306f8e
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65787601"
---
| Ressource | Limite par défaut | Limite maximale |
| --- | --- | --- |
| Paramètres de mise à l'échelle automatique |100 par région et par abonnement. | Identique à la valeur par défaut. |
| Alertes de métrique (classique) |100 règles d’alerte actives par abonnement. | Appelez le support. |
| Alertes de métrique |100 règles d’alerte actives par abonnement. | Appelez le support. |
| Alertes de journal d'activité | 100 règles d’alerte actives par abonnement. | Identique à la valeur par défaut. |
| Alertes de journal | 512 | Appelez le support. |
| Groupes d'actions |2 000 groupes d’actions par abonnement. | Appelez le support. |

**Limites spécifiques à un groupe d’action**

| Ressource | Limite par défaut | Limite maximale |
| --- | --- | --- |
| Envoi (Push) d'application Azure | 10 actions d’application azure par groupe d’actions. | Appelez le support. |
| Email | 1 000 actions de messagerie dans un groupe d’actions. Consultez également le [informations de limitation du débit](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Appelez le support. |
| ITSM | 10 actions ITSM dans un groupe d’actions. | Appelez le support. | 
| Application logique | 10 actions d’application logique dans un groupe d’actions. | Appelez le support. |
| Runbook | 10 actions de runbook dans un groupe d’actions. | Appelez le support. |
| SMS | 10 actions de SMS dans un groupe d’actions. Consultez également le [informations de limitation du débit](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Appelez le support. |
| Voix | 10 actions de voice dans un groupe d’actions. Consultez également le [informations de limitation du débit](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Appelez le support. |
| Webhook | 10 actions de webhook dans un groupe d’actions.  Nombre maximal d’appels de webhook est 1500 par minute par abonnement. Autres limites sont disponibles à l’adresse [les informations spécifiques à certaines actions](../articles/azure-monitor/platform/action-groups.md#action-specific-information).  | Appelez le support. |
