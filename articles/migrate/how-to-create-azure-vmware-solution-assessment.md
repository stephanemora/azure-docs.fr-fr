---
title: Créer une évaluation AVS avec Azure Migrate Server Assessment | Microsoft Docs
description: Décrit comment créer une évaluation Azure VMware Solution avec l’outil Azure Migrate Server Assessment
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: e386db1ee2042d75a31d4a9de2a5174e904c6b5c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732970"
---
# <a name="create-an-azure-vmware-solution-avs-assessment"></a>Créer une évaluation Azure VMware Solution (AVS)

Cet article explique comment créer une évaluation Azure VMware Solution (AVS) pour les machines virtuelles VMware locales avec Azure Migrate : Server Assessment.

[Azure Migrate](migrate-services-overview.md) vous aide à migrer vers Azure. Azure Migrate offre un hub centralisé pour suivre la découverte, l’évaluation et la migration d’une infrastructure, d’applications et de données locales vers Azure. Le hub fournit des outils Azure pour l’évaluation et la migration, ainsi que des offres d’éditeurs de logiciels indépendant (ISV) tiers.

## <a name="before-you-start"></a>Avant de commencer

- Assurez-vous que vous avez [créé](./create-manage-projects.md) un projet Azure Migrate.
- Si vous avez déjà créé un projet, assurez-vous que vous avez [ajouté](how-to-assess.md) Azure Migrate : Server Assessment.
- Pour créer une évaluation, vous devez configurer une appliance Azure Migrate pour [VMware](how-to-set-up-appliance-vmware.md), qui découvre les machines locales et envoie des métadonnées et des données de performances à Azure Migrate : Server Assessment. [Plus d’informations](migrate-appliance.md)
- Vous pouvez également [importer des métadonnées de serveur](./tutorial-discover-import.md) au format CSV (valeurs séparées par des virgules).


## <a name="azure-vmware-solution-avs-assessment-overview"></a>Vue d’ensemble d’évaluation Azure VMware Solution (AVS)

Vous pouvez créer deux types d’évaluation à l’aide d’Azure Migrate : Server Assessment.

