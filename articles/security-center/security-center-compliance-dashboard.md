---
title: Améliorer la conformité aux réglementations à l’aide d’Azure Security Center | Microsoft Docs
description: 'Didacticiel : Découvrez comment améliorer la conformité aux réglementations à l’aide d’Azure Security Center.'
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5f50c4dc-ea42-418d-9ea8-158ffeb93706
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/30/2019
ms.author: memildin
ms.openlocfilehash: 20842997c5df81835024a6f458cd863b4e4d78b0
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202430"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Didacticiel : Améliorer votre conformité aux normes
---

Le tableau de bord Conformité avec la réglementation d’Azure Security Center simplifie le processus visant à respecter les exigences de conformité aux règlementations. Dans le tableau de bord, Security Center fournit des insights sur votre niveau de conformité sur la base d’évaluations continues de votre environnement Azure. Les évaluations effectuées par Security Center analysent les facteurs de risque de votre environnement cloud hybride conformément aux bonnes pratiques de sécurité. Ces évaluations sont comparées à des contrôles de conformité provenant d’un ensemble de normes prises en charge. Le tableau de bord Conformité avec la réglementation offre une vision claire de l’état de toutes ces évaluations au sein de votre environnement dans le contexte d’une norme ou d’une réglementation en particulier. Si vous suivez les recommandations et réduisez les facteurs de risque de votre environnement, vous pourrez constater une amélioration de votre niveau de conformité.

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

-   Évaluer votre conformité aux réglementations à l’aide du tableau de bord Conformité avec la réglementation

