---
title: Tutoriel pour expliquer comment évaluer des applications web en vue de leur migration vers Azure App Service
description: Découvrez comment créer une évaluation pour Azure App Service dans Azure Migrate
author: rashi-ms
ms.author: rajosh
ms.topic: tutorial
ms.date: 07/28/2021
ms.openlocfilehash: 1bbe267aef27151b3ef70e88e0a8e85091d8a33d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121782396"
---
# <a name="tutorial-assess-aspnet-web-apps-for-migration-to-azure-app-service"></a>Tutoriel : évaluer des applications web ASP.NET en vue de leur migration vers Azure App Service

Dans le cadre de votre migration vers Azure, vous devez évaluer vos charges de travail locales afin de déterminer la préparation nécessaire, d’identifier les risques et d’estimer les coûts ainsi que le niveau de complexité de la tâche.
Cet article vous montre comment évaluer les applications web ASP.NET découvertes s’exécutant sur des serveurs web II pour préparer la migration vers Azure App Service, en utilisant l’outil Azure Migrate : découverte et évaluation.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Exécutez une évaluation basée sur des données de configuration d’applications web.
> * Réviser une évaluation Azure App Service

> [!NOTE]
> Les tutoriels indiquent le moyen le plus rapide de tester un scénario, et ils utilisent les options par défaut lorsque cela est possible. 

## <a name="prerequisites"></a>Prérequis

- Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) avant de commencer.
- Avant de suivre ce tutoriel pour évaluer vos applications web en vue de leur migration vers Azure App Service, assurez-vous d’avoir découvert les applications web que vous souhaitez évaluer à l’aide de l’appliance Azure Migrate, [en suivant ce tutoriel](tutorial-discover-vmware.md)
- Si vous voulez tester cette fonctionnalité dans un projet existant, vérifiez que vous avez rempli les [prérequis](how-to-discover-sql-existing-project.md) indiqués dans cet article.

## <a name="run-an-assessment"></a>Exécuter une évaluation

Exécutez une évaluation comme suit :

1. Sur la page **Vue d’ensemble** > **Serveurs, bases de données et applications web**, cliquez sur **Découvrir, évaluer et migrer**.
    :::image type="content" source="./media/tutorial-assess-webapps/discover-assess-migrate.png" alt-text="Page Vue d’ensemble d’Azure Migrate":::
2. Sur **Azure Migrate : détection et évaluation**, cliquez sur **Évaluer** et choisissez le type d’évaluation **Azure App Service**.
    :::image type="content" source="./media/tutorial-assess-webapps/assess.png" alt-text="Liste déroulante permettant de choisir le type d’évaluation Azure App Service":::
3. Dans **Créer une évaluation**, vous pouvez voir que le type d’évaluation **Azure App Service** est présélectionné et que la source de détection est **Serveurs découverts par l’appliance Azure Migrate** par défaut.
4. Cliquez sur **Modifier** pour passer en revue les propriétés de l’évaluation.
     :::image type="content" source="./media/tutorial-assess-webapps/assess-webapps.png" alt-text="Bouton Modifier à partir duquel les propriétés de l’évaluation peuvent être personnalisées":::
