---
title: Fichier Include
description: Fichier Include
services: logic-apps
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.author: estfan
ms.custom: include file
ms.date: 05/15/2018
ms.openlocfilehash: aa1001661d8fe03855e1a28b882f674bee3606b2
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2019
ms.locfileid: "72312023"
---
* Si vous utilisez Azure SQL Database, suivez les étapes décrites dans [Connexion à Azure SQL Database](#connect-azure-sql-db).

* Si vous utilisez SQL Server, suivez les étapes décrites dans [Se connecter à SQL Server](#connect-sql-server).

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database"></a>Connexion à Azure SQL Database

Lorsque le déclencheur ou l’action SQL vous invite à fournir des informations de connexion, effectuez les étapes suivantes, qui fonctionnent à la fois pour les déclencheurs et les actions.

1. Pour **Nom de la connexion**, créez un nom pour votre connexion.

1. Sélectionnez votre serveur SQL Azure sous **Nom du serveur SQL**. Lorsque la liste **Nom de la base de données SQL**, sélectionnez votre base de données. Fournissez le nom d’utilisateur et le mot de passe associés à votre serveur SQL Azure.

   Ces informations se trouvent également dans le Portail Azure sous les propriétés de votre base de données SQL ou dans votre chaîne de connexion :

   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![Créer une connexion à Azure SQL Database](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. Sélectionnez **Créer** lorsque vous avez terminé.

1. Après avoir créé votre connexion, poursuivez avec [Ajouter un déclencheur SQL](#add-sql-trigger) ou [Ajouter une action SQL](#add-sql-action).

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>Se connecter à SQL Server

Lorsque le déclencheur ou l’action SQL vous invite à fournir des informations de connexion, effectuez les étapes suivantes, qui fonctionnent à la fois pour les déclencheurs et les actions. Toutefois, avant de commencer, assurez-vous que vous avez déjà [configuré votre passerelle de données locale](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection). Sinon, votre passerelle n’apparaît pas dans la liste des passerelles lorsque vous créez votre connexion.

1. Pour **Nom de la connexion**, créez un nom pour votre connexion.

1. Dans le déclencheur ou l’action, sélectionnez **Se connecter via une passerelle de données locale** afin que les options de serveur SQL s’affichent.

1. Pour **Nom du serveur SQL** et **Nom de la base de données SQL**, indiquez l’adresse de votre serveur SQL et le nom de votre base de données. Pour **Nom d'utilisateur** et **Mot de passe**, indiquez le nom d’utilisateur et le mot de passe de votre serveur.

   Ces informations se trouvent également dans votre chaîne de connexion :

   * `Server=<your-server-address>`
   * `Database=<your-database-name>`
   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![Créer une connexion SQL Server](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)

1. Si votre serveur SQL utilise l’authentification Windows ou l’authentification De base, sélectionnez le **type d’authentification**.

1. Sous **Passerelles**, sélectionnez l’abonnement Azure associé à votre passerelle de données locale créée précédemment, puis sélectionnez le nom de votre passerelle de données locale.

   Si votre passerelle n’apparaît pas dans la liste, vérifiez que vous avez correctement [configuré votre passerelle](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection).

   ![Créer une connexion SQL Server terminé](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. Sélectionnez **Créer** lorsque vous avez terminé.

1. Après avoir créé votre connexion, poursuivez avec [Ajouter un déclencheur SQL](#add-sql-trigger) ou [Ajouter une action SQL](#add-sql-action).
