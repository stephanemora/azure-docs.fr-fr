---
title: 'Didacticiel : utiliser Azure Database Migration Service pour migrer MongoDB vers l’API Azure Cosmos DB pour MongoDB hors connexion | Microsoft Docs'
description: Apprenez à migrer une instance locale de MongoDB vers l’API Azure Cosmos DB pour MongoDB hors connexion à l’aide d’Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: douglasl
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 12/11/2018
ms.openlocfilehash: 5fd3200ab787a26b11feb121b5db125e4a79365c
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960376"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-offline-using-dms"></a>Didacticiel : migrer MongoDB vers l’API Azure Cosmos DB pour MongoDB hors connexion à l’aide de DMS
Vous pouvez utiliser Azure Database Migration Service pour effectuer une migration hors connexion (ponctuelle) de bases de données à partir d’une instance locale ou cloud de MongoDB vers l’API Azure Cosmos DB pour MongoDB.

Ce tutoriel vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * Créer une instance Azure Database Migration Service.
> * Créer un projet de migration en utilisant Azure Database Migration Service.
> * Exécuter la migration.
> * Surveiller la migration.

Dans ce tutoriel, vous migrez un jeu de données dans MongoDB hébergé dans une machine virtuelle Azure vers l’API Azure Cosmos DB pour MongoDB à l’aide d’Azure Database Migration Service. Si vous n'avez pas encore configuré d'instance source de MongoDB, consultez l'article [Installer et configurer MongoDB sur une machine virtuelle Windows dans Azure](https://docs.microsoft.com/azure/virtual-machines/windows/install-mongodb).

