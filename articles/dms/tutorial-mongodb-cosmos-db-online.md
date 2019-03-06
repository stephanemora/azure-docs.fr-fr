---
title: 'Tutoriel : Utiliser le service Azure Database Migration Service pour migrer MongoDB vers l’API Azure Cosmos DB pour MongoDB en ligne | Microsoft Docs'
description: Apprenez à migrer une instance locale de MongoDB vers l’API Azure Cosmos DB pour MongoDB en ligne à l’aide du service Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: douglasl
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 02/27/2019
ms.openlocfilehash: 06e76b8eed283c6ef09f38e876c60b05477cf0ce
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2019
ms.locfileid: "56985816"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-online-using-dms-preview"></a>Tutoriel : Migrer MongoDB vers l’API Azure Cosmos DB pour MongoDB en ligne à l’aide de DMS (préversion)
Vous pouvez utiliser le service Azure Database Migration Service pour effectuer une migration en ligne (temps d’arrêt minimal) de bases de données à partir d’une instance locale ou cloud de MongoDB vers l’API Azure Cosmos DB pour MongoDB.

Ce tutoriel vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * Créer une instance Azure Database Migration Service.
> * Créer un projet de migration en utilisant Azure Database Migration Service.
> * Exécuter la migration.
> * Surveiller la migration.
> * Effectuer la migration quand vous êtes prêt

Dans ce tutoriel, vous migrez un jeu de données dans MongoDB hébergé dans une machine virtuelle Azure vers l’API Azure Cosmos DB pour MongoDB avec un temps d’arrêt minimal à l’aide du service Azure Database Migration Service. Si vous n'avez pas encore configuré d'instance source de MongoDB, consultez l'article [Installer et configurer MongoDB sur une machine virtuelle Windows dans Azure](https://docs.microsoft.com/azure/virtual-machines/windows/install-mongodb).

> [!NOTE]
> Effectuer une migration en ligne à l’aide d’Azure Database Migration Service nécessite la création d’une instance basée sur le niveau tarifaire Premium.

> [!IMPORTANT]
> Pour une expérience de migration optimale, Microsoft vous recommande de créer une instance Azure Database Migration Service dans la même région Azure que la base de données cible. Le déplacement des données entre les régions ou les zones géographiques peut ralentir le processus de migration.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Cet article décrit une migration en ligne de MongoDB vers l’API Azure Cosmos DB pour MongoDB. Pour une migration hors connexion, consultez [Migrer MongoDB vers l’API Azure Cosmos DB pour MongoDB hors connexion à l’aide de DMS](tutorial-mongodb-cosmos-db.md).

