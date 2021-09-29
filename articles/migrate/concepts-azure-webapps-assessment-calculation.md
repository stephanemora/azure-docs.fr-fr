---
title: Évaluations d’Azure App Service dans l’outil de détection et d’évaluation d’Azure Migrate Discovery
description: En savoir plus sur les évaluations Azure App Service dans l’outil de détection et d’évaluation d’Azure Migrate Discovery
author: rashi-ms
ms.author: rajosh
ms.topic: conceptual
ms.date: 07/27/2021
ms.openlocfilehash: 3328fb0fb20a9b0e492c3cd4281ee35dcf12dda0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124792027"
---
# <a name="assessment-overview-migrate-to-azure-app-service"></a>Vue d’ensemble de l’évaluation (migrer vers Azure App Service)

Cet article fournit une vue d’ensemble des évaluations relatives à la migration d’applications web ASP.NET locales d’un environnement VMware vers Azure App Service à l’aide de l’[outil de détection et d’évaluation d’Azure Migrate](./migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool).

## <a name="whats-an-assessment"></a>Qu'est-ce qu'une évaluation ?
L’évaluation réalisée à l’aide de l’outil de détection et d’évaluation est un instantané des données qui mesure l’état de préparation et fournit des informations sur le coût de l’hébergement de serveurs, de bases de données et d’applications web locales vers Azure.

## <a name="types-of-assessments"></a>Types d'évaluations

Il existe quatre types d’évaluations que vous pouvez créer à l’aide de l’outil de détection et d’évaluation d’Azure Migrate.