-   Améliorer votre niveau de conformité en suivant les recommandations

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour parcourir les fonctionnalités traitées dans ce tutoriel, vous devez avoir accès au niveau tarifaire Standard de Security Center. Vous pouvez essayer Security Center Standard gratuitement.
Pour en savoir plus, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/security-center/). Le démarrage rapide [Intégrer votre abonnement Azure à Security Center Standard](https://docs.microsoft.com/azure/security-center/security-center-get-started) vous guide dans la mise à niveau vers le plan Standard.

##  <a name="assess-your-regulatory-compliance"></a>Évaluer votre conformité aux réglementations

Security Center évalue en permanence la configuration de vos ressources pour identifier les vulnérabilités et les problèmes de sécurité. Ces évaluations sont présentées comme des recommandations visant à améliorer votre hygiène en matière de sécurité. Dans le tableau de bord Conformité avec la réglementation, vous pouvez voir un ensemble de normes de conformité et leurs exigences, celles qui sont prises en charge étant comparées aux évaluations de sécurité applicables. Vous pouvez ainsi examiner votre niveau de conformité par rapport à la norme, en fonction de l’état de ces évaluations.

La vue du tableau de bord Conformité avec la réglementation peut vous aider à prendre conscience d’un manque de conformité à une norme ou à une réglementation qui est importante pour vous. Cette vue ciblée vous permet également de faire le suivi de votre score de conformité au fil du temps dans les environnements cloud et hybrides dynamiques.

>[!NOTE]
> Les normes de conformité actuellement prises en charge sont les suivantes : Azure CIS, PCI DSS 3.2, ISO 27001 et SOC TSP. Des normes supplémentaires seront reflétées dans le tableau de bord au fur et à mesure de son développement.
1.  Dans le menu principal de Security Center, sous **STRATÉGIE ET CONFORMITÉ**, sélectionnez **Conformité avec la réglementation**. <br>
Un tableau de bord apparaît en haut de l’écran avec une vue d’ensemble de votre état de conformité et l’ensemble des réglementations de conformité prises en charge. Vous pouvez voir votre score de conformité global et le nombre d’évaluations ayant réussi ou échoué pour chaque norme.

    ![description ordinateur grande confiance](./media/security-center-compliance-dashboard/compliance-dashboard.png)


2.  Sélectionnez un onglet correspondant à une norme de conformité qui vous intéresse. Tous les contrôles relatifs à cette norme sont alors listés. Pour les contrôles applicables, vous pouvez voir les détails des évaluations ayant réussi ou échoué associées à ce contrôle. Certains contrôles sont grisés. Aucune évaluation Security Center n’est associée à ces contrôles. Vous devez analyser les exigences de ces contrôles et les évaluer vous-même dans votre environnement. Certaines d’entre eux peuvent être liés au processus et ne pas être d’ordre technique.

    ![onglet conformité](./media/security-center-compliance-dashboard/compliance-pci.png)

3. Sélectionnez l’onglet **Tout** pour voir toutes les recommandations pertinentes de Security Center et leurs normes associées. Cette vue peut être utile pour identifier les différentes normes impactées par une recommandation spécifique. <br> Vous pouvez utiliser cette vue pour classer par priorité les recommandations que vous devez suivre. Par exemple, si vous constatez que la recommandation **Activer MFA pour les comptes disposant d’autorisations de type propriétaire sur votre abonnement** échoue sur plusieurs ressources et qu’elle est associée à plusieurs normes, le fait de suivre cette recommandation a un impact élevé sur votre score de conformité global.

    ![impact sur le score de conformité](./media/security-center-compliance-dashboard/compliance-all-tabs.png)

1. Pour générer et télécharger un rapport PDF résumant votre état de compatibilité actuel pour une norme particulière, cliquez sur **Télécharger un rapport**.

    Le rapport fournit un résumé détaillé de votre état de conformité pour la norme sélectionnée en fonction des données des évaluations de Security Center, et il est organisé selon les contrôles de cette norme particulière. Le rapport peut être partagé avec les parties prenantes concernées et servir de preuve aux auditeurs internes et externes.

    ![télécharger](./media/security-center-compliance-dashboard/download-report.png)

## <a name="improve-your-compliance-posture"></a>Améliorer votre niveau de conformité

Compte tenu des informations figurant dans le tableau de bord Conformité avec la réglementation, vous pouvez améliorer votre niveau de conformité en suivant les recommandations directement dans le tableau de bord.

1.  Cliquez sur l’une des évaluations ayant échoué dans le tableau de bord pour voir les détails de cette recommandation. Chaque recommandation comprend un ensemble d’étapes de correction que vous devez suivre pour résoudre le problème.

2.  Vous pouvez sélectionner une ressource particulière pour voir plus de détails et suivre la recommandation associée à cette ressource. <br>Par exemple, sous l’onglet de la norme **Azure CIS**, vous pouvez cliquer sur la recommandation **Exiger un transfert sécurisé au compte de stockage**.

    ![recommandation de conformité](./media/security-center-compliance-dashboard/compliance-recommendation.png)

3. Quand vous cliquez pour accéder aux informations de la recommandation et sélectionnez une ressource non intègre, vous accédez directement à l’expérience d’activation **du transfert sécurisé vers le stockage** au sein du portail Azure.<br>Pour plus d’informations sur la façon d’appliquer des recommandations, consultez [Implémentation des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md).

    ![recommandation de conformité](./media/security-center-compliance-dashboard/compliance-remediate-recommendation.png)

4.  Quand vous prenez des mesures pour résoudre les recommandations, vous pouvez constater une amélioration de votre score de conformité dans le rapport du tableau de bord de conformité.

    > [!NOTE]
    > Les évaluations sont exécutées toutes les 12 heures environ. L’impact sur vos données de conformité n’est donc visible qu’après leur exécution.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à utiliser le tableau de bord Conformité avec la réglementation de Security Center pour effectuer les tâches suivantes :

-   Afficher et superviser votre niveau de conformité par rapport à des normes et à des réglementations qui sont importantes pour vous.

-   Améliorer votre état de conformité en suivant les recommandations appropriées et en augmentant le score de conformité.

Le tableau de bord Conformité avec la réglementation peut simplifier grandement le processus de conformité et réduire considérablement le temps nécessaire à la collecte des preuves de conformité pour votre environnement Azure et hybride.

Pour plus d’informations sur Security Center, consultez :

-   [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md): découvrez comment surveiller l’intégrité de vos ressources Azure.

-   [Gestion des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md) : découvrez comment utiliser les recommandations proposées par Azure Security Center pour protéger vos ressources Azure.

-   [Améliorer votre score de sécurisation dans Azure Security Center](security-center-secure-score.md) : découvrez comment classer par ordre de priorité les vulnérabilités et les recommandations pour améliorer votre niveau de sécurité.

-   [FAQ de Azure Security Center](security-center-faq.md): forum aux questions concernant l’utilisation de ce service.