## <a name="prerequisites"></a>Prérequis
Pour suivre ce didacticiel, vous devez effectuer les opérations suivantes :
- [Créez un compte d’API Azure Cosmos DB pour MongoDB](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
- Créez un réseau virtuel Azure (VNET) pour le service Azure Database Migration Service à l’aide du modèle de déploiement Azure Resource Manager, qui fournit une connectivité de site à site à vos serveurs sources locaux via [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou une passerelle [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Vérifiez que les règles du groupe de sécurité Réseau virtuel ne bloquent pas les ports de communication suivants : 443, 53, 9354, 445 et 12000. Pour plus d’informations sur le filtrage de groupe de sécurité Réseau virtuel Microsoft Azure, consultez l’article [Filtrer le trafic réseau avec les groupes de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Changez le pare-feu du serveur source pour permettre au service Azure Database Migration Service d’accéder au serveur MongoDB source, lequel est accessible par défaut sur le port TCP 27 017.
- Lorsque vous utilisez une appliance de pare-feu devant vos bases de données sources, vous devrez peut-être ajouter des règles de pare-feu pour permettre à Azure Database Migration Service d’accéder aux bases de données sources pour la migration.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Inscrire le fournisseur de ressources Microsoft.DataMigration
1. Connectez-vous au portail Azure, sélectionnez **Tous les services**, puis **Abonnements**.

   ![Afficher les abonnements au portail](media/tutorial-mongodb-to-cosmosdb-online/portal-select-subscription1.png)
       
2. Sélectionnez l’abonnement dans lequel vous souhaitez créer l’instance Azure Database Migration Service, puis sélectionnez **Fournisseurs de ressources**.
 
    ![Afficher les fournisseurs de ressources](media/tutorial-mongodb-to-cosmosdb-online/portal-select-resource-provider.png)
    
3.  Recherchez migration, puis à droite de **Microsoft.DataMigration**, sélectionnez **Inscrire**.
 
    ![S’inscrire auprès du fournisseur de ressources](media/tutorial-mongodb-to-cosmosdb-online/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Créer une instance
1.  Dans le portail Azure, sélectionnez **+ Créer une ressource**, recherchez Azure Database Migration Service, puis sélectionnez **Azure Database Migration Service** dans la liste déroulante.

    ![Place de marché Azure](media/tutorial-mongodb-to-cosmosdb-online/portal-marketplace.png)

2.  Dans l’écran **Azure Database Migration Service**, sélectionnez **Créer**.
 
    ![Créer une instance Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb-online/dms-create1.png)
  
3.  Dans l’écran **Créer un service de migration**, spécifiez un nom pour le service, l’abonnement, et un réseau virtuel nouveau ou existant.

4. Sélectionnez l’emplacement dans lequel vous souhaitez créer l’instance Azure Database Migration Service. 

5. Sélectionnez un réseau virtuel existant ou créez-en un.

    Le réseau virtuel fournit à Azure Database Migration Service un accès à l’instance MongoDB source et au compte Azure Cosmos DB cible.

    Pour plus d’informations sur la création d’un réseau virtuel dans le portail Azure, consultez l’article [Créer un réseau virtuel à l’aide du portail Azure](https://aka.ms/DMSVnet).

6. Sélectionnez une référence (SKU) dans le niveau tarifaire Premium.

    > [!NOTE]
    > Les migrations en ligne sont uniquement prises en charge lorsque vous utilisez le niveau Premium. Pour plus d’informations sur les coûts et les niveaux de tarification, consultez la [page de tarification](https://aka.ms/dms-pricing).

    Si vous avez besoin d’aide pour choisir le niveau Azure Database Migration Service approprié, consultez les suggestions du billet de blog disponible [ici](https://go.microsoft.com/fwlink/?linkid=861067).  

     ![Configurer des paramètres d’instance Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb-online/dms-settings3.png)

7.  Sélectionnez **Créer** pour créer le service.

## <a name="create-a-migration-project"></a>Créer un projet de migration
Une fois le service créé, recherchez-le dans le portail Azure, ouvrez-le, puis créez un projet de migration.

1. Dans le portail Azure, sélectionnez **Tous les services**, recherchez Azure Database Migration Service, puis sélectionnez **Azure Database Migration Services**.
 
    ![Rechercher toutes les instances Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb-online/dms-search.png)

2. Dans l’écran **Azure Database Migration Services**, recherchez le nom de l’instance Azure Database Migration Service que vous avez créée, puis sélectionnez l’instance.

    Vous pouvez également découvrir l’instance de service Azure Database Migration Service à partir du volet de recherche du Portail Azure.

    ![Utiliser le volet de recherche dans le Portail Azure](media/tutorial-mongodb-to-cosmosdb-online/dms-search-portal.png)

3. Sélectionnez **+ Nouveau projet de migration**.

4. Dans l’écran **Nouveau projet de migration**, spécifiez un nom pour le projet. Dans la zone de texte **Type de serveur source**, sélectionnez **MongoDB**. Dans la zone de texte **Type de serveur cible**, sélectionnez **Cosmos DB (API MongoDB)**, puis dans **Choisir un type d’activité**, sélectionnez **Migration de données en ligne [préversion]**.

    ![Créer un projet Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb-online/dms-create-project1.png)

5.  Sélectionnez **Enregistrer**, puis **Créer et exécuter une activité** pour créer le projet et exécuter l’activité de migration.

## <a name="specify-source-details"></a>Spécifier les détails de la source
1. Sur l'écran **Détails de la source**, spécifiez les détails de connexion du serveur MongoDB source.

    Il existe trois modes de connexion à une source :
       * **Mode standard**, qui accepte un nom de domaine complet ou une adresse IP, un numéro de port et des informations d’identification de connexion.
       * **Mode de chaîne de connexion**, qui accepte une chaîne de connexion MongoDB comme décrit dans l’article sur le [format d’URI de la chaîne de connexion](https://docs.mongodb.com/manual/reference/connection-string/).
       * **Mode de données du stockage Azure**, qui accepte une URL SAP du conteneur d’objets blob. Sélectionnez **L’objet blob contient des images mémoire BSON** si le conteneur d’objets blob contient des images mémoire BSON produites par l’[outil bsondump](https://docs.mongodb.com/manual/reference/program/bsondump/) MongoDB, et désélectionnez-le si le conteneur contient des fichiers JSON.

      Si vous sélectionnez cette option, vérifiez que la chaîne de connexion de compte de stockage s’affiche au format suivant :

    ```
    https://blobnameurl/container?SASKEY
    ```
      De plus, en fonction des informations d’images mémoire de type dans le stockage Azure, gardez à l’esprit les informations suivantes.

      * Pour les images mémoire BSON, les données dans le conteneur d’objets blob doivent être au format bsondump, de sorte que les fichiers de données sont placés dans des dossiers nommés d’après les bases de données conteneur au format collection.bson. Les fichiers de métadonnées (le cas échéant) doivent être nommés en utilisant le format *collection*.metadata.json.

      * Pour les images mémoire JSON, les fichiers présents dans le conteneur d’objets blob doivent être placés dans des dossiers nommés d’après les bases de données conteneur. Dans chaque dossier de base de données, les fichiers de données doivent être placés dans un sous-dossier appelé « data » et nommé en utilisant le format *collection*.json. Les fichiers de métadonnées (le cas échéant) doivent être placés dans un sous-dossier appelé « metadata » et nommé en utilisant le même format, *collection*.json. Les fichiers de métadonnées doivent être au même format que celui produit par l’outil de bsondump MongoDB.

   Vous pouvez utiliser l’adresse IP quand la résolution de noms DNS est impossible.

   ![Spécifier les détails de la source](media/tutorial-mongodb-to-cosmosdb-online/dms-specify-source1.png)

2. Sélectionnez **Enregistrer**.

   > [!NOTE]
   > L’adresse du serveur source doit être l’adresse du serveur principal si la source est un jeu de réplicas, et le routeur si la source est un cluster MongoDB shardé. Pour un cluster MongoDB shardé, Azure Database Migration Service doit pouvoir se connecter aux shards individuels du cluster, ce qui peut nécessiter l’ouverture du pare-feu sur des machines supplémentaires.          

## <a name="specify-target-details"></a>Spécifier les détails de la cible
1. Sur l’écran **Détails de la cible de migration**, spécifiez les détails de connexion du compte Azure Cosmos DB cible, à savoir le compte d’API Azure Cosmos DB pour MongoDB préprovisionné vers lequel vous migrez vos données MongoDB.

    ![Spécifier les détails de la cible](media/tutorial-mongodb-to-cosmosdb-online/dms-specify-target1.png)

2. Sélectionnez **Enregistrer**.

## <a name="map-to-target-databases"></a>Mapper aux bases de données cibles
1. Dans l’écran **Mapper aux bases de données cibles**, mappez les bases de données source et cible pour la migration.

    Si la base de données cible porte le même nom que la base de données source, Azure Database Migration Service sélectionne la base de données cible par défaut.

    Si la chaîne **Create** apparaît en regard du nom de la base de données, cela indique qu'Azure Database Migration Service n'a pas trouvé la base de données cible et qu'il va créer la base de données pour vous.

    À ce stade de la migration, si un débit de partage est souhaité sur la base de données, spécifiez un RU de débit. Dans Cosmos DB, vous pouvez configurer le débit au niveau de la base de données ou individuellement pour chaque collection. Le débit est mesuré en [unités de requête](https://docs.microsoft.com/azure/cosmos-db/request-units) (RU). Apprenez-en davantage sur les [tarifs d'Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

    ![Mapper aux bases de données cibles](media/tutorial-mongodb-to-cosmosdb-online/dms-map-target-databases1.png)

2. Sélectionnez **Enregistrer**.

3. Sur l'écran **Paramètre de collection**, développez la liste des collections, puis consultez la liste des collections à migrer.

    Notez qu'Azure Database Migration Service sélectionne automatiquement toutes les collections qui existent sur l'instance source de MongoDB et qui n'existent pas sur le compte Azure Cosmos DB cible. Si vous souhaitez migrer à nouveau des collections contenant déjà des données, vous devez les sélectionner explicitement dans cet écran.

    Vous pouvez spécifier le nombre de RU à utiliser par les collections. Dans la plupart des cas, une valeur comprise entre 500 (1 000 au minimum pour les collections shardées) et 4 000 doit suffire. Azure Database Migration Service suggère des paramètres par défaut intelligents en fonction de la taille de la collection.

    Vous pouvez également spécifier une clé de partition afin de tirer parti du [partitionnement dans Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview) pour une extensibilité optimale. Veillez à consulter les [meilleures pratiques de sélection d'une clé de partition](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey). Si vous n’avez pas de clé de partition, vous pouvez toujours utiliser **_id** comme clé de sharding pour un meilleur débit.

    ![Sélectionner des tables de collections](media/tutorial-mongodb-to-cosmosdb-online/dms-collection-setting1.png)

4. Sélectionnez **Enregistrer**.

5. Dans l’écran **Récapitulatif de la migration**, spécifiez un nom pour l’activité de migration dans la zone de texte **Nom de l’activité**.

    ![Résumé de la migration](media/tutorial-mongodb-to-cosmosdb-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>Exécuter la migration
- Sélectionnez **Exécuter la migration**.

   La fenêtre d’activité de migration s’affiche, et l’**état** de l’activité est indiqué.

   ![État de l'activité](media/tutorial-mongodb-to-cosmosdb-online/dms-activity-status1.png)

## <a name="monitor-the-migration"></a>Surveiller la migration
- Dans l’écran d’activité de migration, sélectionnez **Actualiser** pour mettre à jour l’affichage jusqu’à ce que l’**état** de la migration indique **Relecture**.

   > [!NOTE]
   > Vous pouvez sélectionner l'activité pour obtenir des détails sur les métriques de migration au niveau des bases de données et des collections.

   ![État d’activité Relecture](media/tutorial-mongodb-to-cosmosdb-online/dms-activity-replaying.png)

## <a name="verify-data-in-cosmos-db"></a>Vérifier les données dans Cosmos DB

1. Apportez des changements à votre base de données MongoDB source.
2. Connectez-vous à Cosmos DB pour vérifier si les données sont répliquées à partir du serveur MongoDB source.

    ![État d’activité Relecture](media/tutorial-mongodb-to-cosmosdb-online/dms-verify-data.png)
 
## <a name="complete-the-migration"></a>Effectuer la migration

* Une fois que tous les documents de la source sont disponibles sur la cible Cosmos DB, sélectionnez **Terminer** dans le menu contextuel de l’activité de migration pour effectuer la migration.

    Cette action va permettre d’achever la relecture de toutes les modifications en attente et entraîner l’exécution de la migration.

    ![État d’activité Relecture](media/tutorial-mongodb-to-cosmosdb-online/dms-finish-migration.png)

## <a name="additional-resources"></a>Ressources supplémentaires

 * [Informations sur le service Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>Étapes suivantes
- Pour accéder à d'autres scénarios, lisez les instructions de migration du [Guide de migration de bases de données](https://datamigration.microsoft.com/) de Microsoft.