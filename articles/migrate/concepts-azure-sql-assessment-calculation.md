---
title: Évaluations d’Azure SQL dans l’outil de découverte et d’évaluation d’Azure Migrate
description: En savoir plus sur les évaluations d’Azure SQL dans l’outil de découverte et d’évaluation d’Azure Migrate
author: rashi-ms
ms.author: rajosh
ms.topic: conceptual
ms.date: 02/07/2021
ms.openlocfilehash: d1ea328575cf07a22ce39549c34d5cd21e916427
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054095"
---
# <a name="assessment-overview-migrate-to-azure-sql"></a>Vue d’ensemble de l’évaluation (migrer vers Azure SQL)

Cet article fournit une vue d’ensemble des évaluations relatives à la migration d’instances SQL Server locales d’un environnement VMware vers des bases de données SQL Azure ou des instances gérées à l’aide de l’[outil de découverte et d’évaluation d’Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-server-assessment-tool).

> [!Note]
> La découverte et l’évaluation d’instances et de bases de données SQL Server s’exécutant dans votre environnement VMware sont actuellement en préversion. Pour tester cette fonctionnalité, utilisez [**ce lien**](https://aka.ms/AzureMigrate/SQL) afin de créer un projet dans la région **Australie Est**. Si vous avez déjà un projet en Australie Est et si vous souhaitez tester cette fonctionnalité, vérifiez que vous avez effectué ces [**prérequis**](how-to-discover-sql-existing-project.md) dans le portail.

## <a name="whats-an-assessment"></a>Qu'est-ce qu'une évaluation ?
Une évaluation avec l’outil de découverte et d’évaluation est une capture instantanée de données à un point dans le temps, qui permet de mesurer l’état de préparation à la migration de serveurs locaux vers Azure et d’en estimer l’effet.

## <a name="types-of-assessments"></a>Types d'évaluations

Il existe trois types d’évaluations que vous pouvez créer à l’aide de l’outil de découverte et d’évaluation d’Azure Migrate.

**Type d’évaluation** | **Détails**
--- | --- 
**Microsoft Azure** | Évaluations pour migrer vos serveurs locaux vers des machines virtuelles Azure. Ce type d’évaluation vous permet d’évaluer vos serveurs locaux dans un environnement [VMware](how-to-set-up-appliance-vmware.md) et [Hyper-V](how-to-set-up-appliance-hyper-v.md), ainsi que vos [serveurs physiques](how-to-set-up-appliance-physical.md) pour une migration vers des machines virtuelles Azure.
**Azure SQL** | Évaluations pour migrer vos serveurs SQL Server locaux de votre environnement VMware vers Azure SQL Database ou Azure SQL Managed Instance.
**Azure VMware Solution (AVS)** | Évaluations pour migrer vos serveurs locaux vers [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). Vous pouvez évaluer vos [machines virtuelles VMware](how-to-set-up-appliance-vmware.md) locales pour la migration vers Azure VMware Solution (AVS) avec ce type d’évaluation. [En savoir plus](concepts-azure-vmware-solution-assessment-calculation.md)

Une évaluation Azure SQL fournit un critère de dimensionnement :

**Critère de dimensionnement** | **Détails** | **Données**
--- | --- | ---
**Basée sur les performances** | Évaluations qui donnent des recommandations en fonction des données de performances collectées | La configuration d’Azure SQL est basée sur les données de performances d’instances et de bases de données SQL, à savoir : utilisation du processeur, utilisation de la mémoire, IOPS (fichiers de données et fichiers journaux), débit et latence des opérations d’E/S.

## <a name="how-do-i-assess-my-on-premises-sql-servers"></a>Comment évaluer mes serveurs SQL Server locaux ?

Vous pouvez évaluer vos instances SQL Server locales à l’aide des données de configuration et d’utilisation collectées par une appliance Azure Migrate légère. L’appliance découvre les instances et les bases de données SQL Server locales, et envoie les données de configuration et de performances à Azure Migrate. [En savoir plus](how-to-set-up-appliance-vmware.md)

## <a name="how-do-i-assess-with-the-appliance"></a>Comment procéder à une évaluation à l'aide de l'appliance ?
Si vous déployez une appliance Azure Migrate pour découvrir des serveurs locaux, procédez comme suit :
1.  Configurez Azure et votre environnement local de manière à ce qu’ils opèrent avec Azure Migrate.
2.  Pour votre première évaluation, créez un projet Azure Migrate et ajoutez-y l’outil de découverte et d’évaluation d’Azure Migrate.
3.  Déployez une appliance Azure Migrate légère. L’appliance découvre en permanence les machines locales et envoie les données de configuration et de performances à Azure Migrate. Déployez l’appliance en tant que machine virtuelle ou serveur physique. Vous n’avez rien à installer sur les serveurs que vous souhaitez évaluer.

Une fois que l’appliance a commencé la découverte, vous pouvez rassembler les serveurs que vous souhaitez évaluer dans un groupe et exécuter une évaluation de celui-ci avec le type d’évaluation **Azure SQL**.

Suivez notre tutoriel relatif à l’évaluation d’[instances SQL Server](tutorial-assess-sql.md) pour essayer ces étapes.

## <a name="how-does-the-appliance-calculate-performance-data-for-sql-instances-and-databases"></a>Comment l’appliance calcule-t-elle les données de performances pour les instances et les bases de données SQL ?

L’appliance collecte des données de performances pour les paramètres de calcul en procédant comme suit :
1. L’appliance collecte un point d’échantillonnage en temps réel. Pour les serveurs SQL Server, un point d’échantillonnage est collecté toutes les 30 secondes.
2. L’appliance agrège les points d’échantillonnage de données collectés toutes les 30 secondes pendant plus de 10 minutes. Pour créer le point de données, l’appliance sélectionne les valeurs maximales de tous les échantillons. Elle envoie à Azure la valeur maximale, la valeur moyenne et la variance pour chaque compteur.
3. Azure Migrate stocke tous les points d’échantillonnage de 10 minutes pour le dernier mois.
4. Quand vous créez une évaluation, Azure Migrate identifie le point de données approprié à utiliser pour un dimensionnement adéquat. L’identification est basée sur les valeurs de centile pour Historique des performances et Utilisation en centile.
    - Par exemple, si l’historique des performances est d’une semaine et que l’utilisation en centile est le 95e centile, l’évaluation trie les points d’échantillonnage de 10 minutes pour la dernière semaine. Il les trie dans l’ordre croissant et sélectionne la valeur du 95e centile pour le dimensionnement adéquat.
    - Le 95e centile vous permet d’ignorer les valeurs hors norme qui peuvent être incluses quand vous choisissez le 99e centile.
    - Si vous souhaitez choisir l’utilisation maximale de la période et que vous ne souhaitez pas ignorer les valeurs hors norme, sélectionnez le 99e centile comme utilisation en centile.
5. Cette valeur est multipliée par le facteur de confort pour obtenir les données réelles d’utilisation de performances pour ces mesures collectées par l’appliance :
    - Utilisation du processeur (%)
    - Utilisation de la mémoire (%)
    - E/S de lecture et d’écriture (fichiers de données et fichiers journaux)
    - Mo/s en lecture et Mo/s en écriture (débit)
    - Latence des opérations d’E/S

## <a name="what-properties-are-used-to-create-and-customize-an-azure-sql-assessment"></a>Quelles sont les propriétés utilisées pour créer et personnaliser une évaluation Azure SQL ?

Voici ce qu’incluent les propriétés d’évaluation d’Azure SQL :

**Propriété** | **Détails**
--- | ---
**Emplacement cible** | Région Azure vers laquelle vous souhaitez opérer la migration. Les recommandations en matière de configuration et de coût d’Azure SQL sont basées sur l’emplacement que vous spécifiez.
**Type de déploiement cible** | Type de déploiement cible que vous souhaitez évaluer : sélectionnez **Recommandé** si vous souhaitez qu’Azure Migrate évalue l’état de préparation de vos serveurs SQL Server pour la migration vers Azure SQL MI et Azure SQL DB, et recommande l’option de déploiement cible, le niveau cible, la configuration d’Azure SQL et les estimations mensuelles les mieux adaptés. Sélectionnez **Azure SQL DB**, si vous souhaitez évaluer vos serveurs SQL pour la migration uniquement vers des bases de données Azure SQL, et examiner le niveau cible, la configuration d’Azure SQL DB et les estimations mensuelles. Sélectionnez **Azure SQL MI**, si vous souhaitez évaluer vos serveurs SQL pour la migration uniquement vers des bases de données Azure SQL, et examiner le niveau cible, la configuration d’Azure SQL MI et les estimations mensuelles.
**Capacité réservée** | Spécifie une capacité de réserve afin que les estimations de coût dans l’évaluation la prenne en compte. Si vous sélectionnez une option capacité de réserve, vous ne pouvez pas spécifier de « Remise (%) ».
**Critère de dimensionnement** | Cette propriété permet de dimensionner correctement la configuration d’Azure SQL. Par défaut, elle est **basée sur les performances**, ce qui signifie que l’évaluation collecte les métriques de performances des instances et des bases de données SQL Server afin de recommander une Azure SQL Managed Instance de taille optimale et/ou un niveau ou une configuration d’Azure SQL Database.
**Historique des performances** | L’historique des performances spécifie la durée utilisée lors de l’évaluation des données de performances.
**Utilisation en centile** | L’utilisation en centile spécifie la valeur de centile de l’échantillon de performances utilisé pour le dimensionnement adéquat.
**Facteur de confort** | Mémoire tampon utilisée pendant l’évaluation. Il prend en compte les problèmes, tels que l’utilisation saisonnière, l’historique des performances de courte durée et l’augmentation probable de l’utilisation future. Par exemple, une instance à 10 cœurs avec 20 % d’utilisation correspond normalement à une instance à deux cœurs. Avec un facteur de confort de 2.0, le résultat est plutôt une instance à quatre cœurs.
**Programme d’offre/de licences** | [Offre Azure](https://azure.microsoft.com/support/legal/offer-details/) dans laquelle vous êtes inscrit. Pour le moment, vous ne pouvez choisir qu’entre le paiement à l’utilisation et le Dev/Test de paiement à l’utilisation. Notez que vous pouvez bénéficier d’une remise supplémentaire en demandant une capacité de réserve et Azure Hybrid Benefit en plus de l’offre de paiement à l’utilisation.
**Niveau de service** | Option de niveau de service la plus appropriée pour répondre aux besoins de votre entreprise en lien avec une migration vers Azure SQL Database et/ou Azure SQL Managed Instance : sélectionnez **Recommandé** si vous souhaitez qu’Azure Migrate recommande le niveau de service le mieux adapté à vos serveurs. Ce peut être Usage général ou Critique pour l’entreprise. Sélectionnez **Usage général** si vous souhaitez une configuration d’Azure SQL conçue pour des charges de travail axées sur le budget. [En savoir plus](https://docs.microsoft.com/azure/azure-sql/database/service-tier-general-purpose) Sélectionnez **Critique pour l’entreprise** si vous souhaitez une configuration d’Azure SQL conçue pour des charges de travail à faible latence avec une haute résilience aux échecs et des basculements rapides. [En savoir plus](https://docs.microsoft.com/azure/azure-sql/database/service-tier-business-critical)
**Devise** | Devise de facturation de votre compte.
**Remise (%)** | Toute remise propre à un abonnement que vous recevez en plus de l’offre Azure. Le paramètre par défaut est 0 %.
**Azure Hybrid Benefit** | Spécifie si vous disposez déjà d’une licence SQL Server. Si c’est le cas et si vous êtes couvert par une Software Assurance active des abonnements SQL Server, vous pouvez demander Azure Hybrid Benefit au moment d’importer les licences dans Azure.

[Passez en revue les meilleures pratiques](best-practices-assessment.md) de création et d’évaluation avec Azure Migrate.

## <a name="calculate-readiness"></a>Calculer l’état de préparation

> [!NOTE]
> L’évaluation inclut uniquement les bases de données dont l’état est en ligne. Si la base de données est dans un autre état, l’évaluation ignore la préparation, le dimensionnement et le calcul du coût de ces bases de données. Si vous souhaitez évaluer de telles bases de données, changez l’état de la base de données et recalculez l’évaluation au bout d’un certain délai.

### <a name="azure-sql-readiness"></a>État de préparation d’Azure SQL

L’état de préparation d’Azure SQL pour les instances et les bases de données SQL est basé sur une vérification de la compatibilité des fonctionnalités avec Azure SQL Database et Azure SQL Managed Instance :
- L’évaluation d’Azure SQL prend en compte les fonctionnalités d’instance SQL Server qui sont actuellement utilisées par les charges de travail SQL Server sources (travaux de SQL Agent, serveurs liés, etc.) et les schémas de bases de données utilisateur (tables, affichages, déclencheurs, procédures stockées, etc.) pour identifier des problèmes de compatibilité.
- Si aucun problème de compatibilité n’est détecté, l’état de préparation est marqué comme **Prêt** pour le type de déploiement cible (Azure SQL Database ou Managed instance SQL Azure).
- S’il existe des problèmes de compatibilité non critiques, tels que des fonctionnalités dégradées ou non prises en charge qui ne bloquent pas la migration vers un type de déploiement cible spécifique, l’état de préparation est marqué comme **Prêt** (icône d’informations bleue avec lien hypertexte) avec des détails d’**avertissement** et des recommandations de correction.
- S’il existe des problèmes de compatibilité susceptibles de bloquer la migration vers un type de déploiement cible spécifique, L’état de préparation est marqué comme **Non prêt** avec des détails sur le **problème** et des recommandations de correction.
    - S’il y a ne serait-ce même qu’une base de données dans une instance SQL qui n’est pas prête pour un type de déploiement cible particulier, l’instance est marquée comme **Non prête** pour ce type de déploiement.
- Si la détection est toujours en cours ou s’il existe des problèmes de découverte pour une instance ou une base de données SQL, l’état de préparation est marqué comme **Inconnu** , car l’évaluation n’a pas pu calculer la préparation pour cette instance SQL.

### <a name="recommended-deployment-type"></a>Type de déploiement recommandé

Si vous sélectionnez le type de déploiement cible **Recommandé** dans les propriétés d’évaluation d’Azure SQL, Azure Migrate recommande un type de déploiement d’Azure SQL compatible avec votre instance SQL. La migration vers une cible recommandée par Microsoft réduit votre effort global de migration. 

#### <a name="recommended-deployment-type-based-on-azure-sql-readiness"></a>Type de déploiement recommandé basé sur l’état de préparation d’Azure SQL

 **Préparation à Azure SQL DB** | **Préparation à Azure SQL MI** | **Type de déploiement recommandé** | **Configuration d’Azure SQL et estimations de coût calculées ?**
 --- | --- | --- | --- |
 Ready | Ready | Azure SQL DB ou Azure SQL MI | Oui
 Ready | Non prêt ou Inconnu | Azure SQL DB | Oui
 Non prêt ou Inconnu | Ready | Azure SQL Database Managed Instance  | Oui
 Pas prêt | Pas prêt | Potentiellement prêt pour une machine virtuelle Azure | Non
 Non prêt ou Inconnu | Non prêt ou Inconnu | Unknown | Non

> [!NOTE]
> Si le type de déploiement recommandé sélectionné est **Recommandé** dans les propriétés d’évaluation et si le serveur SQL Server source est adapté tant pour une base de données unique Azure SQL Database que pour Azure SQL Managed Instance, l’évaluation recommande une option spécifique qui optimise vos coûts et s’adapte aux limites de taille et de performances.

#### <a name="potentially-ready-for-azure-vm"></a>Potentiellement prêt pour une machine virtuelle Azure

Si l’instance SQL n’est pas prête pour Azure SQL Database et Azure SQL Managed Instance, le type de déploiement Recommandé est marqué comme *Potentiellement prêt pour une machine virtuelle Azure*.
- L’utilisateur est incité à créer une évaluation dans Azure Migrate avec un type d’évaluation tel que « Machine virtuelle Azure » pour déterminer si le serveur sur lequel l’instance s’exécute est prêt à migrer plutôt vers une machine virtuelle Azure. Notez les points suivants :
    - Les évaluations des machines virtuelles Azure dans Azure Migrate sont actuellement axées sur une approche lift-and-shift et ne prennent pas en compte les métriques de performances spécifiques de l’exécution des instances et des bases de données SQL sur la machine virtuelle Azure. 
    - Quand vous exécutez une évaluation de machine virtuelle Azure sur un serveur, les estimations de taille et de coût recommandées sont effectuées pour toutes les instances en cours d’exécution sur le serveur et peuvent être migrées vers une machine virtuelle Azure à l’aide de l’outil de migration de serveur. Avant de migrer, [passez en revue les instructions relatives aux performances](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/performance-guidelines-best-practices) de SQL Server sur les machines virtuelles Azure.


## <a name="calculate-sizing"></a>Calculer le dimensionnement

### <a name="azure-sql-configuration"></a>Configuration d’Azure SQL

Une fois que l’évaluation a déterminé l’état de préparation et le type de déploiement d’Azure SQL recommandé, elle calcule un niveau de service et une configuration d’Azure SQL (taille de référence SKU) spécifiques pouvant atteindre ou dépasser les performances de l’instance SQL locale :
1. Au cours du processus de découverte, Azure Migrate collecte la configuration et les performances de l’instance SQL, à savoir :
    - vCores (alloués) et utilisation du processeur (%)
        - L’utilisation du processeur pour une instance SQL est le pourcentage de processeur alloué que l’instance utilise sur le serveur SQL Server.
        - L’utilisation du processeur pour une base de données est le pourcentage de processeur alloué qu’utilise la base de données sur l’instance SQL.
    - Mémoire (allouée) et utilisation de la mémoire (%)
    - E/S de lecture et d’écriture (fichiers de données et fichiers journaux)
        - Les E/S de lecture et d’écriture au niveau d’une instance SQL sont calculées par addition des E/S de lecture et d’écriture de toutes les bases de données découvertes dans cette instance.
    - Mo/s en lecture et Mo/s en écriture (débit)
    - Latence des opérations d’E/S
    - Taille totale de la base de données et organisations des fichiers de base de données
        - La taille de la base de données est calculée en ajoutant l’ensemble des fichiers de données et des fichiers journaux.
1. L’évaluation agrège toutes les données de configuration et de performances, et tente de trouver la meilleure concordance entre les divers niveaux de service et configurations d’Azure SQL, et choisit une configuration qui peut correspondre ou dépasser les exigences de performances de l’instance SQL, en optimisant le coût.

### <a name="confidence-ratings"></a>Niveaux de confiance 
Chaque évaluation d’Azure SQL est associée à un classement de confiance. Le niveau de confiance est compris entre une étoile (le plus bas) et cinq étoiles (le plus haut). Le niveau de confiance vous aide à estimer la fiabilité des recommandations de taille fournies par Azure Migrate.
- Le niveau de confiance est attribué à une évaluation. Le niveau de confiance est basé sur la disponibilité des points de données nécessaires au calcul de l’évaluation.
- Pour le dimensionnement basé sur les performances, l’évaluation collecte les données de performances de l’ensemble des instances et bases de données SQL, à savoir :
    - Utilisation du processeur (%)
    - Utilisation de la mémoire (%)
    - E/S de lecture et d’écriture (fichiers de données et fichiers journaux)
    - Mo/s en lecture et Mo/s en écriture (débit)
    - Latence des opérations d’E/S
    
Si l’un de ces chiffres d’utilisation n’est pas disponible, les recommandations de taille peuvent ne pas être fiables.
Ce tableau indique les niveaux de confiance des évaluations, qui dépendent du pourcentage de points de données disponibles :

**Disponibilité des points de données** | **Niveau de confiance**
--- | ---
0 %-20 % | 1 étoile
21 %-40 % | 2 étoiles
41 %-60 % | 3 étoiles
61 %-80 % | 4 étoiles
81 %-100 % | 5 étoiles

#### <a name="low-confidence-ratings"></a>Niveaux de confiance faibles
Voici quelques raisons pour lesquelles une évaluation peut avoir un niveau de confiance faible :
- Vous n’avez pas profilé votre environnement pendant la durée pour laquelle vous créez l’évaluation. Par exemple, si vous créez l’évaluation avec une durée des performances définie sur un jour, vous devez attendre au moins un jour après le démarrage de la découverte pour que tous les points de données soient collectés.
- L’évaluation ne parvient pas à collecter les données de performances d’une partie ou de la totalité des serveurs pendant la période d’évaluation. Pour obtenir un niveau de confiance élevé, vérifiez les éléments suivants :
    - Les serveurs sont sous tension pendant toute la durée de l’évaluation.
    - Les connexions sortantes sont autorisées sur le port 443.
    - Si l’état de connexion à Azure Migrate de l’agent SQL dans Azure Migrate est « Connecté » et vérifie la dernière pulsation 
    - Si l’état de connexion d’Azure Migrate pour toutes les instances SQL est « Connecté » dans le panneau des instances SQL détectées

    « Recalculez » l’évaluation pour qu’elle reflète l’évolution récente de la note de confiance.
- Certaines machines virtuelles ou instances ont été créées pendant la période de calcul de l’évaluation. Par exemple, supposons que vous avez créé une évaluation de l’historique des performances du dernier mois, mais que certaines bases de données ou instances n’ont été créées qu’il y a une semaine. Dans ce cas, les données de performances pour les nouveaux serveurs ne seront pas disponibles pendant toute la durée et le classement de confiance sera faible.

> [!NOTE]
> Étant donné que les évaluations d’Azure SQL sont basées sur les performances, si le classement de confiance d’une évaluation est inférieur à cinq étoiles, nous vous recommandons d’attendre au moins un jour pour que l’appliance profile l’environnement, puis de recalculer l’évaluation. Dans le cas contraire, le dimensionnement basé sur les performances peut ne pas être fiable.

## <a name="calculate-monthly-costs"></a>Calculer les coûts mensuels
Une fois les recommandations de dimensionnement terminées, l’évaluation d’Azure SQL calcule les coûts de calcul et de stockage pour les configurations d’Azure SQL recommandées à l’aide d’une API de tarification interne. Elle agrège le coût de calcul et de stockage de toutes les instances pour calculer le coût de calcul mensuel total. 
### <a name="compute-cost"></a>Coût de calcul
- Pour le calcul du coût de calcul pour une configuration d’Azure SQL, l’évaluation prend en compte les propriétés suivantes :
    - Licences Azure Hybrid Benefit pour SQL
    - Capacité réservée
    - Emplacement cible Azure
    - Devise
    - Programme d’offre/de licences
    - Remise (%)

### <a name="storage-cost"></a>Coût de stockage
- Les estimations de coût de stockage incluent uniquement les fichiers de données, pas les fichiers journaux. 
- Pour calculer le coût de stockage d’une configuration d’Azure SQL, l’évaluation prend en compte les propriétés suivantes :
    - Emplacement cible Azure
    - Devise
    - Programme d’offre/de licences
    - Remise (%)
- Le coût du stockage de sauvegarde n’est pas inclus dans l’évaluation.
- **Azure SQL Database**
    - Un coût de stockage de minimum 5 Go est ajouté à l’estimation du coût, et des coûts de stockage supplémentaires sont ajoutés pour le stockage par incréments de 1 Go. [En savoir plus](https://azure.microsoft.com/pricing/details/sql-database/single/)
- **Azure SQL Managed Instance**
    - Aucun coût de stockage n’est ajouté pour le stockage des premiers 32 Go/instance/mois, et des coûts de stockage supplémentaires sont ajoutés pour le stockage par incréments de 32 Go. [En savoir plus](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)
        
## <a name="next-steps"></a>Étapes suivantes
- [Passer en revue](best-practices-assessment.md) les meilleures pratiques pour la création d’évaluations. 
- Découvrez comment exécuter une [évaluation d’Azure SQL](how-to-create-azure-sql-assessment.md).
