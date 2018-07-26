---
title: Réarchitecturer une application Contoso dans un conteneur Azure et Azure SQL Database | Microsoft Docs
description: Découvrez comment Contoso réarchitecture une application dans des conteneurs Azure et Azure SQL Database.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/05/2018
ms.author: raynew
ms.openlocfilehash: 7146865270accb73981b09be6409180c4ef1440f
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39003195"
---
# <a name="contoso-migration-rearchitect-an-on-premises-app-to-an-azure-container-and-azure-sql-database"></a>Migration de Contoso : Réarchitecturer une application locale vers un conteneur Azure et Azure SQL Database

Cet article explique comment Contoso migre et réarchitecture son application SmartHotel dans Azure. Ils migrent la machine virtuelle frontend de l’application vers un conteneur Azure Windows, et la base de données de l’application vers une base de données SQL Azure.

Ce document fait partie d’une série d’articles qui montrent comment la société fictive Contoso migre ses ressources locales vers le cloud Microsoft Azure. La série comprend des informations générales et des scénarios qui illustrent comment configurer une infrastructure de migration, évaluer des ressources locales pour la migration et exécuter différents types de migration. Les scénarios croissant en complexité, nous ajouterons des articles au fil du temps.

**Article** | **Détails** | **État**
--- | --- | ---
[Article 1 : vue d’ensemble](contoso-migration-overview.md) | Fournit une vue d’ensemble de la stratégie de migration de Contoso, de la série d’articles et des exemples d’application que nous utilisons. | Disponible
[Article 2 : Déployer une infrastructure Azure](contoso-migration-infrastructure.md) | Décrit comment Contoso prépare son infrastructure locale et son infrastructure Azure pour la migration. La même infrastructure est utilisée pour tous les articles de migration. | Disponible
[Article 3 : Évaluer les ressources locales](contoso-migration-assessment.md)  | Montre comment Contoso évalue une application à deux niveaux locale SmartHotel s’exécutant sur VMware. Contoso évalue les machines virtuelles de l’application avec le service [Azure Migrate](migrate-overview.md) et la base de données SQL Server de l’application avec [l’Assistant Migration de données Azure](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Disponible
[Article 4 : Réhéberger une application sur des machines virtuelles Azure et une instance SQL Managed Instance](contoso-migration-rehost-vm-sql-managed-instance.md) | Montre comment Contoso exécute une migration lift-and-shift vers Azure pour l’application SmartHotel. Elle migre la machine virtuelle frontale de l’application à l’aide d’[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), et la base de données de l’application vers une instance SQL Managed Instance à l’aide du [service Azure Database Migration](https://docs.microsoft.com/azure/dms/dms-overview). | Disponible
[Article 5 : Réhéberger une application sur des machines virtuelles Azure](contoso-migration-rehost-vm.md) | Montre comment Contoso migre les machines virtuelles de l’application SmartHotel en utilisant uniquement Site Recovery. | Disponible
[Article 6 : Réhéberger une application sur des machines virtuelles Azure et un groupe de disponibilité Always On SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Montre comment Contoso migre l’application SmartHotel. Elle utilise Site Recovery pour migrer la machine virtuelle de l’application, et Database Migration Service pour migrer la base de données de l’application vers un cluster SQL Server protégé par un groupe de disponibilité AlwaysOn. | Disponible
[Article 7 : Réhéberger une application Linux sur des machines virtuelles Azure](contoso-migration-rehost-linux-vm.md) | Montre comment Contoso effectue une migration lift-and-shift de l’application osTicket Linux sur des machines virtuelles Azure à l’aide de Site Recovery. | Disponible
[Article 8 : ré-héberger une application Linux sur des machines virtuelles Azure et Azure MySQL Server](contoso-migration-rehost-linux-vm-mysql.md) | Montre comment Contoso migre l’application osTicket Linux vers des machines virtuelles Azure à l’aide de Site Recovery, et migre la base de données de l’application vers une instance Azure MySQL Server à l’aide de MySQL Workbench. | Disponible
[Article 9 : Refactoriser une application vers une application web Azure et Azure SQL Database](contoso-migration-refactor-web-app-sql.md) | Montre comment Contoso migre l’application SmartHotel vers une application web Azure, et migre la base de données d’application vers une instance de serveur SQL Azure. | Disponible
[Article 10 : Refactoriser une application Linux vers Azure Web Apps et Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Montre comment Contoso migre l’application Linux osTicket vers Azure Web Apps dans plusieurs sites intégrés avec GitHub pour assurer une livraison continue. Il migre la base de données d’application vers une instance Azure MySQL. | Disponible
Article 11 : Réarchitecturer une application sur des conteneurs Azure et Azure SQL Database | Montre comment Contoso migre et réarchitecture son application SmartHotel vers Azure. Il réarchitecturent la couche web d’application en tant que conteneur Windows, et la base de données d’application en une base de données Azure SQL Database. | Cet article.
[Article 12 : Réarchitecturer une application sur des conteneurs Azure et Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Montre comment Contoso migre et réarchitecture son application SmartHotel sur Azure. Il réarchitecture la couche web d’application en tant que conteneur Windows et la base de données d’application en une base de données Azure SQL Database. | Disponible
[Article 13 : Regénérer une application dans Azure](contoso-migration-rebuild.md) | Montre comment Contoso regénère son application SmartHotel à l’aide d’une série de fonctionnalités et services Azure, notamment App Services, Azure Kubernetes, Azure Functions, Cognitive Services et Cosmos DB. | Disponible

Dans cet article, Contoso migre Windows à deux niveaux. Application NET SmartHotel s’exécutant sur des machines virtuelles VMware vers Azure. Si vous souhaitez utiliser cette application, elle est disponible en open source et vous pouvez la télécharger à partir de [GitHub](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>Axes stratégiques

L’équipe informatique a travaillé en étroite collaboration avec ses partenaires commerciaux pour comprendre le résultat qu’ils souhaitent obtenir avec cette migration :

- **Répondre à la croissance** : Contoso étant en croissance, son infrastructure et ses systèmes locaux subissent une pression.
- **Augmenter l’efficacité** : Contoso doit supprimer les procédures inutiles et rationaliser les processus pour les développeurs et les utilisateurs.  L’entreprise a besoin d’une informatique rapide et doit éviter de perdre du temps ou d’argent en répondant plus rapidement aux exigences des clients.
- **Augmenter l’agilité** : l’informatique de Contoso doit être plus réactive aux besoins de l’entreprise. Elle doit être en mesure de réagir plus rapidement que l’évolution du marché pour réussir dans une économie mondiale.  L’informatique ne doit pas devenir une entrave à l’activité.
- **Mise à l’échelle** : à mesure que l’entreprise croît, l’informatique de Contoso doit fournir des systèmes capables de croître au même rythme.
- **Coûts**  : Contoso souhaite réduire les coûts de licence.

## <a name="migration-goals"></a>Objectifs de la migration

L’équipe cloud de Contoso a épinglé les objectifs de cette migration. Ces objectifs ont été utilisés pour déterminer la meilleure méthode de migration.

**Objectifs** | **Détails**
--- | --- 
**Exigences d’application** | L’application dans Azure restera aussi critique qu’aujourd’hui.<br/><br/> Elle doit offrir les mêmes performances qu’actuellement dans VMWare.<br/><br/> Ils souhaitent cesser de prendre en charge Windows Server 2008 R2, sur lequel l’application s’exécute actuellement, et sont disposés à investir dans l’application.<br/><br/> Ils souhaitent passer de SQL Server 2008 R2 à une plateforme de base de données PaaS moderne afin de réduire les besoins de gestion.<br/><br/> Contoso souhaite autant que possible tirer parti de ses investissements en licences SQL Server et en Software Assurance.<br/><br/> Ils veulent être en mesure de faire monter en puissance la couche web d’applications.
**Limitations** | L’application consiste en une application ASP.NET et un service WCF (Windows Communication Foundation) s’exécutant sur la même machine virtuelle. Ils veulent fractionner cela en deux applications web utilisant Azure App Service. 
**Exigences Azure** | Ils souhaitent déplacer l’application vers Azure et l’exécuter dans un conteneur pour étendre la durée de vie de l’application. Ils ne souhaitent pas tout reprendre à zéro afin d’implémenter l’application dans Azure. 

## <a name="solution-design"></a>Conception de la solution

Après avoir défini précisément les objectifs et exigences, Contoso conçoit et examine une solution de déploiement, et identifie le processus de migration, notamment les services Azure à utiliser pour la migration.

### <a name="current-app"></a>Application actuelle

- L’application SmartHotel locale est hiérarchisée sur deux machines virtuelles (WEBVM et SQLVM).
- Les machines virtuelles sont situées sur un hôte VMware ESXi **contosohost1.contoso.com** (version 6.5).
- L’environnement VMware est géré par le serveur vCenter Server 6.5 (**vcenter.contoso.com**) s’exécutant sur une machine virtuelle.
- Contoso dispose d’un centre de données local (contoso-datacenter), avec un contrôleur de domaine local (**contosodc1**).
- Les machines virtuelles locales dans le centre de données Contoso seront désaffectées une fois la migration terminée.


### <a name="proposed-architecture"></a>Architecture proposée

- Pour la couche Base de données de l’application, Contoso a comparé Azure SQL Database à SQL Server en se basant sur [cet article](https://docs.microsoft.com/azure/sql-database/sql-database-features). Ils ont choisi d’utiliser Azure SQL Database pour plusieurs raisons :
    - Azure SQL Database est un service managé de base de données relationnelle. Il offre des performances prévisibles à plusieurs niveaux de service, et ne nécessite pratiquement aucune administration. Ses avantages sont une extensibilité dynamique sans aucun temps d’arrêt, une optimisation intelligente intégrée, ainsi qu’une scalabilité et une disponibilité globales.
    - Ils peuvent tirer parti de l’Assistant Migration de données (DMA) allégé pour évaluer et migrer la base de données locale vers SQL Azure.
    - Grâce à Software Assurance, ils peuvent échanger leurs licences existantes pour bénéficier de tarifs réduits sur SQL Database, en utilisant Azure Hybrid Benefit pour SQL Server. Cela pourrait leur permettre de réaliser jusqu’à 30 % d’économies.
    - Azure SQL Database fournit un certain nombre de fonctionnalités de sécurité, notamment le chiffrement permanent, le masquage dynamique des données, la sécurité au niveau des lignes et la détection des menaces.
- Pour la couche d’application web, ils ont décidé de la convertir vers le conteneur Windows à l’aide de Visual Studio.
    - Ils déploieront l’application à l’aide d’Azure Service Fabric et extrairont l’image de conteneur Windows à partir d’Azure Container Registry (ACR).
    - Un prototype visant à étendre l’application pour qu’elle inclue l’analyse des sentiments sera implémenté en tant que service distinct dans Service Fabric, connecté à Cosmos DB.  Celui-ci lira les informations de Tweets et affichera des données sur l’application.

    ![Architecture du scénario](./media/contoso-migration-rearchitect-container-sql/architecture.png) 

  
### <a name="solution-review"></a>Examen de la solution
Contoso évalue la conception proposée en dressant une liste des avantages et des inconvénients.

**Considération** | **Détails**
--- | ---
**Avantages** | Le code de l’application SmartHotel devra être modifié pour la migration vers Azure Service Fabric. Toutefois, l’effort est minimal, car on utilisera les outils du SDK Service Fabric pour les modifications.<br/><br/> Avec le déplacement vers Service Fabric, Contoso peut commencer à développer rapidement des microservices à ajouter à l’application au fil du temps, sans risque pour la base de code d’origine.<br/><br/> Les conteneurs Windows offrent les mêmes avantages que les conteneurs standard. Ils améliorent la portabilité, la flexibilité et le contrôle.<br/><br/> Ils peuvent tirer parti de leur investissement dans Software Assurance en utilisant Azure Hybrid Benefit tant pour SQL Server que pour Windows Server.<br/><br/> Après la migration, ils n’auront plus besoin de prendre en charge Windows Server 2008 R2. [Plus d’informations](https://support.microsoft.com/lifecycle)<br/><br/> Ils peuvent configurer la couche web de l’application avec plusieurs instances, de façon à ce qu’elle ne soit plus un point de défaillance unique.<br/><br/> Ils ne seront plus dépendants du vieillissement de SQL Server 2008 R2.<br/><br/> Azure SQL Database répond aux exigences techniques de Contoso. Ils ont évalué la base de données locale à l’aide de l’Assistant Migration de données, et découvert qu’elle était compatible.<br/><br/> Azure SQL Database intègre une tolérance de panne que Contoso n’aura pas à configurer. Cela signifie que la couche Données n’est plus un point de basculement unique.
**Inconvénients** | Les conteneurs sont plus complexes que d’autres options de migration. La courbe d’apprentissage pour les conteneurs pourrait poser problème à Contoso.  Ils introduisent un nouveau niveau de complexité qui apporte beaucoup de valeur en dépit de la courbe.<br/><br/> L’équipe des opérations chez Contoso devra redoubler d’efforts pour comprendre et prendre en charge Azure, les conteneurs et les microservices pour l’application.<br/><br/> En cas d’utilisation de l’Assistant Migration de données au lieu de Database Migration Service pour migrer sa base de données, Contoso n’aura pas d’infrastructure prête pour la migration de bases de données à grande échelle.



### <a name="migration-process"></a>Processus de migration

1. Contoso provisionne le cluster Azure Service Fabric pour Windows.
2. Ils provisionnent une instance SQL Azure et y migrent la base de données SmartHotel.
3. Ils convertissent la machine virtuelle de couche web en un conteneur Docker à l’aide des outils du SDK Service Fabric.
4. Ils connectent le cluster Service Fabric et l’ACR, et déploient l’application à l’aide d’Azure Service Fabric.

    ![Processus de migration](./media/contoso-migration-rearchitect-container-sql/migration-process.png) 

### <a name="azure-services"></a>Services Azure

**Service** | **Description** | **Coût**
--- | --- | ---
[Assistant Migration de données Microsoft (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Ils vont utiliser DMA pour évaluer et détecter des problèmes de compatibilité susceptibles d’affecter les fonctionnalités de base de données dans Azure. DMA évalue la parité des fonctionnalités entre SQL sources et cibles, et recommande des améliorations des performances et de la fiabilité. | Cet outil est téléchargeable gratuitement.
[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) | Service de base de données cloud relationnelle entièrement managé et intelligent. | Coût basé sur les fonctionnalités, le débit et la taille. [Plus d’informations](https://azure.microsoft.com/pricing/details/sql-database/managed/)
[Azure Container Registry](https://azure.microsoft.com/services/container-registry/) | Stockez les images pour tous types de déploiement de conteneur. | Coût basé sur les fonctionnalités, le stockage et la durée d’utilisation. [Plus d’informations](https://azure.microsoft.com/pricing/details/container-registry/)
[Azure Service Fabric](https://azure.microsoft.com/services/service-fabric/) | Générez et exploitez des applications distribuées, scalables et toujours disponibles. | Coûts basé sur la taille, l’emplacement et la durée des nœuds de calcul. [Plus d’informations](https://azure.microsoft.com/pricing/details/service-fabric/)

## <a name="prerequisites"></a>Prérequis

Voici ce que vous (et Contoso) devez faire dans le cadre de ce scénario :

**Configuration requise** | **Détails**
--- | ---
**Abonnement Azure** | Vous devez avoir déjà créé un abonnement lorsque vous avez effectué l’évaluation dans le premier article de cette série. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Si vous créez un compte gratuit, vous êtes l’administrateur de votre abonnement et pouvez effectuer toutes les actions.<br/><br/> Si vous utilisez un abonnement existant et que vous n’êtes pas l’administrateur, vous devez collaborer avec l’administrateur pour qu’il vous donne les autorisations Propriétaire ou Contributeur.
**Infrastructure Azure** | [Découvrez comment](contoso-migration-infrastructure.md) Contoso configure une infrastructure Azure.
**Prérequis pour développeur** | Contoso a besoin des outils suivants sur une station de travail de développeur :<br/><br/> - [Visual Studio 2017 Community Edition : Version 15.5](https://www.visualstudio.com/)<br/><br/> - Charge de travail .NET activée.<br/><br/> - [Git](https://git-scm.com/)<br/><br/> - [SDK Service Fabric v 3.0 ou ultérieure](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)<br/><br/> - [Docker CE (Windows 10) ou Docker EE (Windows Server)](https://docs.docker.com/docker-for-windows/install/), configurés pour utiliser des conteneurs Windows.



## <a name="scenario-steps"></a>Étapes du scénario

Voici comment Contoso exécutera la migration :

> [!div class="checklist"]
> * **Étape 1 : Provisionner une instance SQL Database dans Azure** : Contoso provisionne une instance SQL dans Azure. Une fois la machine virtuelle web frontend migrée vers un conteneur Azure, l’instance de conteneur avec l’application web frontend pointe vers cette base de données.
> * **Étape 2 : Provisionner Azure Service Fabric** : ils provisionnent un cluster Service Fabric.
> * **Étape 4 : Migrer la base de données avec DMA** : ils migrent la base de données de l’application avec l’Assistant Migration de données.
> * **Étape 5 : Convertir l’application en conteneur** : ils convertissent l’application en conteneur à l’aide de Visual Studio et SDK Tools.
> * **Étape 6 : Publier l’application**: elles publient l’application dans l’ACR et le cluster Service Fabric.
> * **Étape 7 : Étendre l’application** : une fois l’application publique, ils l’étendent afin de tirer parti des fonctionnalités Azure, et ils la republient sur Azure.



## <a name="step-1-provision-an-azure-sql-database"></a>Étape 1 : Provisionner Azure SQL Database

1. Ils choisissent de créer une base de données SQL dans Azure. 

    ![Provisionner SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql1.png)

2. Ils spécifient un nom de base de données correspondant à la base de données en cours d’exécution sur la machine virtuelle locale (**SmartHotel.Registration**). Ils placent la base de données dans le groupe de ressources ContosoRG. Il s’agit du groupe de ressources qu’ils utilisent pour les ressources de production dans Azure.

    ![Provisionner SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql2.png)

3. Ils configurent une nouvelle instance de SQL Server (**sql-smarthotel-eus2**) dans la région primaire.

    ![Provisionner SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql3.png)

4. Ils définissent le niveau tarifaire pour qu’il corresponde à leurs besoins en matière de serveur et de base de données. Et ils choisissent d’économiser de l’argent avec Azure Hybrid Benefit, car ils ont déjà une licence SQL Server.
5. Pour le dimensionnement, ils utilisent un achat basé sur v-Core, et définissent les limites de leurs exigences attendues.

    ![Provisionner SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql4.png)

6. Ils créent ensuite l’instance de base de données.

    ![Provisionner SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql5.png)

7. Une fois l’instance créée, ils ouvrent la base de données et notent les informations dont ils auront besoin quand ils utiliseront l’Assistant Migration de données pour la migration.

    ![Provisionner SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql6.png)


**Besoin de plus d’aide ?**

- [Obtenir de l’aide](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) pour le provisionnement d’une base de données SQL.
- [En savoir plus](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools) sur les limites de ressources v-Core.



## <a name="step-2-create-an-acr-and-provision-an-azure-container"></a>Étape 2 : Créer un ACR et provisionner un conteneur Azure

Le conteneur Azure est créé à l’aide des fichiers exportés à partir de la machine virtuelle web. Le conteneur est hébergé dans Azure Container Registry (ACR).


1. Contoso crée un Container Registry dans le portail Azure.

     ![Container Registry](./media/contoso-migration-rearchitect-container-sql/container-registry1.png)

2. Ils fournissent un nom pour le registre (**contosoacreus2**) et le placent dans la région primaire, dans le groupe de ressources qu’ils utilisent pour leurs ressources d’infrastructure. Ils activent l’accès pour les utilisateurs administrateurs et le configurent comme une référence SKU premium afin de pouvoir tirer parti de la géoréplication.

    ![Container Registry](./media/contoso-migration-rearchitect-container-sql/container-registry2.png)  


## <a name="step-3-provision-azure-service-fabric"></a>Étape 3 : Provisionner Azure Service Fabric

Le conteneur SmartHotel s’exécutera dans le cluster Service Fabric Azure. Contoso crée le cluster Service Fabric comme suit :

1. Créer une ressource Service Fabric à partir de la Place de Marché Azure

     ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric1.png)

2. Dans **De base**, ils fournissent un nom de service d’annuaire unique pour le cluster et des informations d’identification pour accéder à la machine virtuelle locale. Ils placent la ressource dans le groupe de ressources de production (**ContosoRG**) dans la région primaire USA Est 2.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric2.png) 

3. Dans **Configuration du type de nœud**, ils entrent un nom de type de nœud, des paramètres de durabilité, la taille de machine virtuelle et les points de terminaison d’application.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric3.png) 


4. Dans **Créer un coffre de clés**, ils créent un coffre de clés dans leur groupe de ressources d’infrastructure pour héberger le certificat.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric4.png) 


5. Dans **Stratégies d’accès**, ils activent l’accès aux machines virtuelles pour déployer le coffre de clés.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric5.png) 

6. Ils spécifient un nom pour le certificat.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric6.png) 

7. Dans la page de synthèse, ils copient le lien qui est utilisé pour télécharger le certificat. Ils en ont besoin pour se connecter au cluster Service Fabric.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric7.png) 

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric8.png) 

8. Une fois la validation effectuée, ils provisionnent le cluster.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric9.png) 

9. Dans l’Assistant Importation de certificat, ils importent le certificat téléchargé sur les ordinateurs de développement. Le certificat est utilisé pour l’authentification auprès du cluster.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric10.png) 

10. Une fois le cluster provisionné, ils se connectent à l’Explorateur de clusters Service Fabric.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric11.png) 

11. Ils doivent sélectionner le certificat approprié.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric12.png) 

12. Service Fabric Explorer se charge, et l’administrateur Contoso peut gérer le cluster.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric13.png) 


## <a name="step-3-migrate-the-database-with-dma"></a>Étape 3 : Migrer la base de données avec DMA

Contoso va migrer la base de données SmartHotel à l’aide du DMA.

### <a name="install-dma"></a>Installer DMA

1. Télécharger l’outil à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=53595) sur la machine virtuelle locale SQL Server (**SQLVM**).
2. Exécuter le programme d’installation (DownloadMigrationAssistant.msi) sur la machine virtuelle.
3. Dans la page **Terminer**, sélectionner **Lancer l’Assistant Migration de données Microsoft** avant la fin de l’exécution de l’Assistant.

