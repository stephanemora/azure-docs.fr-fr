---
title: Refactoriser une application Contoso en la migrant vers des machines virtuelles Azure et vers un groupe de disponibilité AlwaysOn SQL Server | Microsoft Docs
description: Découvrez comment Contoso réhéberge une application locale en la migrant vers une application web Azure Container et une base de données Azure SQL Server.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: raynew
ms.openlocfilehash: f4a348815ef058cb795ed12e8f118b494650a555
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39005188"
---
# <a name="contoso-migration-refactor-an-on-premises-app-to-an-azure-web-app-and-azure-sql-database"></a>Migration de Contoso : refactoriser une application locale vers une application web Azure et une base de données SQL Azure

Cet article explique comment Contoso refactorise son application SmartHotel dans Azure. Ils migrent la machine virtuelle frontale de l’application vers une application Web Azure, et la base de données de l'application vers une base de données SQL Azure.

Ce document fait partie d’une série d’articles qui montrent comment la société fictive Contoso migre ses ressources locales vers le cloud Microsoft Azure. La série comprend des informations générales et des scénarios qui illustrent comment configurer une infrastructure de migration, évaluer des ressources locales pour la migration et exécuter différents types de migration. Les scénarios croissant en complexité, nous ajouterons des articles au fil du temps.

