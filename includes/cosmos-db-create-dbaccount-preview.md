---
title: Fichier Include
description: Fichier Include
services: cosmos-db
author: deborahc
ms.service: cosmos-db
ms.topic: include
ms.date: 11/24/2018
ms.author: dech
ms.custom: include file
ms.openlocfilehash: 6169821d0e81f0af108dccbfa761f5512e352628
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52853345"
---
1. Dans une nouvelle fenêtre du navigateur, connectez-vous au [portail Azure](https://portal.azure.com/).
2. Cliquez sur **Créer une ressource** > **Bases de données** > **Azure Cosmos DB**.
   
   ![Volet Bases de données du portail Azure](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

3. Dans la page **Créer un compte Azure Cosmos DB**, entrez les paramètres du nouveau compte Azure Cosmos DB. 
 
    Paramètre|Valeur|Description
    ---|---|---
    Abonnement|*Votre abonnement*|Sélectionnez l’abonnement Azure que vous souhaitez utiliser pour ce compte Azure Cosmos DB. 
    Groupe de ressources|Création<br><br>*Entrez un nom unique*|Sélectionnez **Créer**, puis entrez le nom du nouveau groupe de ressources pour votre compte. Pour plus de simplicité, vous pouvez utiliser votre nom de compte. 
    Nom du compte|*Entrez un nom unique*|Entrez un nom unique pour identifier votre compte Azure Cosmos DB. Étant donné que *documents.azure.com* est ajouté à l’ID que vous fournissez pour créer votre URI, utilisez un ID unique.<br><br>L’ID ne peut contenir que des lettres minuscules, des chiffres et le caractère de trait d’union (-), et doit comporter entre 3 et 50 caractères.
    API|Core (SQL)|L’API détermine le type de compte à créer. Azure Cosmos DB fournit cinq API : SQL (base de données orientée document), Gremlin (base de données orientée graphe), MongoDB (base de données orientée document), API Table et Cassandra. Pour chaque API, vous devez créer un compte distinct. <br><br>Sélectionnez **Core (SQL)**, car dans cet article, vous allez créer une base de données de documents et faire des requêtes avec une syntaxe SQL. <br><br>[En savoir plus sur l’API SQL](../articles/cosmos-db/documentdb-introduction.md)|
    Lieu|*Sélectionner la région la plus proche de vos utilisateurs*|Sélectionnez la zone géographique dans laquelle héberger votre compte Azure Cosmos DB. Utilisez l’emplacement le plus proche de vos utilisateurs, pour leur donner l’accès le plus rapide possible aux données.
    Activer la géoredondance| Laisser vide | Ceci crée une version répliquée de votre base de données dans une seconde région (appairée). Laissez ce champ vide.  
    Écritures multirégions| Laisser vide | Ce paramètre permet de rendre vos régions de base de données accessibles en lecture et en écriture. Laissez ce champ vide.  

    Puis, cliquez sur **Vérifier + créer**. Vous pouvez ignorer les sections **Réseau** et **Balises**. 

    ![Page de nouveau compte pour Azure Cosmos DB](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account-preview.png)

    Examinez les informations récapitulatives et cliquez sur **Créer**. 

    ![Page résumée de vérification de compte](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account-summary-preview.png)

4. La création du compte prend quelques minutes. Attendez que le portail affiche le message **Votre déploiement a été effectué** et cliquez sur **Accéder à la ressource**.     

    ![Page indiquant que le compte été créé correctement](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account-complete-preview.png)

5. Le portail affiche maintenant la page **Félicitations ! Votre compte Azure Cosmos DB a été créé**.

    ![Volet Notifications du portail Azure](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)