### <a name="configure-the-firewall"></a>Configurer le pare-feu

Pour se connecter à la base de données SQL Azure, une règle de pare-feu est nécessaire.

1. Dans les propriétés de **Pare-feu et réseaux virtuels** de la base de données, ils autorisent l’accès aux services Azure et ajoutent une règle pour l’adresse IP cliente de la machine virtuelle SQL Server locale.
2. Une règle de pare-feu au niveau du serveur est créée.

    ![Pare-feu](./media/contoso-migration-rearchitect-container-sql/sql-firewall.png)

Besoin de plus d’aide ?

[En savoir plus](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#creating-and-managing-firewall-rules) la création et la gestion des règles de pare-feu pour Azure SQL Database.

### <a name="migrate"></a>Migrer

1. Dans DMA, créer un projet (**SmartHotelDB**), puis sélectionner **Migration** 
2. Ils sélectionnent le type de serveur source **SQL Server** et la cible **Azure SQL Database**. 

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-1.png)

3. Dans les détails de la migration, ils ajoutent **SQLVM** comme serveur source, et la base de données **SmartHotel.Registration**. 

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-2.png)

4. Ils reçoivent un message d’erreur qui semble être associé à l’authentification. Toutefois, après examen, le problème résulte de la présence d’un point (.) dans le nom de la base de données. Pour résoudre ce problème, ils décident de provisionner une nouvelle base de données SQL en utilisant le nom **SmartHotel-Registration**. Quand ils réexécutent DMA, ils peuvent sélectionner **SmartHotel-Registration** et continuer avec l’Assistant.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-3.png)

