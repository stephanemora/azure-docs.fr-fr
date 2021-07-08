---
title: 'Tutoriel : Migrer MongoDB hors connexion vers l’API Azure Cosmos DB pour MongoDB'
titleSuffix: Azure Database Migration Service
description: Procédez à une migration hors connexion d'une instance locale de MongoDB vers l'API Azure Cosmos DB pour MongoDB à l'aide d'Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 05/19/2021
ms.openlocfilehash: 31f3dc8adcd17c7795351877c856fa854582007b
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110456550"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-db-api-for-mongodb-offline"></a>Tutoriel : Migrer MongoDB vers l'API Azure Cosmos DB pour MongoDB hors connexion
[!INCLUDE[appliesto-mongodb-api](../cosmos-db/includes/appliesto-mongodb-api.md)]

> [!IMPORTANT]  
> Veuillez lire ce guide dans son intégralité avant d’effectuer les étapes de migration.
>

Ce guide de migration de MongoDB fait partie d’une série sur la migration de MongoDB. Les étapes critiques de la migration de MongoDB sont la [pré-migration](../cosmos-db/mongodb-pre-migration.md), la migration proprement dite et la [post-migration](../cosmos-db/mongodb-post-migration.md), comme indiqué ci-dessous.

![Diagramme des étapes de migration.](../cosmos-db/media/mongodb-pre-migration/overall-migration-steps.png)

## <a name="overview-of-offline-data-migration-from-mongodb-to-azure-cosmos-db-using-dms"></a>Vue d’ensemble de la migration des données hors connexion de MongoDB vers Azure Cosmos DB avec DMS

Utilisez Azure Database Migration Service pour procéder à une migration hors connexion et ponctuelle de bases de données situées sur une instance locale ou cloud de MongoDB vers l'API Azure Cosmos DB pour MongoDB.

Dans ce tutoriel, vous allez apprendre à :
> [!div class="checklist"]
>
> * Créer une instance Azure Database Migration Service.
> * Créer un projet de migration en utilisant Azure Database Migration Service.
> * Exécuter la migration.
> * Surveiller la migration.

Dans le cadre de ce tutoriel, vous allez migrer un jeu de données MongoDB hébergé sur une machine virtuelle Azure. Vous allez utiliser Azure Database Migration Service pour migrer le jeu de données vers l'API Azure Cosmos DB pour MongoDB. Si vous n'avez pas encore configuré d'instance source de MongoDB, consultez [Installer et configurer MongoDB sur une machine virtuelle Windows dans Azure](/previous-versions/azure/virtual-machines/windows/install-mongodb).

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous devez effectuer les opérations suivantes :

