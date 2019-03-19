---
title: Détection intelligente Azure Application Insights – Modification à venir des destinataires par défaut des notifications | Microsoft Docs
description: Surveillez les suivis d’application avec Azure Application Insights afin de déterminer si les données de télémétrie du suivi présentent des anomalies.
services: application-insights
author: harelbr
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 03/13/2019
ms.author: harelbr
ms.openlocfilehash: f984a34be1c5d5fdd18a00812107318df8f5d9bf
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57889282"
---
# <a name="smart-detection-e-mail-notification-change"></a>Modification des notifications par e-mail de la détection intelligente

Sur la base des commentaires des clients, nous modifions, le 1er avril 2019, les rôles par défaut qui reçoivent les notifications par e-mail de la détection intelligente.

## <a name="what-is-changing"></a>Qu’est-ce qui change ?

Actuellement, les notifications par e-mail de la détection intelligente sont envoyées par défaut aux rôles _Propriétaire de l’abonnement_, _Contributeur de l’abonnement_ et _Lecteur de l’abonnement_. Ces rôles englobent souvent des utilisateurs qui ne participent pas activement au monitoring, et qui reçoivent donc inutilement des notifications. Pour améliorer cette expérience, nous sommes en train d’apporter une modification : les notifications par e-mail ne seront par défaut envoyées qu’aux rôles [Lecteur de monitoring](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) et [Contributeur de monitoring](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor).

## <a name="scope-of-this-change"></a>Portée de ce changement

Cette modification touchera toutes les règles de détection intelligente, à l’exception des suivantes :

* les règles de détection intelligente marquées comme étant en préversion, qui à l’heure actuelle ne prennent pas en charge les notifications par e-mail ;

* la règle Anomalies de défaillance, qui commencera à cibler les nouveaux rôles par défaut une fois migrée d’une alerte classique à la plateforme d’alertes unifiée (pour plus d’informations, cliquez [ici](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement)).

## <a name="how-to-prepare-for-this-change"></a>Préparation à cette modification

Pour vous assurer que les notifications par courrier électronique à partir de la détection intelligente sont envoyées aux utilisateurs appropriés, ces utilisateurs doivent être assignés à la [lecteur d’analyse](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) ou [contributeur de surveillance](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) rôles de l’abonnement.

Pour attribuer le rôle Lecteur de monitoring ou Contributeur de monitoring à des utilisateurs sur le Portail Azure, suivez les étapes de l’article [Ajouter une attribution de rôle](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment). Veillez à sélectionner le rôle _Lecteur de monitoring_ ou _Contributeur de monitoring_ pour l’attribuer aux utilisateurs.

> [!NOTE]
> Les destinataires spécifiques des notifications de la détection intelligente, configurés avec l’option _Autres destinataires des e-mails_ dans les paramètres des règles, ne seront pas concernés par cette modification. Ils continueront de recevoir les notifications par e-mail.

Si vous avez des questions ou des commentaires au sujet de cette modification, n’hésitez pas à [nous contacter](mailto:smart-alert-feedback@microsoft.com).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la détection intelligente :

- [Défaillances](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Fuites de mémoire](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Anomalies de performance](../../azure-monitor/app/proactive-performance-diagnostics.md)