---
title: Évaluer de grands nombres de serveurs dans un environnement Hyper-V pour la migration vers Azure avec Azure Migrate Microsoft Docs
description: Décrit comment évaluer un grand nombre de serveurs dans un environnement Hyper-V pour la migration vers Azure à l’aide du service Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/10/2019
ms.openlocfilehash: 495e1bf415146471fcccad34e2879398e12e1769
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104780291"
---
# <a name="assess-large-numbers-of-servers-in-hyper-v-environment-for-migration-to-azure"></a>Évaluer de grands nombres de serveurs dans un environnement Hyper-V pour la migration vers Azure

Cet article explique comment évaluer de grands nombres de serveurs locaux dans un environnement Hyper-V pour les migrer vers Azure à l’aide de l’outil de découverte et d’évaluation d’Azure Migrate.

[Azure Migrate](migrate-services-overview.md) fournit un hub d’outils qui vous permettent de découvrir, d’évaluer et de migrer des applications, une infrastructure et des charges de travail vers Microsoft Azure. Le hub comprend des outils Azure Migrate et des offres d’ISV (fournisseurs de logiciels indépendants) tiers. 


Dans cet article, vous apprendrez comment :
> [!div class="checklist"]
> * Planifier l’évaluation à grande échelle.
> * Configurer les autorisations Azure et préparer Hyper-V pour l’évaluation.
> * Créer un projet Azure Migrate et une évaluation.
> * Examiner l’évaluation au fur et à mesure que vous planifiez la migration.


> [!NOTE]
> Si vous voulez tester une preuve de concept afin d’évaluer quelques serveurs avant d’effectuer une évaluation à grande échelle, suivez notre [série de tutoriels](./tutorial-discover-hyper-v.md).

## <a name="plan-for-assessment"></a>Planifier l’évaluation

Lorsque vous planifiez l’évaluation d’un grand nombre de serveurs physiques dans un environnement Hyper-V, vous devez prendre en considérations quelques points :

- **Planification de projets Azure Migrate** : Découvrez comment déployer les projets Azure Migrate. Par exemple, si vos centres de données sont situés dans des zones géographiques différentes, ou si vous avez besoin de stocker des métadonnées de découverte, d’évaluation ou de migration dans une autre zone géographique, vous pouvez avoir besoin de plusieurs projets.
- **Planifier des appliances** : Azure Migrate utilise une appliance Azure Migrate locale, déployée en tant que machine virtuelle Hyper-V, afin de découvrir en permanence des serveurs à des fins d’évaluation et de migration. L’appliance surveille des changements d’environnement tels que l’ajout de serveurs, de disques ou de cartes réseau. Elle envoie également à Azure les métadonnées et les données de performance concernant ces opérations. Vous devez déterminer le nombre d’appliances à déployer.


## <a name="planning-limits"></a>Limites de planification
 
Utilisez les limites résumées dans ce tableau pour la planification.

**Planification** | **Limites**
--- | --- 
**Projets Azure Migrate** | Évaluez jusqu’à 35 000 serveurs par projet.
**Appliance Azure Migrate** | Une appliance peut découvrir jusqu’à 5 000 serveurs.<br/> Une appliance peut connecter à jusqu’à 300 hôtes Hyper-V.<br/> Une appliance ne peut être associée qu’à un seul projet Azure Migrate.<br/> Un nombre quelconque d’appliances peut être associé à un même projet Azure Migrate. <br/><br/> 
**Groupe** | Vous pouvez ajouter jusqu’à 35 000 serveurs dans un groupe unique.
**Évaluation d’Azure Migrate** | Vous pouvez évaluer jusqu’à 35 000 serveurs par évaluation.



## <a name="other-planning-considerations"></a>Autres considérations en matière de planification

- Pour démarrer la découverte à partir de l’appliance, vous devez sélectionner chaque hôte Hyper-V. 
- Si vous exécutez un environnement multi-locataire, vous ne pouvez pas découvrir uniquement les serveurs qui appartiennent à un locataire spécifique. 

## <a name="prepare-for-assessment"></a>Préparer pour l’évaluation

Préparer Azure et Hyper-V pour l’outil de découverte et d’évaluation : 

1. Vérifiez les [spécifications et limitations de la prise en charge Hyper-V](migrate-support-matrix-hyper-v.md).
2. Configurer des autorisations pour permettre à votre compte Azure d’interagir avec Azure Migrate
3. Préparer les hôtes et les serveurs Hyper-V

Suivez les instructions de [ce tutoriel](./tutorial-discover-hyper-v.md) pour configurer ces paramètres.

## <a name="create-a-project"></a>Création d’un projet

Conformément à vos exigences de planification, procédez comme suit :

1. Créez un projet Azure Migrate.
2. Ajoutez l’outil Azure Migrate : découverte et évaluation aux projets.

[En savoir plus](./create-manage-projects.md)

## <a name="create-and-review-an-assessment"></a>Créer et examiner une évaluation

1. Créer des évaluations pour des serveurs dans un environnement Hyper-V.
1. Passez en revue les évaluations en préparation de la planification de la migration.

[En savoir](tutorial-assess-hyper-v.md) plus sur la création et l’examen des évaluations.
    

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous découvrirez comment :
 
> [!div class="checklist"] 
> * Planifier la mise à l’échelle des évaluations d’Azure Migrate pour les serveurs dans un environnement Hyper-V
> * Préparer Azure et Hyper-V pour l’évaluation
> * Créer un projet Azure Migrate et effectuer des évaluations
> * Passer en revue les évaluations en préparation de la migration.

Maintenant, [découvrez comment](concepts-assessment-calculation.md) les évaluations sont calculées, et comment les [modifier](how-to-modify-assessment.md).