**Type d’évaluation** | **Détails**
--- | --- 
**Microsoft Azure** | Évaluations pour migrer vos serveurs locaux vers des machines virtuelles Azure. <br/><br/> Vous pouvez évaluer vos [machines virtuelles VMware](how-to-set-up-appliance-vmware.md), [machines virtuelles Hyper-V](how-to-set-up-appliance-hyper-v.md) et [serveurs physiques](how-to-set-up-appliance-physical.md) locaux pour la migration vers Azure à l’aide de ce type d’évaluation. [En savoir plus](concepts-assessment-calculation.md)
**Azure VMware Solution (AVS)** | Évaluations pour migrer vos serveurs locaux vers [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> Vous pouvez évaluer vos [machines virtuelles VMware](how-to-set-up-appliance-vmware.md) locales pour la migration vers Azure VMware Solution (AVS) avec ce type d’évaluation. [En savoir plus](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> L’évaluation Azure VMware Solution (AVS) ne peut être créée que pour des machines virtuelles VMware.


Il existe deux types de critères de dimensionnement que vous pouvez utiliser pour créer des évaluations Azure VMware Solution (AVS) :

**Évaluation** | **Détails** | **Données**
--- | --- | ---
**Basée sur les performances** | Évaluations basées sur les données de performances collectées sur des machines virtuelles locales. | **Taille de nœud recommandée**: En fonction des données d’utilisation du processeur et de la mémoire, ainsi que du type de nœud, du type de stockage et du paramètre FTT que vous sélectionnez pour l’évaluation.
**Localement** | Évaluations basées sur le dimensionnement local. | **Taille de nœud recommandée**: En fonction de la taille des machines virtuelles locales, ainsi que du type de nœud, du type de stockage, et du paramètre FTT que vous sélectionnez pour l’évaluation.


## <a name="run-an-azure-vmware-solution-avs-assessment"></a>Exécuter une évaluation Azure VMware Solution (AVS)

1. Dans la page **Serveurs** > **Serveurs Windows et Linux**, cliquez sur **Évaluer et migrer des serveurs**.

   ![Emplacement du bouton Évaluer et migrer des serveurs](./media/tutorial-assess-vmware-azure-vmware-solution/assess.png)

1. Dans **Azure Migrate : Server Assessment**, cliquez sur **Évaluer**.

1. Dans **Évaluer les serveurs** > **Type d’évaluation**, sélectionnez **Azure VMware Solution (AVS)** .

1. Dans **Source de découverte** :

    - Si vous avez découvert des ordinateurs à l’aide de l’appliance, sélectionnez **Machines découvertes par l’appliance Azure Migrate**.
    - Si vous avez découvert des ordinateurs à l’aide d’un fichier CSV importé, sélectionnez **Machines importées**. 
    
1. Cliquez sur **Modifier** pour examiner les propriétés d’évaluation.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-servers.png" alt-text="Page de sélection des paramètres d’évaluation":::
 

1. Dans **Propriétés d’évaluation** > **Propriétés de la cible** :

    - Dans **Emplacement cible**, sélectionnez la région Azure vers laquelle vous souhaitez effectuer la migration.
       - Les recommandations concernant la taille et le coût sont basées sur l’emplacement que vous spécifiez.
   - Le **type de stockage** est défini par défaut sur **vSAN**. Il s’agit du type de stockage par défaut pour un cloud privé AVS.
   - Les **instances réservées** ne sont pas prises en charge pour les nœuds AVS.
1. Dans **Taille de la machine virtuelle** :
    - Le **type de nœud** est défini par défaut sur **AV36**. Azure Migrate recommande le type des nœuds nécessaires à la migration des machines virtuelles vers AVS.
    - Dans **Paramètre FTT, niveau RAID**, sélectionnez une combinaison de valeurs Échecs à tolérer et RAID.  L’option Échecs à tolérer sélectionnée associée à la configuration de disque de machine virtuelle locale détermine le stockage vSAN total nécessaire dans AVS.
    - Dans **Surabonnement pour les processeurs** , spécifiez combien de cœurs virtuels doivent être associés à un même cœur physique dans le nœud AVS. Un surabonnement supérieur à 4:1 peut entraîner une dégradation des performances. Toutefois, il peut être utilisé pour les charges de travail de type serveur web.
    - Dans **Facteur de surengagement de la mémoire**, spécifiez le rapport entre la mémoire et l’engagement sur le cluster. La valeur 1 représente une utilisation de la mémoire de 100 %. Par exemple, 0,5 correspond à 50 % et 2 correspond à une utilisation de 200 % de la mémoire disponible. Vous ne pouvez ajouter que des valeurs comprises entre 0,5 et 10, avec un seul chiffre après la virgule.
    - Dans **Facteur de déduplication et de compression**, spécifiez le facteur de déduplication et de compression anticipé pour vos charges de travail. La valeur réelle peut être obtenue à partir de la configuration du stockage ou du vSAN local, et peut varier en fonction de la charge de travail. La valeur 3 signifie 3x, donc pour 300 Go sur disque, seuls 100 Go de stockage sont utilisés. La valeur 1 signifie qu’il n’y a pas de déduplication ni de compression. Vous ne pouvez ajouter que des valeurs comprises entre 1 et 10, avec un seul chiffre après la virgule.
1. Dans **Taille du nœud** : 
    - Dans **Critère de dimensionnement**, indiquez si vous souhaitez baser l’évaluation sur les métadonnées statiques ou sur les données de performances. Si vous utilisez des données de performances :
        - Dans **Historique des performances**, indiquez la période de données sur laquelle vous souhaitez baser l’évaluation.
        - Dans **Utilisation en centile**, spécifiez la valeur de centile que vous souhaitez utiliser pour l’échantillon de performances. 
    - Dans **Facteur de confort**, indiquez la mémoire tampon que vous souhaitez utiliser lors de l’évaluation. Celle-ci prend en compte les problèmes, tels que l’utilisation saisonnière, l’historique des performances de courte durée et l’augmentation probable de l’utilisation. Par exemple, si vous utilisez un facteur de confort de 2 :
    
        **Composant** | **Utilisation efficace** | **Ajouter un facteur de confort (2.0)**
        --- | --- | ---
        Cœurs | 2  | 4
        Mémoire | 8 Go | 16 Go  

1. Dans **Tarifs** :
    - Dans **Offre**, l’[offre Azure](https://azure.microsoft.com/support/legal/offer-details/) à laquelle vous êtes abonné affiche les estimations de coûts Server Assessment.
    - Dans **Devise**, sélectionnez la devise de facturation de votre compte.
    - Dans **Remise (%)** , ajoutez les remises applicables à votre abonnement que vous recevez en plus de l’offre Azure. Le paramètre par défaut est 0 %.

1. Si vous avez apporté des modifications, cliquez sur **Enregistrer**.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-view-all.png" alt-text="Propriétés de l’évaluation":::

1. Dans **Évaluer les serveurs**, cliquez sur **Suivant**.

1. Dans **Sélectionner les machines à évaluer** > **Nom de l’évaluation**, spécifiez un nom pour l’évaluation. 
 
1. Dans **Sélectionner ou créer un groupe**, sélectionnez **Créer**, puis spécifiez un nom de groupe. 
    
    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-group.png" alt-text="Ajouter des machines virtuelles à un groupe":::
 
1. Sélectionnez l’appliance, puis sélectionnez les machines virtuelles que vous souhaitez ajouter au groupe. Cliquez ensuite sur **Suivant**.

1. Dans **Vérifier + créer l’évaluation**, passez en revue les détails de l’évaluation, puis cliquez sur **Créer une évaluation** pour créer le groupe et lancer l’évaluation.

    > [!NOTE]
    > Pour les évaluations basées sur les performances, nous vous recommandons d’attendre au moins un jour après le démarrage de la découverte pour créer l’évaluation. Cela permet de collecter des données de performances avec un plus haut niveau de confiance. Dans l’idéal, une fois que vous avez démarré la découverte, vous devriez attendre la période de performances que vous avez spécifiée (jour/semaine/mois) afin d’obtenir un haut niveau de confiance.


## <a name="review-an-azure-vmware-solution-avs-assessment"></a>Vérifier une évaluation Azure VMware Solution (AVS)

Une évaluation Azure VMware Solution (AVS) indique :

- **Préparation pour Azure VMware Solution (AVS)** : Si les machines virtuelles locales sont adaptées à la migration vers Azure VMware Solution (AVS).
- **Nombre de nœuds AVS**: Estimation du nombre de nœuds AVS requis pour exécuter les machines virtuelles.
- **Utilisation des nœuds AVS**: Utilisation estimée du processeur, de la mémoire et du stockage sur tous les nœuds.
    - L’utilisation comprend la factorisation initiale dans le cadre des opérations de gestion de cluster suivantes, par exemple vCenter Server, NSX Manager (grand), NSX Edge, HCX Manager (si HCX est déployé) et l’appliance IX, qui consomment environ 44 vCPU (11 processeurs), 75 Go de RAM et 722 Go de stockage avant compression et déduplication.
- **Estimation des coûts mensuels** : Les coûts mensuels estimés pour tous les nœuds Azure VMware Solution (AVS) exécutant les machines virtuelles locales.


### <a name="view-an-assessment"></a>Voir une évaluation

1. Dans **Objectifs de migration** >  **Serveurs**, cliquez sur **Évaluations** dans **Azure Migrate : Évaluation de serveur**.

2. Dans **Évaluations**, cliquez sur une évaluation pour l’ouvrir.

    :::image type="content" source="./media/how-to-create-avs-assessment/avs-assessment-summary.png" alt-text="Récapitulatif de l’évaluation AVS":::

### <a name="review-azure-vmware-solution-avs-readiness"></a>Vérifier la préparation pour Azure VMware Solution (AVS)

1. Dans **Préparé pour Azure**, vérifiez si les machines virtuelles sont prêtes pour la migration vers AVS.

2. Passez en revue l’état des machines virtuelles :
    - **Disponible pour Azure VMware Solution** : la machine peut être migrée telle quelle vers Azure (Azure VMware Solution) sans aucune modification. Elle démarrera dans AVS avec une prise en charge complète d’AVS.
    - **Disponible sous conditions** : La machine virtuelle peut présenter des problèmes de compatibilité avec la version actuelle de vSphere, ainsi qu’exiger des outils VMware et ou d’autres paramètres avant que toutes les fonctionnalités de la machine virtuelle puissent être obtenues dans AVS.
    - **Non disponible pour AVS** : La machine virtuelle ne démarrera pas dans AVS. Par exemple, si la machine virtuelle VMware locale est associée à un appareil externe tel qu’un CD-ROM, l’opération VMotion échouera (si vous utilisez VMware VMotion).
    - **État de la préparation inconnu** : Azure Migrate n’a pas pu déterminer l’état de préparation de la machine, car les métadonnées collectées dans l’environnement local sont insuffisantes.

3. Vérifiez l’outil suggéré :
    - **VMware HCX ou Enterprise** : pour les machines VMware, la solution VMware Hybrid Cloud Extension (HCX) est l’outil de migration recommandé pour migrer votre charge de travail locale vers votre cloud privé Azure VMware Solution (AVS). [En savoir plus](../azure-vmware/tutorial-deploy-vmware-hcx.md)
    - **Inconnue** : Pour les machines importées via un fichier CSV, l’outil de migration par défaut est inconnu. Pour les machines VMware, il est cependant suggéré d’utiliser la solution VMware Hybrid Cloud Extension (HCX). 

4. Cliquez sur un état **Préparé pour Azure VMware Solution**. Vous pouvez voir les informations relatives à l’état de préparation des machines virtuelles et explorer ces dernières en détail, notamment en ce qui concerne les valeurs pour le calcul, le stockage et le réseau.

### <a name="review-cost-details"></a>Passer en revue les détails des coûts

Cette vue montre une estimation des coûts liés à l’exécution des machines virtuelles dans Azure VMware Solution (AVS).

1. Vérifiez les coûts totaux mensuels. Les coûts sont agrégés pour toutes les machines virtuelles du groupe évalué. 

    - Les estimations de coût se basent sur le nombre total de nœuds AVS requis en tenant compte des besoins en ressources de toutes les machines virtuelles.
    - Étant donné que la tarification pour Azure VMware Solution (AVS) se fait par nœud, le coût total ne comprend pas de coût de calcul ni de distribution des coûts de stockage.
    - L’estimation des coûts porte sur l’exécution des machines virtuelles locales dans AVS. L’évaluation AVS ne prend pas en compte les coûts PaaS ni SaaS.
    
2. Vous pouvez passer en revue les estimations des coûts de stockage mensuels. Cette vue montre les coûts de stockage agrégés pour le groupe évalué, répartis selon différents types de disque de stockage.

3. Vous pouvez explorer les détails de machines virtuelles spécifiques.


### <a name="review-confidence-rating"></a>Examiner le niveau de confiance

Quand vous effectuez des évaluations basées sur les performances, un niveau de confiance est affecté à l’évaluation.

![Niveau de confiance](./media/how-to-create-assessment/confidence-rating.png)

- Un niveau allant de 1 étoile (le plus faible) à 5 étoiles (le plus élevé) est attribué.
- Le niveau de confiance vous aide à estimer la fiabilité des suggestions de taille fournies par l’évaluation.
- Le niveau de confiance est basé sur la disponibilité des points de données nécessaires pour calculer l’évaluation.
- Pour le dimensionnement basé sur les performances, les évaluations AVS dans Évaluation du Serveur nécessitent les données d’utilisation du processeur et de la mémoire de la machine virtuelle. Les données de performances suivantes sont collectées, mais ne sont pas utilisées dans les suggestions de dimensionnement pour les évaluations AVS :
  - Les données IOPS et de débit du disque pour chaque disque attaché à la machine virtuelle.
  - Les E/S réseau pour gérer le dimensionnement basé sur les performances pour chaque carte réseau attachée à une machine virtuelle.

Les niveaux de confiance d’une évaluation sont les suivants.

**Disponibilité des points de données** | **Niveau de confiance**
--- | ---
0 %-20 % | 1 étoile
21 %-40 % | 2 étoiles
41 %-60 % | 3 étoiles
61 %-80 % | 4 étoiles
81 %-100 % | 5 étoiles

[En savoir plus](concepts-azure-vmware-solution-assessment-calculation.md) sur les données performances 


## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment utiliser le [mappage de dépendances](how-to-create-group-machine-dependencies.md) pour créer des groupes à haute fiabilité.
- [En savoir plus](concepts-azure-vmware-solution-assessment-calculation.md) sur le mode de calcul des évaluations AVS.