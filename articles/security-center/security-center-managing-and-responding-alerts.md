---
title: Gérer les alertes de sécurité dans le centre de sécurité Azure | Microsoft Docs
description: Ce document est conçu pour vous aider à utiliser les fonctionnalités du Centre de sécurité Azure pour gérer et résoudre les alertes de sécurité.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: how-to
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 75ca0438336825bf8d4bbdc6e08eca109f430fde
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92785916"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>Gérer et résoudre les alertes de sécurité dans Azure Security Center

Cette rubrique vous montre comment afficher et traiter les alertes que vous avez reçues afin de protéger vos ressources. 

* Pour en savoir plus sur les différents types d’alertes, consultez [Types d’alertes de sécurité](alerts-reference.md).
* Pour obtenir une vue d’ensemble de la façon dont Security Center génère des alertes, consultez [Comment Azure Security Center détecte les menaces et y répond](security-center-alerts-overview.md).

> [!NOTE]
> Pour activer les détections avancées, activez Azure Defender. Un essai gratuit est disponible. Pour mettre à niveau, sous [Stratégie de sécurité](tutorial-security-policy.md), sélectionnez Niveau tarifaire. Consultez [Tarification d’Azure Security Center](security-center-pricing.md) pour en savoir plus.

## <a name="what-are-security-alerts"></a>Que sont les alertes de sécurité ?
Le Centre de sécurité collecte, analyse et intègre automatiquement les données de journaux provenant de vos ressources Azure, du réseau et des solutions partenaires connectées, telles que les solutions de protection des points de terminaison et des pare-feu, pour détecter les menaces réelles et réduire le nombre de faux positifs. Une liste hiérarchisée d’alertes de sécurité est affichée dans le Centre de sécurité, ainsi que les informations nécessaires pour trouver rapidement la cause d’une attaque et des recommandations sur la façon d’y remédier.

> [!NOTE]
> Pour plus d’informations sur le fonctionnement des fonctionnalités de détection de Security Center, consultez [Comment Azure Security Center détecte les menaces et y répond](security-center-alerts-overview.md#detect-threats).

## <a name="manage-your-security-alerts"></a>Gérer vos alertes de sécurité

1. Dans le tableau de bord de Security Center, consultez la vignette **Protection contre les menaces** pour afficher une vue d’ensemble des alertes.

    ![Vignette Alertes de sécurité dans le Centre de sécurité](./media/security-center-managing-and-responding-alerts/security-center-dashboard-alert.png)

1. Pour obtenir plus de détails sur les alertes, cliquez sur la vignette.

   ![Panneau Alertes de sécurité dans Security Center](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. Pour filtrer les alertes affichées, cliquez sur **Filtrer** , puis dans le panneau **Filtre** qui s’ouvre, sélectionnez les options de filtre que vous souhaitez appliquer. La liste est mise à jour en fonction du filtre sélectionné. Le filtrage peut être très utile. Supposons que vous souhaitiez vérifier les alertes de sécurité qui se sont produites au cours des dernières 24 heures, car vous recherchez une violation de sécurité potentielle du système.

    ![Filtrage des alertes dans le Centre de sécurité](./media/security-center-managing-and-responding-alerts/security-center-filter-alerts.png)

## <a name="respond-to-security-alerts"></a>Répondre à des alertes de sécurité

1. Dans la liste **Alertes de sécurité** , cliquez sur une alerte de sécurité. Les ressources impliquées et les étapes à suivre pour résoudre une attaque sont affichées.

    ![Répondre à des alertes de sécurité](./media/security-center-managing-and-responding-alerts/security-center-alert.png)

1. Après avoir examiné les informations, cliquez sur une ressource qui a été attaquée.

    Le volet gauche de la page d’alerte de sécurité affiche des informations générales concernant l’alerte de sécurité : titre, gravité, état, durée d’activité, description de l’activité suspecte et ressource affectée. En regard de la ressource affectée figurent les étiquettes Azure pertinentes pour la ressource. Utilisez-les pour déduire le contexte organisationnel de la ressource lors de l’examen de l’alerte.

    Le volet droit comprend l’onglet **Détails de l’alerte** contenant des détails supplémentaires sur l’alerte pour vous aider à examiner le problème : adresses IP, fichiers, processus et bien plus encore.
     
    ![Suggestions sur la façon de traiter les alertes de sécurité](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    Dans le volet droit figure aussi l’onglet **Entreprendre une action**. Utilisez cet onglet pour prendre d’autres mesures concernant l’alerte de sécurité. Actions telles que :
    - *Atténuer la menace*  : fournit des étapes de correction manuelle pour cette alerte de sécurité
    - *Empêcher les attaques futures*  : fournit des recommandations de sécurité pour aider à réduire la surface d’attaque, améliorer la posture de sécurité et ainsi empêcher les attaques futures
    - *Déclencher une réponse automatisée*  : permet de déclencher une application logique en guide de réponse à cette alerte de sécurité.
    - *Supprimer les alertes similaires*  : permet de supprimer les alertes futures ayant des caractéristiques similaires si l’alerte n’est pas pertinente pour votre organisation.

    ![Onglet Entreprendre une action](./media/security-center-managing-and-responding-alerts/alert-take-action.png)




## <a name="see-also"></a>Voir aussi

Dans ce document, vous avez découvert comment afficher les alertes de sécurité. Pour d’autres informations connexes, consultez les pages suivantes :

- [Configurer des règles de suppression d’alerte](alerts-suppression-rules.md)
- [Automatiser les réponses aux déclencheurs Security Center](workflow-automation.md)
