---
title: Questions sur la découverte, l’évaluation et l’analyse des dépendances dans Azure Migrate
description: Obtenez des réponses aux questions courantes sur la découverte, l’évaluation et l’analyse des dépendances dans Azure Migrate.
author: rashijoshi
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/09/2020
ms.openlocfilehash: ff7bd9145b64f82ca514897ebb67526bc28de867
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108143572"
---
# <a name="discovery-assessment-and-dependency-analysis---common-questions"></a>Découverte, évaluation et analyse des dépendances - Questions courantes

Cet article donne des réponses aux questions courantes sur la découverte, l’évaluation et l’analyse des dépendances dans Azure Migrate. Si vous avez d’autres questions, consultez les ressources suivantes :

- [Questions générales](resources-faq.md) sur Azure Migrate.
- Questions sur l’[appliance Azure Migrate](common-questions-appliance.md)
- Questions sur la [migration de serveur](common-questions-server-migration.md)
- Obtenez des réponses à vos questions sur le [forum Azure Migrate](https://aka.ms/AzureMigrateForum)


## <a name="what-geographies-are-supported-for-discovery-and-assessment-with-azure-migrate"></a>Quelles sont les zones géographiques prises en charge pour la détection et l’évaluation avec Azure Migrate ?

Passez en revue les zones géographiques prises en charge pour les clouds [publics](migrate-support-matrix.md#supported-geographies-public-cloud) et [gouvernementaux](migrate-support-matrix.md#supported-geographies-azure-government).


## <a name="how-many-servers-can-i-discover-with-an-appliance"></a>Combien de serveurs puis-je découvrir avec une appliance ?

Vous pouvez découvrir jusqu’à 10 000 serveurs dans un environnement VMware, jusqu’à 5 000 serveurs dans un environnement Hyper-V et jusqu’à 1 000 serveurs physiques en utilisant une seule appliance. Si vous avez davantage de serveurs, découvrez comment [mettre à l’échelle une évaluation Hyper-V](scale-hyper-v-assessment.md), [mettre à l’échelle une évaluation VMware](scale-vmware-assessment.md) ou [mettre à l’échelle une évaluation de serveur physique](scale-physical-assessment.md).

## <a name="how-do-i-choose-the-assessment-type"></a>Comment choisir le type d’évaluation ?

- Utilisez les **évaluations de machine virtuelle Azure** quand vous voulez évaluer les serveurs de votre environnement [VMware](how-to-set-up-appliance-vmware.md) et [Hyper-V](how-to-set-up-appliance-hyper-v.md) local, ainsi que les [serveurs physiques](how-to-set-up-appliance-physical.md) en vue de leur migration vers des machines virtuelles Azure. [En savoir plus](concepts-assessment-calculation.md)

- Utilisez le type d’évaluation **Azure SQL** lorsque vous souhaitez évaluer votre SQL Server local à partir de votre environnement VMware pour la migration vers Azure SQL Database ou Azure SQL Managed instance. [En savoir plus](concepts-assessment-calculation.md)

- Utilisez les évaluations **Azure VMware Solution (AVS)** quand vous voulez évaluer vos [machines virtuelles VMware](how-to-set-up-appliance-vmware.md) locales pour la migration vers [Azure VMware Solution (AVS)](../azure-vmware/introduction.md) avec ce type d’évaluation. [En savoir plus](concepts-azure-vmware-solution-assessment-calculation.md)

- Vous pouvez utiliser un groupe commun avec seulement des machines VMware pour effectuer les deux types d’évaluations. Notez que si vous effectuez des évaluations AVS dans Azure Migrate pour la première fois, il est recommandé de créer un groupe de machines VMware.
 

## <a name="why-is-performance-data-missing-for-someall-servers-in-my-azure-vm-andor-avs-assessment-report"></a>Pourquoi des données de performances sont-elles manquantes pour certains ou la totalité des serveurs dans ma machine virtuelle Azure et/ou mon rapport d’évaluation AVS ?

Pour l’évaluation « En fonction des performances », le rapport d’évaluation signale « PercentageOfCoresUtilizedMissing » ou « PercentageOfMemoryUtilizedMissing » quand l’appliance Azure Migrate ne peut pas collecter les données de performances pour les serveurs locaux. Vérifiez :

- Si les serveurs sont sous tension pendant que vous créez l’évaluation.
- Si seuls les compteurs de mémoire manquent et si vous essayez d’évaluer des serveurs dans un environnement Hyper-V. Dans ce scénario, activez la mémoire dynamique sur les serveurs et recalculez l’évaluation pour tenir compte des dernières modifications. L’appliance peut collecter des valeurs d’utilisation de la mémoire pour des serveurs dans un environnement Hyper-V uniquement lorsque la mémoire dynamique est activée sur le serveur.

- Si tous les compteurs de performances sont manquants, assurez-vous que les connexions sortantes sur le port 443 (HTTPS) sont autorisées.

    > [!Note]
    > Si l’un des compteurs de performances est manquant, l’outil d’évaluation de serveur Azure Migrate se base à la place sur les cœurs ou la mémoire alloués localement pour recommander une taille de machine virtuelle appropriée.


## <a name="why-is-performance-data-missing-for-someall-sql-instancesdatabases-in-my-azure-sql-assessment"></a>Pourquoi des données de performances sont-elles manquantes pour certaines ou la totalité des instances/bases de données SQL dans mon évaluation Azure SQL ?

Pour vous assurer que les données de performances soient collectées, vérifiez les éléments suivants :

- Les serveurs SQL Server sont sous tension pendant que vous créez l’évaluation.
- L’état de connexion de l’agent SQL dans Azure Migrate est « Connecté » (contrôlez la dernière pulsation). 
- L’état de connexion d’Azure Migrate pour toutes les instances SQL est « Connecté » dans le panneau des instances SQL découvertes.
- Si tous les compteurs de performances sont manquants, assurez-vous que les connexions sortantes sur le port 443 (HTTPS) sont autorisées

Si des compteurs de performances sont manquant, l’évaluation SQL Azure recommande la plus petite configuration d’Azure SQL pour cette instance/cette base de données.

## <a name="why-is-the-confidence-rating-of-my-assessment-low"></a>Pourquoi la note de confiance de mon évaluation est faible ?

La note de confiance pour les évaluations « En fonction des performances » dépend du pourcentage de [points de données disponibles](./concepts-assessment-calculation.md#ratings) nécessaires pour calculer l’évaluation. Voici les raisons pour lesquelles une évaluation pourrait obtenir une note de confiance faible :

- Vous n’avez pas profilé votre environnement pour la durée pour laquelle vous créez l’évaluation. Par exemple, si vous créez une évaluation avec une durée des performances définie sur une semaine, vous devez attendre au moins une semaine après le démarrage de la découverte pour que tous les points de données soient recueillis. Si vous ne pouvez pas attendre pendant cette durée, définissez la durée des performances sur une période plus courte, puis **Recalculez** l’évaluation.
 
- L’évaluation ne parvient pas à collecter les données de performances d’une partie ou de la totalité des serveurs pendant la période d’évaluation. Pour obtenir un niveau de confiance élevé, vérifiez les éléments suivants : 
    - Les serveurs sont sous tension pendant toute la durée de l’évaluation.
    - Les connexions sortantes sont autorisées sur le port 443.
    - Pour les serveurs Hyper-V, la mémoire dynamique est activée. 
    - L’état de connexion des agents dans Azure Migrate est « Connecté » (et contrôlez la dernière pulsation).
    - Pour les évaluations d’Azure SQL, l’état de connexion d’Azure Migrate pour toutes les instances SQL est « Connecté » dans le panneau des instances SQL découvertes.

    **Recalculez** l’évaluation pour qu’elle reflète les dernières modifications du niveau de confiance.

- Pour les évaluations de machines virtuelles Azure et d’AVS, peu de serveurs ont été créés après le démarrage de la découverte. Par exemple, si vous créez une évaluation de l’historique des performances du mois dernier, mais si la création de quelques serveurs dans l’environnement ne remonte qu’à une semaine. Dans ce cas, les données de performances pour les nouveaux serveurs ne seront pas disponibles pendant toute la durée et le classement de confiance sera faible. [En savoir plus](./concepts-assessment-calculation.md#confidence-ratings-performance-based)

- Pour les évaluations Azure SQL, peu d’instances et de bases de données SQL ont été créées après le démarrage de la découverte. Par exemple, si vous créez une évaluation de l’historique des performances du mois dernier, mais si la création de quelques instances ou bases de données SQL dans l’environnement ne remonte qu’à une semaine. Dans ce cas, les données de performances pour les nouveaux serveurs ne seront pas disponibles pendant toute la durée et le classement de confiance sera faible. [En savoir plus](./concepts-azure-sql-assessment-calculation.md#confidence-ratings)

## <a name="-the-number-of-azure-vm-or-avs-assessments-on-the-discovery-and-assessment-tool-are-incorrect"></a>> Le nombre d’évaluations de machines virtuelles Azure ou AVS sur l’outil de découverte et d’évaluation est incorrect
 Pour résoudre ce problème, cliquez sur le nombre total d’évaluations pour accéder à toutes les évaluations et recalculer l’évaluation de la machine virtuelle Azure ou AVS. L’outil de découverte et d’évaluation affiche alors le nombre correct pour ce type d’évaluation.


## <a name="i-want-to-try-out-the-new-azure-sql-assessment"></a>Je souhaite essayer la nouvelle fonctionnalité d’évaluation Azure SQL
La découverte et l’évaluation d’instances et de bases de données SQL Server s’exécutant dans votre environnement VMware sont actuellement en préversion. Pour bien démarrer, suivez [ce tutoriel](tutorial-discover-vmware.md). Si vous voulez tester cette fonctionnalité dans un projet existant, vérifiez que vous avez rempli les [prérequis](how-to-discover-sql-existing-project.md) indiqués dans cet article.

## <a name="i-cant-see-some-servers-when-i-am-creating-an-azure-sql-assessment"></a>Je ne peux pas voir certains serveurs quand je crée une évaluation Azure SQL

- L’évaluation Azure SQL ne peut être effectuée que sur des serveurs qui s’exécutent là où des instances SQL ont été découvertes. Si vous ne voyez pas les serveurs et les instances SQL que vous souhaitez évaluer, patientez pendant un certain temps pour que la découverte se termine, puis créez l’évaluation. 
- Si vous n’êtes pas en mesure de voir un groupe créé précédemment lors de la création de l’évaluation, supprimez du groupe n’importe quel serveur non-VMware ou sans instance SQL.
- Si vous effectuez pour la première fois des évaluations Azure SQL dans Azure Migrate, il est recommandé de créer un nouveau groupe de serveurs.

## <a name="i-want-to-understand-how-was-the-readiness-for-my-instance-computed"></a>Je souhaite comprendre comment l’état de préparation de mon instance a été calculé.
La préparation de vos instances SQL a été calculée après vérification de la compatibilité des fonctionnalités avec le type de déploiement Azure SQL ciblé (Azure SQL Database ou Azure SQL Managed Instance). [En savoir plus](./concepts-azure-sql-assessment-calculation.md#calculate-readiness)

## <a name="why-is-the-readiness-for-all-my-sql-instances-marked-as-unknown"></a>Pourquoi l’état de préparation de mes instances SQL est-il marquée comme Inconnu ?
Si votre découverte a été lancée récemment et est toujours en cours, vous pouvez voir que l’état de préparation de certaines ou de la totalité des instances SQL est inconnue. Nous vous recommandons d’attendre un certain temps pour que l’appliance profile l’environnement, puis recalcule l’évaluation.
La découverte SQL est effectuée une fois toutes les 24 heures et vous devrez peut-être attendre un jour pour que les dernières modifications de configuration s’y reflètent. 

## <a name="why-is-the-readiness-for-some-of-my-sql-instances-marked-as-unknown"></a>Pourquoi l’état de préparation d’une partie de mes instances SQL est-il marqué comme Inconnue ?
Cela peut se produire dans les cas suivants : 
- La découverte est encore en cours. Nous vous recommandons d’attendre un certain temps pour que l’appliance profile l’environnement, puis recalcule l’évaluation.
- Vous devez résoudre certains problèmes de découverte dans le panneau Erreurs et notifications.

La découverte SQL est effectuée une fois toutes les 24 heures et vous devrez peut-être attendre un jour pour que les dernières modifications de configuration s’y reflètent.

## <a name="my-assessment-is-in-outdated-state"></a>Mon évaluation est à l’état Obsolète

### <a name="azure-vmavs-assessment"></a>Évaluation de la machine virtuelle Azure ou de l’AVS
En cas de modification locale des serveurs appartenant à un groupe qui a été évalué, l’évaluation est marquée comme obsolète. Une évaluation peut être marquée comme « Obsolète » en raison d’une ou de plusieurs modifications dans les propriétés ci-dessous :
- Nombre de cœurs du processeur
- Mémoire allouée
- Type de démarrage ou microprogramme
- Nom du système d'exploitation, version et architecture
- Nombre de disques
- Nombre de cartes réseau
- Modification de la taille du disque (Go alloués)
- Mise à jour de la carte d’interface réseau. Exemple : Modifications des adresses Mac, ajout d’adresses IP, etc.

**Recalculez** l’évaluation pour qu’elle reflète les dernières modifications apportées.

### <a name="azure-sql-assessment"></a>Évaluation d’Azure SQL
En cas de modifications des instances et bases de données SQL locales figurant dans un groupe qui a été évalué, l’évaluation est marquée comme **obsolète** :
- L’instance SQL a été ajoutée ou supprimée sur un serveur
- La base de données SQL a été ajoutée ou supprimée sur une instance SQL
- La taille totale de la base de données dans une instance SQL a changé de plus de 20 %
- Le nombre de cœurs de processeurs et/ou la quantité de mémoire allouée ont été modifiés

**Recalculez** l’évaluation pour qu’elle reflète les dernières modifications apportées.

## <a name="why-was-i-recommended-a-particular-target-deployment-type"></a>Pourquoi un type de déploiement cible particulier m’a-t-il été recommandé ?
Azure Migrate recommande un type de déploiement Azure SQL spécifique qui est compatible avec votre instance SQL. La migration vers une cible recommandée par Microsoft réduit votre effort de migration global. Cette configuration Azure SQL (SKU) a été recommandée après la prise en compte des caractéristiques de performances de votre instance SQL et des bases de données qu’elle gère. Si plusieurs configurations Azure SQL sont éligibles, nous vous recommandons la solution la plus économique. [En savoir plus](./concepts-azure-sql-assessment-calculation.md#calculate-sizing)

## <a name="what-deployment-target-should-i-choose-if-my-sql-instance-is-ready-for-azure-sql-db-and-azure-sql-mi"></a>Quelle cible de déploiement choisir si mon instance SQL est prête pour Azure SQL DB et Azure SQL MI ? 
Si votre instance est prête pour Azure SQL DB et Azure SQL MI, nous vous recommandons d’utiliser le type de déploiement cible pour lequel le coût estimé de la configuration Azure SQL est inférieur.

## <a name="why-is-my-instance-marked-as-potentially-ready-for-azure-vm-in-my-azure-sql-assessment"></a>Pourquoi mon instance est marquée comme « Potentiellement prête pour les machines virtuelles Azure » dans mon évaluation Azure SQL ?
Cela peut se produire lorsque le type de déploiement cible choisi dans les propriétés d’évaluation est **recommandé** et que l’instance SQL n’est pas prête pour Azure SQL Database et Azure SQL Managed Instance. L’utilisateur est incité à créer une évaluation dans Azure Migrate avec le type d’évaluation **Machine virtuelle Azure** pour déterminer si le serveur sur lequel l’instance s’exécute est prêt à migrer vers une machine virtuelle Azure.
L’utilisateur est incité à créer une évaluation dans Azure Migrate avec un type d’évaluation tel que **Machine virtuelle Azure** pour déterminer si le serveur sur lequel l’instance s’exécute est prêt à migrer plutôt vers une machine virtuelle Azure :
- Les évaluations des machines virtuelles Azure dans Azure Migrate sont actuellement axées sur une approche lift-and-shift et ne prennent pas en compte les métriques de performances spécifiques de l’exécution des instances et des bases de données SQL sur la machine virtuelle Azure. 
- Quand vous exécutez une évaluation de machine virtuelle Azure sur un serveur, les estimations de taille et de coût recommandées sont effectuées pour toutes les instances en cours d’exécution sur le serveur et peuvent être migrées vers une machine virtuelle Azure à l’aide de l’outil de migration de serveur. Avant de migrer, [passez en revue les instructions relatives aux performances](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices-checklist.md) de SQL Server sur les machines virtuelles Azure.

## <a name="i-cant-see-some-databases-in-my-assessment-even-though-the-instance-is-part-of-the-assessment"></a>Je ne peux pas voir certaines bases de données dans mon évaluation même si l’instance fait partie de l’évaluation

L’évaluation Azure SQL inclut uniquement des bases de données dont l’état est en ligne. Si la base de données est dans un autre état, l’évaluation ignore la préparation, le dimensionnement et le calcul du coût de ces bases de données. Si vous souhaitez évaluer de telles bases de données, changez l’état de la base de données et recalculez l’évaluation au bout d’un certain délai.

## <a name="i-want-to-compare-costs-for-running-my-sql-instances-on-azure-vm-vs-azure-sql-databaseazure-sql-managed-instance"></a>Je souhaite comparer les coûts liés à l’exécution de mes instances SQL sur une machine virtuelle Azure ou sur Azure SQL Database/Azure SQL Managed Instance

Vous pouvez créer une évaluation avec le type **Machine virtuelle Azure** sur le groupe utilisé dans votre évaluation **Azure SQL**. Vous pouvez ensuite comparer les deux rapports côte à côte. Toutefois, les évaluations des machines virtuelles Azure dans Azure Migrate sont actuellement axées sur une approche lift-and-shift et ne prennent pas en compte les métriques de performances spécifiques de l’exécution des instances et des bases de données SQL sur la machine virtuelle Azure. Quand vous exécutez une évaluation de machine virtuelle Azure sur un serveur, les estimations de taille et de coût recommandées sont effectuées pour toutes les instances en cours d’exécution sur le serveur et peuvent être migrées vers une machine virtuelle Azure à l’aide de l’outil de migration de serveur. Avant de migrer, [passez en revue les instructions relatives aux performances](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices-checklist.md) de SQL Server sur les machines virtuelles Azure.

## <a name="the-storage-cost-in-my-azure-sql-assessment-is-zero"></a>Le coût du stockage dans mon évaluation de SQL Azure est égal à zéro
Pour Azure SQL Managed Instance, aucun coût de stockage n’est ajouté pour le stockage des premiers 32 Go/instance/mois, et des coûts de stockage supplémentaires sont ajoutés pour le stockage par incréments de 32 Go. [En savoir plus](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)

## <a name="i-cant-see-some-groups-when-i-am-creating-an-azure-vmware-solution-avs-assessment"></a>Je ne peux pas voir certains groupes quand je crée une évaluation Azure VMware Solution (AVS)

- L’évaluation AVS peut être effectuée sur des groupes qui ne contiennent que des machines VMware. Supprimez du groupe les machines non-VMware si vous prévoyez d’effectuer une évaluation AVS.
- Si vous effectuez des évaluations AVS dans Azure Migrate pour la première fois, il est recommandé de créer un groupe de machines VMware.

## <a name="i-cant-see-some-vm-types-and-sizes-in-azure-government"></a>Je ne vois pas certains types et certaines tailles de machines virtuelles dans Azure Government.

Les types et les tailles de machines virtuelles pris en charge pour l’évaluation et la migration dépendent de la disponibilité dans l’emplacement Azure Government. Vous pouvez [examiner et comparer](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) les types de machines virtuelles dans Azure Government.

## <a name="the-size-of-my-server-changed-can-i-run-an-assessment-again"></a>La taille de mon serveur a changé. Puis-je réexécuter une évaluation ?

L’appliance Azure Migrate collecte en permanence des informations sur l’environnement local.  Une évaluation est une capture instantanée à une date et heure de serveurs locaux. Si vous changez les paramètres d’un serveur que vous voulez évaluer, utilisez l’option de recalcul pour mettre à jour l’évaluation avec les derniers changements.

## <a name="how-do-i-discover-servers-in-a-multitenant-environment"></a>Comment découvrir les serveurs dans un environnement multilocataire ?

- **VMware** : Si un environnement est partagé entre des locataires et que vous ne voulez pas découvrir les serveurs d’un locataire se trouvant dans l’abonnement d’un autre locataire, créez des informations d’identification de serveur VMware vCenter permettant d’accéder uniquement aux serveurs que vous voulez découvrir. Utilisez ensuite ces informations d’identification quand vous démarrez la détection dans l’appliance Azure Migrate.
- **Hyper-V** : La découverte utilise les informations d’identification de l’hôte Hyper-V. Si les serveurs partagent le même hôte Hyper-V, aucun moyen ne permet actuellement de séparer la détection.  

## <a name="do-i-need-vcenter-server"></a>Ai-je besoin de vCenter Server ?

Oui, Azure Migrate nécessite vCenter Server dans un environnement VMware pour effectuer la découverte. Azure Migrate ne prend pas en charge la découverte des hôtes ESXi qui ne sont pas gérés par vCenter Server.

## <a name="what-are-the-sizing-options-in-an-azure-vm-assessment"></a>Quelles sont les options de dimensionnement dans une évaluation de machine virtuelle Azure ?

Grâce à un dimensionnement local, Azure Migrate ne tient pas compte des données de performances des serveurs pour l’évaluation. Azure Migrate évalue les tailles des machines virtuelles en fonction de la configuration locale. Avec un dimensionnement basé sur les performances, le dimensionnement est basé sur les données d’utilisation.

Par exemple, si un serveur local a 4 cœurs et 8 Go de mémoire et qu’il présente 50 % d’utilisation de l’UC et 50 % d’utilisation de la mémoire :
- Le dimensionnement local recommandera une référence (SKU) de machine virtuelle Azure dotée de quatre cœurs et de 8 Go de mémoire.
- Le dimensionnement basé sur les performances recommandera une référence SKU de machine virtuelle de deux cœurs et de 4 Go de mémoire, car le pourcentage d’utilisation est pris en compte.

De même, le dimensionnement de disque dépend du critère de dimensionnement et du type de stockage :
- Si le critère de dimensionnement est basé sur les performances et que le type de stockage est automatique, Azure Migrate prend en compte les valeurs de débit et d’IOPS du disque quand il identifie le type de disque cible (Standard ou Premium).
- Si le critère de dimensionnement est basé sur les performances et que le type de stockage est Premium, Azure Migrate recommande une référence SKU de disque Premium en fonction de la taille du disque local. La même logique est appliquée au dimensionnement du disque quand le dimensionnement est local et que le type de stockage est Standard ou Premium.

## <a name="does-performance-history-and-utilization-affect-sizing-in-an-azure-vm-assessment"></a>Est-ce que l’historique des performances et le niveau d’utilisation affectent l’évaluation d’une machine virtuelle Azure ?

Oui, l’historique des performances et le niveau d’utilisation affectent l’évaluation d’une machine virtuelle Azure.

### <a name="performance-history"></a>Historique des performances

Pour le dimensionnement basé sur les performances uniquement, Azure Migrate collecte l’historique des performances des machines locales, puis l’utilise pour recommander la taille de la machine virtuelle et le type de disque dans Azure :

1. L’appliance profile en continu l’environnement local pour collecter les données d’utilisation en temps réel toutes les 20 secondes.
2. L’appliance cumule les échantillons de 20 secondes collectés et les utilise pour créer un point de données unique toutes les 15 minutes.
3. Pour créer le point de données, l’appliance sélectionne la valeur maximale dans les échantillons de 20 secondes.
4. L’appliance envoie le point de données à Azure.

### <a name="utilization"></a>Utilisation

Quand vous créez une évaluation dans Azure (en fonction de la durée des performances et de la valeur de centile de l’historique des performances définie), Azure Migrate calcule la valeur d’utilisation effective, puis l’utilise pour le dimensionnement.

Par exemple, si vous définissez la durée des performances à un jour et la valeur de centile au 95e centile, Azure Migrate trie les points d’échantillonnage de 15 minutes envoyés par le collecteur pour le dernier jour dans l’ordre croissant. Il choisit la valeur du 95e centile comme utilisation effective.

L’utilisation de la valeur du 95e centile garantit que les valeurs hors norme sont ignorées. Des valeurs hors norme peuvent être incluses si votre appliance Azure Migrate utilise le 99e centile. Pour choisir l’utilisation maximale de la période sans manquer les valeurs hors norme, définissez Azure Migrate pour qu’il utilise le 99e centile.


## <a name="how-are-import-based-assessments-different-from-assessments-with-discovery-source-as-appliance"></a>En quoi les évaluations basées sur l’importation diffèrent-elles des évaluations avec une source de découverte sous forme d’appliance ?

Les évaluations basées sur l’importation sont des évaluations de machine virtuelle Azure créées avec des machines importées dans Azure Migrate à l’aide d’un fichier CSV. Seuls quatre champs sont obligatoires pour l’importation : nom du serveur, cœurs, mémoire et système d’exploitation. Voici quelques points à prendre en considération : 
 - Les critères de préparation sont moins stricts pour les évaluations basées sur l’importation vis-à-vis du paramètre de type de démarrage. Si le type de démarrage n’est pas fourni, la machine est supposée avoir le type de démarrage BIOS et ne pas être marquée comme **Préparée sous condition**. Pour les évaluations avec une source de découverte sous forme d’appliance, la préparation est marquée comme **Préparée sous condition** si le type de démarrage est manquant. Cette différence dans le calcul de la préparation est due au fait que les utilisateurs ne disposent peut-être pas de toutes les informations sur les machines au début de la planification de la migration, quand les évaluations basées sur l’importation sont effectuées. 
 - Les évaluations d’importation basées sur les performances utilisent la valeur d’utilisation fournie par l’utilisateur pour les calculs du dimensionnement correct. Étant donné que la valeur d’utilisation est fournie par l’utilisateur, les options **Historique des performances** et **Utilisation en centile** sont désactivées dans les propriétés d’évaluation. Pour les évaluations avec une source de découverte sous forme d’appliance, la valeur de centile est choisie parmi les données de performances collectées par l’appliance.

## <a name="why-is-the-suggested-migration-tool-in-import-based-avs-assessment-marked-as-unknown"></a>Pourquoi l’outil de migration suggéré dans l’évaluation AVS basé sur l’importation est-il marqué comme inconnu ?

Pour les machines importées via un fichier CSV, l’outil de migration par défaut dans l’évaluation AVS est inconnu. Pour les machines VMware, il est toutefois recommandé d’utiliser la solution VMware Hybrid Cloud Extension (HCX). [En savoir plus](../azure-vmware/tutorial-deploy-vmware-hcx.md)


## <a name="what-is-dependency-visualization"></a>Qu’est-ce que la visualisation des dépendances ?

La visualisation des dépendances permet d’évaluer les groupes de serveurs à migrer avec une meilleure fiabilité. La visualisation des dépendances vérifie les dépendances croisées des machines avant que vous exécutiez une évaluation. Elle vous permet de vérifier que rien n’est oublié et ainsi d’éviter des interruptions inattendues quand vous migrez vers Azure. Azure Migrate utilise la solution Service Map dans Azure Monitor pour activer la visualisation des dépendances. [Plus d’informations](concepts-dependency-visualization.md)

> [!NOTE]
> L’analyse des dépendances basée sur les agents n’est pas disponible dans Azure Government. Vous pouvez utiliser l’analyse des dépendances sans agent.

## <a name="whats-the-difference-between-agent-based-and-agentless"></a>Quelle est la différence entre la visualisation basée sur les agents et la visualisation sans agent ?

Les différences entre la visualisation sans agent et la visualisation basée sur les agents sont résumées dans le tableau.

**Prérequis** | **Sans agent** | **Basé sur un agent**
--- | --- | ---
Support | Cette option est en préversion et est disponible uniquement pour les serveurs en environnement VMware. [Vérifiez](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) quels sont les systèmes d’exploitation pris en charge. | En disponibilité générale.
Agent | Inutile d’installer des agents sur les machines qui doivent faire l’objet d’une vérification croisée. | Agents à installer sur chaque machine locale à analyser : [Microsoft Monitoring Agent (MMA)](../azure-monitor/agents/agent-windows.md) et l’[Agent de dépendances](../azure-monitor/agents/agents-overview.md#dependency-agent). 
Prérequis | [Passez en revue](concepts-dependency-visualization.md#agentless-analysis) les prérequis et la configuration requise pour le déploiement. | [Passez en revue](concepts-dependency-visualization.md#agent-based-analysis) les prérequis et la configuration requise pour le déploiement.
Log Analytics | Non requis. | Azure Migrate utilise la solution [Service Map](../azure-monitor/vm/service-map.md) dans [Journaux d’activité Azure Monitor](../azure-monitor/logs/log-query-overview.md) pour la visualisation des dépendances. [Plus d’informations](concepts-dependency-visualization.md#agent-based-analysis)
Fonctionnement | Capture les données de connexion TCP sur les ordinateurs activés pour la visualisation des dépendances. Après la découverte, il collecte des données toutes les cinq minutes. | Les agents Service Map installés sur un ordinateur collectent des données sur les processus TCP et sur les connexions entrantes/sortantes pour chaque processus.
Données | Nom du serveur de l’ordinateur source, processus, nom de l’application.<br/><br/> Nom du serveur de l’ordinateur de destination, processus, nom de l’application et port. | Nom du serveur de l’ordinateur source, processus, nom de l’application.<br/><br/> Nom du serveur de l’ordinateur de destination, processus, nom de l’application et port.<br/><br/> Le nombre de connexions, la latence et les informations de transfert de données sont collectés et mis à la disposition des requêtes Log Analytics. 
Visualisation | La carte des dépendances d’un serveur unique peut être affichée sur une durée allant d’une heure à 30 jours. | Carte des dépendances d’un serveur unique.<br/><br/> La carte peut être affichée pendant une heure uniquement.<br/><br/> Carte des dépendances d’un groupe de serveurs.<br/><br/> Ajoutez et supprimez des serveurs dans un groupe à partir de la vue cartographique.
Exportation de données | Les données des 30 derniers jours peuvent être téléchargées dans un format CSV. | Les données peuvent être interrogées avec Log Analytics.


## <a name="do-i-need-to-deploy-the-appliance-for-agentless-dependency-analysis"></a>Est-ce que je dois déployer l’appliance pour l’analyse des dépendances sans agent ?

Oui, l’[appliance Azure Migrate](migrate-appliance.md) doit être déployée.

## <a name="do-i-pay-for-dependency-visualization"></a>Dois-je payer pour la visualisation des dépendances ?

Non. Apprenez-en davantage sur la [tarification Azure Migrate](https://azure.microsoft.com/pricing/details/azure-migrate/).

## <a name="what-do-i-install-for-agent-based-dependency-visualization"></a>Que dois-je installer pour la visualisation des dépendances basée sur les agents ?

Pour utiliser la visualisation des dépendances basée sur les agents, téléchargez et installez des agents sur chaque machine locale que vous souhaitez évaluer :

- [Microsoft Monitoring Agent (MMA)](../azure-monitor/agents/agent-windows.md)
- [Agent de dépendances](../azure-monitor/agents/agents-overview.md#dependency-agent)
- Si certaines de vos machines sont dépourvues de connexion Internet, téléchargez et installez la passerelle Log Analytics sur ces machines.

Vous avez besoin de ces agents uniquement si vous utilisez la visualisation des dépendances basée sur les agents.

## <a name="can-i-use-an-existing-workspace"></a>Puis-je utiliser un espace de travail existant ?

Oui, pour la visualisation des dépendances basée sur les agents, vous pouvez attacher un espace de travail existant au projet de migration et l’utiliser pour la visualisation des dépendances. 

## <a name="can-i-export-the-dependency-visualization-report"></a>Puis-je exporter le rapport de visualisation des dépendances ?

Non, le rapport de visualisation des dépendances dans la visualisation basée sur les agents ne peut pas être exporté. Toutefois, Azure Migrate utilise Service Map, et vous pouvez utiliser les [API REST Service Map](/rest/api/servicemap/machines/listconnections) pour récupérer les dépendances au format JSON.

## <a name="can-i-automate-agent-installation"></a>Puis-je automatiser l’installation de l’agent ?

Pour la visualisation des dépendances basée sur les agents :

- Utilisez un [script pour installer l’agent de dépendances](../azure-monitor/vm/vminsights-enable-hybrid.md#dependency-agent).
- Pour MMA, [utilisez la ligne de commande ou l’automatisation](../azure-monitor/agents/log-analytics-agent.md#installation-options), ou utilisez un [script](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).
- En plus des scripts, vous pouvez utiliser des outils de déploiement tels que Microsoft Endpoint Configuration Manager et [Intigua](https://www.intigua.com/intigua-for-azure-migration) pour déployer les agents.

## <a name="what-operating-systems-does-mma-support"></a>Quels sont les systèmes d’exploitation pris en charge par MMA ?

- Consultez la liste des [systèmes d’exploitation Windows pris en charge par MMA](../azure-monitor/agents/log-analytics-agent.md#installation-options).
- Consultez la liste des [systèmes d’exploitation Linux pris en charge par MMA](../azure-monitor/agents/log-analytics-agent.md#installation-options).

## <a name="can-i-visualize-dependencies-for-more-than-one-hour"></a>Puis-je visualiser des dépendances durant plus d’une heure ?

Pour la visualisation basée sur les agents, vous pouvez visualiser les dépendances durant une heure au maximum. Vous pouvez revenir à une date donnée de l’historique jusqu’à un mois, mais la durée maximale de visualisation est d’une heure. Par exemple, vous pouvez utiliser la durée mentionnée dans la carte des dépendances pour voir les dépendances d’hier, mais seulement pour une fenêtre d’une heure. Vous pouvez cependant utiliser les journaux Azure Monitor pour [interroger les données de dépendance](./how-to-create-group-machine-dependencies.md) sur une durée plus longue.

Pour la visualisation sans agent, vous pouvez voir la carte des dépendances d’un serveur unique sur une durée allant d’une heure à 30 jours.

## <a name="can-i-visualize-dependencies-for-groups-of-more-than-10-servers"></a>Puis-je visualiser les dépendances pour des groupes de plus de 10 serveurs ?

Vous pouvez [visualiser les dépendances](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping) de groupes ayant 10 serveurs au maximum. En présence d’un groupe de plus de 10 serveurs, nous vous recommandons de diviser le groupe en groupes plus petits, puis de visualiser les dépendances.

## <a name="next-steps"></a>Étapes suivantes

Consultez la [vue d’ensemble d’Azure Migrate](migrate-services-overview.md).