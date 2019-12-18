---
title: Connecter une application MongoDB à Azure Cosmos DB
description: Découvrir comment connecter une application MongoDB à Azure Cosmos DB à l’aide d’une chaîne de connexion à partir du Portail Azure
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 12/02/2019
ms.reviewer: sngun
ms.openlocfilehash: 0f2cd1b7228f2cc9cadb84232222f658a512a81f
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872160"
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Connecter une application MongoDB à Azure Cosmos DB
Découvrez comment connecter votre application MongoDB à une instance Azure Cosmos DB à l’aide d’une chaîne de connexion MongoDB. Vous pouvez ensuite utiliser une base de données Azure Cosmos en tant que magasin de données pour votre application MongoDB. 

Ce didacticiel fournit deux façons de récupérer les informations de la chaîne de connexion :

- [Méthode du guide de démarrage rapide](#QuickstartConnection), à utiliser avec les pilotes .NET, Node.js, MongoDB Shell, Java et Python
- [Méthode de la chaîne de connexion personnalisée](#GetCustomConnection), à utiliser avec d’autres pilotes.

## <a name="prerequisites"></a>Prérequis

- Un compte Azure. Si vous ne possédez pas de compte Azure, vous pouvez créer un [compte Azure gratuit](https://azure.microsoft.com/free/) dès maintenant. 
- Un compte Cosmos. Pour obtenir des instructions, consultez [Générer une application web en utilisant l’API pour MongoDB d’Azure Cosmos DB et le kit SDK .NET](create-mongodb-dotnet.md).

## <a id="QuickstartConnection"></a>Obtenir la chaîne de connexion MongoDB à l’aide du démarrage rapide
1. Dans un navigateur Internet, connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le panneau **Azure Cosmos DB**, sélectionnez l’API. 
3. Dans le volet gauche du panneau Compte, cliquez sur **Démarrage rapide**. 
4. Choisissez votre plateforme ( **.NET**, **Node.js**, **MongoDB Shell**, **Java** ou **Python**). Si votre pilote ou outil n’est pas répertorié, ne vous inquiétez pas, nous développons en permanence de nouveaux extraits de code de connexion. Entrez ci-dessous un commentaire sur ce que vous voudriez voir. Pour savoir comment créer votre propre connexion, lisez [Obtenir les informations de chaîne de connexion du compte](#GetCustomConnection).
5. Copiez et collez l’extrait de code dans votre application MongoDB.

    ![Panneau Démarrage rapide](./media/connect-mongodb-account/QuickStartBlade.png)

## <a id="GetCustomConnection"></a> Obtenir la chaîne de connexion MongoDB à personnaliser
1. Dans un navigateur Internet, connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le panneau **Azure Cosmos DB**, sélectionnez l’API. 
3. Dans le volet gauche du panneau Compte, cliquez sur **Chaîne de connexion**. 
4. Le panneau **Chaîne de connexion** s’ouvre. Il affiche toutes les informations nécessaires pour la connexion au compte à l’aide d’un pilote pour MongoDB, dont une chaîne de connexion prédéfinie.

    ![Panneau Chaîne de connexion](./media/connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>Exigences relatives à la chaîne de connexion
> [!Important]
> Azure Cosmos DB obéit à des normes et à des exigences strictes en matière de sécurité. Les comptes Azure Cosmos DB requièrent une authentification et une communication sécurisée via *SSL*. 
>
>

Azure Cosmos DB prend en charge le format d’URI de la chaîne de connexion MongoDB standard sous certaines conditions spécifiques : l’authentification et la communication sécurisée des comptes Azure Cosmos DB doivent s’effectuer via SSL. Le format de chaîne de connexion est donc :

    mongodb://username:password@host:port/[database]?ssl=true

Les valeurs de cette chaîne sont disponibles dans le panneau **Chaîne de connexion** illustré précédemment :

* Nom d’utilisateur (obligatoire) : Nom du compte Cosmos.
* Mot de passe (obligatoire) : Mot de passe du compte Cosmos.
* Hôte (obligatoire) : Nom de domaine complet du compte Cosmos.
* Port (obligatoire) : 10255.
* Base de données (facultatif) : Base de données utilisée par la connexion. Si aucune base de données n’est fournie, la base de données par défaut est « test ».
* ssl = true (obligatoire)

Par exemple, observez le compte figurant dans le panneau **Chaîne de connexion**. Exemple de chaîne de connexion valide :

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [utiliser Studio 3T](mongodb-mongochef.md) avec l’API Azure Cosmos DB pour MongoDB.
- Découvrez comment [utiliser Robo 3T](mongodb-robomongo.md) avec l’API Azure Cosmos DB pour MongoDB.
- Explorez les [exemples](mongodb-samples.md) MongoDB avec l’API Azure Cosmos DB pour MongoDB.