**Type d’évaluation** | **Détails**
--- | ---
**Microsoft Azure** | Évaluations pour migrer vos serveurs locaux vers des machines virtuelles Azure. <br/><br/> Ce type d’évaluation vous permet d’évaluer vos serveurs locaux dans un environnement [VMware](how-to-set-up-appliance-vmware.md) et [Hyper-V](how-to-set-up-appliance-hyper-v.md), ainsi que vos [serveurs physiques](how-to-set-up-appliance-physical.md) pour une migration vers des machines virtuelles Azure.
**Azure SQL** | Évaluations pour migrer vos serveurs SQL Server locaux de votre environnement VMware vers Azure SQL Database ou Azure SQL Managed Instance.
**Azure App Service** | Évaluations pour migrer vos applications web ASP.NET locales, exécutées sur des serveurs web IIS, de votre environnement VMware vers Azure App Service.
**Azure VMware Solution (AVS)** | Évaluations pour migrer vos serveurs locaux vers [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> Vous pouvez évaluer vos [machines virtuelles VMware](how-to-set-up-appliance-vmware.md) locales pour la migration vers Azure VMware Solution (AVS) avec ce type d’évaluation. [En savoir plus](concepts-azure-vmware-solution-assessment-calculation.md)

Une évaluation Azure App Service fournit un critère de dimensionnement :

**Critère de dimensionnement** | **Détails** | **Données**
--- | --- | ---
**Basé sur la configuration** | Évaluations qui donnent des recommandations en fonction des données de configuration collectées | L’évaluation d’Azure App Service accepte uniquement les données de configuration pour le calcul d’évaluation. Les données de performances pour les applications web ne sont pas collectées.

## <a name="how-do-i-assess-my-on-premises-aspnet-web-apps"></a>Comment faire pour évaluer mes applications web ASP.NET locales ?

Vous pouvez évaluer vos applications web locales à l’aide des données de configuration collectées par une appliance Azure Migrate légère. L’appliance détecte les applications web locales et envoie les données de configuration à Azure Migrate. [En savoir plus](how-to-set-up-appliance-vmware.md)

## <a name="how-do-i-assess-with-the-appliance"></a>Comment procéder à une évaluation à l'aide de l'appliance ?

Si vous déployez une appliance Azure Migrate pour découvrir des serveurs locaux, procédez comme suit :

1. Configurez Azure et votre environnement local de manière à ce qu’ils opèrent avec Azure Migrate.
2. Pour la première évaluation, créez un projet Azure Migrate. L’outil Azure Migrate : détection et évaluation est automatiquement ajouté au projet.
3. Déployez une appliance Azure Migrate légère. L’appliance découvre en permanence les machines locales et envoie les données de configuration et de performances à Azure Migrate. Déployez l’appliance en tant que machine virtuelle ou serveur physique. Vous n’avez rien à installer sur les serveurs que vous souhaitez évaluer.

Une fois que l’appliance a commencé la détection, vous pouvez rassembler les serveurs (hébergement d’applications web) que vous souhaitez évaluer dans un groupe et exécuter une évaluation de celui-ci avec le type d’évaluation **Azure App Service**.

Suivez notre tutoriel relatif à l’évaluation d’[applications web ASP.NET](tutorial-assess-webapps.md) pour essayer ces étapes.

## <a name="what-properties-are-used-to-customize-the-assessment"></a>Quelles sont les propriétés utilisées pour personnaliser l’évaluation ?

Voici ce qu’incluent les propriétés d’évaluation d’Azure App Service :

**Propriété** | **Détails**
--- | ---
**Emplacement cible** | Région Azure vers laquelle vous souhaitez opérer la migration. Les recommandations en matière de configuration et de coût d’Azure App Service sont basées sur l’emplacement que vous spécifiez.
**Isolation requise** | Sélectionnez Oui si vous souhaitez que vos applications web s’exécutent dans un environnement privé et dédié dans un centre de données Azure à l’aide de machines virtuelles de la série Dv2 avec des processeurs plus rapides, un stockage SSD et le double de mémoire ratio cœur par rapport aux plans Standard.
**Instances réservées** | Spécifie des instances réservées afin que les estimations de coût dans l’évaluation les prennent en compte.<br/><br/> Si vous sélectionnez une option d’instance réservée, vous ne pouvez pas spécifier de « Remise (%) ».
**Offer** | [Offre Azure](https://azure.microsoft.com/support/legal/offer-details/) dans laquelle vous êtes inscrit. L’évaluation estime le coût de cette offre.
**Devise** | Devise de facturation de votre compte.
**Remise (%)** | Toute remise propre à un abonnement que vous recevez en plus de l’offre Azure. Le paramètre par défaut est 0 %.
**Abonnement Contrat Entreprise** | Spécifie qu’un abonnement Contrat Entreprise (EA) est utilisé pour l’estimation des coûts. Prend en compte la remise applicable à l’abonnement. <br/><br/> Conserve les paramètres par défaut pour les instances réservées, la remise (en %) et les propriétés de durée de bon fonctionnement des machines virtuelles.

[Passez en revue les meilleures pratiques](best-practices-assessment.md) de création et d’évaluation avec Azure Migrate.

## <a name="calculate-readiness"></a>Calculer l’état de préparation

### <a name="azure-app-service-readiness"></a>Préparation d’Azure App Service

La préparation d’Azure App Service pour les applications web est basée sur les vérifications de compatibilité des fonctionnalités entre la configuration locale des applications Web et Azure App Service :

1. L’évaluation Azure App Service prend en compte les données de configuration des applications web pour identifier les problèmes de compatibilité.
1. Si aucun problème de compatibilité n’est détecté, la préparation est marquée comme **Prête** pour le type de déploiement cible.
1. S’il existe des problèmes de compatibilité non critiques, tels que des fonctionnalités détériorées ou non prises en charge qui ne bloquent pas la migration vers un type de déploiement cible spécifique, l’état de préparation est marqué comme **Prêt sous conditions** (avec lien hypertexte) avec des détails d’**avertissement** et des recommandations de correction.
1. S’il existe des problèmes de compatibilité susceptibles de bloquer la migration vers un type de déploiement cible spécifique, L’état de préparation est marqué comme **Non prêt** avec des détails sur le **problème** et des recommandations de correction.
1. Si la détection est toujours en cours ou s’il existe des problèmes de détection pour une instance ou une application web, l’état de préparation est marqué comme **Inconnu** , car l’évaluation n’a pas pu calculer la préparation pour cette application web.

## <a name="calculate-sizing"></a>Calculer le dimensionnement

### <a name="azure-app-service-sku"></a>Référence SKU Azure App Service

Une fois que l’évaluation a déterminé le niveau de préparation en fonction des données de configuration, elle détermine la référence SKU Azure App Service qui convient à l’exécution de vos applications dans Azure App Service.
Les plans Premium sont destinés aux charges de travail de production. Ils s’exécutent sur des instances dédiées de machine virtuelle. Chaque instance peut prendre en charge plusieurs applications et domaines. Les plans Isolé hébergent vos applications dans un environnement Azure privé dédié et ils conviennent parfaitement aux applications nécessitant des connexions sécurisées avec votre réseau local.

> [!NOTE]
> Actuellement, Azure Migrate recommande uniquement les références SKU I1, P1v2 et P1v3. D’autres références SKU sont disponibles dans Azure App Service. [Plus d’informations](https://azure.microsoft.com/pricing/details/app-service/windows/)

### <a name="azure-app-service-plan"></a>Plan Azure App Service

Dans App Service, une application s’exécute toujours dans un [plan App Service](../app-service/overview-hosting-plans.md). Un plan App Service définit un ensemble de ressources de calcul nécessaires à l’exécution d’une application web. À un niveau élevé, le plan/la référence SKU est déterminé en fonction du tableau ci-dessous.

**Isolation requise** | **Instance réservée** | **Plan App Service/SKU**
--- | --- | ---
Oui  | Oui | I1
Oui  | Non  | I1
Non  | Oui | P1v3
Non  | Non | P1v2

### <a name="azure-app-service-cost-details"></a>Détails des coûts d’Azure App Service

Un [plan App Service](../app-service/overview-hosting-plans.md) comporte une [facturation](https://azure.microsoft.com/pricing/details/app-service/windows/) des ressources de calcul qu’il utilise. Dans App Service, vous payez les frais par plans App Service et non par application web. Une ou plusieurs applications peuvent être configurées pour s’exécuter sur les mêmes ressources informatiques (ou dans le même plan App Service). Toutes les applications que vous placez dans ce plan App Service s’exécutent sur ces ressources de calcul telles que définies par votre plan App Service.
Pour optimiser les coûts, l’évaluation d’Azure Migrate alloue plusieurs applications web à chaque plan App Service recommandé. Le nombre d’applications web allouées à chaque instance de plan est indiqué dans le tableau ci-dessous.

**Plan App Service** | **Applications web par plan App Service**
--- | ---
I1  | 8
P1v2  | 8
P1v3  | 16

> [!NOTE]
> Un plan App Service peut être mis à l’échelle à tout moment. [Plus d’informations](../app-service/overview-hosting-plans.md#what-if-my-app-needs-more-capabilities-or-features)

## <a name="next-steps"></a>Étapes suivantes
- [Passer en revue](best-practices-assessment.md) les meilleures pratiques pour la création d’évaluations. 
- Découvrez comment exécuter une [Évaluation Azure App Service](how-to-create-azure-app-service-assessment.md)