5. Dans **Sélectionner des objets**, ils sélectionnent les tables de base de données et génèrent un script SQL.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-4.png)

6. Après que DMS a créé le script, ils cliquent sur **Déployer le schéma**.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-5.png)

7. DMA vérifie que le déploiement a réussi.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-6.png)

8. À présent, ils commencent la migration.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-7.png)

9. Une fois la migration terminée, Contoso peut vérifier que la base de données est en cours d’exécution sur l’instance SQL Azure.

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-8.png)

10. Ils suppriment la base de données SQL supplémentaire **SmartHotel.Registration** dans le portail Azure.

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-9.png)



## <a name="step-4-convert-the-app-to-a-container"></a>Étape 4 : Convertir l’application en conteneur

L’application locale est une application à trois niveaux traditionnelle :

- Elle contient des Web Forms et un service WCF se connectant à SQL Server.
- Elle utilise Entity Framework pour l’intégration aux données dans la base de données SQL, et les expose au moyen d’un service WCF.
- L’application Web Forms interagit avec le service WCF.

Contoso va convertir l’application en conteneur à l’aide de Visual Studio et de SDK Tools en procédant comme suit :

1. Ils clonent le dépôt localement sur un ordinateur de développeur :

    **Clone Git https://github.com/Microsoft/SmartHotel360-internal-booking-apps.git**

    ![Conteneur](./media/contoso-migration-rearchitect-container-sql/container1.png)

