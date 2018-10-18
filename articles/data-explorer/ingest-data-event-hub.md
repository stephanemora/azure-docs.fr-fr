---
title: 'Démarrage rapide : Ingérer des données Event Hub dans l’Explorateur de données Azure'
description: Dans ce guide de démarrage rapide, vous allez apprendre à ingérer (charger) des données dans l’Explorateur de données Azure depuis Event Hub.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 09/24/2018
ms.openlocfilehash: efaf551d134d339205d40966cb84f41b408559bd
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49394176"
---
# <a name="quickstart-ingest-data-from-event-hub-into-azure-data-explorer"></a>Démarrage rapide : Ingérer des données Event Hub dans l’Explorateur de données Azure

L’Explorateur de données Azure est un service d’exploration de données rapide et hautement scalable pour les données des journaux et de télémétrie. L’Explorateur de données Azure offre une ingestion (chargement de données) à partir d’Event Hubs, plateforme de streaming de big data et service d’ingestion d’événements. Event Hubs peut traiter des millions d’événements par seconde en quasi-temps réel. Dans ce guide de démarrage rapide, vous créez un hub d’événements, vous vous y connectez à partir de l’Explorateur de données Azure et vous voyez le flux de données via le système.

Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

En plus d’un abonnement Azure, vous devez disposer des éléments suivants pour suivre ce guide de démarrage rapide :

* [Un cluster de test et une base de données](create-cluster-database-portal.md)

* [Une application exemple](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) qui génère des données