5. Voici ce qu’incluent les propriétés d’évaluation d’évaluation Azure App Service :

    **Propriété** | **Détails**
    --- | ---
    **Emplacement cible** | Région Azure vers laquelle vous souhaitez opérer la migration. Les recommandations en matière de configuration et de coût d’Azure App Service sont basées sur l’emplacement que vous spécifiez.
    **Isolation requise** | Sélectionnez Oui si vous souhaitez que vos applications web s’exécutent dans un environnement privé et dédié dans un centre de données Azure à l’aide de machines virtuelles de la série Dv2 avec des processeurs plus rapides, un stockage SSD et le double de mémoire ratio cœur par rapport aux plans Standard.
    **Instances réservées** | Spécifie des instances réservées afin que les estimations de coût dans l’évaluation les prennent en compte.<br/><br/> Si vous sélectionnez une option d’instance réservée, vous ne pouvez pas spécifier de « Remise (%) ».
    **Offer** | [Offre Azure](https://azure.microsoft.com/support/legal/offer-details/) dans laquelle vous êtes inscrit. L’évaluation estime le coût de cette offre.
    **Devise** | Devise de facturation de votre compte.
    **Remise (%)** | Toute remise propre à un abonnement que vous recevez en plus de l’offre Azure. Le paramètre par défaut est 0 %.
    **Abonnement Contrat Entreprise** | Spécifie qu’un abonnement Contrat Entreprise (EA) est utilisé pour l’estimation des coûts. Prend en compte la remise applicable à l’abonnement. <br/><br/> Conservez les paramètres par défaut pour les instances réservées et les propriétés de remise (en %).

    :::image type="content" source="./media/tutorial-assess-webapps/webapps-assessment-properties.png" alt-text="Propriétés de l’évaluation App Service":::

1. Dans **Créer une évaluation** > cliquez sur Suivant.
1. Dans **Sélectionner les serveurs à évaluer** > **Nom de l’évaluation**, spécifiez un nom pour l’évaluation.
1. Dans **Sélectionner ou créer un groupe**, sélectionnez **Créer**, puis spécifiez un nom de groupe.
1. Sélectionnez l’appliance, puis sélectionnez les serveurs que vous souhaitez ajouter au groupe. Puis cliquez sur Suivant.
1. Dans **Vérifier + créer l’évaluation**, passez en revue les détails de l’évaluation, puis cliquez sur Créer une évaluation pour créer le groupe et lancer l’évaluation.
1. Une fois l’évaluation créée, accédez à la mosaïque **Serveurs, bases de données et applications web** > **Azure Migrate : découverte et évaluation** > Actualiser les données de la mosaïque en cliquant sur l’option Actualiser en haut de la mosaïque. Attendez que les données soient actualisées.
    :::image type="content" source="./media/tutorial-assess-webapps/tile-refresh.png" alt-text="Actualiser les données des outils de découverte et d’évaluation":::
1. Cliquez sur le nombre en regard de l’évaluation d’Azure App Service.
    :::image type="content" source="./media/tutorial-assess-webapps/assessment-webapps-navigation.png" alt-text="Navigation jusqu’à l’évaluation créée":::
1. Cliquez sur le nom de l’évaluation que vous souhaitez afficher.

## <a name="review-an-assessment"></a>Réviser une évaluation

**Pour voir une évaluation** :

1. **Serveurs, bases de données et applications web** > **Azure Migrate : découverte et évaluation** > cliquez sur le numéro en regard de l’évaluation d’Azure App Service.
2. Cliquez sur le nom de l’évaluation que vous souhaitez afficher.
:::image type="content" source="./media/tutorial-assess-webapps/assessment-webapps-summary.png" alt-text="Vue d’ensemble de l’évaluation d’App Service":::
3. Passez en revue le récapitulatif de l’évaluation. Vous pouvez également modifier les propriétés de l’évaluation ou recalculer celle-ci.

#### <a name="azure-app-service-readiness"></a>Préparé pour Azure App Service

Cela indique la distribution des applications web évaluées. Vous pouvez descendre dans la hiérarchie pour comprendre les détails concernant les avertissements/problèmes de migration que vous pouvez résoudre avant la migration vers Azure App Service. [En savoir plus](concepts-azure-webapps-assessment-calculation.md) Vous pouvez également consulter la référence SKU App Service recommandée pour la migration vers Azure App Service.

#### <a name="azure-app-service-cost-details"></a>Détails des coûts d’Azure App Service

Un [plan App Service](/azure/app-service/overview-hosting-plans) comporte les [frais](https://azure.microsoft.com/pricing/details/app-service/windows/) des ressources de calcul qu’il utilise.

### <a name="review-readiness"></a>Vérifier la préparation nécessaire

1. Cliquez sur **Préparé pour Azure App Service**.
    :::image type="content" source="./media/tutorial-assess-webapps/assessment-webapps-readiness.png" alt-text="Détails de la préparation pour Azure App Service":::
1. Consultez la colonne de préparation pour Azure App Service dans la table, pour les applications web évaluées :
    1. Si aucun problème de compatibilité n’est détecté, la préparation est marquée comme **Prête** pour le type de déploiement cible.
    1. S’il existe des problèmes de compatibilité non critiques, tels que des fonctionnalités détériorées ou non prises en charge qui ne bloquent pas la migration vers un type de déploiement cible spécifique, l’état de préparation est marqué comme **Prêt sous conditions** (avec lien hypertexte) avec des détails d’**avertissement** et des recommandations de correction.
    1. S’il existe des problèmes de compatibilité susceptibles de bloquer la migration vers un type de déploiement cible spécifique, L’état de préparation est marqué comme **Non prêt** avec des détails sur le **problème** et des recommandations de correction.
    1. Si la détection est toujours en cours ou s’il existe des problèmes de découverte pour une instance ou une application web, l’état de préparation est marqué comme **Inconnu**, car l’évaluation n’a pas pu calculer la préparation pour cette application web.
1. Examinez la référence SKU recommandée pour les applications web, déterminée selon la matrice ci-dessous :

    **Isolation requise** | **Instance réservée** | **Plan App Service/SKU**
    --- | --- | ---
    Oui  | Oui | I1
    Oui  | Non  | I1
    Non  | Oui | P1v3
    Non  | Non | P1v2

    **Préparé pour Azure App Service** | **Déterminer la référence SKU d’App Service** | **Déterminer les estimations de coût**
    --- | --- | ---
    Ready  | Oui | Oui
    Disponible sous conditions  | Oui  | Oui
    Pas prêt  | Non | Non
    Unknown  | Non | Non

1. Cliquez sur le lien hypertexte Plan App Service dans la table pour afficher les détails du plan App Service, comme les ressources de calcul et les autres applications web qui font partie du même plan.

### <a name="review-cost-estimates"></a>Réviser les estimations de coûts

Le résumé de l’évaluation affiche les coûts mensuels estimés pour l’hébergement de vos applications web dans App Service. Dans App Service, vous payez les frais par plan App Service et non par application web. Une ou plusieurs applications peuvent être configurées pour s’exécuter sur les mêmes ressources informatiques (ou dans le même plan App Service). Toutes les applications que vous placez dans ce plan App Service s’exécutent sur ces ressources de calcul telles que définies par votre plan App Service.
Pour optimiser les coûts, l’évaluation d’Azure Migrate alloue plusieurs applications web à chaque plan App Service recommandé. Le nombre d’applications web allouées à chaque instance de plan est indiqué dans le tableau ci-dessous.

**Plan App Service** | **Applications web par plan App Service**
--- | ---
I1  | 8
P1v2  | 8
P1v3  | 16

:::image type="content" source="./media/tutorial-assess-webapps/assessment-webapps-cost.png" alt-text="Détails des coûts":::

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus](concepts-azure-webapps-assessment-calculation.md) sur le mode de calcul des évaluations d’Azure App Service.