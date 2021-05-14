---
title: 'Tutoriel : Examiner l’intégrité de vos ressources - Azure Security Center'
description: 'Tutoriel : Apprenez à examiner l’intégrité de vos ressources en utilisant Azure Security Center.'
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: tutorial
ms.date: 04/28/2021
ms.author: memildin
ms.openlocfilehash: fcadb8879f718913d93e33f81f6ec544ce9edb80
ms.sourcegitcommit: 43be2ce9bf6d1186795609c99b6b8f6bb4676f47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2021
ms.locfileid: "108277981"
---
# <a name="tutorial-investigate-the-health-of-your-resources"></a>Tutoriel : Examiner l’intégrité de vos ressources

> [!NOTE]
> La page Intégrité des ressources décrite dans ce tutoriel est une préversion.
> 
> [!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] | |Tarifs :|**Azure Defender pour DNS** est facturé conformément aux [tarifs de Security Center](https://azure.microsoft.com/pricing/details/security-center/).

La page Intégrité des ressources fournit une vue instantanée de l’état d’intégrité global d’une ressource unique. Vous pouvez consulter des informations détaillées sur la ressource et toutes les recommandations qui s’y appliquent. De plus, si vous utilisez [Azure Defender](azure-defender.md), vous pouvez aussi voir les alertes de sécurité en suspens pour cette ressource spécifique.

Cette page unique, actuellement en préversion dans les pages du portail Security Center, propose ceci :

1. **Informations sur la ressource** : Groupe de ressources et abonnement auxquels elle est associée, lieu géographique, etc.
1. **Fonctionnalité de sécurité appliquée** : Indique si Azure Defender est activé pour la ressource.
1. **Nombre de recommandations et d’alertes en suspens** : Nombre de recommandations de sécurité en suspens et d’alertes Azure Defender.
1. **Recommandations et alertes actionnables** : Deux onglets listent les recommandations et alertes qui s’appliquent à la ressource.

:::image type="content" source="media/investigate-resource-health/resource-health-page-virtual-machine.gif" alt-text="Page Intégrité des ressources dans Azure Security Center présentant des informations sur l’intégrité d’une machine virtuelle":::

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Accéder à la page Intégrité des ressources de tous les types de ressources
> * Évaluer les problèmes de sécurité en suspens d’une ressource
> * Améliorer la posture de sécurité pour de la ressource

## <a name="prerequisites"></a>Prérequis

Pour parcourir les fonctionnalités couvertes dans ce tutoriel :

- Un abonnement Azure : Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
- Pour appliquer des recommandations de sécurité, vous devez être connecté avec un compte disposant des autorisations appropriées (contributeur du groupe de ressources, propriétaire du groupe de ressources, contributeur de l’abonnement ou propriétaire de l’abonnement).
- Pour ignorer les alertes, vous devez être connecté avec un compte disposant des autorisations appropriées (administrateur de la sécurité, contributeur de l’abonnement ou propriétaire de l’abonnement).

##  <a name="access-the-health-information-for-a-resource"></a>Accéder aux informations d’intégrité d’une ressource

> [!TIP]
> Dans les captures d’écran ci-dessous, nous ouvrons une machine virtuelle, mais la page Intégrité des ressources peut vous indiquer les détails de tous les types de ressources. 

Pour ouvrir la page Intégrité des ressources d’une ressource :

1. Sélectionnez une ressource dans la [page d’inventaire des ressources](asset-inventory.md).

    :::image type="content" source="media/investigate-resource-health/inventory-select-resource.png" alt-text="Sélectionnez une ressource dans l’inventaire des ressources pour afficher la page Intégrité des ressources." lightbox="./media/investigate-resource-health/inventory-select-resource.png":::

1. Utilisez le volet gauche de la page Intégrité des ressources pour obtenir une vue d’ensemble de l’abonnement, de l’état et des informations de supervision de la ressource. Vous pouvez également voir si Azure Defender est activé pour la ressource :

    :::image type="content" source="media/investigate-resource-health/resource-health-left-pane.png" alt-text="Le volet gauche de la page Intégrité des ressources dans Azure Security Center présente l’abonnement, l’état et les informations de supervision de la ressource. Elle comprend également le nombre total de recommandations de sécurité et d’alertes Azure Defender en suspens.":::

1. Utilisez les deux onglets du volet droit pour passer en revue les listes des recommandations de sécurité et les alertes Azure Defender qui s’appliquent à cette ressource :

    :::image type="content" source="media/investigate-resource-health/resource-health-right-pane.png" alt-text="Le volet droit de la page Intégrité des ressources dans Azure Security Center comporte deux onglets : Recommandations et Alertes." lightbox="./media/investigate-resource-health/resource-health-right-pane.png":::

    > [!NOTE]
    > Azure Security Center utilise les termes « sain » et « non sain » pour décrire l’état de sécurité d’une ressource. Ces termes indiquent si la ressource est conforme à une [recommandation de sécurité](security-policy-concept.md#what-is-a-security-recommendation) spécifique.
    >
    > Dans la capture d’écran ci-dessus, vous pouvez voir que les recommandations sont listées même quand cette ressource est « saine ». L’un des avantages de la page Intégrité des ressources est que toutes les recommandations sont justement listées pour vous donner une image complète de l’intégrité de vos ressources. 


## <a name="evaluate-the-outstanding-security-issues-for-a-resource"></a>Évaluer les problèmes de sécurité en suspens d’une ressource

La page Intégrité des ressources liste les recommandations pour lesquelles votre ressource n’est pas saine et les alertes actives. 

- Pour vérifier que votre ressource est protégée conformément aux stratégies appliquées à vos abonnements, corrigez les problèmes décrits dans les recommandations :
    1. Dans le volet droit, sélectionnez une recommandation.
    1. Procédez comme indiqué à l’écran.

        > [!TIP]
        > Les instructions sur la correction des problèmes soulevés par les recommandations de sécurité diffèrent pour chacune des recommandations Security Center.
        >
        > Pour déterminer quelles recommandations doivent être suivies en premier, examinez la gravité de chacune d’entre elles et son [impact potentiel sur votre degré de sécurisation](secure-score-security-controls.md#security-controls-and-their-recommendations).

- Pour examiner une alerte Azure Defender :
    1. Dans le volet droit, sélectionnez une alerte.
    1. Suivez les instructions fournies dans [Répondre à des alertes de sécurité](security-center-managing-and-responding-alerts.md#respond-to-security-alerts).


## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à utiliser la page Intégrité des ressources dans Security Center.

Pour en savoir plus, consultez les pages connexes suivantes :

- [Répondre à des alertes de sécurité](security-center-managing-and-responding-alerts.md#respond-to-security-alerts)
- [Consulter vos recommandations de sécurité](security-center-recommendations.md)