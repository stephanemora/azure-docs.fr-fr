---
title: Créer une évaluation avec l’évaluation de serveur Azure Migrate | Microsoft Docs
description: Décrit comment créer une évaluation avec l’outil d’évaluation de serveur Azure Migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: cffde2a677650387dffd19733e082ff7002ccb55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68228976"
---
# <a name="create-an-assessment"></a>Créer une évaluation

Cet article explique comment créer une évaluation pour les machines virtuelles VMware locales ou les machines virtuelles Hyper-V avec Azure Migrate : Server Assessment.

[Azure Migrate](migrate-services-overview.md) vous aide à migrer vers Azure. Azure Migrate offre un hub centralisé pour suivre la découverte, l’évaluation et la migration d’une infrastructure, d’applications et de données locales vers Azure. Le hub fournit des outils Azure pour l’évaluation et la migration, ainsi que des offres d’éditeurs de logiciels indépendant (ISV) tiers. 

## <a name="before-you-start"></a>Avant de commencer

- Assurez-vous que vous avez [créé](how-to-add-tool-first-time.md) un projet Azure Migrate.
- Si vous avez déjà créé un projet, assurez-vous que vous avez [ajouté](how-to-assess.md) Azure Migrate : Server Assessment.
- Pour créer une évaluation, vous devez configurer une appliance Azure Migrate pour [VMware](how-to-set-up-appliance-vmware.md) ou [Hyper-V](how-to-set-up-appliance-hyper-v.md). L’appliance découvre les machines locales et envoie les métadonnées et les données de performances à Azure Migrate : Server Assessment. [Plus d’informations](migrate-appliance.md)


## <a name="assessment-overview"></a>Vue d’ensemble de l’évaluation
Vous pouvez créer deux types d’évaluation à l’aide d’Azure Migrate : Server Assessment.

**Évaluation** | **Détails** | **Données**
--- | --- | ---
**Basée sur les performances** | Évaluations basées sur les données de performances collectées | **Taille de machine virtuelle recommandée** : En fonction des données d’utilisation du processeur et de la mémoire.<br/><br/> **Type de disque recommandé (disque managé Standard ou Premium)**  : En fonction des IOPS et du débit des disques locaux.
**Localement** | Évaluations basées sur le dimensionnement local. | **Taille de machine virtuelle recommandée** : En fonction de la taille de machine virtuelle locale<br/><br> **Type de disque recommandé** : En fonction du paramètre de type de stockage que vous sélectionnez pour l’évaluation.

[Apprenez-en davantage](concepts-assessment-calculation.md) sur les évaluations.

## <a name="run-an-assessment"></a>Exécuter une évaluation

Exécutez une évaluation comme suit :

1. Passez en revue les [meilleures pratiques](best-practices-assessment.md) liées à la création d’évaluations.
2. Sous l’onglet **Serveurs**, dans la vignette **Azure Migrate : Mosaïque**  d’évaluation de serveur, cliquez sur **Évaluer**.

    ![Évaluer](./media/how-to-create-assessment/assess.png)

