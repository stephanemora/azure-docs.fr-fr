---
title: Évaluer des serveurs physiques en vue d’une migration vers Azure avec Azure Migrate
description: Décrit comment évaluer des serveurs physiques locaux pour la migration vers Azure avec Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: MVC
ms.openlocfilehash: 44d92bbf5c3767a15e600836acd23cf3055f842e
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114464984"
---
# <a name="tutorial-assess-physical-servers-for-migration-to-azure"></a>Tutoriel : Évaluer des serveurs physiques pour la migration vers Azure

Dans le cadre de votre migration vers Azure, vous devez évaluer vos charges de travail locales afin de déterminer la préparation nécessaire, d’identifier les risques et d’estimer les coûts ainsi que le niveau de complexité de la tâche.

Cet article explique comment évaluer des serveurs physiques locaux en vue de leur migration vers Azure, en utilisant l’outil Azure Migrate : découverte et évaluation.


Dans ce tutoriel, vous allez apprendre à :
> [!div class="checklist"]
- Exécuter une évaluation basée sur les métadonnées et les informations de configuration du serveur
- Exécuter une évaluation basée sur les données de performances

> [!NOTE]
> Les tutoriels indiquent le moyen le plus rapide de tester un scénario, et ils utilisent les options par défaut lorsque cela est possible. 

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) avant de commencer.


## <a name="prerequisites"></a>Prérequis

- Avant de suivre ce tutoriel pour évaluer vos serveurs en vue de les migrer vers des machines virtuelles Azure, vérifiez que vous avez découvert les serveurs que vous souhaitez évaluer :
    - Pour découvrir des serveurs avec l’appliance Azure Migrate, [suivez ce tutoriel](tutorial-discover-physical.md). 
    - Pour découvrir des serveurs avec un fichier CSV importé, [suivez ce tutoriel](tutorial-discover-import.md).
- Vérifiez que les serveurs physiques que vous souhaitez évaluer n’exécutent pas Windows Server 2003 ni SUSE Linux. L’évaluation n’est pas prise en charge pour ces serveurs.


## <a name="decide-which-assessment-to-run"></a>Décider de l’évaluation à exécuter


Déterminez si vous souhaitez exécuter une évaluation à l’aide de critères de dimensionnement basés sur les données de configuration du serveur, sur les métadonnées collectées telles quelles localement ou sur les données de performances dynamiques.

**Évaluation** | **Détails** | **Recommandation**
--- | --- | ---
**Telle quelle locale** | Pour une évaluation basée sur les données de configuration ou les métadonnées du serveur.  | La taille recommandée pour la machine virtuelle Azure est basée sur la taille de la machine virtuelle locale.<br/><br> Le type de disque Azure recommandé est basé sur le paramètre de type de stockage sélectionné pour l’évaluation.
**Basée sur les performances** | Pour une évaluation basée sur les données de performances dynamiques collectées. | La taille recommandée pour la machine virtuelle Azure est basée sur les données d’utilisation du processeur et de la mémoire.<br/><br/> Le type de disque recommandé est basé sur les IOPS et le débit des disques locaux.

## <a name="run-an-assessment"></a>Exécuter une évaluation

Exécutez une évaluation comme suit :

1. Sur la page **Vue d’ensemble** > **Windows, Linux et SQL Server**, cliquez sur **Évaluer et migrer des serveurs**.

   ![Emplacement du bouton Évaluer et migrer des serveurs.](./media/tutorial-assess-vmware-azure-vm/assess.png)

2. Dans **Azure Migrate : découverte et évaluation**, cliquez sur **Évaluer**.

    ![Emplacement du bouton Évaluer](./media/tutorial-assess-vmware-azure-vm/assess-servers.png)

