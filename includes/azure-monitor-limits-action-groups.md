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
ms.openlocfilehash: 6f9405e1b402b029b628821824a1727dd825a031
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101734032"
---
| Ressource | Limite par défaut | Limite maximale |
| --- | --- | --- |
| Envoi de l’application Azure | 10 actions d’application Azure par groupe d’actions. | Identique à la valeur par défaut |
| Email | 1,000 actions d’e-mail par groupe d’actions.<br>pas plus de 100 e-mails par heure.<br>Consultez également [Informations de limitation du débit](../articles/azure-monitor/alerts/alerts-rate-limiting.md). | Identique à la valeur par défaut |
| ITSM | 10 actions ITSM (gestion des services informatiques) par groupe d’actions. | Identique à la valeur par défaut | 
| Application logique | 10 actions d’application logique par groupe d’actions. | Identique à la valeur par défaut |
| Runbook | 10 actions de runbook par groupe d’actions. | Identique à la valeur par défaut |
| sms | 10 actions SMS par groupe d’actions.<br>Pas plus de 1 SMS toutes les 5 minutes.<br>Consultez également [Informations de limitation du débit](../articles/azure-monitor/alerts/alerts-rate-limiting.md). | Identique à la valeur par défaut |
| Voix | 10 actions de voix par groupe d’actions.<br>Pas plus de 1 appel vocal toutes les 5 minutes.<br>Consultez également [Informations de limitation du débit](../articles/azure-monitor/alerts/alerts-rate-limiting.md). | Identique à la valeur par défaut |
| webhook | 10 actions de webhook par groupe d’actions.  Le nombre maximal d’appels de webhook est de 1 500 par minute et par abonnement. Les autres limites sont disponibles sur [Informations spécifiques à certaines actions](../articles/azure-monitor/alerts/action-groups.md#action-specific-information).  | Identique à la valeur par défaut |