2. Dans **Évaluer les serveurs**, spécifiez un nom pour l’évaluation.
3. Cliquez sur **Tout afficher** pour passer en revue les propriétés de l’évaluation.

    ![Propriétés de l’évaluation](./media/how-to-create-assessment//view-all.png)

3. Dans **Sélectionner ou créer un groupe**, sélectionnez **Créer**, puis spécifiez un nom de groupe. Un groupe rassemble une ou plusieurs machines virtuelles à évaluer.
4. Dans **Ajouter des machines au groupe**, sélectionnez les machines virtuelles à ajouter au groupe.
5. Cliquez sur **Créer une évaluation** pour créer le groupe, puis exécutez l’évaluation.

    ![Créer une évaluation](./media/how-to-create-assessment//assessment-create.png)

6. Une fois l’évaluation créée, vous pouvez la voir dans **Serveurs** > **Azure Migrate : Server Assessment** > **Évaluations**.
7. Cliquez sur **Exporter l’évaluation** pour la télécharger sous la forme d’un fichier Excel.



## <a name="review-an-assessment"></a>Réviser une évaluation

Une évaluation décrit les éléments suivants :

- **Préparé pour Azure** : Indique si les machines virtuelles peuvent faire l’objet d’une migration vers Azure.
- **Estimation des coûts mensuels** : Coûts mensuels de calcul et de stockage estimés pour l’exécution des machines virtuelles dans Azure.
- **Estimation des coûts de stockage mensuels** : Coûts estimés pour le stockage sur disque après la migration.

### <a name="view-an-assessment"></a>Voir une évaluation

1. Dans **Objectifs de migration** >  **Serveurs**, cliquez sur **Évaluations** dans **Azure Migrate : Évaluation de serveur**.
2. Dans **Évaluations**, cliquez sur une évaluation pour l’ouvrir.

    ![Récapitulatif de l’évaluation](./media/how-to-create-assessment/assessment-summary.png)

### <a name="review-azure-readiness"></a>Examiner la préparation pour Azure

1. Dans **Préparé pour Azure**, vérifiez si les machines virtuelles sont prêtes pour la migration vers Azure.
2. Passez en revue l’état des machines virtuelles :
    - **Disponible pour Azure** : Azure Migrate recommande une taille de machine virtuelle et donne des estimations de coût pour les machines virtuelles de l’évaluation.
    - **Disponible sous conditions** : Montre des problèmes et leur correction suggérée.
    - **Non disponible pour Azure** : Montre des problèmes et leur correction suggérée.
    - **État de la préparation inconnu** : Utilisé quand Azure Migrate ne peut pas évaluer la préparation, en raison de problèmes de disponibilité des données.

2. Cliquez sur un état **Préparé pour Azure**. Vous pouvez voir les informations relatives à l’état de préparation des machines virtuelles et explorer ces dernières en détail, notamment en ce qui concerne les valeurs pour le calcul, le stockage et le réseau.



### <a name="review-cost-details"></a>Passer en revue les détails des coûts

Cette vue montre une estimation des coûts de calcul et de stockage liés à l’exécution des machines virtuelles dans Azure.

1. Passez en revue les coûts mensuels de calcul et de stockage. Les coûts sont agrégés pour toutes les machines virtuelles du groupe évalué.

    - Les estimations des coûts sont basées sur les suggestions de taille d’une machine ainsi que sur ses disques et ses propriétés.
    - Les coûts mensuels estimés pour le calcul et le stockage sont affichés.
    - L’estimation des coûts porte sur l’exécution des machines virtuelles locales en tant que machines virtuelles IaaS. Azure Migrate Server Assessment ne prend pas en compte les coûts PaaS ou SaaS.

2. Vous pouvez passer en revue les estimations des coûts de stockage mensuels. Cette vue montre les coûts de stockage agrégés pour le groupe évalué, répartis selon différents types de disque de stockage.
3. Vous pouvez explorer les détails de machines virtuelles spécifiques.


### <a name="review-confidence-rating"></a>Examiner le niveau de confiance

Quand vous effectuez des évaluations basées sur les performances, un niveau de confiance est affecté à l’évaluation.

![Niveau de confiance](./media/how-to-create-assessment/confidence-rating.png)

- Un niveau allant de 1 étoile (le plus faible) à 5 étoiles (le plus élevé) est attribué.
- Le niveau de confiance vous aide à estimer la fiabilité des suggestions de taille fournies par l’évaluation.
- Le niveau de confiance est basé sur la disponibilité des points de données nécessaires pour calculer l’évaluation.

Les niveaux de confiance d’une évaluation sont les suivants.

**Disponibilité des points de données** | **Niveau de confiance**
--- | ---
0 %-20 % | 1 étoile
21 %-40 % | 2 étoiles
41 %-60 % | 3 étoiles
61 %-80 % | 4 étoiles
81 %-100 % | 5 étoiles




## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment utiliser le [mappage de dépendances](how-to-create-group-machine-dependencies.md) pour créer des groupes à haute fiabilité.
- [Découvrez plus en détail](concepts-assessment-calculation.md) le mode de calcul des évaluations.
