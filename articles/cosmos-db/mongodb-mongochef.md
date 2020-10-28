---
title: Utiliser Studio 3T pour se connecter à l’API Azure Cosmos DB pour MongoDB
description: Découvrez comment vous connecter à une API Azure Cosmos DB pour MongoDB à l’aide de Studio 3T.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/20/2020
author: timsander1
ms.author: tisande
ms.custom: seodec18
ms.openlocfilehash: 6c9ea401a4a62f617b5acf6f96c60532d929ff1c
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2020
ms.locfileid: "92167965"
---
# <a name="connect-to-an-azure-cosmos-account-using-studio-3t"></a>Se connecter à un compte Azure Cosmos DB à l’aide de Studio 3T

Pour vous connecter à l’API Azure Cosmos DB pour MongoDB avec Studio 3T, vous devez procéder comme suit :

* Téléchargez et installez [Studio 3T](https://studio3t.com/).
* Obtenez les informations de la [chaîne de connexion](connect-mongodb-account.md) de votre compte Azure Cosmos.

## <a name="create-the-connection-in-studio-3t"></a>Créer la connexion dans Studio 3T

Pour ajouter votre compte Azure Cosmos DB au gestionnaire de connexions Studio 3T, procédez comme suit :

1. Récupérez les informations de connexion de votre API Azure Cosmos DB pour votre compte MongoDB en suivant les instructions figurant dans l’article [Connecter une application MongoDB à Azure Cosmos DB](connect-mongodb-account.md).

    :::image type="content" source="./media/mongodb-mongochef/ConnectionStringBlade.png" alt-text="Capture d’écran de la page Chaîne de connexion":::

2. Cliquez sur **Connexion** pour ouvrir le gestionnaire de connexions, puis cliquez sur **Nouvelle connexion** .

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManager.png" alt-text="Capture d’écran de la page Chaîne de connexion":::
3. Sous l’onglet **Serveur** de la fenêtre **Nouvelle connexion** , saisissez l’HÔTE (FQDN) du compte Azure Cosmos DB et le PORT.

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerServerTab.png" alt-text="Capture d’écran de la page Chaîne de connexion":::
4. Dans la fenêtre **Nouvelle connexion** , sous l’onglet **Authentification** , choisissez le mode d’authentification **De base (MONGODB-CR ou SCARM-SHA-1)** et entrez les NOM D’UTILISATEUR et MOT DE PASSE.  Acceptez la base de données d’authentification par défaut (admin) ou indiquez votre propre valeur.

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png" alt-text="Capture d’écran de la page Chaîne de connexion":::
5. Dans la fenêtre **Nouvelle connexion** , sous l’onglet **SSL** , cochez la case **Utiliser le protocole SSL pour se connecter** et sélectionnez **Accepter les certificats SSL auto-signés** .

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerSSLTab.png" alt-text="Capture d’écran de la page Chaîne de connexion":::
6. Cliquez sur le bouton **Tester la connexion** pour valider les informations de connexion, cliquez sur **OK** pour revenir à la fenêtre Nouvelle connexion, puis cliquez sur **Enregistrer** .

    :::image type="content" source="./media/mongodb-mongochef/TestConnectionResults.png" alt-text="Capture d’écran de la page Chaîne de connexion":::

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Utiliser Studio 3T pour créer une base de données, une collection et des documents
Pour créer une base de données, une collection et des documents à l’aide de Studio 3T, procédez comme suit :

1. Dans le **Gestionnaire de connexions** , sélectionnez la connexion et cliquez sur **Connexion** .

    :::image type="content" source="./media/mongodb-mongochef/ConnectToAccount.png" alt-text="Capture d’écran de la page Chaîne de connexion":::
2. Cliquez avec le bouton droit sur l’hôte et choisissez **Ajouter une base de données** .  Spécifiez un nom de base de données, puis cliquez sur **OK** .

    :::image type="content" source="./media/mongodb-mongochef/AddDatabase1.png" alt-text="Capture d’écran de la page Chaîne de connexion":::
3. Cliquez avec le bouton droit sur la base de données et choisissez **Ajouter une collection** .  Spécifiez un nom de collection et cliquez sur **Créer** .

    :::image type="content" source="./media/mongodb-mongochef/AddCollection.png" alt-text="Capture d’écran de la page Chaîne de connexion":::
4. Cliquez sur l’élément de menu **Collection** , puis cliquez sur **Ajouter un document** .

    :::image type="content" source="./media/mongodb-mongochef/AddDocument1.png" alt-text="Capture d’écran de la page Chaîne de connexion" },
        "isRegistered": false
    }
    ```

7. Exécutez un exemple de requête. Par exemple, recherchez des familles portant le nom « Andersen » en retournant les champs parents et état.

    :::image type="content" source="./media/mongodb-mongochef/QueryDocument1.png" alt-text="Capture d’écran de la page Chaîne de connexion":::

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [utiliser Robo 3T](mongodb-robomongo.md) avec l’API Azure Cosmos DB pour MongoDB.
- Explorez les [exemples](mongodb-samples.md) MongoDB avec l’API Azure Cosmos DB pour MongoDB.
