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
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: df8b4534c6da446bd4ceeb18acf70036cd63f83c
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112061179"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>Gérer et résoudre les alertes de sécurité dans Azure Security Center

Cette rubrique montre comment afficher et traiter les alertes du Security Center et protéger vos ressources.

Les détections avancées qui déclenchent des alertes de sécurité sont uniquement disponibles avec Azure Defender. Un essai gratuit est disponible. Pour effectuer la mise à niveau, consultez [activer Azure Defender](enable-azure-defender.md).

## <a name="what-are-security-alerts"></a>Que sont les alertes de sécurité ?
Security Center collecte, analyse et intègre automatiquement les données de journaux provenant de vos ressources Azure, du réseau et des solutions de partenaires connectées, telles que les solutions de pare-feu et de protection des points de terminaison, pour détecter les menaces réelles et réduire le nombre de faux positifs. Une liste hiérarchisée des alertes de sécurité est affichée dans Security Center, ainsi que les informations dont vous avez besoin pour investiguer rapidement le problème et les mesures à prendre pour remédier à une attaque.

Pour en savoir plus sur les différents types d’alertes, consultez [Alertes de sécurité – guide de référence](alerts-reference.md).

Pour obtenir une vue d’ensemble de la façon dont Security Center génère des alertes, consultez [Comment Azure Security Center détecte les menaces et y répond](security-center-alerts-overview.md).


## <a name="manage-your-security-alerts"></a>Gérer vos alertes de sécurité

1. Dans la page de vue d’ensemble de Security Center, sélectionnez la vignette **Alertes de sécurité** en haut, ou le lien dans la barre latérale.

    :::image type="content" source="media/security-center-managing-and-responding-alerts/overview-page-alerts-links.png" alt-text="Accès à la page des alertes de sécurité à partir de la page de vue d’ensemble d’Azure Security Center":::

    La page des alertes de sécurité s’ouvre.

    :::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Liste d’alertes de sécurité d’Azure Security Center":::

1. Pour filtrer la liste des alertes, sélectionnez l’un des filtres appropriés. Vous pouvez éventuellement ajouter d’autres filtres à l’aide de l’option **Ajouter un filtre**.

    :::image type="content" source="./media/security-center-managing-and-responding-alerts/alerts-adding-filters-small.png" alt-text="Ajout de filtres à l’affichage des alertes" lightbox="./media/security-center-managing-and-responding-alerts/alerts-adding-filters-large.png":::

    La liste se met à jour en fonction des options de filtrage que vous avez sélectionnées. Le filtrage peut être très utile. Supposons que vous souhaitiez vérifier les alertes de sécurité qui se sont produites au cours des dernières 24 heures, car vous recherchez une violation de sécurité potentielle du système.


## <a name="respond-to-security-alerts"></a>Répondre à des alertes de sécurité

1. Dans la liste **Alertes de sécurité**, sélectionnez une alerte. Un volet latéral s’ouvre, affichant une description de l’alerte et de toutes les ressources affectées. 

    :::image type="content" source="./media/security-center-managing-and-responding-alerts/alerts-details-pane.png" alt-text="Mini vue détaillée d’une alerte de sécurité":::

    > [!TIP]
    > Ce volet latéral ouvert vous permet d’examiner rapidement la liste d’alertes avec les flèches haut et le bas de votre clavier.

1. Pour plus d’informations, sélectionnez **Afficher tous les détails**.

    Le volet gauche de la page d’alerte de sécurité affiche des informations générales concernant l’alerte de sécurité : titre, gravité, état, durée d’activité, description de l’activité suspecte et ressource affectée. En regard de la ressource affectée figurent les étiquettes Azure pertinentes pour la ressource. Utilisez-les pour déduire le contexte organisationnel de la ressource lors de l’examen de l’alerte.

    Le volet droit comprend l’onglet **Détails de l’alerte** contenant des détails supplémentaires sur l’alerte pour vous aider à examiner le problème : adresses IP, fichiers, processus et bien plus encore.
     
    ![Suggestions sur la façon de traiter les alertes de sécurité](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    Dans le volet droit figure aussi l’onglet **Entreprendre une action**. Utilisez cet onglet pour prendre d’autres mesures concernant l’alerte de sécurité. Actions telles que :
    - *Atténuer la menace* : fournit des étapes de correction manuelle pour cette alerte de sécurité
    - *Empêcher les attaques futures* : fournit des recommandations de sécurité pour aider à réduire la surface d’attaque, améliorer la posture de sécurité et ainsi empêcher les attaques futures
    - *Déclencher une réponse automatisée* : permet de déclencher une application logique en guide de réponse à cette alerte de sécurité.
    - *Supprimer les alertes similaires* : permet de supprimer les alertes futures ayant des caractéristiques similaires si l’alerte n’est pas pertinente pour votre organisation.

    ![Onglet Entreprendre une action](./media/security-center-managing-and-responding-alerts/alert-take-action.png)




## <a name="see-also"></a>Voir aussi

Dans ce document, vous avez découvert comment afficher les alertes de sécurité. Pour d’autres informations connexes, consultez les pages suivantes :

- [Configurer des règles de suppression d’alerte](alerts-suppression-rules.md)
- [Automatiser les réponses aux déclencheurs Security Center](workflow-automation.md)
- [Alertes de sécurité - guide de référence](alerts-reference.md)