* [Visual studio 2017 version 15.3.2 ou ultérieure](https://www.visualstudio.com/vs/) pour exécuter l’application exemple

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [Portail Azure](https://portal.azure.com/).

## <a name="create-an-event-hub"></a>Créer un hub d’événements

Dans ce guide de démarrage rapide, vous générez des exemples de données et les envoyez à un hub d’événements. La première étape consiste à créer un hub d’événements. Pour cela, utilisez un modèle Azure Resource Manager (ARM) dans le portail Azure.

1. Sélectionnez le bouton suivant pour démarrer le déploiement.

    [![Déployer sur Azure](media/ingest-data-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

    Le bouton **Déployer sur Azure** vous amène dans le portail Azure pour remplir un formulaire de déploiement.

    ![Déployer dans Azure](media/ingest-data-event-hub/deploy-to-azure.png)

1. Sélectionnez l’abonnement dans lequel vous souhaitez créer le hub d’événements et créez un groupe de ressources nommé *test-hub-rg*.

    ![Créer un groupe de ressources](media/ingest-data-event-hub/create-resource-group.png)

1. Renseignez le formulaire avec les informations suivantes.

    ![Formulaire de déploiement](media/ingest-data-event-hub/deployment-form.png)

    Utilisez les valeurs par défaut pour tous les paramètres non listés dans le tableau suivant.

    **Paramètre** | **Valeur suggérée** | **Description du champ**
    |---|---|---|
    | Abonnement | Votre abonnement | Sélectionnez l’abonnement Azure que vous souhaitez utiliser pour votre hub d’événements.|
    | Groupe de ressources | *test-hub-rg* | Créez un groupe de ressources. |
    | Lieu | *USA Ouest* | Pour ce guide de démarrage rapide, sélectionnez *USA Ouest*. Pour un système de production, sélectionnez la région qui répond le mieux à vos besoins.
    | Nom de l’espace de noms | Nom unique de l’espace de noms | Choisissez un nom unique qui identifie votre espace de noms. Par exemple, *mytestnamespace*. Le nom de domaine *servicebus.windows.net* est ajouté au nom que vous fournissez. Le nom ne peut contenir que des lettres, des chiffres et des traits d’union. Le nom doit commencer par une lettre et se terminer par une lettre ou un chiffre. La valeur doit être comprise entre 6 et 50 caractères.
    | Nom du hub d’événements | *test-hub* | Le hub d’événements se trouve sous l’espace de noms, qui fournit un conteneur d’étendue unique. Le hub d’événements doit être unique dans l’espace de noms. |
    | Nom du groupe de consommateurs | *test-group* | Les groupes de consommateurs permettent que chacune des applications de consommation ait une vue distincte du flux d’événements. |
    | | |

1. Sélectionnez **Achat**, ce qui confirme que vous créez des ressources dans votre abonnement.

1. Dans la barre d’outils, sélectionnez **Notifications** (icône de cloche) pour superviser le processus de provisionnement. Le déploiement peut prendre plusieurs minutes, mais vous pouvez passer à l’étape suivante sans attendre.

## <a name="create-a-target-table-in-azure-data-explorer"></a>Créer une table cible dans l’Explorateur de données Azure

Créez maintenant une table dans l’Explorateur de données Azure, à laquelle Event Hubs envoie des données. Créez la table dans le cluster et la base de données provisionnés dans **Prérequis**.

1. Dans le portail Azure, sous votre cluster, sélectionnez **Requête**.

    ![Lien d’application de requête](media/ingest-data-event-hub/query-explorer-link.png)

1. Copiez la commande suivante dans la fenêtre et sélectionnez **Exécuter**.

    ```Kusto
    .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
    ```

    ![Exécuter la requête de création](media/ingest-data-event-hub/run-create-query.png)

1. Copiez la commande suivante dans la fenêtre et sélectionnez **Exécuter**.

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.timeStamp","datatype":"datetime"},{"column":"Name","path":"$.name","datatype":"string"},{"column":"Metric","path":"$.metric","datatype":"int"},{"column":"Source","path":"$.source","datatype":"string"}]'
    ```
    Cette commande mappe les données JSON entrantes aux noms de colonne et aux types de données utilisés lors de la création de la table.

## <a name="connect-to-the-event-hub"></a>Se connecter au hub d’événements

Maintenant, connectez-vous au hub d’événements à partir de l’Explorateur de données Azure afin que les données s’acheminant vers le hub d’événements soient envoyées dans la table de test.

1. Sélectionnez **Notifications** dans la barre d’outils pour vérifier que le déploiement du hub d’événements a réussi.

1. Sous le cluster que vous avez créé, sélectionnez **Bases de données** et **TestDatabase**.

    ![Sélectionner la base de données de test](media/ingest-data-event-hub/select-test-database.png)

1. Sélectionnez **Ingestion des données**, puis **Ajouter une connexion de données**.

    ![Ingestion de données](media/ingest-data-event-hub/data-ingestion-create.png)

1. Renseignez le formulaire avec les informations suivantes, puis sélectionnez **Créer**.

    ![Connexion du hub d’événements](media/ingest-data-event-hub/event-hub-connection.png)

    **Paramètre** | **Valeur suggérée** | **Description du champ**
    |---|---|---|
    | Nom de la connexion de données | *test-hub-connection* | Nom de la connexion que vous souhaitez créer dans l’Explorateur de données Azure.|
    | Espace de noms du hub d’événements | Nom unique de l’espace de noms | Nom choisi précédemment qui identifie votre espace de noms. |
    | Event Hub | *test-hub* | Hub d’événements que vous avez créé. |
    | Groupe de consommateurs | *test-group* | Groupe de consommateurs défini dans le hub d’événements que vous avez créé. |
    | Table | *TestTable* | Table que vous avez créée dans **TestDatabase**. |
    | Format de données | *JSON* | Les formats CSV et JSON sont pris en charge. |
    | Mappage de colonnes | *TestMapping* | Mappage que vous avez créé dans **TestDatabase**. |

    Pour ce guide de démarrage rapide, utilisez le *routage statique* du hub d’événements, où vous spécifiez le nom de la table, le format de fichier et le mappage. Vous pouvez également utiliser le routage dynamique, où votre application définit ces propriétés.

## <a name="copy-the-connection-string"></a>Copier la chaîne de connexion

Lorsque vous exécutez l’application pour générer des exemples de données, vous avez besoin de la chaîne de connexion pour l’espace de noms du hub d’événements.

1. Sous l’espace de noms du hub d’événements que vous avez créé, sélectionnez **Stratégies d’accès partagé**, puis **RootManageSharedAccessKey**.

    ![Stratégies d’accès partagé](media/ingest-data-event-hub/shared-access-policies.png)

1. Copiez **Chaîne de connexion - Clé primaire**.

    ![Chaîne de connexion](media/ingest-data-event-hub/connection-string.png)

## <a name="generate-sample-data"></a>Générer un exemple de données

Maintenant que l’Explorateur de données Azure et le hub d’événements sont connectés, utilisez l’application exemple que vous avez téléchargée pour générer des données.

1. Ouvrez la solution de l’application exemple dans Visual Studio.

1. Dans le fichier *program.cs*, mettez à jour la constante `connectionString` avec la chaîne de connexion que vous avez copiée à partir de l’espace de noms du hub d’événements.

    ```csharp
    const string eventHubName = "test-hub";
    // Copy the connection string ("Connection string-primary key") from your Event Hub namespace.
    const string connectionString = @"<YourConnectionString>";
    ```

1. Générez et exécutez l'application. L’application envoie des messages au hub d’événements et affiche un état toutes les dix secondes.

1. Une fois que l’application a envoyé quelques messages, passez à l’étape suivante : examiner le flux de données dans votre hub d’événements et votre table de test.

## <a name="review-the-data-flow"></a>Examiner le flux de données

1. Dans le portail Azure, sous votre hub d’événements, vous voyez le pic de l’activité pendant l’exécution de l’application.

    ![Graphe du hub d’événements](media/ingest-data-event-hub/event-hub-graph.png)

1. Revenez à l’application et arrêtez-la une fois qu’elle a atteint le message 99.

1. Exécutez la requête suivante dans votre base de données de test pour vérifier combien de messages sont arrivés dans la base de données jusqu’à présent.

    ```Kusto
    TestTable
    | count
    ```

1. Exécutez la requête suivante pour voir le contenu des messages.

    ```Kusto
    TestTable
    ```

    Le jeu de résultats doit ressembler à ce qui suit.

    ![Jeu de résultats des messages](media/ingest-data-event-hub/message-result-set.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous ne prévoyez pas de réutiliser votre hub d’événements, nettoyez **test-hub-rg** pour éviter des frais.

1. Dans le portail Azure, sélectionnez **Groupes de ressources** tout à gauche, puis sélectionnez le groupe de ressources que vous avez créé.  

    Si le menu de gauche est réduit, sélectionnez le ![Bouton Développer](media/ingest-data-event-hub/expand.png) pour le développer.

   ![Sélectionner un groupe de ressources à supprimer](media/ingest-data-event-hub/delete-resources-select.png)

1. Sous **test-resource-group**, sélectionnez **Supprimer le groupe de ressources**.

1. Dans la nouvelle fenêtre, tapez le nom du groupe de ressources à supprimer (*test-hub-rg*), puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Démarrage rapide : Interroger des données dans l’Explorateur de données Azure](web-query-data.md)
