---
title: Migrer MongoDB hors connexion vers l’API Azure Cosmos DB pour MongoDB à l’aide des outils natifs MongoDB
description: Découvrez comment les outils natifs MongoDB peuvent être utilisés pour migrer de petits jeux de données à partir d’instances MongoDB vers Azure Cosmos DB
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 02/10/2021
ms.reviewer: sngun
ms.openlocfilehash: 2e9f3c877a5c4650d3e31fa414cac76837f4c9e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101655749"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-offline-using-mongodb-native-tools"></a>Tutoriel : Migrer MongoDB vers l’API Azure Cosmos DB pour MongoDB hors connexion à l’aide des outils natifs MongoDB

Vous pouvez utiliser les outils natifs MongoDB pour effectuer une migration hors connexion (ponctuelle) de bases de données à partir d’une instance locale ou cloud de MongoDB vers l’API Azure Cosmos DB pour MongoDB.

Dans ce tutoriel, vous allez apprendre à :
> [!div class="checklist"]
>
> * Choisir l’outil natif MongoDB approprié pour votre cas d’utilisation.
> * Exécuter la migration.
> * Surveiller la migration.
> * Vérifier que la migration s’est correctement déroulée.

Dans ce tutoriel, vous effectuez la migration d’un jeu de données dans MongoDB, hébergé sur une machine virtuelle Azure, vers l’API Azure Cosmos DB pour MongoDB à l’aide des outils natifs MongoDB. Les outils natifs MongoDB sont un ensemble de fichiers binaires qui facilitent la manipulation de données sur une instance MongoDB existante. Comme Azure Cosmos DB expose une API Mongo, les outils natifs MongoDB sont en mesure d’insérer des données dans Azure Cosmos DB. Le sujet de ce document est de migrer des données à partir d’une instance MongoDB à l’aide de *mongoexport/mongoimport* ou de *mongodump/mongorestore*. Comme les outils natifs se connectent à MongoDB à l’aide de chaînes de connexion, vous pouvez exécuter les outils n’importe où, mais nous vous recommandons d’exécuter ces outils dans le même réseau que l’instance MongoDB pour éviter les problèmes de pare-feu. 

