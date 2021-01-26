---
title: Créer une évaluation de machine virtuelle Azure avec Azure Migrate Server Assessment | Microsoft Docs
description: Décrit comment créer une évaluation de machine virtuelle Azure avec l’outil Azure Migrate Server Assessment
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/15/2019
ms.openlocfilehash: 178bdca78c6f011c607de8e1f5d5eabcdbaab7d4
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567703"
---
# <a name="create-an-azure-vm-assessment"></a>Créer une évaluation de machine virtuelle Azure

Cet article explique comment créer une évaluation de machine virtuelle Azure pour des machines virtuelles VMware locales ou des machines virtuelles Hyper-V avec Azure Migrate : Server Assessment.

[Azure Migrate](migrate-services-overview.md) vous aide à migrer vers Azure. Azure Migrate offre un hub centralisé pour suivre la découverte, l’évaluation et la migration d’une infrastructure, d’applications et de données locales vers Azure. Le hub fournit des outils Azure pour l’évaluation et la migration, ainsi que des offres d’éditeurs de logiciels indépendant (ISV) tiers. 

## <a name="before-you-start"></a>Avant de commencer

- Assurez-vous que vous avez [créé](./create-manage-projects.md) un projet Azure Migrate.
- Si vous avez déjà créé un projet, assurez-vous que vous avez [ajouté](how-to-assess.md) Azure Migrate : Server Assessment.
- Pour créer une évaluation, vous devez configurer une appliance Azure Migrate pour [VMware](how-to-set-up-appliance-vmware.md) ou [Hyper-V](how-to-set-up-appliance-hyper-v.md). L’appliance découvre les machines locales et envoie les métadonnées et les données de performances à Azure Migrate : Server Assessment. [Plus d’informations](migrate-appliance.md)


## <a name="azure-vm-assessment-overview"></a>Vue d’ensemble des évaluations de machine virtuelle Azure
Il existe deux types de critères de dimensionnement que vous pouvez utiliser pour créer des évaluations de machine virtuelle Azure à l’aide d’Azure Migrate : Server Assessment.

**Évaluation** | **Détails** | **Données**
--- | --- | ---
**Basée sur les performances** | Évaluations basées sur les données de performances collectées | **Taille de machine virtuelle recommandée** : En fonction des données d’utilisation du processeur et de la mémoire.<br/><br/> **Type de disque recommandé (disque managé Standard ou Premium)**  : En fonction des IOPS et du débit des disques locaux.
**Localement** | Évaluations basées sur le dimensionnement local. | **Taille de machine virtuelle recommandée** : En fonction de la taille de machine virtuelle locale<br/><br> **Type de disque recommandé** : En fonction du paramètre de type de stockage que vous sélectionnez pour l’évaluation.

[Apprenez-en davantage](concepts-assessment-calculation.md) sur les évaluations.

## <a name="run-an-assessment"></a>Exécuter une évaluation

Exécutez une évaluation comme suit :

1. Dans la page **Serveurs** > **Serveurs Windows et Linux**, cliquez sur **Évaluer et migrer des serveurs**.

   ![Emplacement du bouton Évaluer et migrer des serveurs](./media/tutorial-assess-vmware-azure-vm/assess.png)

2. Dans **Azure Migrate : Server Assessment**, cliquez sur **Évaluer**.

    ![Emplacement du bouton Évaluer](./media/tutorial-assess-vmware-azure-vm/assess-servers.png)

3. Dans **Évaluer les serveurs** > **Type d’évaluation**, sélectionnez **Machine virtuelle Azure**.
4. Dans **Source de découverte** :

    - Si vous avez découvert des ordinateurs à l’aide de l’appliance, sélectionnez **Machines découvertes par l’appliance Azure Migrate**.
    - Si vous avez découvert des ordinateurs à l’aide d’un fichier CSV importé, sélectionnez **Machines importées**. 
    
1. Cliquez sur **Modifier** pour examiner les propriétés d’évaluation.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vm/assessment-name.png" alt-text="Emplacement du bouton Modifier pour examiner les propriétés d’évaluation":::

