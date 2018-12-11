---
title: 'Portail Azure : Créer une règle de pare-feu SQL Database | Microsoft Docs'
description: Créer une règle de pare-feu au niveau du serveur SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 12/01/2018
ms.openlocfilehash: cab92539b5019d4807ddefb2b84279c844f53016
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2018
ms.locfileid: "52721899"
---
# <a name="quickstart-create-a-server-level-firewall-rule-for-your-sql-database-using-the-azure-portal"></a>Guide de démarrage rapide : Créer une règle de pare-feu au niveau du serveur pour votre base de données SQL à l’aide du portail Azure

Ce guide de démarrage rapide vous montre comment créer une règle de pare-feu au niveau du serveur pour une base de données SQL Azure à laquelle vous pourrez vous connecter à partir d’une ressource locale.

## <a name="prerequisites"></a>Prérequis

Ce tutoriel utilise les ressources créées dans [Créer une base de données SQL Azure dans le portail Azure](sql-database-get-started-portal.md) comme point de départ.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [Portail Azure](https://portal.azure.com/).

## <a name="create-a-server-level-firewall-rule"></a>Créer une règle de pare-feu au niveau du serveur

Le service SQL Database crée un pare-feu au niveau du serveur. Ce pare-feu empêche les applications et les outils de se connecter au serveur ou à des bases de données de serveur sauf si vous créez une règle pour ouvrir le pare-feu. Pour établir une connexion à partir d’une adresse IP hors Azure, créez une règle de pare-feu pour une adresse IP spécifique ou une plage d’adresses. Pour en savoir plus sur les règles de pare-feu, consultez [Règle de pare-feu SQL Database au niveau de la base de données](sql-database-firewall-configure.md).

> [!NOTE]
> SQL Database communique par le biais du port 1433. Si vous essayez de vous connecter à partir d’un réseau d’entreprise, le trafic sortant sur le port 1433 peut être bloqué par le pare-feu de votre réseau. Dans ce cas, vous ne pouvez pas vous connecter à votre serveur Azure SQL Database, sauf si votre service informatique ouvre le port 1433.
>

Suivez ces étapes pour créer une règle de pare-feu au niveau du serveur pour l’adresse IP de votre client afin de permettre la connectivité externe via le pare-feu SQL Database pour votre adresse IP uniquement.

1. Une fois le déploiement de [base de données Azure SQL prérequis](#prerequisites) terminé, sélectionnez **Bases de données SQL** dans le menu de gauche, puis **mySampleDatabase** dans la page **Bases de données SQL**. La page de présentation de votre base de données s’ouvre, elle affiche le nom de serveur complet (tel que **mynewserver-20170824.database.windows.net**) et fournit des options pour poursuivre la configuration.

2. Copiez ce nom de serveur complet pour vous connecter à votre serveur et à ses bases de données dans les guides de démarrage rapide suivants.

   ![nom du serveur](./media/sql-database-get-started-portal/server-name.png)

3. Sélectionnez **Définir le pare-feu du serveur** dans la barre d’outils. La page **Paramètres de pare-feu** du serveur de base de données SQL s’ouvre.

   ![règle de pare-feu de serveur](./media/sql-database-get-started-portal/server-firewall-rule.png)

4. Dans la barre d’outils, choisissez **Ajouter une adresse IP de client** afin d’ajouter votre adresse IP actuelle à une nouvelle règle de pare-feu. Une règle de pare-feu peut ouvrir le port 1433 pour une seule adresse IP ou une plage d’adresses IP.

   > [!IMPORTANT]
   > Par défaut, l’accès via le pare-feu SQL Database est activé pour tous les services Azure. Choisissez **ÉTEINT** dans cette page pour le désactiver pour tous les services Azure.
   >

5. Sélectionnez **Enregistrer**. Une règle de pare-feu au niveau du serveur est créée pour votre adresse IP actuelle et ouvre le port 1433 sur le serveur logique.

6. Fermez la page **Paramètres de pare-feu**.

À l’aide de SQL Server Management Studio ou de tout autre outil de votre choix, vous pouvez maintenant vous connecter au serveur SQL Database et à ses bases de données à partir de cette adresse IP à l’aide du compte Administrateur de serveur créé au préalable.

## <a name="clean-up-resources"></a>Supprimer des ressources

Enregistrez les ressources si vous souhaitez passer aux [Étapes suivantes](#next-steps) et découvrir comment vous connecter à votre base de données et les différentes méthodes à votre disposition pour l’interroger. Toutefois, si vous souhaitez supprimer les ressources créées dans ce guide de démarrage rapide, effectuez les étapes suivantes.


1. Dans le menu gauche du portail Azure, sélectionnez **Groupes de ressources**, puis **myResourceGroup**.
2. Dans la page de votre groupe de ressources, sélectionnez **Supprimer**, tapez **myResourceGroup** dans la zone de texte, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

- Maintenant que vous disposez d’une base de données, vous pouvez [vous y connecter et l’interroger](sql-database-connect-query.md) à l’aide d’un de vos outils ou langages préférés. Par exemple :
  - [Se connecter et interroger à l’aide de SQL Server Management Studio](sql-database-connect-query-ssms.md)
  - [Se connecter et effectuer des requêtes à l’aide d’Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Pour apprendre à concevoir votre première base de données, créer des tables et insérer des données, consultez un de ces didacticiels :
  - [Concevoir votre première base de données SQL Azure](sql-database-design-first-database.md)
  - [Concevoir une base de données SQL Azure et se connecter avec C# et ADO.NET](sql-database-design-first-database-csharp.md)