3. Dans **Évaluer les serveurs** > **Type d’évaluation**, sélectionnez **Machine virtuelle Azure**.
4. Dans **Source de découverte** :

    - Si vous avez découvert des serveurs avec l’appliance, sélectionnez **Serveurs découverts par l’appliance Azure Migrate**.
    - Si vous avez découvert des serveurs avec un fichier CSV importé, sélectionnez **Serveurs importés**. 
    
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
     - Dans **Critères de dimensionnement**, sélectionnez si vous souhaitez baser l’évaluation sur les données/métadonnées de la configuration du serveur ou sur les données basées sur le niveau de performance. Si vous utilisez des données de performances :
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
    - Dans **Offre**, spécifiez l’[offre Azure](https://azure.microsoft.com/support/legal/offer-details/) à laquelle vous êtes inscrit, le cas échéant. L’évaluation estime le coût de cette offre.
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

1. Dans **Sélectionner les serveurs à évaluer** > **Nom de l’évaluation**, spécifiez un nom pour l’évaluation. 

1. Dans **Sélectionner ou créer un groupe**, sélectionnez **Créer**, puis spécifiez un nom de groupe. 
    

1. Sélectionnez l’appliance, puis sélectionnez les machines virtuelles que vous souhaitez ajouter au groupe. Cliquez ensuite sur **Suivant**.


1. Dans **Vérifier + créer l’évaluation**, passez en revue les détails de l’évaluation, puis cliquez sur **Créer une évaluation** pour créer le groupe et lancer l’évaluation.

1. Une fois l’évaluation créée, affichez-la dans **Serveurs** > **Azure Migrate : découverte et évaluation** > **Évaluations**.

1. Cliquez sur **Exporter l’évaluation** pour la télécharger sous la forme d’un fichier Excel.
    > [!NOTE]
    > Pour les évaluations basées sur les performances, nous vous recommandons d’attendre au moins un jour après le démarrage de la découverte pour créer l’évaluation. Cela permet de collecter des données de performances avec un plus haut niveau de confiance. Dans l’idéal, une fois que vous avez démarré la découverte, vous devriez attendre la période de performances que vous avez spécifiée (jour/semaine/mois) afin d’obtenir un haut niveau de confiance.

## <a name="review-an-assessment"></a>Réviser une évaluation

Une évaluation décrit les éléments suivants :

- **Préparé pour Azure** : Indique si les machines virtuelles peuvent faire l’objet d’une migration vers Azure.
- **Estimation des coûts mensuels** : Coûts mensuels de calcul et de stockage estimés pour l’exécution des machines virtuelles dans Azure.
- **Estimation des coûts de stockage mensuels** : Coûts estimés pour le stockage sur disque après la migration.

Pour voir une évaluation :

1. Dans **Windows, Linux et SQL Server** > **Azure Migrate : découverte et évaluation**, cliquez sur le numéro à côté de **Évaluations**.
2. Dans **Évaluations**, sélectionnez une évaluation pour l’ouvrir. Voici un exemple (estimations et coûts, à titre d’exemple uniquement) : 

    ![Récapitulatif de l’évaluation](./media/tutorial-assess-physical/assessment-summary.png)

3. Passez en revue le récapitulatif de l’évaluation. Vous pouvez également modifier les propriétés de l’évaluation ou recalculer celle-ci.
 
 
### <a name="review-readiness"></a>Vérifier la préparation nécessaire

1. Cliquez sur **Préparé pour Azure**.
2. Dans **Préparé pour Azure**, vérifiez l’état de la machine virtuelle :
    - **Disponible pour Azure** : Utilisé quand Azure Migrate recommande une taille de machine virtuelle et donne des estimations de coût pour les machines virtuelles de l’évaluation.
    - **Disponible sous conditions** : Montre des problèmes et leur correction suggérée.
    - **Non disponible pour Azure** : Montre des problèmes et leur correction suggérée.
    - **État de la préparation inconnu** : Utilisé quand Azure Migrate ne peut pas évaluer la préparation en raison de problèmes de disponibilité des données.

3. Sélectionnez un état **Préparé pour Azure**. Vous pouvez voir les détails de la préparation de la machine virtuelle. Vous pouvez également consulter les détails de la machine virtuelle, notamment les paramètres de calcul, de stockage et de réseau.

### <a name="review-cost-estimates"></a>Réviser les estimations de coûts

Ce résumé d’évaluation montre une estimation des coûts de calcul et de stockage liés à l’exécution des machines virtuelles dans Azure. 

1. Vérifiez les coûts totaux mensuels. Les coûts sont agrégés pour toutes les machines virtuelles du groupe évalué.

    - Les estimations des coûts sont basées sur les suggestions de taille d’un serveur, sur ses disques et sur ses propriétés.
    - Les coûts mensuels estimés pour le calcul et le stockage sont affichés.
    - L’estimation des coûts porte sur l’exécution de machines virtuelles locales dans Azure. L’estimation ne prend pas en compte les coûts PaaS ni SaaS.

2. Passez en revue les coûts de stockage mensuels. Cette vue montre les coûts de stockage agrégés pour le groupe évalué, répartis selon les différents types de disque de stockage. 
3. Vous pouvez consulter le détail des coûts de machines virtuelles spécifiques.

### <a name="review-confidence-rating"></a>Examiner le niveau de confiance

Azure Migrate attribue un niveau de confiance aux évaluations basées sur les performances. Ce niveau est exprimé en étoiles, 1 étoile étant le plus bas niveau et 5 étoiles le plus haut.

![Niveau de confiance](./media/tutorial-assess-physical/confidence-rating.png)

Le niveau de confiance vous permet d’estimer la fiabilité des suggestions qui sont fournies par l’évaluation concernant la taille. Le niveau de confiance est basé sur la disponibilité des points de données nécessaires au calcul de l’évaluation.

> [!NOTE]
> Aucun niveau de confiance n’est attribué si vous créez une évaluation basée sur un fichier CSV.

Les niveaux de confiance sont les suivants.

**Disponibilité des points de données** | **Niveau de confiance**
--- | ---
0 %-20 % | 1 étoile
21 %-40 % | 2 étoiles
41 %-60 % | 3 étoiles
61 %-80 % | 4 étoiles
81 %-100 % | 5 étoiles

[En savoir plus](concepts-assessment-calculation.md#confidence-ratings-performance-based) sur les niveaux de confiance.

## <a name="next-steps"></a>Étapes suivantes

- Recherchez des dépendances de serveur avec le [mappage des dépendances](concepts-dependency-visualization.md).
- Configurez un mappage des dépendances [basé sur un agent](how-to-create-group-machine-dependencies.md).
