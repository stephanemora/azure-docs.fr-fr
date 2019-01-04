---
title: Utiliser Robomongo pour Azure Cosmos DB
description: 'Découvrez comment utiliser Robomongo avec un compte Azure Cosmos DB : API pour MongoDB'
keywords: robomongo
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: sngun
ms.openlocfilehash: 78f0158c9a80a60717b81b4788531c7efd979111
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52863803"
---
# <a name="use-robomongo-with-an-azure-cosmos-db-api-for-mongodb-account"></a>Utiliser Robomongo avec un compte Azure Cosmos DB : API pour MongoDB
Pour se connecter à un compte Azure Cosmos DB : API pour MongoDB à l’aide de Robomongo, vous devez :

* Télécharger et installer [Robomongo](https://robomongo.org/)
* Disposer pour votre compte Azure Cosmos DB : API pour MongoDB des informations de [chaîne de connexion](connect-mongodb-account.md)

## <a name="connect-using-robomongo"></a>Connexion à l’aide de Robomongo
Pour ajouter votre compte Azure Cosmos DB : API MongoDB aux connexions Robomongo MongoDB, procédez comme suit.

1. Récupérez pour votre compte Azure Cosmos DB : API MongoDB les informations de connexion à l’aide des instructions données [ici](connect-mongodb-account.md).

    ![Capture d’écran du panneau Chaîne de connexion](./media/mongodb-robomongo/connectionstringblade.png)
2. Exécutez *Robomongo.exe*

3. Cliquez sur le bouton de connexion sous **Fichier** pour gérer vos connexions. Ensuite, cliquez sur **Créer** dans la fenêtre **Connexions MongoDB**, ce qui ouvre la fenêtre **Paramètres de connexion**.

4. Dans la fenêtre **Paramètres de connexion**, choisissez un nom. Ensuite, recherchez **Hôte** et **Port** dans vos informations de connexion à l’étape 1 et entrez-les dans les champs **Adresse** et **Port**, respectivement.

    ![Capture d’écran de gestion des connexions avec Robomongo](./media/mongodb-robomongo/manageconnections.png)
5. Dans l’onglet **Authentification**, cliquez sur **Effectuer l’authentification**. Entrez ensuite votre base de données (la valeur par défaut est *Admin*), le **Nom d’utilisateur** et le **Mot de passe**.
Le **Nom d’utilisateur** et le **Mot de passe** figurent tous deux dans vos informations de connexion à l’étape 1.

    ![Capture d’écran de l’onglet authentification de Robomongo](./media/mongodb-robomongo/authentication.png)
6. Dans l’onglet **SSL**, cochez **Utiliser le protocole SSL**, puis modifiez la **Méthode d’authentification** sur **Certificat auto-signé**.

    ![Capture d’écran de l’onglet SSL de Robomongo](./media/mongodb-robomongo/SSL.png)
7. Enfin, cliquez sur **Test** pour vérifier que vous êtes en mesure de vous connecter, puis sur **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes
* Explorer les [exemples](mongodb-samples.md) Azure Cosmos DB : API pour MongoDB.
