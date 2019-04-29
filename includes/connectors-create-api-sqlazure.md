---
title: Fichier Include
description: Fichier Include
services: logic-apps
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 05/15/2018
ms.author: estfan
ms.custom: include file
ms.openlocfilehash: da03c5247b8ebe0a3305b08a05d661264497663f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60540967"
---
* Si vous utilisez Azure SQL Database, suivez les étapes décrites dans [Connexion à la base de données SQL Azure](#connect-azure-sql-db). 

* Si vous utilisez SQL Server, suivez les étapes décrites dans [Se connecter à SQL Server](#connect-sql-server).

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database"></a>Connexion à la base de données SQL Azure

1. Lorsque le déclencheur ou l’action SQL vous invite à fournir des informations de connexion, effectuez les étapes suivantes :

   1. Créez un nom pour votre connexion.

   2. Sélectionnez votre serveur SQL, puis votre base de données. 

      La liste des bases de données s’affiche seulement après que vous avez sélectionné votre serveur SQL.
 
   3. Indiquez le nom d’utilisateur et le mot de passe de votre serveur.

      Ces informations se trouvent dans le Portail Azure sous les propriétés de votre base de données SQL ou dans votre chaîne de connexion : 
      
      “User ID = <*votre_nom_utilisateur*>”
      <br>
      “Password=<*votre_mot_de_passe*>”

   Cet exemple montre les informations de connexion d’un déclencheur, mais ces étapes peuvent également être appliquées aux actions.

   ![Créer une connexion à la base de données SQL Azure](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)
   <br>
   Les astérisques (*) indiquent des valeurs requises.

   | Propriété | Valeur | Détails | 
   |----------|-------|---------| 
   | Nom de connexion | <*ma-connexion-sql-*> | Nom de votre connexion | 
   | Nom du serveur SQL Server | <*mon-serveur-sql*> | Nom de votre serveur SQL |
   | Nom de la base de données SQL | <*ma-base-de-données-sql*>  | Nom de votre base de données SQL | 
   | Nom d’utilisateur | <*mon-nom-utilisateur-sql*> | Nom d’utilisateur pour accéder à votre base de données |
   | Mot de passe | <*mon-mot-de-depasse-sql*> | Mot de passe pour accéder à votre base de données | 
   |||| 

2. Lorsque vous êtes prêt, choisissez **Créer**.

3. Après avoir créé votre connexion, poursuivez avec [Ajouter un déclencheur SQL](#add-sql-trigger) ou [Ajouter une action SQL](#add-sql-action).

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>Se connecter à SQL Server

Avant de pouvoir sélectionner votre passerelle, assurez-vous d’avoir [configuré votre passerelle de données](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection). Votre passerelle apparaît alors dans la liste des passerelles lorsque vous créez votre connexion.

1. Lorsque le déclencheur ou l’action SQL vous invite à fournir des informations de connexion, effectuez les étapes suivantes :

   1. Dans le déclencheur ou l’action, sélectionnez **Se connecter via une passerelle de données locale** afin que les options de serveur SQL s’affichent.

   2. Créez un nom pour votre connexion.

   3. Indiquez l’adresse de votre serveur SQL server, puis le nom de votre base de données.
   
      Ces informations se trouvent dans votre chaîne de connexion : 
      
      * “Server=<*Adresse_de_votre_serveur*>”
      * “Database=<*nom_de_votre_base_de_données*>”

   4. Indiquez le nom d’utilisateur et le mot de passe de votre serveur.

      Ces informations se trouvent dans votre chaîne de connexion : 
      
      * “User ID = <*votre_nom_utilisateur*>”
      * “Password=<*votre_mot_de_passe*>”

   5. Si votre serveur SQL utilise l’authentification Windows ou l’authentification De base, sélectionnez le type d’authentification.

   6. Sélectionnez le nom de la passerelle de données locale que vous avez créée précédemment.
   
      Si votre passerelle n’apparaît pas dans la liste, vérifiez que vous avez correctement [configuré votre passerelle](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection).

   Cet exemple montre les informations de connexion d’un déclencheur, mais ces étapes peuvent également être appliquées aux actions.

   ![Créer une connexion SQL Server](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)
   <br>
   Les astérisques (*) indiquent des valeurs requises.

   | Propriété | Valeur | Détails | 
   |----------|-------|---------| 
   | Se connecter via une passerelle locale | Sélectionnez tout d’abord cette option pour les paramètres SQL Server. | | 
   | Nom de connexion | <*ma-connexion-sql-*> | Nom de votre connexion | 
   | Nom du serveur SQL Server | <*mon-serveur-sql*> | Nom de votre serveur SQL |
   | Nom de la base de données SQL | <*ma-base-de-données-sql*>  | Nom de votre base de données SQL |
   | Nom d’utilisateur | <*mon-nom-utilisateur-sql*> | Nom d’utilisateur pour accéder à votre base de données |
   | Mot de passe | <*mon-mot-de-depasse-sql*> | Mot de passe pour accéder à votre base de données | 
   | Type d’authentification | Windows ou De base | Facultatif : Le type d’authentification utilisé par votre serveur SQL server | 
   | Passerelles | <*ma-passerelle-de-données*> | Nom de votre passerelle de données locale | 
   |||| 

2. Lorsque vous êtes prêt, choisissez **Créer**. 

3. Après avoir créé votre connexion, poursuivez avec [Ajouter un déclencheur SQL](#add-sql-trigger) ou [Ajouter une action SQL](#add-sql-action).
