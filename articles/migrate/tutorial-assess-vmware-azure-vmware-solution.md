---
title: Évaluer les serveurs VMware en vue de les migrer vers Azure VMware Solution (AVS) avec Azure Migrate
description: Découvrez comment évaluer les serveurs dans un environnement VMware en vue de les migrer vers AVS avec Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: MVC
ms.openlocfilehash: 31bf3909012231996bd340cfa4d388f0fe20a4f5
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104782125"
---
# <a name="tutorial-assess-vmware-servers-for-migration-to-avs"></a>Tutoriel : Évaluer les serveurs VMware pour les migrer vers AVS

Dans le cadre de votre migration vers Azure, vous devez évaluer vos charges de travail locales afin de déterminer la préparation nécessaire, d’identifier les risques et d’estimer les coûts ainsi que le niveau de complexité de la tâche.

Cet article explique comment évaluer les machines virtuelles/serveurs VMware découverts en vue de les migrer vers Azure VMware Solution (AVS) avec Azure Migrate. AVS est un service managé qui vous permet d’exécuter la plateforme VMware dans Azure.

Dans ce tutoriel, vous allez apprendre à :
> [!div class="checklist"]
- Exécuter une évaluation basée sur les métadonnées et les informations de configuration du serveur
- Exécuter une évaluation basée sur les données de performances

> [!NOTE]
> Les tutoriels indiquent le moyen le plus rapide de tester un scénario, et ils utilisent les options par défaut lorsque cela est possible. 

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) avant de commencer.



## <a name="prerequisites"></a>Prérequis

Avant de suivre ce tutoriel pour évaluer vos serveurs en vue de leur migration vers AVS, vérifiez que vous avez découvert les serveurs que vous souhaitez évaluer :

- Pour découvrir des serveurs avec l’appliance Azure Migrate, [suivez ce tutoriel](tutorial-discover-vmware.md). 
- Pour découvrir des serveurs avec un fichier CSV importé, [suivez ce tutoriel](tutorial-discover-import.md).


## <a name="decide-which-assessment-to-run"></a>Décider de l’évaluation à exécuter


Déterminez si vous souhaitez exécuter une évaluation à l’aide de critères de dimensionnement basés sur les données de configuration du serveur, sur les métadonnées collectées telles quelles localement ou sur les données de performances dynamiques.

**Évaluation** | **Détails** | **Recommandation**
--- | --- | ---
**Telle quelle locale** | Pour une évaluation basée sur les données de configuration ou les métadonnées du serveur.  | La taille de nœud recommandée dans AVS est basée sur la taille de la machine virtuelle/du serveur local ainsi que sur les paramètres que vous spécifiez dans l’évaluation pour le type de nœud, le type de stockage et le paramètre Échecs à tolérer.
**Basée sur les performances** | Pour une évaluation basée sur les données de performances dynamiques collectées. | La taille de nœud recommandée dans AVS est basée sur les données d’utilisation du processeur et de la mémoire, ainsi que sur les paramètres que vous spécifiez dans l’évaluation pour le type de nœud, le type de stockage et le paramètre Échecs à tolérer.

> [!NOTE]
> L’évaluation Azure VMware Solution (AVS) ne peut être créée que pour des serveurs/machines virtuelles VMware.

## <a name="run-an-assessment"></a>Exécuter une évaluation

Exécutez une évaluation comme suit :

1.  Sur la page **Vue d’ensemble** > **Windows, Linux et SQL Server**, cliquez sur **Évaluer et migrer des serveurs**.
    :::image type="content" source="./media/tutorial-assess-sql/assess-migrate.png" alt-text="Page Vue d’ensemble d’Azure Migrate":::

1. Dans **Azure Migrate : découverte et évaluation**, cliquez sur **Évaluer**.

   ![Emplacement du bouton Évaluer](./media/tutorial-assess-vmware-azure-vmware-solution/assess.png)

1. Dans **Évaluer les serveurs** > **Type d’évaluation**, sélectionnez **Azure VMware Solution (AVS)** .

1. Dans **Source de découverte** :

    - Si vous avez découvert des serveurs avec l’appliance, sélectionnez **Serveurs découverts par l’appliance Azure Migrate**.
    - Si vous avez découvert des serveurs avec un fichier CSV importé, sélectionnez **Serveurs importés**. 
    
