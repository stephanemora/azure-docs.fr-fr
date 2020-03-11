---
title: Gérer les alertes de sécurité dans le centre de sécurité Azure | Microsoft Docs
description: Ce document est conçu pour vous aider à utiliser les fonctionnalités du Centre de sécurité Azure pour gérer et résoudre les alertes de sécurité.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/27/2019
ms.author: memildin
ms.openlocfilehash: 834a097eb77cc7de2d0a3643bf1499ff7517ddb0
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2020
ms.locfileid: "77912271"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>Gérer et résoudre les alertes de sécurité dans Azure Security Center

Cette rubrique vous montre comment afficher et traiter les alertes que vous avez reçues afin de protéger vos ressources. 

* Pour en savoir plus sur les différents types d’alertes, consultez [Types d’alertes de sécurité](security-center-alerts-overview.md#security-alert-types).
* Pour obtenir une vue d’ensemble de la façon dont Security Center génère des alertes, consultez [Comment Azure Security Center détecte les menaces et y répond](security-center-alerts-overview.md#detect-threats).

> [!NOTE]
> Pour activer la détection avancée, effectuez une mise à niveau vers Azure Security Center Standard. Un essai gratuit est disponible. Pour mettre à niveau, sous [Stratégie de sécurité](tutorial-security-policy.md), sélectionnez Niveau tarifaire. Consultez [Tarification d’Azure Security Center](security-center-pricing.md) pour en savoir plus.

## <a name="what-are-security-alerts"></a>Que sont les alertes de sécurité ?
Le Centre de sécurité collecte, analyse et intègre automatiquement les données de journaux provenant de vos ressources Azure, du réseau et des solutions partenaires connectées, telles que les solutions de protection des points de terminaison et des pare-feu, pour détecter les menaces réelles et réduire le nombre de faux positifs. Une liste hiérarchisée d’alertes de sécurité est affichée dans le Centre de sécurité, ainsi que les informations nécessaires pour trouver rapidement la cause d’une attaque et des recommandations sur la façon d’y remédier.

> [!NOTE]
> Pour plus d’informations sur le fonctionnement des fonctionnalités de détection de Security Center, consultez [Comment Azure Security Center détecte les menaces et y répond](security-center-alerts-overview.md#detect-threats).

## <a name="manage-your-security-alerts"></a>Gérer vos alertes de sécurité

1. Dans le tableau de bord de Security Center, consultez la vignette **Protection contre les menaces** pour afficher une vue d’ensemble des alertes.

    ![Vignette Alertes de sécurité dans le Centre de sécurité](./media/security-center-managing-and-responding-alerts/security-center-dashboard-alert.png)

1. Pour obtenir plus de détails sur les alertes, cliquez sur la vignette.

   ![Panneau Alertes de sécurité dans Security Center](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. Pour filtrer les alertes affichées, cliquez sur **Filtrer**, puis dans le panneau **Filtre** qui s’ouvre, sélectionnez les options de filtre que vous souhaitez appliquer. La liste est mise à jour en fonction du filtre sélectionné. Le filtrage peut être très utile. Supposons que vous souhaitiez vérifier les alertes de sécurité qui se sont produites au cours des dernières 24 heures, car vous recherchez une violation de sécurité potentielle du système.

    ![Filtrage des alertes dans le Centre de sécurité](./media/security-center-managing-and-responding-alerts/security-center-filter-alerts.png)

## <a name="respond-to-security-alerts"></a>Répondre à des alertes de sécurité

1. Dans la liste **Alertes de sécurité**, cliquez sur une alerte de sécurité. Les ressources impliquées et les étapes à suivre pour résoudre une attaque sont affichées.

    ![Répondre à des alertes de sécurité](./media/security-center-managing-and-responding-alerts/security-center-alert.png)

1. Après avoir examiné les informations, cliquez sur une ressource qui a été attaquée.

    ![Suggestions sur la façon de traiter les alertes de sécurité](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    La section **Informations générales** peut vous donner un aperçu de ce qui a déclenché l’alerte de sécurité. Elle affiche des informations telles que la ressource cible, l’adresse IP source (le cas échéant), elle indique si l’alerte est toujours active et affiche des recommandations sur la façon de résoudre le problème.  

    > [!NOTE]
    >Dans certains cas, l’adresse IP source n’est pas disponible, certains journaux d’activité d’événements Windows de la sécurité n’incluent pas l’adresse IP.

1. Les étapes de correction suggérées par Security Center dépendent de l’alerte de sécurité. Suivez-les pour chaque alerte. 

    Dans certains cas, afin d’atténuer une alerte de sécurité, vous devrez peut-être utiliser d’autres contrôles ou services Azure pour implémenter la correction recommandée. 

    Les rubriques suivantes vous guident tout au long des différentes alertes en fonction des types de ressources :
    
    * [Alertes pour les machines IaaS Windows](threat-protection.md#windows-machines)
    * [Alertes pour les machines IaaS Linux](threat-protection.md#linux-machines)
    * [Alertes pour Azure App Service](threat-protection.md#app-services)
    * [Alertes pour les conteneurs Azure](threat-protection.md#azure-containers)
    * [Alertes pour SQL Database et SQL Data Warehouse](threat-protection.md#data-sql)
    * [Alertes pour Stockage Azure](threat-protection.md#azure-storage)
    * [Alertes pour Cosmos DB](threat-protection.md#cosmos-db)

    Les rubriques suivantes expliquent comment Security Center utilise les différentes données de télémétrie qu’il collecte à partir de l’intégration à l’infrastructure Azure afin d’appliquer des couches de protection supplémentaires pour les ressources déployées sur Azure :
    
    * [Alertes pour la couche de gestion Azure (Azure Resource Manager) (préversion)](threat-protection.md#management-layer)
    * [Alertes pour Azure Key Vault (préversion)](threat-protection.md#azure-keyvault)
    * [Alertes pour la couche réseau Azure](threat-protection.md#network-layer)
    * [Alertes provenant d’autres services](threat-protection.md#alerts-other)    

## <a name="see-also"></a>Voir aussi

Dans ce document, vous avez appris à configurer des stratégies de sécurité dans le Centre de sécurité. Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

* [Alertes de sécurité dans Azure Security Center](security-center-alerts-overview.md).
* [Gestion des incidents de sécurité](security-center-incident.md)
* [Guide des opérations et de planification du Centre de sécurité Azure](security-center-planning-and-operations-guide.md)