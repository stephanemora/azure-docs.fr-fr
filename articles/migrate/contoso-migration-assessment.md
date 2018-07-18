---
title: Évaluer les charges de travail locales pour la migration de Contoso vers Azure | Microsoft Docs
description: Découvrez comment Contoso évalue ses ordinateurs locaux pour la migration vers Azure avec la migration Azure et la migration de base de données
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: raynew
ms.openlocfilehash: fb987c95afc0f77386f4f78c44f3c6825f86ee43
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232037"
---
# <a name="contoso-migration-assess-on-premises-workloads-for-migration-to-azure"></a>Migration de Contoso : évaluer facilement vos charges de travail locales en vue d’une migration vers Azure

Cet article vous explique comment Contoso évalue son application SmartHotel localement en vue de sa migration vers Azure.

Ce document est le troisième d’une série d’articles décrivant comment l’entreprise fictive Contoso migre ses ressources locales vers le cloud Microsoft Azure. La série comprend des informations générales, et des scénarios de déploiement qui illustrent comment configurer une infrastructure de la migration, évaluer l’adéquation des ressources locales pour la migration, et exécuter différents types de migration. Les scénarios croissent en complexité, et nous ajouterons des articles au fil du temps.

**Article** | **Détails** | **État**
--- | --- | ---
[Article 1 : vue d’ensemble](contoso-migration-overview.md) | Fournit une vue d’ensemble de la stratégie de migration de Contoso, de la série d’articles et des exemples d’application que nous utilisons. | Disponible
[Article 2 : Déployer une infrastructure Azure](contoso-migration-infrastructure.md) | Décrit comment Contoso prépare son infrastructure locale et son infrastructure Azure pour la migration. La même infrastructure est utilisée pour tous les scénarios de migration de Contoso. | Disponible
Article 3 : évaluer les ressources locales (cet article)  | Montre comment Contoso évalue son application à deux niveaux locale SmartHotel s’exécutant sur VMware. Ils évaluent les machines virtuelles de l’application avec le service [Azure Migrate](migrate-overview.md) et la base de données SQL Server de l’application avec [l’Assistant Migration de données Azure](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Disponible
[Article 4 : ré-héberger sur des machines virtuelles Azure et une instance SQL Managed Instance](contoso-migration-rehost-vm-sql-managed-instance.md) | Montre comment Contoso migre l’application SmartHotel vers Azure. Ils migrent la machine virtuelle frontale de l’application à l’aide d’[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), et la base de données de l’application à l’aide du service [Azure Database Migration](https://docs.microsoft.com/azure/dms/dms-overview) pour opérer la migration vers une instance SQL Managed Instance. | Disponible
[Article 5 : Réhéberger sur des machines virtuelles Azure](contoso-migration-rehost-vm.md) | Montre comment Contoso migre les machines virtuelles de son application SmartHotel en utilisant uniquement Site Recovery.
[Article 6 : ré-héberger sur des machines virtuelles Azure et des groupes de disponibilité SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Montre comment Contoso migre l’application SmartHotel. Ils utilisent Site Recovery pour migrer les machines virtuelles de l’application, et Database Migration Service pour migrer la base de données de l’application vers un groupe de disponibilité SQL Server. | Disponible
[Article 7 : ré-héberger une application Linux sur des machines virtuelles Azure](contoso-migration-rehost-linux-vm.md) | Montre comment Contoso migre sont application Linux osService à l’aide d’Azure Site Recovery.
[Article 8 : ré-héberger une application Linux sur des machines virtuelles Azure et Azure MySQL Server](contoso-migration-rehost-linux-vm-mysql.md) | Montre comment Contoso migre l’application Linux osService à l’aide de Site Recovery pour la migration de machines virtuelles, et de MySQL Workbench pour migrer vers une instance Azure MySQL Server. | Disponible


## <a name="overview"></a>Vue d'ensemble

Lorsqu’elle envisage d’effectuer une migration vers Azure, la société Contoso souhaite réaliser une évaluation technique et financière pour déterminer si ses charges de travail locales peuvent être migrées vers le cloud. L’équipe de Contoso souhaite en particulier évaluer la compatibilité des machines et des bases de données par rapport à la migration, ainsi qu’estimer la capacité et les coûts associés à l’exécution de ses ressources dans Azure.

Pour faire un premier pas et de mieux comprendre les technologies impliquées, ils vont pour évaluer deux de leurs applications locales, reprises dans le tableau suivant. Notez qu’ils évaluent des scénarios de migration qui ré-hébergent et refactorisent des applications pour la migration. Apprenez-en davantage sur le ré-hébergement et la refactorisation en consultant la [vue d’ensemble de la migration de Contoso](contoso-migration-overview.md).

**Nom de l’application** | **Plateforme** | **Couches application** | **Détails**
--- | --- | --- | ---
SmartHotel<br/><br/> Gère les besoins de déplacement de Contoso | S’exécute sur Windows avec une base de données SQL Server | Application à deux niveaux avec le site web ASP.NET frontal s’exécutant sur une machine virtuelle (WEBVM), et SQL Server d’exécutant sur un autre machine virtuelle (SQLVM) | Machines virtuelles et VMware s’exécutant sur un hôte ESXi géré par un serveur vCenter.<br/><br/> Vous pouvez télécharger l’exemple d’application à partir de [GitHub](https://github.com/Microsoft/SmartHotel360).
OSTicket<br/><br/> Application de Service Desk de Contoso | S’exécute sur Linux/Apache avec PHP MySQL (LAMP). | Application à deux niveaux avec un site web PHP frontal sur une machine virtuelle (OSTICKETWEB) et la base de données MySQL s’exécutant sur une autre machine virtuelle (OSTICKETMYSQL) | L’application est utilisée par les applications de service clientèle pour effectuer le suivi des problèmes pour les employés et les clients externes.<br/><br/> Vous pouvez télécharger l’exemple d’application à partir de [GitHub](https://github.com/osTicket/osTicket).

## <a name="current-architecture"></a>Architecture actuelle


Voici un diagramme montrant l’infrastructure locale actuelle de Contoso.

![Architecture de Contoso](./media/contoso-migration-assessment/contoso-architecture.png)  

- Contoso dispose d’un centre de données principal situé dans la ville de New York dans l’Est des États-Unis.
- Ils ont trois branches locales supplémentaires aux États-Unis.
- Le centre de données principal est connecté à Internet via une connexion Ethernet Fiber Metro (500 Mbits/s).
- Chaque branche est connectée localement à Internet via des connexions de qualité professionnelle, avec des tunnels VPN IPSec vers le centre de données principal. Cela permet que le réseau entier soit connecté en permanence et optimise la connexion Internet.
- Le centre de données principal est entièrement virtualisé avec VMware. Il dispose de deux hôtes de virtualisation ESXi 6.5, gérés par un vCenter Server 6.5.
- Contoso utilise Active Directory pour la gestion des identités, et des serveurs DNS sur le réseau interne.
- Les contrôleurs de domaine dans le centre de données s’exécutent sur des machines virtuelles VMware. Les contrôleurs de domaine au niveau des branches locales s’exécutent sur des serveurs physiques.





## <a name="business-drivers"></a>Axes stratégiques

L’équipe informatique a travaillé en étroite collaboration avec ses partenaires commerciaux pour comprendre le résultat que l’entreprise souhaite obtenir avec cette migration :

- **Répondre à la croissance** : Contoso étant en croissance, son infrastructure et ses systèmes locaux subissent une pression.
- **Augmenter l’efficacité** : Contoso doit supprimer les procédures inutiles et rationaliser les processus pour ses développeurs et utilisateurs.  L’entreprise a besoin d’une informatique rapide et doit éviter de perdre du temps ou d’argent en répondant plus rapidement aux exigences des clients.
- **Augmenter l’agilité** : l’informatique de Contoso doit être plus réactive aux besoins de l’entreprise. Elle doit être en mesure de réagir plus rapidement que l’évolution du marché pour réussir dans une économie mondiale.  L’informatique ne doit pas devenir une entrave à l’activité.
- **Mise à l’échelle** : à mesure que l’entreprise croît, l’informatique de Contoso doit fournir des systèmes capables de croître au même rythme.

## <a name="assessment-goals"></a>Objectifs d’évaluation

L’équipe cloud de Contoso a épinglé les objectifs de ces évaluations de la migration :

- Après la migration, les applications dans Azure devront offrir les mêmes performances qu’aujourd’hui dans leur environnement VMWare local.  Migrer vers le cloud ne signifie nullement que les performances de l’application deviennent moins importantes.
- Contoso doit comprendre la compatibilité de ses applications et bases de données avec les exigences d’Azure, ainsi que leurs options d’hébergement dans Azure.
- L’administration de base de données de Contoso doit être réduite au minimum après la migration des applications dans le cloud.  
- Contoso souhaite comprendre non seulement les options de migration, mais aussi les coûts associés à l’infrastructure après déplacement de celle-ci vers le cloud.

## <a name="assessment-tools"></a>Outils d’évaluation
Contoso utilise des outils Microsoft pour l’évaluation. Ces outils s’alignent sur leurs objectifs et doivent leur fournir toutes les informations dont ils ont besoin.

**Technology** | **Description** | **Coût**
--- | --- | ---
[Assistant Migration de données Microsoft (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Ils vont utiliser DMA pour évaluer et détecter des problèmes de compatibilité susceptibles d’affecter les fonctionnalités de base de données dans Azure. DMA évalue la parité des fonctionnalités entre SQL sources et cibles, et recommande des améliorations des performances et de la fiabilité. | Cet outil est téléchargeable gratuitement.
[Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-overview) | Contoso utilisera ce service pour évaluer ses machines virtuelles VMware. Il évalue si les machines peuvent être migrées et propose des estimations de coûts et de dimensionnement pour leur exécution dans Azure.  | À la date de rédaction de cet article (mai 2018), l’utilisation de ce service est gratuite.
[Service Map](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) | Azure Migrate utilise Service Map pour afficher des dépendances entre les machines que vous souhaitez migrer. |  Service Map fait partie d’Azure Log Analytics. Actuellement, il peut être utilisé gratuitement pendant 180 jours.

Dans ce scénario, Contoso télécharge et exécute DMA pour évaluer la base de données SQL Server locale pour son application de voyage. Ils utilisent Azure Migrate avec un mappage des dépendances pour évaluer les machines virtuelles de l’application avant d’opérer la migration vers Azure.



## <a name="assessment-architecture"></a>Architecture pour l’évaluation


![Architecture pour l’évaluation de la migration](./media/contoso-migration-assessment/migration-assessment-architecture.png)

- Contoso est un nom fictif représentant une entreprise classique. 
- Contoso dispose d’un centre de données local (**contoso-datacenter**), avec des contrôleurs de domaine locaux (CONTOSODC1, CONTOSODC2).
- Les machines virtuelles VMware sont situées sur des hôtes VMware ESXI exécutant la version 6.5. Hôtes : **contosohost1**, **contosohost2**
- L’environnement VMware est géré par un serveur vCenter 6.5 (**venter**) s’exécutant sur une machine virtuelle.
- L’application de voyage SmartHotel :
    - L’application est hiérarchisée sur deux machines virtuelles VMware, **WEBVM** et **SQLVM**.
    - Les machines virtuelles sont situées sur un hôte VMware ESXi, **contosohost1.contoso.com**.
    - Les machines virtuelles exécutent Windows Server 2008 R2 Datacenter avec SP1.
- L’environnement VMware est géré par le serveur vCenter Server (**vcenter.contoso.com**) s’exécutant sur une machine virtuelle.
- L’application de Service Desk OSTicket :
    - L’application est hiérarchisée sur deux machines virtuelles, **OSTICKETWEB** et **OSTICKETMYSQL**.
    - Les machines virtuelles s’exécutent sur Ubuntu Linux Server 16.04-LTS.
    - La machine virtuelle OSTICKETWEB exécute Apache 2 et PHP 7.0.
    - La machine virtuelle OSTICKETMYSQL exécute MySQL 5.7.22.

![Architecture](./media/contoso-migration-assessment/architecture.png)


## <a name="prerequisites"></a>Prérequis

Voici ce dont Contoso a besoin pour l’évaluation :

- Un accès Propriétaire ou Contributeur pour l’abonnement Azure ou pour un groupe de ressources dans l’abonnement Azure.
- Un serveur vCenter Server local qui exécute la version 5.5, 6.0 ou 6.5.
- Un compte en lecture seule dans le serveur vCenter Server, ou des autorisations pour en créer un.
- Des autorisations pour créer une machine virtuelle sur le serveur vCenter Server, à l’aide d’un modèle .OVA.
- Au moins un hôte ESXi exécutant la version 5.0 ou une version ultérieure.
- Au moins deux machines virtuelles VMware locales, dont l’une exécutant une base de données SQL Server.
- Les autorisations nécessaires pour installer des agents Azure Migrate sur chaque machine virtuelle.
- Les machines virtuelles doivent avoir une connectivité Internet directe.
        - Vous pouvez limiter l’accès Internet aux [URL requises](https://docs.microsoft.com/azure/migrate/concepts-collector#collector-pre-requisites).
        - si les machines n’ont pas de connexion Internet, la [passerelle OMS](../log-analytics/log-analytics-oms-gateway.md) doit être installée sur celles-ci.
- Le nom de domaine complet de la machine virtuelle qui exécute l’instance SQL Server, pour l’évaluation de la base de données.
- Le pare-feu Windows s’exécutant sur la machine virtuelle SQL Server doit autoriser les connexions externes sur le port TCP 1433 (par défaut), afin que le DMA puisse se connecter.


## <a name="assessment-overview"></a>Vue d’ensemble de l’évaluation

Voici comment Contoso va procéder à l’évaluation :


> [!div class="checklist"]
> * **Étape 1 : télécharger et installer DMA** : préparez DMA pour l’évaluation de la base de données SQL Server locale.
> * **Étape 2 : évaluer la base de données avec DMA** : exécuter et analyser l’évaluation de la base de données.
> * **Étape 3 : préparer l’évaluation de la machine virtuelle avec Azure Migrate** : configurer les comptes locaux et ajuster les paramètres VMware.
> * **Étape 4 : détecter les machines virtuelles locales avec Azure Migrate** : créer une machine virtuelle de collecteur Azure Migrate. Ensuite, il s’agit d’exécuter le collecteur pour détecter les machines virtuelles pour l’évaluation.
> * **Étape 5 : préparer l’analyse des dépendances avec Azure Migrate** : installer les agents Azure Migrate sur les machines virtuelles, afin qu’ils puissent voir le mappage des dépendances entre les machines virtuelles.
> * **Étape 6 : évaluer les machines virtuelles avec Azure Migrate**: vérifier les dépendances, regrouper les machines virtuelles et effectuer l’évaluation. Une fois l’évaluation prête, il s’agit de l’analyser pour préparer la migration.


## <a name="step-1-download-and-install-the-dma"></a>Étape 1 : télécharger et installer DMA

1. Contoso télécharge DMA à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=53595).
    - Vous pouvez installer l’Assistant sur toute machine se connectant à l’instance SQL. Vous n’avez pas besoin de l’exécuter sur la machine SQL Server.
    - Il ne faut pas l’exécuter sur la machine hôte de SQL Server.
2. Ils exécutent le fichier d’installation téléchargé (DownloadMigrationAssistant.msi) pour démarrer l’installation.
3. Dans la page **Terminer**, ils sélectionnent **Lancer l’Assistant Migration de données Microsoft** avant la fin de l’exécution de l’Assistant.

## <a name="step-2-run-and-analyze-the-database-assessment-for-smarthotel"></a>Étape 2 : exécuter et analyser l’évaluation de la base de données pour SmartHotel

Contoso peut à présent exécuter une évaluation pour analyser son SQL Server local pour l’application SmartHotel.

1. Dans l’Assistant Migration de données Microsoft, ils cliquent sur **Nouveau**, sélectionnent **Évaluation**, et attribuent un nom de projet à l’évaluation (**SmartHotel**).
2. Ils sélectionnent le **type de serveur source** **SQL Server sur machines virtuelles Azure**. 

    ![Sélectionner une source](./media/contoso-migration-assessment/dma-assessment-1.png)

    > [!NOTE]
      Actuellement, le DMA ne prend pas en charge l’évaluation pour la migration d’une instance SQL Managed Instance. Pour contourner ce problème, Contoso utilise SQL Server sur une machine virtuelle Azure en tant que cible supposée pour l’évaluation.

3. Dans **Sélectionner une version cible**, ils sélectionnent SQL Server 2017 en tant que version cible. Ils doivent opérer cette sélection, car il s’agit de la version utilisée par l’instance SQL Managed Instance.
4. Ils sélectionnent cette option pour découvrir des informations sur la compatibilité et de nouvelles fonctionnalités :
    - La section **Problèmes de compatibilité** signale des modifications qui peuvent interrompre la migration, ou qui nécessitent un ajustement mineur avant celle-ci. Elle vous tient informé de toute fonctionnalité en cours d’utilisation qui a été déconseillée. Les problèmes sont classés par niveau de compatibilité.
    - La section **Recommandation de nouvelles fonctionnalités** signale de nouvelles fonctionnalités de la plateforme SQL Server cible qui peuvent être utilisées pour la base de données après la migration. Elles sont présentées dans des catégories : Performances, Sécurité et Stockage.

    ![Sélectionner la cible](./media/contoso-migration-assessment/dma-assessment-2.png)

2. Dans **Se connecter à un serveur**, ils entrent le nom de la machine virtuelle exécutant la base de données et les informations d’identification pour y accéder. Ils doivent activer **Faire confiance au certificat de serveur** pour s’assurer de pouvoir accéder à SQL Server. Ensuite, ils cliquent sur **Connexion**.

    ![Sélectionner la cible](./media/contoso-migration-assessment/dma-assessment-3.png)

3. Dans **Ajouter une source** , ils ajoutent la base de données qu’ils souhaitent évaluer, puis cliquent sur **Suivant** pour démarrer l’évaluation.
4. L’évaluation est créée.
    
    ![Créer une évaluation](./media/contoso-migration-assessment/dma-assessment-4.png)

5. Dans **Examiner les résultats**, ils peuvent voir les résultats de l’évaluation.


### <a name="analyze-the-database-assessment"></a>Analyser l’évaluation de la base de données

Les résultats s’affichent dès qu’elles sont disponibles. S’ils résolvent les problèmes, ils doivent cliquer sur **Redémarrer l’évaluation** pour réexécuter l’évaluation.

1. Dans le rapport **Problèmes de compatibilité**, ils vérifient tout problème à chaque niveau de compatibilité. Vous trouverez ci-dessous le mappage des niveaux de compatibilité aux versions SQL Server :

    - 100 : SQL Server 2008/Azure SQL Database
    - 110 : SQL Server 2012/Azure SQL Database
    - 120 : SQL Server 2014/Azure SQL Database
    - 130 : SQL Server 2016/Azure SQL Database
    - 140 : SQL Server 2017/Azure SQL Database

    ![Problèmes de compatibilité](./media/contoso-migration-assessment/dma-assessment-5.png)

2. Dans le rapport **Recommandations de fonctionnalités**, Contoso peut voir les fonctionnalités de performances, de sécurité et de stockage que l’évaluation recommande après la migration. Un vaste éventail de fonctionnalités sont recommandées, y compris OLTP en mémoire et Columnstore, Stretch Database, Always Encrypted, Dynamic Data Masking et Transparent Data Encryption (TDE).

    ![Recommandations de nouvelles fonctionnalités](./media/contoso-migration-assessment/dma-assessment-6.png)

    > [!NOTE]
    > Nous recommandons que Contoso [active TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) pour toutes les bases de données SQL Server, et c’est encore plus critique quand les bases de données sont dans le cloud. TDE ne doit être activé qu’après la migration. Si TDE est déjà activé, vous devrez déplacer le certificat ou la clé asymétrique vers la base de données MASTER du serveur cible. [Plus d’informations](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017)

2. Ils peuvent exporter l’évaluation au format JSON ou CSV.

Notez que, si vous exécutez une évaluation à une échelle plus importante :

- Vous pouvez exécuter plusieurs évaluations simultanément et afficher l’état des évaluations en ouvrant la page **All Assessments** (Toutes les évaluations).
- Vous pouvez [centraliser des évaluations dans une base de données SQL Server](https://docs.microsoft.com/sql/dma/dma-consolidatereports?view=ssdt-18vs2017#import-assessment-results-into-a-sql-server-database).
- Vous pouvez [centraliser des évaluations dans un rapport PowerBI](https://docs.microsoft.com/sql/dma/dma-powerbiassesreport?view=ssdt-18vs2017).


## <a name="step-3-prepare-for-vm-assessment-with-azure-migrate"></a>Étape 3 : préparer l’évaluation de la machine virtuelle avec Azure Migrate

Contoso doit créer un compte VMware qu’Azure Migrate pourra utiliser pour détecter automatiquement les machines virtuelles à évaluer, vérifiez les autorisations pour créer une machine virtuelle, noter les ports qui doivent être ouverts et définir le niveau des paramètres de statistiques.

### <a name="set-up-a-vmware-account"></a>Configurer un compte VMware

 La détection de machine virtuelle nécessite un compte en lecture seule dans vCenter, avec les propriétés suivantes : 

- Type d’utilisateur : au moins un utilisateur en lecture seule.
- Autorisations : objet de centre de données -> Propager vers l’objet enfant, rôle = lecture seule.
- Détails : l’utilisateur est affecté au niveau du centre de données et a accès à tous les objets du centre de données.
- Pour restreindre l’accès, attribuez le rôle **Aucun accès** avec l’objet **Propager vers enfant** aux objets enfants (hôtes vSphere, banques de données, machines virtuelles et réseaux).

### <a name="verify-permissions-to-create-a-vm"></a>Vérifier les autorisations pour créer une machine virtuelle

Vérifier la disponibilité des autorisations nécessaires pour créer une machine virtuelle en important un fichier au format .OVA. [Plus d’informations](https://kb.vmware.com/s/article/1023189?other.KM_Utility.getArticleLanguage=1&r=2&other.KM_Utility.getArticleData=1&other.KM_Utility.getArticle=1&ui-comm-runtime-components-aura-components-siteforce-qb.Quarterback.validateRoute=1&other.KM_Utility.getGUser=1)

### <a name="verify-ports"></a>Vérifier les ports

L’évaluation de Contoso utilise le mappage de dépendance. Cette fonctionnalité nécessite qu’un agent soit installé sur les machines virtuelles que vous évaluez. Cet agent doit être en mesure de se connecter à Azure via le port TCP 443 sur chaque machine virtuelle. [En savoir plus](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid) sur les exigences relatives à la connexion.


### <a name="set-statistics-settings"></a>Définir les paramètres de statistiques

Avant de commencer le déploiement, Contoso doit définir les paramètres de statistiques pour le serveur vCenter sur le niveau 3. Notez les points suivants :

- Après avoir défini le niveau, vous devez attendre au moins un jour avant d’exécuter l’évaluation. Sinon, elle peut ne pas fonctionner comme prévu.
- Si le niveau est supérieur à 3, l’évaluation fonctionnera, mais :
    - Les données de performance des disques et du réseau ne seront pas collectées.
    - Pour le stockage, Azure Migrate recommande un disque standard dans Azure, de la même taille que le disque local.
    - Pour le réseau, pour chaque carte réseau locale, une carte réseau sera recommandée dans Azure.
    - Pour le calcul, Azure Migrate examine le nombre de cœurs et la taille de la mémoire de la machine virtuelle, et recommande une machine virtuelle Azure avec la même configuration. S’il existe plusieurs tailles de machine virtuelle Azure éligibles, celle dont le coût est le plus faible est recommandée.
- [En savoir plus](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing) sur le dimensionnement avec le niveau 3.

Ils définissent le niveau comme suit :

1. Dans le client web vSphere, ouvrez l’instance de serveur vCenter.
2. Dans **Gérer** > **Paramètres** > **Général**, ils cliquent sur **Modifier**.
3. Dans **Statistiques**, ils définissent le niveau des paramètres de statistiques sur **Niveau 3**.

    ![Niveau de statistiques vCenter](./media/contoso-migration-assessment/vcenter-statistics-level.png)



## <a name="step-4-discover-vms"></a>Étape 4 : détecter les machines virtuelles

Pour détecter les machines virtuelles, Contoso crée un projet Azure Migrate. Ils téléchargent et configurent la machine virtuelle du collecteur, puis exécutent celui-ci pour détecter leurs machines virtuelles locales.

### <a name="create-a-project"></a>Création d’un projet

1. Dans le [portail Azure](https://portal.azure.com), ils cherchent **Azure Migrate**, puis créent un projet (ContosoMigration).
2. Ils spécifient un nom de projet, l’abonnement Azure, puis créent un groupe de ressources Azure, **ContosoFailoverRG**. Notez les points suivants :

    - Vous ne pouvez créer un projet Azure Migrate que dans la région Centre-Ouest ou Est des États-Unis.
    - Vous pouvez planifier une migration pour n’importe quel emplacement cible.
    - L’emplacement du projet est utilisé uniquement pour stocker les métadonnées collectées à partir des machines virtuelles locales.

    ![Azure Migrate](./media/contoso-migration-assessment/project-1.png)


### <a name="download-the-collector-appliance"></a>Télécharger l’appliance collecteur

Azure Migrate crée une machine virtuelle locale connue en tant qu’appliance collecteur. Cette machine virtuelle découvre les machines virtuelles VMware sur site et envoie les métadonnées les concernant au service Azure Migrate. Pour configurer l’appliance collecteur, Contoso télécharge un modèle .OVA, puis l’importe dans le serveur vCenter local pour créer la machine virtuelle.

1. Dans le projet Azure Migrate project > **Getting Started (Prise en main)** > **Discover & Assess (Détecter et évaluer)** > **Discover Machines (Détecter les machines)**, ils téléchargent le fichier de modèle .OVA.
2. Ils copient l’ID et la clé du projet. Ceux-ci sont nécessaires pour configurer le collecteur.

    ![Télécharger le fichier .ova](./media/contoso-migration-assessment/download-ova.png)

### <a name="verify-the-collector-appliance"></a>Vérifier l’appliance collecteur

Avant de déployer la machine virtuelle, Contoso vérifie que le fichier .OVA est sûr.

1. Sur la machine sur laquelle le fichier a été téléchargé, ils ouvrent une fenêtre de commande d’administrateur.
2. Ils exécutent la commande suivante pour générer le code de hachage pour le modèle OVA :
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemple d’utilisation : ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Le code de hachage généré doit correspondre aux paramètres ci-après (version 1.0.9.7).

    **Algorithme** | **Valeur de hachage**
    --- | ---
    MD5 | d5b6a03701203ff556fa78694d6d7c35
    SHA1 | f039feaa10dccd811c3d22d9a59fb83d0b01151e
    SHA256 | e5e997c003e29036f62bf3fdce96acd4a271799211a84b34b35dfd290e9bea9c


### <a name="create-the-collector-appliance"></a>Créer l’appliance de collecteur

Contoso peut maintenant importer le fichier téléchargé sur le serveur vCenter et approvisionner la machine virtuelle du serveur de configuration.

1. Dans la console client vSphere, cliquez sur **File (Fichier)** > **Deploy OVF Template (Déployer le modèle OVF)**.

    ![Déployer le modèle OVF](./media/contoso-migration-assessment/vcenter-wizard.png)

2. Dans l’Assistant de déploiement du modèle OVF > **Source**, ils spécifient l’emplacement du fichier .OVA.
3. Dans **Name and Location ((Nom et emplacement))**, ils spécifient un nom convivial pour la machine virtuelle du collecteur et l’emplacement d’inventaire dans lequel la machine virtuelle sera hébergée. Ils spécifient aussi l’hôte ou le cluster sur lequel l’appliance collecteur va s’exécuter.
5. Dans **Storage (Stockage)**, ils spécifient l’emplacement de stockage, et dans **Disk Format (Format de disque)**, la manière dont ils souhaitent approvisionner le stockage.
7. Dans **Network Mapping (Mappage réseau)**, ils spécifient le réseau auquel se connectera la machine virtuelle du collecteur. Le réseau requiert une connexion à Internet pour envoyer des métadonnées vers Azure.
8. Ils examinent les paramètres, puis sélectionnent **Power on after deployment (Mettre sous tension après le déploiement)**> **Finish (Terminer)**. Un message confirmant la réussite est émis après que l’appliance est créée.

### <a name="run-the-collector-to-discover-vms"></a>Exécutez le collecteur pour découvrir les machines virtuelles

Ils exécutent à présent le collecteur pour détecter les machines virtuelles. Notez qu’actuellement le collecteur prend uniquement en charge « Anglais (États-Unis) » comme langue du système d’exploitation et langue de l’interface du collecteur.

1. Dans la console client vSphere > **Open Console (Ouvrir la console)**, ils spécifient la langue, le fuseau horaire et des préférences de mot de passe pour la machine virtuelle du collecteur.
2. Sur le bureau, cliquez sur le raccourci **Run collector (Exécuter le collecteur)**.

    ![Raccourci du collecteur](./media/contoso-migration-assessment/collector-shortcut.png)

4. Dans Azure Migrate Collector > **Set up prerequisites (Paramétrer les conditions préalables)**, ils acceptent les termes du contrat de licence et lisent les informations de tiers.
5. Le collecteur vérifie que la machine virtuelle a accès à Internet, que l’heure est synchronisée, que le service du collecteur s’exécute (il est installé par défaut sur la machine virtuelle). Il installe également VMWare PowerCLI.

    > [!NOTE]
    > Nous partons du principe que la machine virtuelle a un accès direct à Internet, sans proxy.

    ![Vérifier la configuration requise](./media/contoso-migration-assessment/collector-verify-prereqs.png)


5. Dans **Specify vCenter Server details (Spécifier les détails du serveur vCenter)**, ils spécifient le nom (FQDN) ou l’adresse IP du serveur vCenter, ainsi que les informations d’identification en lecture seule utilisées pour la détection.
7. Ils sélectionnent une étendue pour la détection de machine virtuelle. Le collecteur peut uniquement découvrir les machines virtuelles situées dans l’étendue spécifiée. L’étendue peut être définie sur un dossier, un centre de données ou un cluster spécifique. Elle ne doit pas contenir plus de 1 500 machines virtuelles.

    ![Se connecter à vCenter](./media/contoso-migration-assessment/collector-connect-vcenter.png)

6. Dans **Specify migration project (Spécifier un projet de migration)**, ils spécifient l’ID et la clé du projet Azure Migrate qui ont été copiés à partir du portail. Vous pouvez les obtenir à nouveau dans la page **Overview (Vue d’ensemble)** du projet > **Discover Machines (Détecter les machines)**.  

    ![Connexion à Azure](./media/contoso-migration-assessment/collector-connect-azure.png)

7. Dans **View collection progress (Afficher la progression de la collecte)**, Contoso peut surveiller le processus de détection et vérifier que les métadonnées collectées à partir des machines virtuelles s’inscrivent dans l’étendue spécifiée. Le collecteur fournit une durée approximative de la découverte.

    ![Collecte en cours](./media/contoso-migration-assessment/collector-collection-process.png) 



### <a name="verify-vms-in-the-portal"></a>Vérifier les machines virtuelles dans le portail

Une fois la collecte terminée, Contoso vérifie que les machines virtuelles apparaissent dans le portail.

1. Dans le projet Azure Migrate > **Manage (Gérer)** > **Machines**, ils vérifient que les machines virtuelles à détecter apparaissent.

    ![Machines détectées](./media/contoso-migration-assessment/discovery-complete.png)

3. Notez que les agents Azure Migrate ne sont pas encore installés sur les machines. Contoso doit les installer pour afficher les dépendances.

    ![Machines détectées](./media/contoso-migration-assessment/machines-no-agent.png)



## <a name="step-5-prepare-for-dependency-analysis"></a>Étape 5 : préparer l’analyse des dépendances

Pour voir les dépendances entre les machines virtuelles auxquelles ils veulent accéder, ils téléchargent et installent des agents sur les machines virtuelles de l’application. Contoso effectue cette opération sur toutes les machines virtuelles de ses applications, tant Windows que Linux.

### <a name="take-a-snapshot"></a>Prendre un instantané

Avant de modifier les machines virtuelles, ils en font une copie en prenant un instantané avant l’installation des agents.

![Instantané de la machine virtuelle](./media/contoso-migration-assessment/snapshot-vm.png)


### <a name="download-and-install-the-vm-agents"></a>Téléchargement et installation des agents de machines virtuelles

1. Sur la page **Machines**, ils sélectionnent la machine, puis **Installation requise** dans la colonne **Dépendances**.
2. Sur la page **Détecter des machines**, ils procèdent comme suit :
    - Télécharger l’agent MMA et l’agent de dépendance pour chaque machine virtuelle Windows
    - Télécharger l’agent MMA et l’agent de dépendance pour chaque machine virtuelle Linux
3. Ils copient à présent l’ID et la clé de l’espace de travail. Ils en auront besoin lors de l’installation de l’agent MMA.

    ![Téléchargement de l’agent](./media/contoso-migration-assessment/download-agents.png)

### <a name="install-the-agents-on-windows-vms"></a>Installer les agents sur les machines virtuelles Windows

Ils exécutent l’installation sur chaque machine virtuelle.

#### <a name="install-the-mma-on-windows-vms"></a>Installer l’agent MMA sur les machines virtuelles Windows

1. Ils double-cliquent sur l’agent téléchargé.
2. Dans **Dossier de destination**, ils conservent le dossier d’installation par défaut > **Suivant**.
2. Dans **Options d’installation de l’agent**, ils sélectionnent **Connect the agent to Azure Log Analytics (Connecter l’agent à Azure Log Analytics)** > **Suivant**.

    ![Installation de MMA](./media/contoso-migration-assessment/mma-install.png)
    
5. Dans **Azure Log Analytics**, ils collent l’ID et la clé de l’espace de travail copiés à partir du portail. 

    ![Installation de MMA](./media/contoso-migration-assessment/mma-install2.png)

6. Dans **Ready to Install (Prêt pour l’installation)**, ils peuvent désormais installer l’agent MMA.

#### <a name="install-the-dependency-agent-on-windows-vms"></a>Installer l’agent de dépendances sur les machines virtuelles Windows

1. Ils double-cliquent sur l’agent de dépendances téléchargé.
2. Ils acceptent les termes du contrat de licence et attendent que l’installation se termine.

    ![Agent de dépendances](./media/contoso-migration-assessment/dependency-agent.png)


### <a name="install-the-agents-on-linux-vms"></a>Installer les agents sur les machines virtuelles Linux

Ils exécutent l’installation sur chaque machine virtuelle.

#### <a name="install-the-mma-on-linux-vms"></a>Installer l’agent MMA sur les machines virtuelles Linux

1. Ils installent la bibliothèque de ctypes Python sur chaque machine virtuelle en utilisant la commande suivante : **sudo apt-get install python-ctypeslib**.
2. Il doivent exécuter la commande pour installer l’agent MMA en tant que racine.  Pour qu’il devienne racine, il faut exécuter la commande suivante et entrer le mot de passe racine : **sudo -i**.
3. Ils installent à présent l’agent MMA.
    - Insérez l’ID et la clé corrects de votre espace de travail dans la commande.
    - Ces commandes sont de type 64 bits.
    - L’**ID d’espace de travail** et la **Clé primaire** sont disponibles sur le portail OMS > **Paramètres**, sous l’onglet **Sources connectées**.
    - Ils exécutent les commandes suivantes pour télécharger l’agent OMS, valider la somme de contrôle, puis installer et intégrer l’agent.

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w 6b7fcaff-7efb-4356-ae06-516cacf5e25d -s k7gAMAw5Bk8pFVUTZKmk2lG4eUciswzWfYLDTxGcD8pcyc4oT8c6ZRgsMy3MmsQSHuSOcmBUsCjoRiG2x9A8Mg==
    ```
 


#### <a name="install-the-dependency-agent-on-linux-vms"></a>Installer l’agent de dépendances sous Linux

Une fois l’agent MMA installé, Contoso peut installer l’agent de dépendances sur les machines virtuelles Linux.

1. L’agent de dépendances (Dependency Agent) est installé sur les ordinateurs Linux avec InstallDependencyAgent-Linux64.bin, un script shell avec un fichier binaire à extraction automatique. Ils peuvent exécuter le fichier à l’aide de sh ou ajouter des autorisations d’exécution au fichier lui-même.

2. Ils installent l’agent de dépendances Linux en tant que racine :

    ```
    wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin && sudo sh InstallDependencyAgent-Linux64.bin -s
    ```


## <a name="step-6-run-and-analyze-the-vm-assessment"></a>Étape 6 : exécuter et analyser l’évaluation de la machine virtuelle

Contoso peut à présent vérifier les dépendances de machine et créer un groupe. Ensuite, ils exécutent l’évaluation pour le groupe.

### <a name="verify-dependencies-and-create-a-group"></a>Vérifier les dépendances et créer un groupe


1. Pour les machines à analyser, ils cliquent sur **Afficher les dépendances**.

    ![Afficher les dépendances de machine](./media/contoso-migration-assessment/view-machine-dependencies.png)

2. Pour la machine virtuelle SQLVM, la carte des dépendances affiche les informations suivantes :

    - Groupes de processus/processus avec des connexions réseau actives s’exécutant sur la machine virtuelle SQLVM au cours d’une période spécifiée (une heure par défaut).
    - Connexion TCP entrantes (client) et sortantes (serveur) vers et depuis toutes les machines dépendantes.
    - Les machines dépendantes avec les agents Azure Migrate installés sont affichées dans des zones distinctes.
    - Les machines sans agents installés affichent des informations de port et d’adresse IP.

3. Pour les machines avec l’agent installé (WEBVM), ils cliquent sur la zone de la machine pour voir plus d’informations, notamment le nom de domaine complet (FQDN), le système d’exploitation, et l’adresse MAC.

    ![Afficher les dépendances de groupe](./media/contoso-migration-assessment/sqlvm-dependencies.png)

4. À présent, ils sélectionnent les machines virtuelles à ajouter au groupe (SQLVM et WEBVM).  Ils peuvent utiliser CTRL+clic pour sélectionner plusieurs machines virtuelles.
5. Ils cliquent sur **Créer un groupe**, puis spécifient un nom (smarthotelapp).

> [!NOTE]
    > Pour afficher des dépendances plus précises, vous pouvez étendre l’intervalle de temps. Vous pouvez sélectionner une durée spécifique ou des dates de début et de fin.


### <a name="run-an-assessment"></a>Exécuter une évaluation


1. Sur la page **Groupes** , ils ouvrent le groupe (smarthotelapp), puis cliquent sur **Créer une évaluation**.

    ![Créer une évaluation](./media/contoso-migration-assessment/run-vm-assessment.png)

2. L’évaluation s’affiche sur la page **Gérer** > **Évaluations**.

Contoso a utilisé les paramètres d’évaluation par défaut, mais vous pouvez personnaliser les paramètres. [Plus d’informations](how-to-modify-assessment.md)



### <a name="analyze-the-vm-assessment"></a>Analyser l’évaluation de la machine virtuelle

Une évaluation Azure Migrate inclut des informations sur la compatibilité des machines virtuelles locales pour Azure, la taille idéale suggérée pour la machine virtuelle Azure, et le coûts mensuels estimés d’Azure.

![Rapport d’évaluation](./media/contoso-migration-assessment/assessment-overview.png)

#### <a name="review-confidence-rating"></a>Examiner le niveau de confiance

![Affichage de l’évaluation](./media/contoso-migration-assessment/assessment-display.png)

Une évaluation obtient un niveau de confiance compris entre 1 étoile et 5 étoiles (1 étoile étant le niveau le plus bas et 5 étoiles le niveau le plus élevé).
- Le niveau de confiance est assigné à une évaluation en fonction de la disponibilité des points de données nécessaires pour calculer l’évaluation.
- Il vous permet d’évaluer la fiabilité des recommandations de taille fournies par Azure Migrate.
- Le niveau de confiance est utile quand vous effectuez un *dimensionnement basé sur les performances*, car Azure Migrate pourrait ne pas disposer de suffisamment de points de données pour effectuer un dimensionnement basé sur l’utilisation. Pour le *dimensionnement local*, le niveau de confiance s’élève toujours à 5 étoiles, car Azure Migrate dispose de tous les points de données nécessaires au dimensionnement de la machine virtuelle.
- Selon le pourcentage de points de données disponibles, le niveau de confiance pour l’évaluation est fourni :

   **Disponibilité des points de données** | **Niveau de confiance**
   --- | ---
   0 %-20 % | 1 étoile
   21 %-40 % | 2 étoiles
   41 %-60 % | 3 étoiles
   61 %-80 % | 4 étoiles
   81 %-100 % | 5 étoiles

#### <a name="verify-readiness"></a>Vérifier l’état de préparation

![Préparation de l’évaluation](./media/contoso-migration-assessment/azure-readiness.png)  

Le rapport d’évaluation affiche les informations résumées dans le tableau. Notez que pour afficher le dimensionnement basé sur les performances, Azure Migrate a besoin des informations suivantes. Si ces informations ne peuvent pas être collectées, l’évaluation du dimensionnement risque de ne pas être suffisamment précise.

- Données d’utilisation pour le processeur et la mémoire.
- E/S par seconde en lecture et en écriture de chaque disque attaché à la machine virtuelle.
- Informations entrantes/sortantes du réseau pour chaque carte réseau attachée à la machine virtuelle.


**Paramètre** | **Indication** | **Détails**
--- | --- | ---
**Préparation des machines virtuelles pour Azure** | Indique si la machine virtuelle est prête pour la migration | États possibles :</br><br/>- Prête pour Azure<br/><br/>- Prête sous conditions <br/><br/>- Pas prête pour Azure<br/><br/>- État de préparation inconnue<br/><br/> Si une machine virtuelle n’est pas prête, nous allons vous présenter une procédure de correction.
**Taille de la machine virtuelle Azure** | Pour les machines virtuelles prêtes, nous vous recommandons une taille de machine virtuelle Azure. | La recommandation de taille dépend des propriétés de l’évaluation :<br/><br/>- Si vous avez utilisé un dimensionnement basé sur les performances, le dimensionnement prend en compte l’historique des performances des machines virtuelles.<br/><br/>- Si vous avez utilisé un dimensionnement local, le dimensionnement est basé sur la taille de la machine virtuelle locale, et les données d’utilisation ne sont pas utilisées.
**Outil suggéré** | Puisque nos machines exécutent les agents, Azure Migrate examine les processus exécutés à l’intérieur de la machine et indique si celle-ci est une machine de base de données ou non.
**VM information** (Informations de machine virtuelle) | Le rapport affiche les paramètres de la machine virtuelle locale, y compris des informations sur le système d’exploitation, le type de démarrage, le disque et le stockage.


#### <a name="review-monthly-cost-estimates"></a>Examiner les estimations des coûts mensuels

Cette vue affiche le coût total du calcul et du stockage de l’exécution des machines virtuelles dans Azure avec les détails pour chaque machine.

![Préparation de l’évaluation](./media/contoso-migration-assessment/azure-costs.png)

- Les estimations de coûts sont calculées à l’aide des recommandations de taille pour une machine.
- L’estimation des coûts mensuels de calcul et de stockage est agrégée pour toutes les machines virtuelles dans le groupe.


## <a name="clean-up-after-assessment"></a>Nettoyer après évaluation

- Une fois l’évaluation terminée, Contoso conserve l’appliance de migration Azure pour des évaluations ultérieures.
- Ils désactivent la machine virtuelle VMware. Ils la redémarreront lors de l’évaluation de machines virtuelles supplémentaires.
- Ils conserveront le projet Migration de Contoso dans Azure.  Il est actuellement déployé dans le groupe de ressources ContosoFailoverRG, dans la région USA Est Azure.
-  Licence d’évaluation de la machine virtuelle du collecteur a une validité de 180 jours. Au-delà de cette limite, ils devront de nouveau télécharger et installer le collecteur.


## <a name="conclusion"></a>Conclusion

Dans ce scénario, Contoso a évalué la base de données de son application SmartHotel à l’aide de l’outil DMA, et les machines virtuelles locales à l’aide du service Azure Migrate. Ils ont examiné les évaluations pour s’assurer que les ressources locales sont prêtes pour la migration vers Azure.

## <a name="next-steps"></a>Étapes suivantes

Dans l’article suivant de cette série, Contoso ré-héberge son application SmartHotel dans Azure avec une migration lift-and-shift. Contoso migre le serveur frontal WEBVM pour l’application à l’aide d’Azure Site Recovery, et la base de données de l’application vers une instance Azure SQL Managed Instance en utilisant le service Database Migration Service. [Commencez](contoso-migration-rehost-vm-sql-managed-instance.md) avec ce déploiement.
