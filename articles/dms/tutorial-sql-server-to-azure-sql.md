---
title: 'Tutoriel : Utiliser Azure Database Migration Service pour migrer SQL Server vers Azure SQL Database hors connexion | Microsoft Docs'
description: Découvrez comment effectuer une migration de SQL Server en local vers Azure SQL Database hors connexion à l’aide d’Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 10/10/2018
ms.openlocfilehash: 616b3d965993eb3578a8496069f59c9cc88301d8
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52971335"
---
# <a name="tutorial-migrate-sql-server-to-azure-sql-database-offline-using-dms"></a>Tutoriel : Migrer SQL Server vers Azure SQL Database hors connexion à l’aide de DMS
Vous pouvez utiliser Azure Database Migration Service pour migrer les bases de données d’une instance SQL Server locale vers [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/). Dans ce tutoriel, vous allez migrer la base de données **Adventureworks2012** restaurée dans une instance locale de SQL Server 2016 (ou une version ultérieure) vers Azure SQL Database à l’aide d’Azure Database Migration Service.

Ce tutoriel vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * Évaluer votre base de données locale à l’aide de l’Assistant Migration des données.
> * Migrer l’exemple de schéma à l’aide de l’Assistant Migration des données.
> * Créer une instance Azure Database Migration Service.
> * Créer un projet de migration en utilisant Azure Database Migration Service.
> * Exécuter la migration.
> * Surveiller la migration.
> * Télécharger un rapport de migration.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Cet article décrit une migration hors connexion de SQL Server vers Azure SQL Database. Pour une migration en ligne, consultez [Migrer SQL Server vers Azure SQL Database en ligen à l’aide de DMS](tutorial-sql-server-azure-sql-online.md).

## <a name="prerequisites"></a>Prérequis
Pour suivre ce didacticiel, vous devez effectuer les opérations suivantes :