Les outils natifs MongoDB ne peuvent pas déplacer les données plus vite que le matériel d’hôte ne le permet ; les outils natifs peuvent être la solution la plus simple pour les petits jeux de données où la durée totale de la migration n’est pas un problème. Le [connecteur MongoDB Spark](https://docs.mongodb.com/spark-connector/current/), [Azure Data Migration Service (DMS)](../dms/tutorial-mongodb-cosmos-db.md) ou [Azure Data Factory (ADF)](../data-factory/connector-azure-cosmos-db-mongodb-api.md) peuvent être de meilleures solutions si vous avez besoin d’un pipeline de migration évolutif.

Si vous n'avez pas encore configuré d'instance source de MongoDB, consultez l'article [Installer et configurer MongoDB sur une machine virtuelle Windows dans Azure](/previous-versions/azure/virtual-machines/windows/install-mongodb).

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous devez effectuer les opérations suivantes :

* Les étapes[Exécuter la prémigration](../cosmos-db/mongodb-pre-migration.md), comme l’estimation du débit, le choix d’une clé de partition et la stratégie d’indexation.
* [Créez un compte d’API Azure Cosmos DB pour MongoDB](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
* Connectez-vous à votre instance MongoDB.
    * [Téléchargez et installez les outils natifs MongoDB à partir de ce lien](https://www.mongodb.com/try/download/database-tools).
        * **Assurez-vous que votre version des outils natifs MongoDB correspond à votre instance MongoDB existante.**
        * Si votre instance MongoDB a une version différente d’API Azure Cosmos DB Mongo, **installez les deux versions des outils natifs MongoDB et utilisez la version d’outil appropriée pour MongoDB et l’API Azure Cosmos DB Mongo, respectivement.**
    * Ajoutez un utilisateur disposant d’autorisations `readWrite`, sauf s’il en existe déjà un. Plus loin dans ce tutoriel, fournissez ce nom d’utilisateur/mot de passe aux outils *mongoexport* et *mongodump*.

## <a name="configure-azure-cosmos-db-server-side-retries"></a>Configurer de nouvelles tentatives Azure Cosmos DB côté serveur

Les clients qui effectuent une migration à partir de MongoDB vers Azure Cosmos DB bénéficient des fonctionnalités de gouvernance des ressources, qui vous permettent d’utiliser pleinement vos RU/s de débit provisionnés. Azure Cosmos DB peut limiter une demande donnée au cours de la migration si cette demande dépasse les unités de requête provisionnées/s du conteneur. Vous devrez alors retenter cette demande. Le temps d’aller-retour impliqué dans le tronçon réseau entre l’outil de migration et Azure Cosmos DB a un impact sur le temps de réponse global de cette demande. En outre, les outils natifs MongoDB peuvent ne pas gérer les nouvelles tentatives. La fonctionnalité de *nouvelle tentative côté serveur* d’Azure Cosmos DB permet au service d’intercepter les codes d’erreur de limitation et d’effectuer une nouvelle tentative avec un temps d’aller-retour beaucoup plus court, améliorant ainsi considérablement les temps de réponse aux requêtes. Du point de vue des outils natifs de MongoDB, le besoin de gérer les nouvelles tentatives est réduit, ce qui aura un impact positif sur votre expérience pendant la migration.

La fonctionnalité de nouvelle tentative côté serveur est accessible via le panneau *Fonctionnalités* du portail Azure Cosmos DB.

![Capture d’écran de la fonctionnalité de nouvelle tentative côté serveur MongoDB](../dms/media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-feature.png)

Si cette fonctionnalité est *désactivée*, nous vous recommandons de l’activer comme indiqué ci-dessous.

![Capture d’écran de l’activation de la fonctionnalité de nouvelle tentative côté serveur MongoDB](../dms/media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-enable.png)

## <a name="choose-the-proper-mongodb-native-tool"></a>Choisir l’outil natif MongoDB approprié

![Diagramme de sélection du meilleur outil natif MongoDB](media/tutorial-mongotools-cosmos-db/mongodb-native-tool-selection-table.png)

* *mongoexport/mongoimport* est la meilleure paire d’outils de migration pour la migration d’un sous-ensemble de votre base de données MongoDB.
    * *mongoexport* exporte vos données existantes vers un fichier JSON ou CSV explicite. *mongoexport* accepte un argument qui spécifie le sous-ensemble de vos données existantes à exporter. 
    * *mongoimport* ouvre un fichier JSON ou CSV et insère le contenu dans l’instance de base de données cible (Azure Cosmos DB dans ce cas). 
    * Notez que JSON et CSV ne sont pas des formats compacts ; vous risquez d’occasionner des frais de réseau excessifs, car *mongoimport* envoie des données à Azure Cosmos DB.
* *mongodump/mongorestore* est la meilleure paire d’outils de migration pour la migration de l’ensemble de votre base de données MongoDB. Le format BSON compact permet une utilisation plus efficace des ressources réseau à mesure que les données sont insérées dans Azure Cosmos DB.
    * *mongodump* exporte vos données existantes sous la forme d’un fichier BSON.
    * *mongorestore* importe votre vidage de fichier BSON dans Azure Cosmos DB.
* En outre, si vous avez simplement un petit fichier JSON que vous souhaitez importer dans l’API Azure Cosmos DB Mongo, l’outil *mongoimport* est une solution rapide d’ingestion des données.

## <a name="collect-the-azure-cosmos-db-mongo-api-credentials"></a>Collecter les informations d’identification de l’API Azure Cosmos DB Mongo

L’API Azure Cosmos DB Mongo fournit des informations d’identification d’accès compatibles que les outils natifs MongoDB peuvent utiliser. Vous devez disposer de ces informations d’identification d’accès pour pouvoir migrer des données dans l’API Azure Cosmos DB Mongo. Pour rechercher ces informations d’identification :

1. Ouvrez le portail Azure
1. Accédez à votre compte d’API Azure Cosmos DB Mongo
1. Dans le volet de navigation de gauche, sélectionnez le panneau *chaîne de connexion*. Vous devriez voir un affichage similaire à ce qui suit :

    ![Capture d’écran des informations d’identification Azure Cosmos DB](media/tutorial-mongotools-cosmos-db/cosmos-mongo-credentials.png)

    * *HÔTE* – le point de terminaison Azure Cosmos DB fonctionne comme un nom d’hôte MongoDB
    * *PORT* – quand les outils natifs MongoDB se connectent à Azure Cosmos DB, vous devez spécifier explicitement ce port
    * *NOM D’UTILISATEUR* – le préfixe du nom de domaine du point de terminaison Azure Cosmos DB fonctionne comme le nom d’utilisateur MongoDB
    * *MOT DE PASSE* – la clé principale Azure Cosmos DB fonctionne comme le mot de passe MongoDB
    * De plus, notez le champ *SSL* qui est `true` – l’outil natif MongoDB **doit** activer SSL lors de l’écriture de données dans Azure Cosmos DB

## <a name="perform-the-migration"></a>Effectuer la migration

1. Choisissez la ou les bases de données et la ou les collections que vous souhaitez migrer. Dans cet exemple, nous migrons la collection de *requêtes* de la base de données *edx* de MongoDB vers Azure Cosmos DB.

Le reste de cette section vous guidera dans l’utilisation de la paire d’outils que vous avez sélectionnés dans la section précédente.

### <a name="mongoexportmongoimport"></a>*mongoexport/mongoimport*

1. Pour exporter les données de l’instance MongoDB source, ouvrez un terminal sur l’ordinateur de l’instance MongoDB. S’il s’agit d’une machine Linux, tapez

    `mongoexport --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection query --out edx.json`

    Sur Windows, l’exécutable est `mongoexport.exe`. Les paramètres *HÔTE*, *PORT*, *NOM D’UTILISATEUR* et *MOT DE PASSE* doivent être renseignés en fonction des propriétés de votre instance de base de données MongoDB existante. 
    
    Vous pouvez également choisir d’exporter uniquement un sous-ensemble du jeu de données MongoDB. Pour ce faire, vous pouvez ajouter un argument de filtre supplémentaire :
    
    `mongoexport --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection query --out edx.json --query '{"field1":"value1"}'`

    Seuls les documents qui correspondent au filtre `{"field1":"value1"}` sont exportés.

    Une fois que vous avez exécuté l’appel, vous devez voir qu’un fichier `edx.json` est produit :

    ![Capture d’écran de l’appel mongoexport](media/tutorial-mongotools-cosmos-db/mongo-export-output.png)
1. Vous pouvez utiliser le même terminal pour importer `edx.json` dans Azure Cosmos DB. Si vous exécutez `mongoimport` sur une machine Linux, tapez

    `mongoimport --host HOST:PORT -u USERNAME -p PASSWORD --db edx --collection importedQuery --ssl --type json --writeConcern="{w:0}" --file edx.json`

    Sur Windows, l’exécutable est `mongoimport.exe`. Les paramètres *HÔTE*, *PORT*, *NOM D’UTILISATEUR* et *MOT DE PASSE* doivent être renseignés en fonction des informations d’identification Azure Cosmos DB que vous avez collectées précédemment. 
1. **Surveillez** la sortie de terminal à partir de *mongoimport*. Vous devez voir qu’il imprime des lignes de texte sur le terminal contenant les mises à jour sur l’état de la migration :

    ![Capture d’écran de l’appel mongoexport](media/tutorial-mongotools-cosmos-db/mongo-import-output.png)    

1. Enfin, examinez Azure Cosmos DB pour **valider** que la migration a réussi. Ouvrez le portail Azure Cosmos DB et accédez à l’Explorateur de données. Vous devez voir (1) qu’une base de données *edx* avec une collection *importedQuery* a été créée et (2) si vous avez exporté uniquement un sous-ensemble de données, *importedQuery* doit contenir *uniquement* les documents correspondant au sous-ensemble souhaité des données. Dans l’exemple ci-dessous, un seul document correspond au filtre `{"field1":"value1"}` :

    ![Capture d’écran de la vérification des données de Cosmos DB](media/tutorial-mongotools-cosmos-db/mongo-review-cosmos.png)    

### <a name="mongodumpmongorestore"></a>*mongodump/mongorestore*

1. Pour créer un vidage des données BSON de votre instance MongoDB, ouvrez un terminal sur l’ordinateur de l’instance MongoDB. S’il s’agit d’une machine Linux, tapez

    `mongodump --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection query --out edx-dump`

    Les paramètres *HÔTE*, *PORT*, *NOM D’UTILISATEUR* et *MOT DE PASSE* doivent être renseignés en fonction des propriétés de votre instance de base de données MongoDB existante. Vous devez voir qu’un répertoire `edx-dump` est généré et que la structure de répertoires de `edx-dump` reproduit la hiérarchie des ressources (structure de la base de données et de la collection) de votre instance MongoDB source. Chaque collection est représentée par un fichier BSON :

    ![Capture d’écran de l’appel mongodump](media/tutorial-mongotools-cosmos-db/mongo-dump-output.png)
1. Vous pouvez utiliser le même terminal pour restaurer le contenu de `edx-dump` dans Azure Cosmos DB. Si vous exécutez `mongorestore` sur une machine Linux, tapez

    `mongorestore --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection importedQuery --ssl edx-dump/edx/query.bson`

    Sur Windows, l’exécutable est `mongorestore.exe`. Les paramètres *HÔTE*, *PORT*, *NOM D’UTILISATEUR* et *MOT DE PASSE* doivent être renseignés en fonction des informations d’identification Azure Cosmos DB que vous avez collectées précédemment. 
1. **Surveillez** la sortie de terminal à partir de *mongorestore*. Vous devez voir qu’il envoie des lignes à la mise à jour du terminal sur l’état de la migration :

    ![Capture d’écran de l’appel mongorestore](media/tutorial-mongotools-cosmos-db/mongo-restore-output.png)    

1. Enfin, examinez Azure Cosmos DB pour **valider** que la migration a réussi. Ouvrez le portail Azure Cosmos DB et accédez à l’Explorateur de données. Vous devez voir (1) qu’une base de données *edx* avec une collection *importedQuery* a été créée et (2) *importedQuery* doit contenir le jeu de données *complet* de la collection source :

    ![Capture d’écran de la vérification des données mongorestore Cosmos DB](media/tutorial-mongotools-cosmos-db/mongo-review-cosmos-restore.png)    

## <a name="post-migration-optimization"></a>Optimisation de la post-migration

Après avoir migré les données stockées dans la base de données MongoDB vers l’API Azure Cosmos DB pour MongoDB, vous pouvez vous connecter à Azure Cosmos DB et gérer les données. Vous pouvez également effectuer d’autres étapes d’optimisation de la post-migration, comme l’optimisation de la stratégie d’indexation, la mise à jour du niveau de cohérence par défaut ou la configuration de la distribution mondiale pour votre compte Azure Cosmos DB. Pour plus d’informations, consultez l’article [Optimisation de la post-migration](../cosmos-db/mongodb-post-migration.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Informations sur le service Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)
* [Documentation des outils de base de données MongoDB](https://docs.mongodb.com/database-tools/)

## <a name="next-steps"></a>Étapes suivantes

* Pour accéder à d'autres scénarios, lisez les instructions de migration du [Guide de migration de bases de données](https://datamigration.microsoft.com/) de Microsoft.