1. Dans **Propriétés d’évaluation** > **Propriétés de la cible** :
    - Dans **Emplacement cible**, sélectionnez la région Azure vers laquelle vous souhaitez effectuer la migration.
        - Les recommandations concernant la taille et le coût sont basées sur l’emplacement que vous spécifiez. Une fois que vous avez modifié l’emplacement cible par défaut, vous êtes invité à spécifier des **instances réservées** et une **série de machines virtuelles**.
        - Dans Azure Government, vous pouvez cibler des évaluations dans [ces régions](migrate-support-matrix.md#supported-geographies-azure-government)
    - Dans **Type de stockage**,
        - Si vous souhaitez utiliser des données basées sur les performances dans l’évaluation, sélectionnez **Automatique** pour qu’Azure Migrate recommande un type de stockage, en fonction des IOPS et du débit du disque.
        - Vous pouvez également sélectionner le type de stockage que vous souhaitez utiliser pour la machine virtuelle lorsque vous effectuer la migration.
    - Dans **Instances réservées**, indiquez si vous souhaitez utiliser des instances réservées pour la machine virtuelle lors de sa migration.
        - Si vous choisissez d’utiliser une instance réservée, vous ne pourrez pas spécifier l’option **Remise (%)** ni l’option **Durée de fonctionnement de la machine virtuelle**. 
        - [Plus d’informations](https://aka.ms/azurereservedinstances)
 1. Dans **Taille de la machine virtuelle** :
     - Dans **Critère de dimensionnement**, choisissez le type d’évaluation que vous souhaitez effectuer : basée sur les données de configuration, basée sur les métadonnées de l’ordinateur ou basée sur les données de performances. Si vous utilisez des données de performances :
        - Dans **Historique des performances**, indiquez la période de données sur laquelle vous souhaitez baser l’évaluation.
        - Dans **Utilisation en centile**, spécifiez la valeur de centile que vous souhaitez utiliser pour l’échantillon de performances. 
    - Dans **Série de machines virtuelles**, spécifiez la série de machines virtuelles Azure que vous souhaitez utiliser.
        - Si vous utilisez l’évaluation basée sur les performances, Azure Migrate vous suggère une valeur.
        - Ajustez les paramètres selon vos besoins. Par exemple, si vous ne disposez pas d’un environnement de production nécessitant des machines virtuelles de série A dans Azure, vous pouvez exclure la série A de la liste de séries.
    - Dans **Facteur de confort**, indiquez la mémoire tampon que vous souhaitez utiliser lors de l’évaluation. Celle-ci prend en compte les problèmes, tels que l’utilisation saisonnière, l’historique des performances de courte durée et l’augmentation probable de l’utilisation. Par exemple, si vous utilisez un facteur de confort de 2 :
    
        **Composant** | **Utilisation efficace** | **Ajouter un facteur de confort (2.0)**
        --- | --- | ---
        Cœurs | 2  | 4
        Mémoire | 8 Go | 16 Go
   
1. Dans **Tarifs** :
    - Dans **Offre**, spécifiez l’[offre Azure](https://azure.microsoft.com/support/legal/offer-details/) à laquelle vous êtes inscrit, le cas échéant. Évaluation de serveur estime le coût de cette offre.
    - Dans **Devise**, sélectionnez la devise de facturation de votre compte.
    - Dans **Remise (%)** , ajoutez les remises applicables à votre abonnement que vous recevez en plus de l’offre Azure. Le paramètre par défaut est 0 %.
    - Dans **Durée de fonctionnement de la machine virtuelle**, spécifiez la durée (en jours par mois ou en heures par jour) pendant laquelle les machines virtuelles doivent s’exécuter.
        - Ce paramètre est utile pour les machines virtuelles Azure qui ne s’exécuteront pas en continu.
        - Les estimations de coût sont basées sur la durée spécifiée.
        - La valeur par défaut est de 31 jours par mois ou de 24 heures par jour.
    - Dans **Abonnement Contrat Entreprise**, indiquez si vous souhaitez prendre en compte une remise sur l’abonnement Contrat Entreprise dans l’estimation des coûts. 
    - Dans **Azure Hybrid Benefit**, indiquez si vous disposez déjà d’une licence Windows Server. Si c’est le cas et si vous êtes couvert par une assurance Software Assurance active pour abonnements Windows Server, vous pouvez faire une demande pour [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/) au moment d’importer les licences dans Azure.

1. Si vous avez apporté des modifications, cliquez sur **Enregistrer**.

    ![Propriétés de l’évaluation](./media/tutorial-assess-vmware-azure-vm/assessment-properties.png)

1. Dans **Évaluer les serveurs**, cliquez sur **Suivant**.

1. Dans **Sélectionner les machines à évaluer** > **Nom de l’évaluation**, spécifiez un nom pour l’évaluation. 

1. Dans **Sélectionner ou créer un groupe**, sélectionnez **Créer**, puis spécifiez un nom de groupe. 

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vm/assess-group.png" alt-text="Ajouter des machines virtuelles à un groupe":::

1. Sélectionnez l’appliance, puis sélectionnez les machines virtuelles que vous souhaitez ajouter au groupe. Cliquez ensuite sur **Suivant**.


1. Dans **Vérifier + créer l’évaluation**, passez en revue les détails de l’évaluation, puis cliquez sur **Créer une évaluation** pour créer le groupe et lancer l’évaluation.

1. Une fois l’évaluation créée, vous pouvez la voir dans **Serveurs** > **Azure Migrate : Server Assessment** > **Évaluations**.

1. Cliquez sur **Exporter l’évaluation** pour la télécharger sous la forme d’un fichier Excel.
    > [!NOTE]
    > Pour les évaluations basées sur les performances, nous vous recommandons d’attendre au moins un jour après le démarrage de la découverte pour créer l’évaluation. Cela permet de collecter des données de performances avec un plus haut niveau de confiance. Dans l’idéal, une fois que vous avez démarré la découverte, vous devriez attendre la période de performances que vous avez spécifiée (jour/semaine/mois) afin d’obtenir un haut niveau de confiance.


## <a name="review-an-azure-vm-assessment"></a>Passer en revue une évaluation de machine virtuelle Azure

Une évaluation de machine virtuelle Azure décrit :

- **Préparé pour Azure** : Indique si les machines virtuelles peuvent faire l’objet d’une migration vers Azure.
- **Estimation des coûts mensuels** : Coûts mensuels de calcul et de stockage estimés pour l’exécution des machines virtuelles dans Azure.
- **Estimation des coûts de stockage mensuels** : Coûts estimés pour le stockage sur disque après la migration.

### <a name="view-an-azure-vm-assessment"></a>Passer en revue une évaluation de machine virtuelle Azure

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

3. Cliquez sur un état **Préparé pour Azure**. Vous pouvez voir les informations relatives à l’état de préparation des machines virtuelles et explorer ces dernières en détail, notamment en ce qui concerne les valeurs pour le calcul, le stockage et le réseau.



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