2. À l’aide de Visual Studio, ils ouvrent le fichier de solution (SmartHotel.Registration.sln) dans le répertoire **SmartHotel360-internal-booking-apps\src\Registration** du dépôt local.  Deux applications sont affichées. L’application web frontend SmartHotel.Registration.Web et l’application de service WCF SmartHotel.Registration.WCF.

    ![Conteneur](./media/contoso-migration-rearchitect-container-sql/container2.png)


3. Ils cliquent avec le bouton droit sur l’application web > **Ajouter** > **Prise en charge des orchestrateurs de conteneurs**.
4. Dans **Ajouter la prise en charge des orchestrateurs de conteneurs**, ils sélectionnent **Service Fabric**.

    ![Conteneur](./media/contoso-migration-rearchitect-container-sql/container3.png)

5. Contoso répète le processus pour l’application SmartHotel.Registration.WCF.
6. Maintenant, Contoso vérifie comment la solution a changé.

    - La nouvelle application est **SmartHotel.RegistrationApplication/**.
    - Elle contient deux services : **SmartHotel.Registration.WCF** et **SmartHotel.Registration.Web**.

    ![Conteneur](./media/contoso-migration-rearchitect-container-sql/container4.png)

7. Visual Studio a créé le fichier Docker et extrait les images nécessaires localement sur l’ordinateur du développeur.

    ![Conteneur](./media/contoso-migration-rearchitect-container-sql/container5.png)

8. Un fichier manifeste (**ServiceManifest.xml**) est créé et ouvert par Visual Studio. Ce fichier indique à Service Fabric comment configurer le conteneur quand il est déployé sur Azure.

    ![Conteneur](./media/contoso-migration-rearchitect-container-sql/container6.png)

9. Un autre fichier manifeste (**ApplicationManifest.xml) contient les applications de configuration pour les conteneurs.

    ![Conteneur](./media/contoso-migration-rearchitect-container-sql/container7.png)

## <a name="step-5-publish-the-app"></a>Étape 5 : Publier l’application


Pour finir, Contoso peut publier l’application sur l’ACR et le cluster Service Fabric.

> [!NOTE]
> Certaines modifications liées au cluster Service Fabric ont été apportées à l’application SmartHotel. Vous pouvez télécharger le code d’application d’origine et le code mis à jour à partir de [GitHub](https://github.com/Microsoft/SmartHotel360-internal-booking-apps). Le fichier modifié est **AppliationModern/ApplicationParameters/Cloud.xml**.


1. Dans Visual Studio, la chaîne de connexion est mise à jour pour connecter l’application à la base de données SQL Azure. Vous trouverez la chaîne de connexion dans la base de données dans le portail Azure.

    ![Publier](./media/contoso-migration-rearchitect-container-sql/publish1.png)

2. Contoso publie l’application sur Service Fabric à l’aide de Visual Studio. Ils cliquent avec le bouton droit sur l’application Service Fabric > **Publier**.

    ![Publier](./media/contoso-migration-rearchitect-container-sql/publish2.png)

3. Ils sélectionnent l’abonnement, le point de terminaison de connexion et l’ACR. Ils cliquent ensuite sur **Publier**.

    ![Publier](./media/contoso-migration-rearchitect-container-sql/publish3.png)

4. Une fois le déploiement terminé, SmartHotel exécutera Service Fabric.

    ![Publier](./media/contoso-migration-rearchitect-container-sql/publish4.png)

5. Pour se connecter à l’application, Contoso dirige le trafic vers l’adresse IP publique de l’équilibreur de charge Azure qui se trouve devant ses nœuds Service Fabric.

    ![Publier](./media/contoso-migration-rearchitect-container-sql/publish5.png)

## <a name="step-6-extend-the-app-and-republish"></a>Étape 6 : Étendre l’application et republier

Une fois l’application SmartHotel et la base de données en cours d’exécution dans Azure, Contoso souhaite étendre l’application.

- Les développeurs de Contoso créent un prototype d’application .NET Core qui s’exécutera sur le cluster Service Fabric.
- L’application sera utilisée pour extraire des données de sentiments à partir de CosmosDB.
- Ces données seront sous la forme de tweets traités à l’aide d’une fonction Azure sans serveur et de l’API Analyse de texte Cognitive Services.

### <a name="provision-azure-cosmos-db"></a>Provisionner Azure Cosmos DB

Dans un premier temps, Contoso provisionne une base de données Azure Cosmos.

1. Ils créent une ressource Azure Cosmos DB dans la Place de Marché Azure.

    ![Étendre](./media/contoso-migration-rearchitect-container-sql/extend1.png)

2. Ils fournissent un nom de base de données (**contososmarthotel**), sélectionnent l’API SQL et placent la ressource dans le groupe de ressources de production, dans la région primaire USA Est 2.

    ![Étendre](./media/contoso-migration-rearchitect-container-sql/extend2.png)

3. Dans **Prise en main**, ils sélectionnent **Explorateur de données** et ajoutent une nouvelle collection.
4. Dans **Ajouter une collection**, ils fournissent des ID et définissent la capacité de stockage et le débit.

    ![Étendre](./media/contoso-migration-rearchitect-container-sql/extend3.png)

5. Dans le portail, ils ouvrent la nouvelle base de données > **Collection** > **Documents** et cliquent sur **Nouveau Document**.
6. Ils collent le code JSON suivant dans la fenêtre de document. Il s’agit d’exemples de données sous la forme d’un seul tweet.

    ```
    {
            "id": "2ed5e734-8034-bf3a-ac85-705b7713d911",
            "tweetId": 927750234331580911,
            "tweetUrl": "https://twitter.com/status/927750237331580911",
            "userName": "CoreySandersWA",
            "userAlias": "@CoreySandersWA",
            "userPictureUrl": "",
            "text": "This is a tweet about #SmartHotel",
            "language": "en",
            "sentiment": 0.5,
            "retweet_count": 1,
            "followers": 500, 
            "hashtags": [
                ""
            ]
    }
    ```

    ![Étendre](./media/contoso-migration-rearchitect-container-sql/extend4.png)

7. Ils localisent le point de terminaison Cosmos DB et la clé d’authentification. Ceux-ci sont utilisés dans l’application pour se connecter à la collection. Dans la base de données, ils cliquent sur **Clés** et copient l’URI et la clé primaire dans le Bloc-notes.

    ![Étendre](./media/contoso-migration-rearchitect-container-sql/extend5.png)

### <a name="update-the-sentiment-app"></a>Mettre à jour l’application de sentiments

Cosmos DB étant provisionné, Contoso peut configurer l’application pour qu’elle s’y connecte.

1. Dans Visual Studio, ils ouvrent le fichier ApplicationModern\ApplicationParameters\cloud.xml dans l’Explorateur de solutions.

    ![Application de sentiments](./media/contoso-migration-rearchitect-container-sql/sentiment1.png)

2. Ils renseignent les deux paramètres suivants :

   ```
   <Parameter Name="SentimentIntegration.CosmosDBEndpoint" Value="[URI]" />
   ```
   
   ```
   <Parameter Name="SentimentIntegration.CosmosDBAuthKey" Value="[Key]" />
   ```

    ![Application de sentiments](./media/contoso-migration-rearchitect-container-sql/sentiment2.png)

### <a name="republish-the-app"></a>Republier l’application

Après avoir étendu l’application, Contoso la republie sur Azure.

1. Dans le portail, ils cliquent avec le bouton droit sur l’application Service Fabric > **Publier**.

    ![Republier](./media/contoso-migration-rearchitect-container-sql/republish1.png)

2. Ils sélectionnent l’abonnement, le point de terminaison de connexion et l’ACR. Ils cliquent ensuite sur **Publier**.

    ![Republier](./media/contoso-migration-rearchitect-container-sql/republish2.png)

4. Une fois le déploiement terminé, SmartHotel exécutera Service Fabric. La console de gestion Service Fabric affiche maintenant trois services.

    ![Republier](./media/contoso-migration-rearchitect-container-sql/republish3.png)

5. Contoso peut cliquer sur les services pour vérifier que l’application SentimentIntegration est en cours d’exécution

    ![Republier](./media/contoso-migration-rearchitect-container-sql/republish4.png)

## <a name="clean-up-after-migration"></a>Nettoyer après la migration

Après la migration, Contoso doit effectuer les étapes de nettoyage suivantes :  

- Supprimer les machines virtuelles locales de l’inventaire vCenter.
- Supprimer les machines virtuelles des travaux de sauvegarde locale.
- Mettre à jour la documentation interne afin qu’elle indique les nouveaux emplacements pour l’application SmartHotel. Afficher la base de données comme étant en cours d’exécution dans Azure SQL Database, et l’application frontend comme étant en cours d’exécution dans Service Fabric.
- Passer en revue toutes les ressources qui interagissent avec les machines virtuelles désactivées, et mettre à jour les paramètres ou la documentation appropriés afin de refléter la nouvelle configuration.


## <a name="review-the-deployment"></a>Examiner le déploiement

Avec les ressources migrées dans Azure, Contoso doit rendre sa nouvelle infrastructure entièrement opérationnelle et la sécuriser.

### <a name="security"></a>Sécurité

- Contoso doit vérifier que sa nouvelle base de données **SmartHotel-Registration** est sécurisée. [Plus d’informations](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview)
- En particulier, ils doivent mettre à jour le conteneur pour utiliser le protocole SSL avec des certificats.
- Ils devraient envisager d’utiliser Key Vault pour protéger les secrets de leurs applications Service Fabric. [Plus d’informations](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management)

### <a name="backups"></a>Sauvegardes

- Contoso doit examiner les exigences de sauvegarde pour Azure SQL Database. [Plus d’informations](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)
- Ils devraient envisager d’implémenter des groupes de basculement afin de fournir un basculement régional pour la base de données. [Plus d’informations](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview)
- Ils peuvent tirer parti de la géoréplication pour la référence (SKU) premium d’ACR. [Plus d’informations](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)
- Contoso doit envisager de déployer l’application web dans la région principale USA Est 2, ainsi que dans la région USA Centre, quand Web App pour conteneurs devient disponible. Ils pourraient configurer Traffic Manager pour garantir le basculement en cas de pannes régionales.

### <a name="licensing-and-cost-optimization"></a>Gestion des licences et optimisation des coûts

- Une fois toutes les ressources déployées, Contoso doit affecter des balises Azure en fonction de la [planification de son infrastructure](contoso-migration-infrastructure.md#set-up-tagging).
- Toutes les licences sont intégrées dans le coût des services PaaS que Contoso consomme. Cela sera déduit de l’Accord Entreprise.
1. Contoso va activer Azure Cost Management sous licence de Cloudyn, une filiale de Microsoft. Il s’agit d’une solution de gestion des coûts multicloud qui aide à utiliser et à gérer Azure ainsi que d’autres ressources cloud.  [En savoir plus](https://docs.microsoft.com/azure/cost-management/overview) sur Azure Cost Management. 

## <a name="conclusion"></a>Conclusion

Cet article décrit comment Contoso a refactorisé l’application SmartHotel dans Azure en migrant la machine virtuelle frontend de l’application vers Service Fabric. Ils ont migré la base de données d’application vers une base de données Azure SQL.





