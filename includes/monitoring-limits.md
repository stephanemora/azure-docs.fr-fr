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
ms.openlocfilehash: 050d3314345e64e3d69a07367a0e9acc318fa106
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66271561"
---
| Ressource | Limite par défaut | Limite maximale |
| --- | --- | --- |
| Paramètres de mise à l'échelle automatique |100 par région et par abonnement. | Identique à la valeur par défaut. |
| Alertes de métrique (classique) |100 règles d’alerte actives par abonnement. | Appelez le support. |
| Alertes de métrique |1000 règles d’alerte actives par abonnement (dans des clouds publics) et 100 règles d’alerte actives par abonnement dans Azure en Chine et Azure Government. | Appelez le support. |
| Alertes de journal d’activité | 100 règles d’alerte actives par abonnement. | Identique à la valeur par défaut. |
| Alertes de journal | 512 | Appelez le support. |
| Groupes d’actions |2 000 groupes d’actions par abonnement. | Appelez le support. |

**Limites spécifiques à un groupe d’action**

| Ressource | Limite par défaut | Limite maximale |
| --- | --- | --- |
| Push de l’application Azure | 10 actions d’application azure par groupe d’actions. | Appelez le support. |
| Email | 1 000 actions de messagerie dans un groupe d’actions. Consultez également le [informations de limitation du débit](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Appelez le support. |
| ITSM | 10 actions ITSM dans un groupe d’actions. | Appelez le support. | 
| Application logique | 10 actions d’application logique dans un groupe d’actions. | Appelez le support. |
| Runbook | 10 actions de runbook dans un groupe d’actions. | Appelez le support. |
| sms | 10 actions de SMS dans un groupe d’actions. Consultez également le [informations de limitation du débit](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Appelez le support. |
| Voix | 10 actions de voice dans un groupe d’actions. Consultez également le [informations de limitation du débit](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Appelez le support. |
| webhook | 10 actions de webhook dans un groupe d’actions.  Nombre maximal d’appels de webhook est 1500 par minute par abonnement. Autres limites sont disponibles à l’adresse [les informations spécifiques à certaines actions](../articles/azure-monitor/platform/action-groups.md#action-specific-information).  | Appelez le support. |