* [Suivez les étapes de prémigration](../cosmos-db/mongodb-pre-migration.md), comme l'estimation du débit et le choix d'une clé de partition.
* [Créez un compte pour l'API Azure Cosmos DB pour MongoDB](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
* Créez un réseau virtuel Microsoft Azure pour Azure Database Migration Service à l'aide d'Azure Resource Manager. Ce modèle de déploiement fournit une connectivité de site à site à vos serveurs sources locaux par le biais d'[Azure ExpressRoute](../expressroute/expressroute-introduction.md) ou d'un [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Pour plus d'informations sur la création d'un réseau virtuel, consultez la [documentation relative aux réseaux virtuels Azure](../virtual-network/index.yml), en particulier les articles de « démarrage rapide » qui fournissent des informations pas à pas.

    > [!NOTE]
    > Pendant la configuration du réseau virtuel, si vous utilisez ExpressRoute avec le peering réseau à Microsoft, ajoutez ces [points de terminaison](../virtual-network/virtual-network-service-endpoints-overview.md) au sous-réseau où doit être provisionné le service :
    >
    > * Point de terminaison de base de données cible (un point de terminaison SQL ou Azure Cosmos DB, par exemple)
    > * Point de terminaison de stockage
    > * Point de terminaison Service Bus
    >
    > Cette configuration est nécessaire car Azure Database Migration Service ne dispose pas d’une connectivité Internet.

* Vérifiez que les règles de votre groupe de sécurité de votre réseau virtuel ne bloquent pas les ports de communication suivants : 53, 443, 445, 9354 et 10000-20000. Pour plus d’informations, consultez [Filtrer le trafic réseau avec les groupes de sécurité réseau](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Ouvrez votre pare-feu Windows pour permettre à Azure Database Migration Service d’accéder au serveur MongoDB source via le port TCP 27017 (par défaut).
* Lorsque vous utilisez une appliance de pare-feu devant votre base de données source, vous pouvez être amené à ajouter des règles de pare-feu afin de permettre à Azure Database Migration Service d'accéder à la base de données source pour la migration.

## <a name="configure-the-server-side-retry-feature"></a>Configurer la fonctionnalité Nouvelle tentative côté serveur

En migrant de MongoDB vers Azure Cosmos DB, vous pouvez bénéficier des fonctionnalités de gouvernance des ressources. Ces fonctionnalités vous permettent de tirer pleinement parti de vos RU (unités de requête) de débit approvisionnées. Azure Cosmos DB peut limiter une requête Database Migration Service au cours de la migration si celle-ci dépasse les RU approvisionnées par le conteneur. Vous devrez alors renouveler cette requête.

Database Migration Service est capable d'effectuer de nouvelles tentatives. Il est important de comprendre que le temps nécessaire aux allers-retours impliqués dans le tronçon réseau entre Database Migration Service et Azure Cosmos DB affecte le temps de réponse global de cette requête. L’amélioration du temps de réponse aux demandes limitées peut raccourcir le temps total nécessaire à la migration.

La fonctionnalité Nouvelle tentative côté serveur d'Azure Cosmos DB permet au service d'intercepter les codes d'erreur de limitation et d'effectuer une nouvelle tentative avec un temps d'aller-retour beaucoup plus court, améliorant ainsi considérablement les temps de réponse aux requêtes.

Pour utiliser la fonctionnalité Nouvelle tentative côté serveur, sur le portail Azure Cosmos DB, sélectionnez **Fonctionnalités** > **Nouvelle tentative côté serveur**.

![Capture d'écran indiquant où trouver la fonctionnalité Nouvelle tentative côté serveur.](media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-feature.png)

Si la fonctionnalité est désactivée, sélectionnez **Activer**.

![Capture d'écran montrant comment activer la fonctionnalité Nouvelle tentative côté serveur.](media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-enable.png)

## <a name="register-the-resource-provider"></a>Inscrire le fournisseur de ressources

1. Connectez-vous au portail Azure, sélectionnez **Tous les services**, puis **Abonnements**.

   ![Capture d'écran illustrant l'option Abonnements sur le portail.](media/tutorial-mongodb-to-cosmosdb/portal-select-subscription1.png)

2. Sélectionnez l’abonnement dans lequel vous voulez créer l’instance Azure Database Migration Service, puis sélectionnez **Fournisseurs de ressources**.

    ![Capture d'écran illustrant l'option Fournisseurs de ressources.](media/tutorial-mongodb-to-cosmosdb/portal-select-resource-provider.png)

3. Recherchez migration, puis à droite de **Microsoft.DataMigration**, sélectionnez **Inscrire**.

    ![Capture d'écran montrant comment inscrire le fournisseur de ressources.](media/tutorial-mongodb-to-cosmosdb/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Créer une instance

1. Dans le portail Azure, sélectionnez **+ Créer une ressource**, recherchez Azure Database Migration Service, puis sélectionnez **Azure Database Migration Service** dans la liste déroulante.

    ![Capture d’écran qui montre la Place de marché Azure.](media/tutorial-mongodb-to-cosmosdb/portal-marketplace.png)

2. Dans l’écran **Azure Database Migration Service**, sélectionnez **Créer**.

    ![Capture d'écran montrant comment créer une instance d'Azure Database Migration Service.](media/tutorial-mongodb-to-cosmosdb/dms-create1.png)
  
3. Dans **Créer un service de migration**, spécifiez le nom du service, l'abonnement, et un groupe de ressources nouveau ou existant.

4. Sélectionnez l’emplacement dans lequel vous souhaitez créer l’instance Azure Database Migration Service. 

5. Sélectionnez un réseau virtuel existant ou créez-en un.

    Le réseau virtuel fournit à Azure Database Migration Service un accès à l’instance MongoDB source et au compte Azure Cosmos DB cible.

    Pour plus d'informations sur la création d'un réseau virtuel sur le portail Azure, consultez [Créer un réseau virtuel au moyen du portail Azure](../virtual-network/quick-create-portal.md).

6. Sélectionnez un niveau tarifaire.

    Pour plus d’informations sur les coûts et les niveaux de tarification, consultez la [page de tarification](https://aka.ms/dms-pricing).

    ![Capture d'écran illustrant les paramètres de configuration de l'instance d'Azure Database Migration Service.](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7. Sélectionnez **Créer** pour créer le service.

## <a name="create-a-migration-project"></a>Créer un projet de migration

Après avoir créé le service, recherchez-le sur le portail Azure, puis ouvrez-le. Créez alors un nouveau projet de migration.

1. Dans le portail Azure, sélectionnez **Tous les services**, recherchez Azure Database Migration Service, puis sélectionnez **Azure Database Migration Services**.

      ![Capture d'écran montrant comment localiser toutes les instances d'Azure Database Migration Service.](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. Dans l’écran **Azure Database Migration Services**, recherchez le nom de l’instance Azure Database Migration Service que vous avez créée, puis sélectionnez l’instance.

3. Sélectionnez **+ Nouveau projet de migration**.

4. Dans **Nouveau projet de migration**, spécifiez le nom du projet, et dans la zone de texte **Type de serveur source**, sélectionnez **MongoDB**. Dans la zone de texte **Type de serveur cible**, sélectionnez **CosmosDB (API MongoDB)** , et sous **Choisir un type d'activité**, sélectionnez **Migration de données hors connexion**. 

    ![Capture d'écran illustrant les options de projets.](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5. Sélectionnez **Créer et exécuter une activité** pour créer le projet et exécuter l’activité de migration.

## <a name="specify-source-details"></a>Spécifier les détails de la source

1. Sur l'écran **Détails de la source**, spécifiez les détails de connexion du serveur MongoDB source.

   > [!IMPORTANT]
   > Azure Database Migration Service ne prend pas en charge Azure Cosmos DB comme source.

    Il existe trois modes de connexion à une source :
   * **Mode standard**, qui accepte un nom de domaine complet ou une adresse IP, un numéro de port et des informations d'identification de connexion.
   * **Mode de chaîne de connexion**, qui accepte une chaîne de connexion MongoDB comme décrit dans [Format d'URI de la chaîne de connexion](https://docs.mongodb.com/manual/reference/connection-string/).
   * **Mode de données du stockage Azure**, qui accepte une URL SAP du conteneur d’objets blob. Sélectionnez **L'objet blob contient des images mémoire BSON** si le conteneur d'objets blob contient des images mémoire BSON produites par l'[outil bsondump](https://docs.mongodb.com/manual/reference/program/bsondump/) MongoDB. Ne sélectionnez pas cette option si le conteneur contient des fichiers JSON.

     Si vous sélectionnez cette option, vérifiez que la chaîne de connexion de compte de stockage s'affiche au format suivant :

     ```
     https://blobnameurl/container?SASKEY
     ```

     Vous trouverez cette chaîne de connexion SAP du conteneur d'objets blob dans l'Explorateur Stockage Azure. La création de la signature d'accès partagé du conteneur concerné fournit l'URL au format requis.
     
     De plus, en fonction des informations d'images mémoire de type qui se trouvent dans Stockage Azure, gardez à l'esprit ce qui suit :

     * Pour les images mémoire BSON, les données stockées dans le conteneur d'objets blob doivent être au format bsondump. Placez les fichiers de données dans des dossiers portant le nom des bases de données qui les contiennent au format *collection.bson*. Nommez tous les fichiers de métadonnées en utilisant le format *collection.metadata.js*.

     * Pour les images mémoire JSON, les fichiers présents dans le conteneur d’objets blob doivent être placés dans des dossiers nommés d’après les bases de données conteneur. Dans chaque dossier de base de données, les fichiers de données doivent être placés dans un sous-dossier appelé *data* et nommé en utilisant le format *collection.json*. Placez les fichiers de métadonnées dans un sous-dossier appelé *metadata* et nommé en utilisant le même format, *collection.json*. Les fichiers de métadonnées doivent être au même format que celui produit par l’outil de bsondump MongoDB.

    > [!IMPORTANT]
    > Nous vous déconseillons d'utiliser des certificats auto-signés sur le serveur MongoDB. Si vous devez en utiliser un, connectez-vous au serveur en utilisant le mode chaîne de connexion, et assurez-vous que la chaîne de connexion contient des guillemets ("").
    >
    >```
    >&sslVerifyCertificate=false
    >```

   Vous pouvez également utiliser l'adresse IP lorsque la résolution de noms DNS est impossible.

   ![Capture d'écran illustrant la spécification des détails de la source.](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. Sélectionnez **Enregistrer**.

## <a name="specify-target-details"></a>Spécifier les détails de la cible

1. Sur l'écran **Détails de la cible de migration**, spécifiez les détails de connexion du compte Azure Cosmos DB cible. Ce compte correspond au compte API Azure Cosmos DB pour MongoDB pré-approvisionné vers lequel vous migrez vos données MongoDB.

    ![Capture d'écran illustrant la spécification des détails de la cible.](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. Sélectionnez **Enregistrer**.

## <a name="map-to-target-databases"></a>Mapper aux bases de données cibles

1. Dans l’écran **Mapper aux bases de données cibles**, mappez les bases de données source et cible pour la migration.

    Si la base de données cible porte le même nom que la base de données source, Azure Database Migration Service sélectionne la base de données cible par défaut.

    Si **Create** apparaît en regard du nom de la base de données, cela indique qu'Azure Database Migration Service n'a pas trouvé la base de données cible et qu'il va créer la base de données pour vous.

    À ce stade de la migration, vous pouvez [configurer le débit](../cosmos-db/set-throughput.md). Dans Azure Cosmos DB, vous pouvez configurer le débit au niveau de la base de données ou individuellement pour chaque collection. Le débit est mesuré en [unités de requête](../cosmos-db/request-units.md). Apprenez-en davantage sur les [tarifs d'Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

    ![Capture d'écran illustrant le mappage avec les bases de données cibles.](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. Sélectionnez **Enregistrer**.
3. Sur l'écran **Paramètre de collection**, développez la liste des collections, puis consultez la liste des collections à migrer.

    Azure Database Migration Service sélectionne automatiquement l'ensemble des collections qui se trouvent sur l'instance source de MongoDB et qui ne se trouvent pas sur le compte Azure Cosmos DB cible. Si vous souhaitez migrer à nouveau les collections qui contiennent déjà des données, vous devez sélectionner explicitement ces collections sur ce volet.

    Vous pouvez spécifier le nombre de RU à utiliser par les collections. Azure Database Migration Service suggère des valeurs par défaut intelligentes en fonction de la taille de la collection.

    > [!NOTE]
    > Procédez à la migration de la base de données et à la collecte en parallèle. Si nécessaire, vous pouvez utiliser plusieurs instances d'Azure Database Migration Service pour accélérer l'exécution.

    Vous pouvez également spécifier une clé de partition afin de tirer parti du [partitionnement dans Azure Cosmos DB](../cosmos-db/partitioning-overview.md) pour une extensibilité optimale. Consultez les [meilleures pratiques de sélection d'une clé de partition](../cosmos-db/partitioning-overview.md#choose-partitionkey).

    ![Capture d'écran illustrant la sélection des tables de collections.](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. Sélectionnez **Enregistrer**.

5. Dans l’écran **Récapitulatif de la migration**, spécifiez un nom pour l’activité de migration dans la zone de texte **Nom de l’activité**.

    ![Capture d'écran illustrant le résumé la migration.](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>Exécuter la migration

Sélectionnez **Exécuter la migration**. La fenêtre de l'activité de migration s'affiche. L'État de l'activité est **Non démarré**.

![Capture d'écran illustrant l'état de l'activité.](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>Surveiller la migration

Sur l'écran de l'activité de migration, sélectionnez **Actualiser** pour mettre à jour l'affichage jusqu'à ce que l'état de la migration soit **Terminé**.

> [!NOTE]
> Vous pouvez sélectionner l'activité pour obtenir des détails sur les métriques de migration au niveau des bases de données et des collections.

![Capture d'écran illustrant l'état Terminé de l'activité.](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-azure-cosmos-db"></a>Vérifier les données dans Azure Cosmos DB

Au terme de la migration, vous pouvez consulter votre compte Azure Cosmos DB pour vérifier que toutes les collections ont bien été migrées.

![Capture d’écran montrant où consulter votre compte Azure Cosmos DB pour vérifier que toutes les collections ont bien été migrées](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="post-migration-optimization"></a>Optimisation de la post-migration

Après avoir migré les données stockées dans la base de données MongoDB vers l'API Azure Cosmos DB pour MongoDB, vous pouvez vous connecter à Azure Cosmos DB et gérer les données. Vous pouvez également effectuer d'autres étapes d'optimisation post-migration. Il peut s'agir de l'optimisation de la stratégie d'indexation, de la mise à jour du niveau de cohérence par défaut ou de la configuration de la distribution globale pour votre compte Azure Cosmos DB. Pour plus d'informations, consultez [Optimisation de la post-migration](../cosmos-db/mongodb-post-migration.md).

## <a name="next-steps"></a>Étapes suivantes

Pour accéder à d'autres scénarios, lisez les instructions de migration du [Guide de migration de bases de données Azure](https://datamigration.microsoft.com/).