- Téléchargez et installez [SQL Server 2016 ou version ultérieure](https://www.microsoft.com/sql-server/sql-server-downloads) (toute édition).
- Activez le protocole TCP/IP, qui est désactivé par défaut pendant l’installation de SQL Server Express, en suivant les instructions de l’article [Activer ou désactiver un protocole réseau de serveur](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Créez une instance d’Azure SQL Database en suivant les indications de l’article [Création d’une base de données SQL Azure à l’aide du portail Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- Téléchargez et installez [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 ou version ultérieure.
- Créez un réseau virtuel pour Azure Database Migration Service à l’aide du modèle de déploiement Azure Resource Manager, qui fournit une connectivité de site à site à vos serveurs sources locaux à l’aide de la fonction [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Assurez-vous que les règles de groupe de sécurité Réseau virtuel Microsoft Azure ne bloquent pas les ports de communication 443, 53, 9354, 445 et 12000. Pour plus d’informations sur le filtrage de groupe de sécurité Réseau virtuel Microsoft Azure, consultez l’article [Filtrer le trafic réseau avec les groupes de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Configurez votre [pare-feu Windows pour accéder au moteur de base de données](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Ouvrez votre Pare-feu Windows pour permettre à Azure Database Migration Service d’accéder au serveur SQL Server source, par défaut le port TCP 1433.
- Si vous exécutez plusieurs instances nommées de SQL Server avec des ports dynamiques, vous pouvez activer le service SQL Browser et autoriser l’accès au port UDP 1434 à travers vos pare-feu, de sorte qu’Azure Database Migration Service puisse se connecter à une instance nommée sur votre serveur source.
- Lorsque vous utilisez une appliance de pare-feu devant vos bases de données sources, vous devrez peut-être ajouter des règles de pare-feu pour permettre à Azure Database Migration Service d’accéder aux bases de données sources pour la migration.
- Créez une [règle de pare-feu](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) de niveau serveur pour le serveur Azure SQL Database afin de permettre à Azure Database Migration Service d’accéder aux bases de données cibles. Fournissez la plage de sous-réseau du réseau virtuel utilisé pour Azure Database Migration Service.
- Assurez-vous que les informations d’identification utilisées pour se connecter à une instance SQL Server source disposent des autorisations [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql).
- Assurez-vous que les informations d’identification utilisées pour se connecter à l’instance Azure SQL Database cible disposent des autorisations CONTROL DATABASE concernant les bases de données SQL Azure cibles.

## <a name="assess-your-on-premises-database"></a>Évaluer votre base de données locale
Avant de pouvoir migrer des données d’une instance SQL Server locale vers Azure SQL Database, vous devez évaluer les problèmes de blocage dans la base de données SQL Server qui peuvent empêcher la migration. Une fois que vous avez téléchargé et installé l’Assistant Migration de données v3.3 ou version ultérieure, suivez les étapes décrites dans l’article [Évaluation de la migration de SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) pour évaluer la base de données locale. Un résumé de la procédure est présenté ci-dessous :
1.  Dans l’Assistant Migration des données, sélectionnez l’icône Nouveau (+), puis sélectionnez le type de projet **Évaluation**.
2.  Spécifiez un nom de projet, dans la zone de texte **Type de serveur source**, sélectionnez **SQL Server** puis, dans la zone de texte **Type de serveur cible**, sélectionnez **Azure SQL Database**, puis sélectionnez **Créer** pour créer le projet.

    Pendant l’évaluation de la migration de la base de données SQL Server source vers Azure SQL Database, vous pouvez choisir un ou les deux types de rapports d’évaluation suivants :
    - Vérifier la compatibilité de la base de données
    - Vérifier la parité de fonctionnalité

    Les deux types de rapports sont sélectionnés par défaut.

3.  Dans l’Assistant Migration des données, dans l’écran **Options**, sélectionnez **suivant**.
4.  Dans l’écran **Sélectionner les sources**, dans la boîte de dialogue **Se connecter à un serveur**, fournissez les détails de connexion à votre SQL Server, puis sélectionnez **Se connecter**.
5.  Dans la boîte de dialogue **Add sources (Ajouter des sources)**, sélectionnez **AdventureWorks2012** et **Ajouter**, puis **Start Assessment (Démarrer l’évaluation)**.

    Une fois l’évaluation terminée, les résultats s’affichent comme illustré dans le graphique suivant :

    ![Évaluer la migration des données](media/tutorial-sql-server-to-azure-sql/dma-assessments.png)

    Pour Azure SQL Database, les évaluations identifient les problèmes bloquant la migration et les problèmes de parité de fonctionnalité.

    - La catégorie de **parité de fonctionnalité SQL Server** fournit un ensemble complet de recommandations, d’approches alternatives disponibles dans Azure et de procédures d’atténuation pour vous aider à planifier les efforts de vos projets de migration.
    - La catégorie de **problèmes de compatibilité** indique les fonctionnalités partiellement ou non prises en charge reflétant les éventuels problèmes de compatibilité susceptibles de bloquer la migration des bases de données SQL Server locales vers Azure SQL Database. Des recommandations sont également fournies pour vous aider à résoudre ces problèmes.

6.  Passez en revue les résultats d’évaluation en ce qui concerne les problèmes de blocage de migration et les problèmes de parité de fonctionnalité en sélectionnant les options spécifiques.

## <a name="migrate-the-sample-schema"></a>Migrer l’exemple de schéma
Une fois que vous êtes à l’aise avec l’évaluation et que la base de données sélectionnée est un bon candidat pour la migration vers Azure SQL Database, utilisez l’Assistant Migration de données pour migrer le schéma vers Azure SQL Database.

> [!NOTE]
> Avant de créer un projet de migration dans Data Migration Assistant, vérifiez que vous avez bien approvisionné une base de données SQL Azure comme indiqué dans les conditions préalables. Dans ce tutoriel, le nom d’Azure SQL Database est supposé être **AdventureWorksAzure**, mais vous pouvez lui attribuer un autre nom si vous le souhaitez.

Pour migrer le schéma **AdventureWorks2012** vers Azure SQL Database, procédez comme suit :

1.  Dans Data Migration Assistant, sélectionnez l’icône Nouveau (+), puis sous **Type de projet**, sélectionnez **Migration**.
2.  Spécifiez un nom de projet, dans la zone de texte **Type de serveur source**, sélectionnez **SQL Server** puis, dans la zone de texte **Type de serveur cible**, sélectionnez **Azure SQL Database**.
3.  Sous **Étendue de la migration**, sélectionnez **Schéma uniquement**.

    Une fois les étapes précédentes effectuées, l’interface de l’Assistant Migration des données doit apparaître comme indiqué dans le graphique suivant :
    
    ![Créer un projet d’Assistant Migration des données](media/tutorial-sql-server-to-azure-sql/dma-create-project.png)

4.  Sélectionnez **Créer** pour créer le projet.
5.  Dans l’Assistant Migration des données, spécifiez les détails de connexion source de votre SQL Server, sélectionnez **Se connecter**, puis sélectionnez la base de données **AdventureWorks2012**.

    ![Détails de connexion source de l’Assistant Migration des données](media/tutorial-sql-server-to-azure-sql/dma-source-connect.png)

6.  Sélectionnez **Suivant**, sous **Se connecter au serveur cible**, spécifiez les détails de connexion cible de la base de données Azure SQL, sélectionnez **Se connecter**, puis sélectionnez la base de données **AdventureWorksAzure** que vous avez pré-approvisionnée dans la base de données Azure SQL.

    ![Détails de connexion cible de l’Assistant Migration des données](media/tutorial-sql-server-to-azure-sql/dma-target-connect.png)

7.  Sélectionnez **Suivant** pour accéder à l’écran **Sélectionner des objets** dans lequel vous pouvez spécifiez les objets de schéma dans la base de données **AdventureWorks2012** à déployer sur Azure SQL Database.

    Par défaut, tous les objets sont sélectionnés.

    ![Générer des scripts SQL](media/tutorial-sql-server-to-azure-sql/dma-assessment-source.png)

8.  Sélectionnez **Générer un script SQL** pour créer les scripts SQL, puis recherchez d’éventuelles erreurs dans les scripts.

    ![Script de schéma](media/tutorial-sql-server-to-azure-sql/dma-schema-script.png)

9.  Sélectionnez **Déployer le schéma** pour déployer sur Azure SQL Database puis, une fois le schéma déployé, recherchez d’éventuelles anomalies sur le serveur cible.

    ![Déployer le schéma](media/tutorial-sql-server-to-azure-sql/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Inscrire le fournisseur de ressources Microsoft.DataMigration
1. Connectez-vous au portail Azure, sélectionnez **Tous les services**, puis **Abonnements**.
 
   ![Afficher les abonnements au portail](media/tutorial-sql-server-to-azure-sql/portal-select-subscription1.png)
       
2. Sélectionnez l’abonnement dans lequel vous souhaitez créer l’instance Azure Database Migration Service, puis sélectionnez **Fournisseurs de ressources**.
 
    ![Afficher les fournisseurs de ressources](media/tutorial-sql-server-to-azure-sql/portal-select-resource-provider.png)
    
3.  Recherchez migration, puis à droite de **Microsoft.DataMigration**, sélectionnez **Inscrire**.
 
    ![S’inscrire auprès du fournisseur de ressources](media/tutorial-sql-server-to-azure-sql/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Créer une instance
1.  Dans le portail Azure, sélectionnez **+ Créer une ressource**, recherchez Azure Database Migration Service, puis sélectionnez **Azure Database Migration Service** dans la liste déroulante.

    ![Place de marché Azure](media/tutorial-sql-server-to-azure-sql/portal-marketplace.png)

2.  Dans l’écran **Azure Database Migration Service**, sélectionnez **Créer**.
 
    ![Créer une instance Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-create1.png)
  
3.  Dans l’écran **Créer un service de migration**, spécifiez un nom pour le service, l’abonnement, et un réseau virtuel nouveau ou existant.

4. Sélectionnez l’emplacement dans lequel vous souhaitez créer l’instance Azure Database Migration Service. 

5. Sélectionnez un réseau virtuel existant ou créez-en un.

    Le réseau virtuel fournit à Azure Database Migration Service un accès au serveur SQL Server source et à l’instance Azure SQL Database Managed Instance cible.

    Pour plus d’informations sur la création d’un réseau virtuel dans le portail Azure, consultez l’article [Créer un réseau virtuel à l’aide du portail Azure](https://aka.ms/DMSVnet).

6. Sélectionnez un niveau tarifaire.

    Pour plus d’informations sur les coûts et les niveaux de tarification, consultez la [page de tarification](https://aka.ms/dms-pricing).

    Si vous avez besoin d’aide dans le choix du bon niveau pour Azure Database Migration Service, consultez les recommandations dans l’article disponible [ici](https://go.microsoft.com/fwlink/?linkid=861067).  

     ![Configurer des paramètres d’instance Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-settings2.png)

7.  Sélectionnez **Créer** pour créer le service.

## <a name="create-a-migration-project"></a>Créer un projet de migration
Une fois le service créé, recherchez-le dans le portail Azure, ouvrez-le, puis créez un projet de migration.

1. Dans le portail Azure, sélectionnez **Tous les services**, recherchez Azure Database Migration Service, puis sélectionnez **Azure Database Migration Services**.
 
      ![Rechercher toutes les instances Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-search.png)

2. Dans l’écran **Azure Database Migration Services**, recherchez le nom de l’instance Azure Database Migration Service que vous avez créée, puis sélectionnez l’instance.
 
     ![Rechercher votre instance Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-instance-search.png)
 
3. Sélectionnez **+ Nouveau projet de migration**.
4. Dans l’écran **Nouveau projet de migration**, spécifiez un nom pour le projet. Dans la zone de texte **Type de serveur source**, sélectionnez **SQL Server**. Dans la zone de texte **Type de serveur cible**, sélectionnez **Azure SQL Database**. Enfin, dans la zone de texte **Choisir un type d’activité**, sélectionnez **Migration de données hors connexion**. 

    ![Créer un projet Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-create-project2.png)

5.  Sélectionnez **Créer et exécuter une activité** pour créer le projet et exécuter l’activité de migration.

## <a name="specify-source-details"></a>Spécifier les détails de la source
1. Dans l’écran **Détails de la source de migration**, spécifiez les détails de connexion de l’instance SQL Server source.
 
    Veillez à utiliser un nom de domaine complet pour le nom de l’instance SQL Server source. Vous pouvez également utiliser l’adresse IP quand la résolution de noms DNS n’est pas possible.

2. Si vous n’avez pas installé de certificat de confiance sur votre serveur source, cochez la case **Faire confiance au certificat de serveur**.

    Quand aucun certificat de confiance n’est installé, SQL Server génère un certificat auto-signé lorsque l’instance est démarrée. Ce certificat permet de chiffrer les informations d’identification des connexions clientes.

    > [!CAUTION]
    > Les connexions SSL chiffrées à l’aide d’un certificat auto-signé n’offrent pas de sécurité renforcée. Elles sont vulnérables aux attaques de l’intercepteur. Vous ne devez pas compter sur SSL utilisant des certificats auto-signés dans un environnement de production ou sur des serveurs connectés à Internet.

   ![Détails de la source](media/tutorial-sql-server-to-azure-sql/dms-source-details2.png)

## <a name="specify-target-details"></a>Spécifier les détails de la cible
1. Sélectionnez **Enregistrer** puis, dans l’écran **Détails de la cible de migration**, spécifiez les détails de connexion du serveur Azure SQL Database cible, à savoir l’instance Azure SQL Database pré-provisionnée sur laquelle le schéma **AdventureWorks2012** a été déployé à l’aide de l’Assistant Migration de données.

    ![Sélectionner la cible](media/tutorial-sql-server-to-azure-sql/dms-select-target2.png)

2. Sélectionnez **Enregistrer**, puis dans l’écran **Mapper aux bases de données cibles**, mappez les bases de données source et cible pour la migration.

    Si la base de données cible porte le même nom que la base de données source, Azure Database Migration Service sélectionne la base de données cible par défaut.

    ![Mapper aux bases de données cibles](media/tutorial-sql-server-to-azure-sql/dms-map-targets-activity2.png)

3. Sélectionnez **Enregistrer**, dans l’écran **Sélectionner des tables**, développez la liste des tables et passez en revue la liste des champs concernés.

    Notez qu’Azure Database Migration Service sélectionne automatiquement toutes les tables sources vides qui existent sur l’instance Azure SQL Database cible. Si vous souhaitez migrer à nouveau les tables qui contiennent déjà des données, vous devez sélectionner explicitement les tables sur ce panneau.

    ![Sélectionner des tables](media/tutorial-sql-server-to-azure-sql/dms-configure-setting-activity2.png)

4.  Sélectionnez **Enregistrer**, dans l’écran **Résumé de la migration**, dans la zone de texte **Nom de l’activité**, spécifiez un nom pour l’activité de migration.

5. Développez la section **Option de validation** pour afficher l’écran **Choisir l’option de validation**, puis indiquez s’il faut valider les bases de données migrées pour la **comparaison de schémas**, la **cohérence des données** et **l’exactitude des requêtes**.
    
    ![Choisir l’option de validation](media/tutorial-sql-server-to-azure-sql/dms-configuration2.png)

6.  Sélectionnez **Enregistrer**, passez en revue le résumé pour vérifier que les détails de la source et de la cible correspondent à ce que vous avez spécifié précédemment.

    ![Résumé de la migration](media/tutorial-sql-server-to-azure-sql/dms-run-migration2.png)

## <a name="run-the-migration"></a>Exécuter la migration
- Sélectionnez **Exécuter la migration**.

    La fenêtre d’activité de migration s’affiche et l’**état** de l’activité est **En attente**.

    ![État de l’activité](media/tutorial-sql-server-to-azure-sql/dms-activity-status1.png)

## <a name="monitor-the-migration"></a>Surveiller la migration
1. Dans l’écran d’activité de migration, sélectionnez **Actualiser** pour mettre à jour l’affichage jusqu’à voir **l’état** de la migration s’afficher comme **Completed** (Terminé).

    ![État d’activité terminé](media/tutorial-sql-server-to-azure-sql/dms-completed-activity1.png)

2. Une fois la migration terminée, sélectionnez **Télécharger le rapport** pour obtenir un rapport mentionnant les détails associés au processus de migration.

3. Vérifiez les bases de données cibles sur le serveur Azure SQL Database cible.

### <a name="additional-resources"></a>Ressources supplémentaires

 * Atelier pratique [Migration de SQL à l’aide d’Azure Data Migration Service (DMS)](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=3b671509-c3cd-4495-8e8f-354acfa09587).