## <a name="prerequisites"></a>Prérequis
Pour suivre ce didacticiel, vous devez effectuer les opérations suivantes :
- [Créez un compte d’API Azure Cosmos DB pour MongoDB](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
- Créez un réseau virtuel pour Azure Database Migration Service à l’aide du modèle de déploiement Azure Resource Manager, qui fournit une connectivité de site à site à vos serveurs sources locaux à l’aide de la fonction [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Assurez-vous que les règles de groupe de sécurité Réseau virtuel Microsoft Azure ne bloquent pas les ports de communication suivants : 443, 53, 9354, 445 et 12000. Pour plus d’informations sur le filtrage de groupe de sécurité Réseau virtuel Microsoft Azure, consultez l’article [Filtrer le trafic réseau avec les groupes de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Ouvrez votre pare-feu Windows pour permettre à Azure Database Migration Service d'accéder au serveur MongoDB source, par défaut le port TCP 27017.
- Lorsque vous utilisez une appliance de pare-feu devant vos bases de données sources, vous devrez peut-être ajouter des règles de pare-feu pour permettre à Azure Database Migration Service d’accéder aux bases de données sources pour la migration.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Inscrire le fournisseur de ressources Microsoft.DataMigration
1. Connectez-vous au portail Azure, sélectionnez **Tous les services**, puis **Abonnements**.

   ![Afficher les abonnements au portail](media/tutorial-mongodb-to-cosmosdb/portal-select-subscription1.png)
       
2. Sélectionnez l’abonnement dans lequel vous souhaitez créer l’instance Azure Database Migration Service, puis sélectionnez **Fournisseurs de ressources**.
 
    ![Afficher les fournisseurs de ressources](media/tutorial-mongodb-to-cosmosdb/portal-select-resource-provider.png)
    
3.  Recherchez migration, puis à droite de **Microsoft.DataMigration**, sélectionnez **Inscrire**.
 
    ![S’inscrire auprès du fournisseur de ressources](media/tutorial-mongodb-to-cosmosdb/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Créer une instance
1.  Dans le portail Azure, sélectionnez **+ Créer une ressource**, recherchez Azure Database Migration Service, puis sélectionnez **Azure Database Migration Service** dans la liste déroulante.

    ![Place de marché Azure](media/tutorial-mongodb-to-cosmosdb/portal-marketplace.png)

2.  Dans l’écran **Azure Database Migration Service**, sélectionnez **Créer**.
 
    ![Créer une instance Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-create1.png)
  
3.  Dans l’écran **Créer un service de migration**, spécifiez un nom pour le service, l’abonnement, et un réseau virtuel nouveau ou existant.

4. Sélectionnez l’emplacement dans lequel vous souhaitez créer l’instance Azure Database Migration Service. 

5. Sélectionnez un réseau virtuel existant ou créez-en un.

    Le réseau virtuel fournit à Azure Database Migration Service un accès à l’instance MongoDB source et au compte Azure Cosmos DB cible.

    Pour plus d’informations sur la création d’un réseau virtuel dans le portail Azure, consultez l’article [Créer un réseau virtuel à l’aide du portail Azure](https://aka.ms/DMSVnet).

6. Sélectionnez un niveau tarifaire.

    Pour plus d’informations sur les coûts et les niveaux de tarification, consultez la [page de tarification](https://aka.ms/dms-pricing).

    Si vous avez besoin d'aide lors du choix du bon niveau Azure Database Migration Service, consultez les recommandations du billet de blog disponible [ici](https://go.microsoft.com/fwlink/?linkid=861067).  

     ![Configurer des paramètres d’instance Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7.  Sélectionnez **Créer** pour créer le service.

## <a name="create-a-migration-project"></a>Créer un projet de migration
Une fois le service créé, recherchez-le dans le portail Azure, ouvrez-le, puis créez un projet de migration.

1. Dans le portail Azure, sélectionnez **Tous les services**, recherchez Azure Database Migration Service, puis sélectionnez **Azure Database Migration Services**.
 
      ![Rechercher toutes les instances Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. Dans l’écran **Azure Database Migration Services**, recherchez le nom de l’instance Azure Database Migration Service que vous avez créée, puis sélectionnez l’instance.

3. Sélectionnez **+ Nouveau projet de migration**.

4. Sur l'écran **Nouveau projet de migration**, spécifiez un nom pour le projet. Dans la zone de texte **Type de serveur source**, sélectionnez **MongoDB**. Dans la zone de texte **Type de serveur cible**, sélectionnez **CosmosDB (API MongoDB)**. Enfin, dans la zone de texte **Choisir un type d’activité**, sélectionnez **Migration de données hors connexion**. 

    ![Créer un projet Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5.  Sélectionnez **Créer et exécuter une activité** pour créer le projet et exécuter l’activité de migration.

## <a name="specify-source-details"></a>Spécifier les détails de la source
1. Sur l'écran **Détails de la source**, spécifiez les détails de connexion du serveur MongoDB source.
    
   Vous pouvez également utiliser le mode chaîne de connexion et fournir un emplacement pour un conteneur de fichiers de magasin d’objets blob dans lequel vous avez vidé les données de collection que vous souhaitez migrer.

   > [!NOTE]
   > Azure Database Migration Service peut également migrer des documents bson ou json vers des collections d’API Azure Cosmos DB pour MongoDB.
    
   Vous pouvez également utiliser l’adresse IP quand la résolution de noms DNS est impossible.

   ![Spécifier les détails de la source](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. Sélectionnez **Enregistrer**.

## <a name="specify-target-details"></a>Spécifier les détails de la cible
1. Sur l’écran **Détails de la cible de migration**, spécifiez les détails de connexion du compte Azure Cosmos DB cible, à savoir le compte d’API Azure Cosmos DB pour MongoDB préprovisionné vers lequel vous migrez vos données MongoDB.

    ![Spécifier les détails de la cible](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. Sélectionnez **Enregistrer**.

## <a name="map-to-target-databases"></a>Mapper aux bases de données cibles
1. Dans l’écran **Mapper aux bases de données cibles**, mappez les bases de données source et cible pour la migration.

    Si la base de données cible porte le même nom que la base de données source, Azure Database Migration Service sélectionne la base de données cible par défaut.

    Si la chaîne **Create** apparaît en regard du nom de la base de données, cela indique qu'Azure Database Migration Service n'a pas trouvé la base de données cible et qu'il va créer la base de données pour vous.

    À ce stade de la migration, vous pouvez [configurer le débit](https://docs.microsoft.com/azure/cosmos-db/set-throughput). Dans Cosmos DB, vous pouvez configurer le débit au niveau de la base de données ou individuellement pour chaque collection. Le débit est mesuré en [unités de requête](https://docs.microsoft.com/azure/cosmos-db/request-units) (RU). Apprenez-en davantage sur les [tarifs d'Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

    ![Mapper aux bases de données cibles](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. Sélectionnez **Enregistrer**.
3. Sur l'écran **Paramètre de collection**, développez la liste des collections, puis consultez la liste des collections à migrer.

    Notez qu'Azure Database Migration Service sélectionne automatiquement toutes les collections qui existent sur l'instance source de MongoDB et qui n'existent pas sur le compte Azure Cosmos DB cible. Si vous souhaitez migrer à nouveau les collections qui contiennent déjà des données, vous devez sélectionner explicitement ces collections sur ce panneau.

    Vous pouvez spécifier le nombre de RU à utiliser par les collections. Azure Database Migration Service suggère des paramètres par défaut intelligents en fonction de la taille de la collection.

    Vous pouvez également spécifier une clé de partition afin de tirer parti du [partitionnement dans Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview) pour une extensibilité optimale. Veillez à consulter les [meilleures pratiques de sélection d'une clé de partition](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey).

    ![Sélectionner des tables de collections](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. Sélectionnez **Enregistrer**.

5. Dans l’écran **Récapitulatif de la migration**, spécifiez un nom pour l’activité de migration dans la zone de texte **Nom de l’activité**.

    ![Résumé de la migration](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>Exécuter la migration
- Sélectionnez **Exécuter la migration**.

    La fenêtre d'activité de migration s'affiche. L'**État** de l'activité est **Non démarré**.

    ![État de l'activité](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>Surveiller la migration
- Dans l’écran d’activité de migration, sélectionnez **Actualiser** pour mettre à jour l’affichage jusqu’à voir **l’état** de la migration s’afficher comme **Completed** (Terminé).

   > [!NOTE]
   > Vous pouvez sélectionner l'activité pour obtenir des détails sur les métriques de migration au niveau des bases de données et des collections.

    ![État de l'activité - Terminé](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-cosmos-db"></a>Vérifier les données dans Cosmos DB

- Au terme de la migration, vous pouvez consulter votre compte Azure Cosmos DB pour vérifier que toutes les collections ont bien été migrées.

    ![État de l'activité - Terminé](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="additional-resources"></a>Ressources supplémentaires

 * [Informations sur le service Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>Étapes suivantes
- Pour accéder à d'autres scénarios, lisez les instructions de migration du [Guide de migration de bases de données](https://datamigration.microsoft.com/) de Microsoft.
