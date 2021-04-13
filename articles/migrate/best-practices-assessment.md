---
title: Évaluations des meilleures pratiques dans l’outil de découverte et d’évaluation d’Azure Migrate
description: Conseils pour la création d’évaluations avec l’outil de découverte et d’évaluation d’Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: fac488ba1881b6b79139eaf2468237e546737177
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077328"
---
# <a name="best-practices-for-creating-assessments"></a>Meilleures pratiques pour la création d’évaluations

[Azure Migrate](./migrate-services-overview.md) fournit un hub d’outils qui vous permettent de découvrir, d’évaluer et de migrer des applications, une infrastructure et des charges de travail vers Microsoft Azure. Le hub comprend des outils Azure Migrate et des offres d’ISV (fournisseurs de logiciels indépendants) tiers.

Cet article résume les meilleures pratiques pour la création d’évaluations à l’aide de l’outil de découverte et d’évaluation d’Azure Migrate.

Les évaluations que vous créez avec l’outil de découverte et d’évaluation d’Azure Migrate sont une capture instantanée de données à un moment donné. Il existe trois types d’évaluations que vous pouvez créer à l’aide de l’outil de découverte et d’évaluation d’Azure Migrate.

**Type d’évaluation** | **Détails**
--- | --- 
**Microsoft Azure** | Évaluations pour migrer vos serveurs locaux vers des machines virtuelles Azure. <br/><br/> Ce type d’évaluation vous permet d’accéder à vos serveurs locaux dans les environnements [VMware](how-to-set-up-appliance-vmware.md) et [Hyper-V](how-to-set-up-appliance-hyper-v.md), ainsi qu’à vos [serveurs physiques](how-to-set-up-appliance-physical.md) en vue d’une migration vers Azure. [En savoir plus](concepts-assessment-calculation.md)
**Azure SQL** | Évaluations pour migrer vos serveurs SQL Server locaux de votre environnement VMware vers Azure SQL Database ou Azure SQL Managed Instance. [En savoir plus](concepts-azure-sql-assessment-calculation.md)
**Azure VMware Solution (AVS)** | Évaluations pour migrer vos serveurs locaux vers [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> Vous pouvez évaluer vos [machines virtuelles VMware](how-to-set-up-appliance-vmware.md) locales pour la migration vers Azure VMware Solution (AVS) avec ce type d’évaluation. [En savoir plus](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> Si le nombre d’évaluations de machines virtuelles Azure ou AVS est incorrect sur l’outil Découverte et évaluation, cliquez sur le nombre total d’évaluations pour accéder à toutes les évaluations et recalculer les évaluations de la machine virtuelle Azure ou AVS. L’outil Découverte et évaluation affiche alors le nombre correct pour ce type d’évaluation. 

### <a name="sizing-criteria"></a>Critères de dimensionnement
Options de critères de dimensionnement dans les évaluations d’Azure Migrate :

**Critère de dimensionnement** | **Détails** | **Données**
--- | --- | ---
**Basée sur les performances** | Évaluations qui donnent des recommandations en fonction des données de performances collectées | **Évaluation des machines virtuelles Azure** : Les recommandations concernant les machines virtuelles sont fonction des données d’utilisation du processeur et de la mémoire.<br/><br/> Les recommandations concernant le type de disque (HDD/SSD ou disque managé premium) sont fonction de l’IOPS et du débit des disques locaux.<br/><br/>**Évaluation d’Azure SQL** : la configuration d’Azure SQL est basée sur les données de performances d’instances et de bases de données SQL, à savoir : utilisation du processeur, utilisation de la mémoire, IOPS (fichiers de données et fichiers journaux), débit et latence des opérations d’E/S.<br/><br/>**Évaluation d’Azure VMware Solution (AVS)**  : Les suggestions concernant les nœuds AVS sont basées sur les données d’utilisation du processeur et de la mémoire.
**Telle quelle locale** | Évaluations qui n’utilisent pas de données de performances pour formuler des recommandations. | **Évaluation des machines virtuelles Azure** : Les recommandations concernant la taille des machines virtuelles sont basées sur la taille de la machines virtuelle locale.<br/><br> Le type de disque recommandé est basé sur ce que vous sélectionnez dans le paramètre type de stockage pour l’évaluation.<br/><br/> **Évaluation d’Azure VMware Solution (AVS)**  : Les suggestions concernant les nœuds AVS sont basées sur la taille des machines virtuelles locales.

#### <a name="example"></a>Exemple
Par exemple, si vous avez une machine virtuelle locale avec quatre cœurs utilisée à 20 % et une mémoire de 8 Go utilisée à 10 %, les évaluations Azure VM seront les suivantes :

- **Évaluation basée sur les performances** :
    - Identifie les cœurs et la mémoire effectifs sur la base des cœurs (4 x 0,20 = 0,8) et de l’utilisation de la mémoire (8 Go x 0,10 = 0,8).
    - Applique le facteur de confort spécifié dans les propriétés d’évaluation (par exemple, 1.3x) pour obtenir les valeurs à utiliser pour le dimensionnement. 
    - Recommande la taille de machine virtuelle la plus proche dans Azure pouvant prendre en charge environ 1,04 cœur (0,8 x 1,3) et environ 1,04 Go (0,8 x 1,3) de mémoire.

- **Évaluation telle quelle (locale)**  :
    -  Recommande une machine virtuelle à quatre cœurs ; 8 Go de mémoire.


## <a name="best-practices-for-creating-assessments"></a>Meilleures pratiques pour la création d’évaluations

L’appliance Azure Migrate profile en permanence votre environnement local et envoie des métadonnées et des données de performances à Azure. Suivez ces meilleures pratiques pour évaluer les serveurs découverts à l’aide d’une appliance :

- **Créer des évaluations telles quelles** : vous pouvez créer des évaluations telles quelles dès que vos serveurs s’affichent dans le portail Azure Migrate. Vous pouvez créer une évaluation d’Azure SQL avec un critère de dimensionnement « Localement ».
- **Créer une évaluation basée sur les performances** : Après la configuration de la détection, nous vous recommandons d’attendre au moins une journée avant d’exécuter une évaluation basée sur les performances :
    - La collecte des données de performances prend du temps. En attendant au moins une journée, vous pouvez être certain qu’il y a suffisamment de points de données de performances avant d’exécuter l’évaluation.
    - Lorsque vous exécutez des évaluations basées sur les performances, veillez à profiler votre environnement pour la durée de l’évaluation. Par exemple, si vous créez une évaluation avec une durée des performances définie sur une semaine, vous devez attendre au moins une semaine après le démarrage de la détection pour que tous les points de données soient collectés. Sinon, l’évaluation n’aura pas cinq étoiles.
- **Recalculer les évaluations** : Étant donné que les évaluations sont des instantanés à un moment donné, elles ne sont pas automatiquement mises à jour avec les données les plus récentes. Pour mettre à jour une évaluation avec les données les plus récentes, vous devez la recalculer.

Suivez ces meilleures pratiques pour évaluer les serveurs importés dans Azure Migrate via. le fichier .CSV :

- **Créer des évaluations telles quelles** : vous pouvez créer des évaluations telles quelles dès que vos serveurs s’affichent dans le portail Azure Migrate.
- **Créer une évaluation basée sur les performances** : Cela permet d’obtenir une meilleure estimation des coûts, surtout si vous avez surapprovisionné la capacité du serveur localement. Toutefois, la précision de l’évaluation basée sur les performances dépend des données de performances que vous avez spécifiées pour les serveurs. 
- **Recalculer les évaluations** : Étant donné que les évaluations sont des instantanés à un moment donné, elles ne sont pas automatiquement mises à jour avec les données les plus récentes. Pour mettre à jour une évaluation avec les dernières données importées, vous devez la recalculer.
 
### <a name="ftt-sizing-parameters-for-avs-assessments"></a>Paramètres de dimensionnement FTT pour les évaluations AVS

Le moteur de stockage utilisé dans AVS est vSAN. Les stratégies de stockage vSAN définissent les exigences de stockage pour vos machines virtuelles. Ces stratégies garantissent le niveau de service exigé pour vos machines virtuelles, car elles déterminent la façon dont le stockage est alloué à la machine virtuelle. Voici les combinaisons FTT-RAID disponibles : 

**Nombre de pannes tolérées (FTT, Failures to Tolerate)** | **Configuration RAID** | **Nombre minimal d’hôtes requis** | **Considérations sur la taille**
--- | --- | --- | --- 
1 | RAID-1 (Mise en miroir) | 3 | Une machine virtuelle de 100 Go consomme 200 Go.
1 | RAID-5 (Code d’effacement) | 4 | Une machine virtuelle de 100 Go consomme 133,33 Go
2 | RAID-1 (Mise en miroir) | 5 | Une machine virtuelle de 100 Go consomme 300 Go.
2 | RAID-6 (Code d’effacement) | 6 | Une machine virtuelle de 100 Go consomme 150 Go.
3 | RAID-1 (Mise en miroir) | 7 | Une machine virtuelle de 100 Go consomme 400 Go.


## <a name="best-practices-for-confidence-ratings"></a>Meilleures pratiques pour les niveaux de confiance

Quand vous effectuez des évaluations basées sur les performances, une note de confiance des évaluations allant de 1 étoile (le plus faible) à 5 étoiles (le plus élevé) est attribuée. Pour utiliser efficacement les notes de confiance des évaluations :

- Les évaluations de machines virtuelles Azure et d’AVS ont besoin des informations suivantes :
    - les données d’utilisation du processeur et de la mémoire pour chacun des serveurs ;
    - les données d’IOPS et de débit de lecture et d’écriture pour chaque disque attaché au serveur local ;
    - les données d’entrée et de sortie du réseau pour chaque carte réseau attachée à la machine virtuelle.
     
- Les évaluations d’Azure SQL ont besoin des données de performances des instances et des bases de données SQL évaluées, à savoir :
    - les données d’utilisation du processeur et de la mémoire ;
    - les données d’IOPS et de débit de lecture et d’écriture des données et des fichiers journaux ;
    - la latence des opérations d’E/S.

Selon le pourcentage de points de données disponibles pour la durée sélectionnée, la note de confiance d’une évaluation est fournie comme le résume le tableau ci-dessous.

   **Disponibilité des points de données** | **Niveau de confiance**
   --- | ---
   0 %-20 % | 1 étoile
   21 %-40 % | 2 étoiles
   41 %-60 % | 3 étoiles
   61 %-80 % | 4 étoiles
   81 %-100 % | 5 étoiles


## <a name="common-assessment-issues"></a>Problèmes d'évaluation courants

Voici comment résoudre certains problèmes d’environnement courants qui affectent les évaluations.

###  <a name="out-of-sync-assessments"></a>Évaluations non synchronisées

Si vous ajoutez ou supprimez des serveurs d’un groupe après avoir créé une évaluation, l’évaluation que vous avez créée sera marquée comme **non synchronisée**. Réexécutez l’évaluation (**Recalculer**) pour refléter les modifications du groupe.

### <a name="outdated-assessments"></a>Évaluations obsolètes

#### <a name="azure-vm-assessment-and-avs-assessment"></a>Évaluation de machine virtuelle Azure et évaluation d’AVS
En cas de modification des serveurs locaux d’un groupe déjà évalué, l’évaluation est marquée comme **obsolète**. Une évaluation peut être marquée comme « Obsolète » en raison d’une ou de plusieurs modifications dans les propriétés ci-dessous :
- Nombre de cœurs du processeur
- Mémoire allouée
- Type de démarrage ou microprogramme
- Nom du système d'exploitation, version et architecture
- Nombre de disques
- Nombre de cartes réseau
- Modification de la taille du disque (Go alloués)
- Mise à jour de la carte d’interface réseau. Exemple : Modifications des adresses Mac, ajout d’adresses IP, etc.
    
Réexécutez l’évaluation (**Recalculer**) pour refléter les modifications.
    
#### <a name="azure-sql-assessment"></a>Évaluation d’Azure SQL
En cas de modifications d’instances et de bases de données SQL locales d’un groupe déjà évalué, l’évaluation est marquée comme **obsolète**. Une évaluation peut être marquée comme « obsolète » pour une ou plusieurs des raisons ci-dessous :
- L’instance SQL a été ajoutée ou supprimée sur un serveur
- La base de données SQL a été ajoutée ou supprimée sur une instance SQL
- La taille totale de la base de données dans une instance SQL a changé de plus de 20 %
- Changement du nombre de cœurs du processeur
- Changement de la mémoire allouée        
  
    Réexécutez l’évaluation (**Recalculer**) pour refléter les modifications.

### <a name="low-confidence-rating"></a>Faible niveau de confiance

Il peut arriver qu’une évaluation n’ait pas tous les points de données pour diverses raisons :

- Vous n’avez pas profilé votre environnement pour la durée pour laquelle vous créez l’évaluation. Par exemple, si vous créez une évaluation avec une durée des performances définie sur une semaine, vous devez attendre au moins une semaine après le démarrage de la découverte pour que tous les points de données soient recueillis. Si vous ne pouvez pas attendre pendant cette durée, définissez la durée des performances sur une période plus courte et « recalculez » l’évaluation.
 
- L’évaluation ne parvient pas à collecter les données de performances d’une partie ou de la totalité des serveurs pendant la période d’évaluation. Pour obtenir un niveau de confiance élevé, vérifiez les éléments suivants : 
    - Les serveurs sont sous tension pendant toute la durée de l’évaluation.
    - Les connexions sortantes sont autorisées sur le port 443.
    - Pour les serveurs Hyper-V, la mémoire dynamique est activée. 
    - L’état de connexion des agents dans Azure Migrate est « Connecté » (et contrôlez la dernière pulsation).
    - Pour les évaluations d’Azure SQL, l’état de connexion d’Azure Migrate pour toutes les instances SQL est « Connecté » dans le panneau des instances SQL découvertes.

    « Recalculez » l’évaluation pour qu’elle reflète l’évolution récente de la note de confiance.

- Pour les évaluations de machines virtuelles Azure et d’AVS, peu de serveurs ont été créés après le démarrage de la découverte. Par exemple, si vous créez une évaluation de l’historique des performances du mois dernier, mais si la création de quelques serveurs dans l’environnement ne remonte qu’à une semaine. Dans ce cas, les données de performances pour les nouveaux serveurs ne seront pas disponibles pendant toute la durée et le classement de confiance sera faible.

- Pour les évaluations Azure SQL, peu d’instances et de bases de données SQL ont été créées après le démarrage de la découverte. Par exemple, si vous créez une évaluation de l’historique des performances du mois dernier, mais si la création de quelques instances ou bases de données SQL dans l’environnement ne remonte qu’à une semaine. Dans ce cas, les données de performances pour les nouveaux serveurs ne seront pas disponibles pendant toute la durée et le classement de confiance sera faible.

### <a name="migration-tool-guidance-for-avs-assessments"></a>Guide de l’outil de migration pour les évaluations AVS

Dans le rapport de préparation à Azure pour l’évaluation Azure VMware Solution (AVS), vous pouvez voir les outils suggérés suivants : 
- **VMware HCX ou Enterprise** : pour les serveurs VMware, la solution VMware Hybrid Cloud Extension (HCX) est l’outil de migration recommandé pour migrer votre charge de travail locale vers votre cloud privé Azure VMware Solution (AVS). [En savoir plus](../azure-vmware/tutorial-deploy-vmware-hcx.md)
- **Inconnue** : pour des serveurs importés via un fichier CSV, l’outil de migration par défaut est inconnu. Cependant, pour des serveurs dans un environnement VMware, il est recommandé d’utiliser la solution VMware Hybrid Cloud Extension (HCX).


## <a name="next-steps"></a>Étapes suivantes

- [Découvrez](concepts-assessment-calculation.md) le mode de calcul des évaluations.
- [Découvrez](how-to-modify-assessment.md) comment personnaliser une évaluation.