1. Cliquez sur **Modifier** pour examiner les propriétés d’évaluation.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-servers.png" alt-text="Page de sélection des paramètres d’évaluation":::
 

1. Dans **Propriétés d’évaluation** > **Propriétés de la cible** :

    - Dans **Emplacement cible**, sélectionnez la région Azure vers laquelle vous souhaitez effectuer la migration.
       - Les recommandations concernant la taille et le coût sont basées sur l’emplacement que vous spécifiez.
   - Le **type de stockage** est défini par défaut sur **vSAN**. Il s’agit du type de stockage par défaut pour un cloud privé AVS.
   - Les **instances réservées** ne sont pas prises en charge pour les nœuds AVS.
1. Dans **Taille de la machine virtuelle** :
    - Le **type de nœud** est défini par défaut sur **AV36**. Azure Migrate recommande le type des nœuds nécessaires à la migration des serveurs vers AVS.
    - Dans **Paramètre FTT, niveau RAID**, sélectionnez une combinaison de valeurs Échecs à tolérer et RAID.  L’option Échecs à tolérer sélectionnée associée à la configuration de disque de serveur local détermine le stockage vSAN total nécessaire dans AVS.
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
    - Dans **Offre**, l’[offre Azure](https://azure.microsoft.com/support/legal/offer-details/) que vous avez inscrite s’affiche. L’évaluation estime le coût de cette offre.
    - Dans **Devise**, sélectionnez la devise de facturation de votre compte.
    - Dans **Remise (%)** , ajoutez les remises applicables à votre abonnement que vous recevez en plus de l’offre Azure. Le paramètre par défaut est 0 %.

1. Si vous avez apporté des modifications, cliquez sur **Enregistrer**.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-view-all.png" alt-text="Propriétés de l’évaluation":::

1. Dans **Évaluer les serveurs**, cliquez sur **Suivant**.

1. Dans **Sélectionner les serveurs à évaluer** > **Nom de l’évaluation**, spécifiez un nom pour l’évaluation. 
 
1. Dans **Sélectionner ou créer un groupe**, sélectionnez **Créer**, puis spécifiez un nom de groupe. 
    
    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-group.png" alt-text="Ajouter des serveurs à un groupe":::
 
1. Sélectionnez l’appliance, puis sélectionnez les serveurs que vous souhaitez ajouter au groupe. Cliquez ensuite sur **Suivant**.

1. Dans **Vérifier + créer l’évaluation**, passez en revue les détails de l’évaluation, puis cliquez sur **Créer une évaluation** pour créer le groupe et lancer l’évaluation.

    > [!NOTE]
    > Pour les évaluations basées sur les performances, nous vous recommandons d’attendre au moins un jour après le démarrage de la découverte pour créer l’évaluation. Cela permet de collecter des données de performances avec un plus haut niveau de confiance. Dans l’idéal, une fois que vous avez démarré la découverte, vous devriez attendre la période de performances que vous avez spécifiée (jour/semaine/mois) afin d’obtenir un haut niveau de confiance.

## <a name="review-an-assessment"></a>Réviser une évaluation

Une évaluation AVS décrit les éléments suivants :

- Préparation AVS : Si les serveurs locaux sont adaptés à la migration vers Azure VMware Solution (AVS).
- Nombre de nœuds AVS : Estimation du nombre de nœuds AVS requis pour exécuter les serveurs.
- Utilisation des nœuds AVS: Utilisation estimée du processeur, de la mémoire et du stockage sur tous les nœuds.
    - L’utilisation comprend la factorisation initiale dans le cadre des opérations de gestion de cluster suivantes, par exemple vCenter Server, NSX Manager (grand), NSX Edge,HCX Manager (si HCX est déployé) et l’appliance IX, qui consomment environ 44 processeurs virtuels (11 processeurs), 75 Go de RAM et 722 Go de stockage avant compression et déduplication. 
- Estimation des coûts mensuels : Les coûts mensuels estimés pour tous les nœuds Azure VMware Solution (AVS) exécutant les serveurs locaux.

## <a name="view-an-assessment"></a>Voir une évaluation

Pour voir une évaluation :

1. **Windows, Linux et SQL Server** > **Azure Migrate : découverte et évaluation**, puis cliquez sur le numéro à côté de ** Azure VMware Solution**.

1. Dans **Évaluations**, sélectionnez une évaluation pour l’ouvrir. Voici un exemple (estimations et coûts, à titre d’exemple uniquement) : 

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-assessment-summary.png" alt-text="Récapitulatif de l’évaluation AVS":::

1. Passez en revue le récapitulatif de l’évaluation. Vous pouvez également modifier les propriétés de l’évaluation ou recalculer celle-ci.
 

### <a name="review-readiness"></a>Vérifier la préparation nécessaire

1. Cliquez sur **Préparé pour Azure**.
2. Dans **Préparé pour Azure**, vérifiez l’état de la préparation.

    - **Disponible pour Azure VMware Solution** : Le serveur peut être migré tel quel vers Azure AVS sans aucune modification. Il démarrera dans AVS avec une prise en charge complète d’AVS.
    - **Disponible sous conditions** : Le serveur peut présenter des problèmes de compatibilité avec la version actuelle de vSphere. Il peut s’avérer nécessaire d’installer les outils VMware ou de configurer d’autres paramètres, afin de disposer de toutes les fonctionnalités AVS.
    - **Non disponible pour AVS** : La machine virtuelle ne démarre pas dans AVS. Par exemple, si un serveur VMware local est associé à un appareil externe (comme un CD-ROM) et que vous utilisez VMware VMotion, l’opération VMotion échouera.
 - **État de la préparation inconnu** : Azure Migrate n’a pas pu déterminer l’état de préparation du serveur, car les métadonnées collectées dans l’environnement local sont insuffisantes.

3. Examinez l’outil suggéré.

    - VMware HCX ou Enterprise : pour les serveurs VMware, la solution VMware Hybrid Cloud Extension (HCX) est l’outil de migration recommandé pour migrer votre charge de travail locale vers votre cloud privé Azure VMware Solution (AVS). En savoir plus.
    - Inconnue : Pour les serveurs importés via un fichier CSV, l’outil de migration par défaut est inconnu. Pour les serveurs VMware, il est cependant suggéré d’utiliser la solution VMware Hybrid Cloud Extension (HCX).
4. Cliquez sur un état Préparé pour Azure VMware Solution. Vous pouvez voir les informations relatives à l’état de préparation des serveurs et explorer ces derniers en détail, notamment en ce qui concerne les valeurs pour le calcul, le stockage et le réseau.

### <a name="review-cost-estimates"></a>Réviser les estimations de coûts

Ce résumé d’évaluation montre une estimation des coûts de calcul et de stockage liés à l’exécution des serveurs dans Azure. 

1. Vérifiez les coûts totaux mensuels. Les coûts sont agrégés pour tous les serveurs du groupe évalué.

    - Les estimations de coût se basent sur le nombre total de nœuds AVS nécessaires en tenant compte des besoins en ressources de tous les serveurs.
    - Étant donné que les tarifs AVS sont donnés pour un nœud, le coût total n’indique pas la répartition entre les coûts de calcul et les coûts de stockage.
    - L’estimation des coûts porte sur l’exécution des serveurs locaux dans AVS. L’évaluation AVS ne prend pas en compte les coûts PaaS ni SaaS.

2. Passez en revue les estimations des coûts de stockage mensuels. Cette vue montre les coûts de stockage agrégés pour le groupe évalué, répartis selon les différents types de disque de stockage. 
3. Vous pouvez consulter le détail des coûts de serveurs spécifiques.

### <a name="review-confidence-rating"></a>Examiner le niveau de confiance

Server Assessment attribue un niveau de confiance aux évaluations basées sur les performances. Ce niveau est exprimé en étoiles, 1 étoile étant le plus bas niveau et 5 étoiles le plus haut.

![Niveau de confiance](./media/tutorial-assess-vmware-azure-vmware-solution/confidence-rating.png)

Le niveau de confiance vous permet d’estimer la fiabilité des suggestions qui sont fournies par l’évaluation concernant la taille. Le niveau de confiance est basé sur la disponibilité des points de données nécessaires pour calculer l’évaluation.

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
- Configurez un mappage des dépendances [sans agent](how-to-create-group-machine-dependencies-agentless.md) ou [basé sur un agent](how-to-create-group-machine-dependencies.md).