**Article** | **Détails** | **État**
--- | --- | ---
[Article 1 : vue d’ensemble](contoso-migration-overview.md) | Fournit une vue d’ensemble de la stratégie de migration de Contoso, de la série d’articles et des exemples d’application que nous utilisons. | Disponible
[Article 2 : Déployer une infrastructure Azure](contoso-migration-infrastructure.md) | Décrit comment Contoso prépare son infrastructure locale et son infrastructure Azure pour la migration. La même infrastructure est utilisée pour tous les articles de migration. | Disponible
[Article 3 : Évaluer les ressources locales](contoso-migration-assessment.md)  | Montre comment Contoso évalue une application à deux niveaux locale SmartHotel s’exécutant sur VMware. Contoso évalue les machines virtuelles de l’application avec le service [Azure Migrate](migrate-overview.md) et la base de données SQL Server de l’application avec [l’Assistant Migration de données Azure](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Disponible
[Article 4 : Réhéberger une application sur des machines virtuelles Azure et une instance SQL Managed Instance](contoso-migration-rehost-vm-sql-managed-instance.md) | Montre comment Contoso exécute une migration lift-and-shift vers Azure pour l’application SmartHotel. Elle migre la machine virtuelle frontale de l’application à l’aide d’[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), et la base de données de l’application vers une instance SQL Managed Instance à l’aide du [service Azure Database Migration](https://docs.microsoft.com/azure/dms/dms-overview). | Disponible
[Article 5 : Réhéberger une application sur des machines virtuelles Azure](contoso-migration-rehost-vm.md) | Montre comment Contoso migre les machines virtuelles de l’application SmartHotel en utilisant uniquement Site Recovery. | Disponible
[Article 6 : Réhéberger une application sur des machines virtuelles et un groupe de disponibilité AlwaysOn SQL Server (cet article)](contoso-migration-rehost-vm-sql-ag.md) | Montre comment Contoso migre l’application SmartHotel. Elle utilise Site Recovery pour migrer la machine virtuelle de l’application, et Database Migration Service pour migrer la base de données de l’application vers un cluster SQL Server protégé par un groupe de disponibilité AlwaysOn. | Disponible
[Article 7 : Réhéberger une application Linux sur des machines virtuelles Azure](contoso-migration-rehost-linux-vm.md) | Montre comment Contoso effectue une migration lift-and-shift de l’application osTicket Linux sur des machines virtuelles Azure à l’aide de Site Recovery. | Disponible
[Article 8 : ré-héberger une application Linux sur des machines virtuelles Azure et Azure MySQL Server](contoso-migration-rehost-linux-vm-mysql.md) | Montre comment Contoso migre l’application osTicket Linux vers des machines virtuelles Azure à l’aide de Site Recovery, et migre la base de données de l’application vers une instance Azure MySQL Server à l’aide de MySQL Workbench. | Disponible
Article 9 : Refactoriser une application vers une application web Azure et une base de données SQL Azure | Montre comment Contoso migre l’application SmartHotel vers une application web Azure, et migre la base de données d’application vers une instance de serveur SQL Azure. | Cet article
[Article 10 : Refactoriser une application Linux sur Azure Web Apps et un serveur MySQL Azure](contoso-migration-refactor-linux-app-service-mysql.md) | Montre comment Contoso migre l’application Linux osTicket vers Azure Web Apps dans plusieurs sites intégrés avec GitHub pour assurer une livraison continue. Ils migrent la base de données d’application vers une instance MySQL Azure. | Disponible
[Article 11 : Refactoriser TFS sur VSTS](contoso-migration-tfs-vsts.md) | Montre comment Contoso migre son déploiement de Team Foundation Server (TFS) local vers Visual Studio Team Services (VSTS) dans Azure. | Disponible
[Article 12 : Réarchitecturer une application sur des conteneurs Azure et Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Montre comment Contoso migre et réarchitecture son application SmartHotel vers Azure. Il réarchitecturent la couche web d’application en tant que conteneur Windows, et la base de données d’application en une base de données Azure SQL Database. | Disponible
[Article 13 : Régénérer une application dans Azure](contoso-migration-rebuild.md) | Montre comment Contoso régénère son application SmartHotel à l’aide d’une série de fonctionnalités et services Azure, dont App Services, Azure Kubernetes, Azure Functions, Cognitive services et Cosmos DB. | Disponible


Dans cet article, Contoso migre Windows à deux niveaux. Application NET SmartHotel s’exécutant sur des machines virtuelles VMware vers Azure. Si vous souhaitez utiliser cette application, elle est disponible en open source et vous pouvez la télécharger à partir de [GitHub](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>Axes stratégiques

L’équipe informatique a travaillé en étroite collaboration avec ses partenaires commerciaux pour comprendre le résultat qu’ils souhaitent obtenir avec cette migration :

- **Répondre à la croissance** : Contoso étant en croissance, son infrastructure et ses systèmes locaux sont sous pression.
- **Augmenter l’efficacité** : Contoso doit supprimer les procédures inutiles et rationaliser les processus pour les développeurs et les utilisateurs.  L’entreprise a besoin d’une informatique rapide et doit éviter de perdre du temps ou d’argent en répondant plus rapidement aux exigences des clients.
- **Augmenter l’agilité** : l’informatique de Contoso doit être plus réactive aux besoins de l’entreprise. Elle doit être en mesure de réagir plus rapidement que l’évolution du marché pour réussir dans une économie mondiale.  L’informatique ne doit pas devenir une entrave à l’activité.
- **Mise à l’échelle** : à mesure que l’entreprise croît, l’informatique de Contoso doit fournir des systèmes capables de croître au même rythme.
- **Coûts**  : Contoso souhaite réduire les coûts de licence.

## <a name="migration-goals"></a>Objectifs de la migration

L’équipe cloud de Contoso a épinglé les objectifs de cette migration. Ces objectifs ont été utilisés pour déterminer la meilleure méthode de migration.

**Configuration requise** | **Détails**
--- | --- 
**Application** | L’application dans Azure restera aussi critique qu’aujourd'hui.<br/><br/> Elle doit offrir les mêmes performances qu’actuellement dans VMWare.<br/><br/> Ils ne souhaitent pas investir dans l’application. Pour l’instant, ils souhaitent simplement la migrer en toute sécurité vers le cloud.<br/><br/> Ils souhaitent cesser de prendre en charge Windows Server 2008 R2, sur lequel l’application s’exécute actuellement.<br/><br/> Ils souhaitent passer de SQL Server 2008 R2 à une plateforme de base de données PaaS moderne afin de réduire les besoins de gestion.<br/><br/> Contoso souhaite autant que possible tirer parti de ses investissements en licences SQL Server et en Software Assurance.<br/><br/> En outre, Contoso souhaite atténuer l’incidence de l’unique point de défaillance sur la couche Web.
**Limitations** | L’application consiste en une application ASP.NET et un service d'application Windows Communication Foundation (WCF) s’exécutant sur la même machine virtuelle. Ils veulent fractionner cela en deux applications web utilisant Azure App Service. 
**Microsoft Azure** | Ils souhaitent migrer l’application vers Azure, mais ne souhaitent pas l’exécuter sur des machines virtuelles. Ils souhaitent tirer parti des services PaaS Azure pour les couches Web et Données. 

## <a name="solution-design"></a>Conception de la solution

Après avoir épinglé les objectifs et exigences, Contoso conçoit et examine une solution de déploiement, et identifie le processus de migration, y compris les services Azure à utiliser pour celle-ci.

### <a name="current-app"></a>Application en cours

- L’application SmartHotel locale est hiérarchisée sur deux machines virtuelles (WEBVM et SQLVM).
- Les machines virtuelles sont situées sur un hôte VMware ESXi **contosohost1.contoso.com** (version 6.5).
- L’environnement VMware est géré par le serveur vCenter Server 6.5 (**vcenter.contoso.com**) s’exécutant sur une machine virtuelle.
- Contoso dispose d’un centre de données local (contoso-datacenter), avec un contrôleur de domaine local (**contosodc1**).
- Les machines virtuelles locales dans le centre de données Contoso seront désaffectées une fois la migration terminée.


### <a name="proposed-solution"></a>Solution proposée

- Pour la couche Base de données de l’application, Contoso a comparé Azure SQL Database à SQL Server en se basant sur [cet article](https://docs.microsoft.com/azure/sql-database/sql-database-features). Ils choisi d’utiliser Azure SQL Database pour plusieurs raisons :
    - Azure SQL Database est un service géré de base de données relationnelle. Il offre des performances prévisibles à plusieurs niveaux de service, et ne nécessite pratiquement aucune administration. Ses avantages sont une extensibilité dynamique sans aucun temps d’arrêt, une optimisation intelligente intégrée, ainsi qu’une extensibilité et une disponibilité globales.
    - Ils peuvent tirer parti de l’Assistant Migration de données (DMA) léger pour évaluer et migrer la base de données locale vers SQL Azure.
    - Grâce à Software Assurance, ils peuvent échanger leurs licences existantes pour bénéficier de tarifs réduits sur SQL Database, en utilisant Azure Hybrid Benefit pour SQL Server. Cela pourrait leur permettre de réaliser jusqu'à 30 % d’économies.
    - Azure SQL Database fournit un certain nombre de fonctionnalités de sécurité, dont le chiffrement permanent, le masquage dynamique des données, la sécurité au niveau des lignes et la détection des menaces.
- Pour la couche Web de l’application, ils ont décidé d’utiliser Azure App Service. Ce service PaaS permet de déployer l’application avec seulement quelques changements de configuration. Ils vont utiliser Visual Studio pour apporter les modifications et déployer deux applications web. L’une pour le site web, et l’autre pour le service WCF.
  
### <a name="solution-review"></a>Examen de la solution
Contoso évalue la conception proposée en dressant une liste des avantages et inconvénients.

**Considération** | **Détails**
--- | ---
**Avantages** | Le code de l’application SmartHotel ne nécessitera pas de modification avant sa migration vers Azure.<br/><br/> Ils peuvent tirer parti de leur investissement dans Software Assurance en utilisant Azure Hybrid Benefit tant pour SQL Server que pour Windows Server.<br/><br/> Après la migration, ils n’auront plus besoin de prendre en charge Windows Server 2008 R2. [Plus d’informations](https://support.microsoft.com/lifecycle)<br/><br/> Ils peuvent configurer la couche Web de l’application avec plusieurs instances, de façon à ce qu’elle ne soit plus un point de défaillance unique.<br/><br/> Ils ne seront plus dépendants du vieillissement de SQL Server 2008 R2.<br/><br/> Azure SQL Database répond aux exigences techniques de Contoso. Ils ont évalué la base de données locale à l’aide de l’Assistant Migration de données, et découvert qu’elle était compatible.<br/><br/> Azure SQL Database intègre une tolérance de panne que Contoso n’aura pas à configurer. Cela signifie que la couche Données n’est plus un point de basculement unique.
**Inconvénients** | Azure App Services ne prend en charge le déploiement que d’une seule application pour chaque application Web. Cela signifie que deux applications web doit être approvisionnées (l’une pour le site web, et l’autre pour le service WCF).<br/><br/> En cas d’utilisation de l’Assistant Migration de données au lieu de Database Migration Service pour migrer sa base de données, Contoso n’aura pas d’infrastructure prête pour la migration de bases de données à grande échelle. Ils doivent créer une autre région pour garantir le basculement en cas d’indisponibilité de la région primaire.

## <a name="proposed-architecture"></a>Architecture proposée

![Architecture du scénario](media/contoso-migration-refactor-web-app-sql/architecture.png) 


### <a name="migration-process"></a>Processus de migration

1. Contoso approvisionne une instance SQL Azure et y migre la base de données SmartHotel.
2. Ils approvisionnent et configurent des Web Apps, puis déploient l’application SmartHotel sur celles-ci.

    ![Processus de migration](media/contoso-migration-refactor-web-app-sql/migration-process.png) 

### <a name="azure-services"></a>Services Azure

**Service** | **Description** | **Coût**
--- | --- | ---
[Assistant Migration de données Microsoft (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Ils vont utiliser DMA pour évaluer et détecter des problèmes de compatibilité susceptibles d’affecter les fonctionnalités de base de données dans Azure. DMA évalue la parité des fonctionnalités entre SQL sources et cibles, et recommande des améliorations des performances et de la fiabilité. | Cet outil est téléchargeable gratuitement.
[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) | Service entièrement géré de base de données cloud relationnelle et intelligente. | Coût en fonction des fonctionnalités, du débit et de la taille. [Plus d’informations](https://azure.microsoft.com/pricing/details/sql-database/managed/)
[Azure App Services - Web Apps](https://docs.microsoft.com/azure/app-service/app-service-web-overview) | Créez des applications cloud performantes en utilisant une plateforme entièrement gérée. | Coût en fonction de la taille, de l’emplacement et de la durée d’utilisation. [Plus d’informations](https://azure.microsoft.com/pricing/details/app-service/windows/)

## <a name="prerequisites"></a>Prérequis

Voici ce que vous (et Contoso) devez faire dans le cadre de ce scénario :

**Configuration requise** | **Détails**
--- | ---
**Abonnement Azure** | Vous devez avoir déjà créé un abonnement lorsque vous avez effectué l’évaluation dans le premier article de cette série. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Si vous créez un compte gratuit, vous êtes l’administrateur de votre abonnement et pouvez effectuer toutes les actions.<br/><br/> Si vous utilisez un abonnement existant et que vous n’êtes pas l’administrateur, vous devez collaborer avec l’administrateur pour qu’il vous donne les autorisations Propriétaire ou Contributeur.
**Infrastructure Azure** | [Découvrez comment](contoso-migration-infrastructure.md) Contoso configure une infrastructure Azure.


## <a name="scenario-steps"></a>Étapes du scénario

Voici comment Contoso exécutera la migration :

> [!div class="checklist"]
> * **Étape 1 : approvisionner une instance SQL Database dans Azure** : Contoso approvisionne une instance SQL dans Azure. Une fois le site web de l’application migré vers Azure, l’application web du service WCF pointe sur cette instance.
> * **Étape 2 : migrer la base de données avec le DMA** : ils migrent la base de données de l’application avec l’Assistant Migration de données.
> * **Étape 3 : approvisionner les Web Apps** : ils approvisionnent les deux applications web.
> * **Étape 4 : configurer les chaînes de connexion** : ils configurent des chaînes de connexion afin que l’application web de la couche Web, l’application web du service WCF et l’instance SQL puissent communiquer.
> * **Étape 5 : publier les applications web** : en guise de dernière étape, Contoso publie les applications sur Azure.


## <a name="step-1-provision-an-azure-sql-database"></a>Étape 1 : Approvisionner Azure SQL Database

1. Ils choisissent de créer une base de données SQL dans Azure. 

    ![Approvisionner SQL](media/contoso-migration-refactor-web-app-sql/provision-sql1.png)

2. Ils spécifient un nom de base de données correspondant à la base de données en cours d’exécution sur la machine virtuelle locale (**SmartHotel.Registration**). Ils placent la base de données dans le groupe de ressources ContosoRG. Il s’agit du groupe de ressources qu’ils utilisent pour les ressources de production dans Azure.

    ![Approvisionner SQL](media/contoso-migration-refactor-web-app-sql/provision-sql2.png)

3. Ils configurent une nouvelle instance SQL Server (**sql-smarthotel-eus2**) dans la région primaire.

    ![Approvisionner SQL](media/contoso-migration-refactor-web-app-sql/provision-sql3.png)

4. Ils définissent le niveau tarifaire correspondant à leurs besoins en matière de serveur et de base de données. Et ils choisissent d’économiser de l’argent avec Azure Hybrid Benefit, car ils ont déjà une licence SQL Server.
5. Pour le dimensionnement, ils utilisent un achat basé sur v-Core, et définissent les limites de leurs exigences attendues.

    ![Approvisionner SQL](media/contoso-migration-refactor-web-app-sql/provision-sql4.png)

6. Ils créent ensuite l’instance de base de données.

    ![Approvisionner SQL](media/contoso-migration-refactor-web-app-sql/provision-sql5.png)

7. Une fois l’instance créée, ils ouvrent la base de données et notent les informations dont ils auront besoin quand ils utiliseront l’Assistant Migration de données pour la migration.

    ![Approvisionner SQL](media/contoso-migration-refactor-web-app-sql/provision-sql6.png)


**Besoin de plus d’aide ?**

- [Obtenir de l’aide](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) pour l’approvisionnement d’une base de données SQL.
- [En savoir plus sur](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools) les limites de ressources v-Core.


## <a name="step-2-migrate-the-database-with-dma"></a>Étape 2 : migrer la base de données avec le DMA

Contoso va migrer la base de données SmartHotel à l’aide du DMA.

### <a name="install-dma"></a>Installer le DMA

1. Télécharger l’outil à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=53595) sur la machine virtuelle locale SQL Server (**SQLVM**).
2. Exécuter le programme d’installation (DownloadMigrationAssistant.msi) sur la machine virtuelle.
3. Dans la page **Terminer**, sélectionner **Lancer l’Assistant Migration de données Microsoft** avant la fin de l’exécution de l’Assistant.

### <a name="migrate-the-database-with-dma"></a>Migrer la base de données avec le DMA

1. Dans le DMA, créer un projet (**SmartHotelDB**), puis sélectionner **Migration** 
2. Ils spécifient le type de serveur source en tant que **SQL Server**, et la cible en tant que **Azure SQL Database**. 

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-1.png)

3. Dans les détails de la migration, ils ajoutent **SQLVM** en tant que serveur source, et la base de données **SmartHotel.Registration**. 

     ![DMA](media/contoso-migration-refactor-web-app-sql/dma-2.png)

4. Ils reçoivent un message d’erreur qui semble être associé à l’authentification. Toutefois, après examen, le problème résulte de la présence d’un point (.) dans le nom de la base de données. Pour résoudre ce problème, ils décident d’approvisionner une nouvelle base de données SQL en utilisant le nom **SmartHotel-Registration**. Lorsqu’ils réexécutent le DMA, ils peuvent sélectionner **SmartHotel-Registration** et continuer avec l’Assistant.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-3.png)

5. Dans **Sélectionner des objets**, ils sélectionnent les tables de base de données et génèrent un script SQL.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-4.png)

6. Après que DMS a créé le script, ils cliquent sur **Déployer le schéma**.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-5.png)

7. DAM vérifie que le déploiement a réussi.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-6.png)

8. À présent, ils commencent la migration.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-7.png)

9. Une fois la migration terminée, Contoso peut vérifier que la base de données est en cours d’exécution sur l’instance SQL Azure.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-8.png)

10. Ils suppriment la base de données SQL supplémentaire **SmartHotel.Registration** dans le portail Azure.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-9.png)


## <a name="step-3-provision-web-apps"></a>Étape 3 : approvisionner les Web Apps

La base de données étant migrée, Contoso peut approvisionner les deux applications web.

1. Ils sélectionnent **Application web** dans le portail.

    ![Application web](media/contoso-migration-refactor-web-app-sql/web-app1.png)

2. Ils fournissent un nom pour l’application (**SHWEB-EUS2**), exécutent celle-ci sur Windows, puis la placent dans le groupe de ressources de production **ContosoRG**. Ils créent ensuite un service et un plan d’application.

    ![Application web](media/contoso-migration-refactor-web-app-sql/web-app2.png)

3. Une fois l’application web approvisionnée, ils répètent le processus pour créer une application web pour le service WCF (**SHWCF-EUS2**)

    ![Application web](media/contoso-migration-refactor-web-app-sql/web-app3.png)

4. Cela fait, ils accèdent à l’adresse des applications pour vérifier si celles-ci ont été correctement créées.

## <a name="step-4-configure-connection-strings"></a>Étape 4 : configurer les chaînes de connexion

Contoso doit s’assurer que les applications web et la base de données peuvent communiquer. Pour ce faire, ils configurent des chaînes de connexion dans le code et dans les applications web.

1. Dans l’application web pour le service WCF (**SHWCF-EUS2**) > **Paramètres** > **Paramètres de l'application**, ils ajoutent une nouvelle chaîne de connexion nommée **DefaultConnection**.
2. La chaîne de connexion est extraite de la base de données **SmartHotel-Registration**, et doit être mise à jour avec les informations d’identification correctes.

    ![Chaîne de connexion](media/contoso-migration-refactor-web-app-sql/string1.png)

3. À l’aide de Visual Studio, Contoso ouvre le fichier solution à partir du dossier **SmartHotel360-internal-booking-apps**. La section **connectionStrings** du fichier web.config pour le service WCF SmartHotel.Registration.Wcf doit être mise à jour avec la chaîne de connexion.

     ![Chaîne de connexion](media/contoso-migration-refactor-web-app-sql/string2.png)

4. La section **client** du fichier web.config pour SmartHotel.Registration.Web doit être modifiée de façon à ce qu’elle pointe vers le nouvel emplacement du service WCF. Il s’agit de l’URL de l’application web WCF qui héberge le point de terminaison de service.

    ![Chaîne de connexion](media/contoso-migration-refactor-web-app-sql/strings3.png)


## <a name="step-5-publish-web-apps"></a>Étape 5 : publier les applications web

En guise de dernière étape, Contoso publie les applications web sur Azure.

1. Dans Visual Studio, ils cliquent avec le bouton droit sur le projet SmartHotel.REgistration.Wcf > **Publier**.

    ![Publier](media/contoso-migration-refactor-web-app-sql/publish-web1.png)

2. Ils commencent à publier.

    ![Publier](media/contoso-migration-refactor-web-app-sql/publish-web2.png)

3. Ils sélectionnent un App Service existant, car ils ont déjà créé l’application web.

    ![Publier](media/contoso-migration-refactor-web-app-sql/publish-web3.png)

4. Une fois l’application WCF sélectionnée, Visual Studio la déploie.

    ![Publier](media/contoso-migration-refactor-web-app-sql/publish-web4.png)

5. Ils répètent ensuite le processus pour publier l’application web SmartHotel.Registration.Web.

    ![Publier](media/contoso-migration-refactor-web-app-sql/publish-web5.png)


À ce stade, la migration de l’application vers Azure a réussi.

## <a name="clean-up-after-migration"></a>Nettoyer après la migration

Après la migration, Contoso doit effectuer les étapes de nettoyage suivantes :  

- Supprimer les machines virtuelles locales de l’inventaire vCenter.
- Supprimer les machines virtuelles des travaux de sauvegarde locale.
- Mettre à jour la documentation interne afin qu’elle indique les nouveaux emplacements pour l’application SmartHotel. Afficher la base de données en cours d’exécution dans la base de données SQL Azure et le serveur frontal en cours d’exécution dans les deux applications web.
- Passer en revue toutes les ressources qui interagissent avec les machines virtuelles désactivées, et mettre à jour les paramètres ou la documentation appropriés afin de refléter la nouvelle configuration.


## <a name="review-the-deployment"></a>Examiner le déploiement

Avec les ressources migrées dans Azure, Contoso doit rendre sa nouvelle infrastructure entièrement opérationnelle et la sécuriser.

### <a name="security"></a>Sécurité

- Contoso doit s’assurer que sa nouvelle base de données **SmartHotel-Registration** est sécurisée. [Plus d’informations](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview)
- En particulier, ils doivent mettre à jour les applications web pour utiliser le protocole SSL avec des certificats.

### <a name="backups"></a>Sauvegardes

- Contoso doit examiner les exigences de sauvegarde pour Azure SQL Database. [Plus d’informations](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)
- Ils devraient envisager d’implémenter des groupes de basculement afin de fournir un basculement régional pour la base de données. [Plus d’informations](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview)
- Contoso doit envisager de déployer l’application web dans la région principale USA Est 2, ainsi que dans la région USA Centre à des fins de résilience. Ils ont pu configurer Traffic Manager pour assurer le basculement en cas de pannes régionales.

### <a name="licensing-and-cost-optimization"></a>Gestion des licences et optimisation des coûts

- Une fois toutes les ressources déployées, Contoso doit affecter des balises Azure basées sur la [planification de son infrastructure](contoso-migration-infrastructure.md#set-up-tagging).
- Toutes les licences sont intégrées dans le coût des services PaaS que Contoso consomme. Cela sera déduit de l’Accord Entreprise.
1. Contoso va activer Azure Cost Management sous licence de Cloudyn, une filiale de Microsoft. Il s’agit d’une solution de gestion des coûts multicloud qui aide à utiliser et à gérer Azure ainsi que d’autres ressources cloud.  [En savoir plus](https://docs.microsoft.com/azure/cost-management/overview) sur Azure Cost Management. 

## <a name="conclusion"></a>Conclusion

Cet article décrit comment Contoso a refactorisé l’application SmartHotel dans Azure en migrant la machine virtuelle frontale de celle-ci vers deux Web Apps Azure. Ils ont migré la base de données d’application vers une base de données SQL Azure.






