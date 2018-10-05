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
ms.date: 09/06/2018
ms.openlocfilehash: ea843344e70d400f05c2767675bacb466c5b3fbd
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47161911"
---
# <a name="create-a-server-level-firewall-rule-for-your-sql-database-using-the-azure-portal"></a>Créer une règle de pare-feu au niveau du serveur pour votre base de données SQL à l’aide du portail Azure

Ce guide de démarrage rapide vous montre comment créer une règle de pare-feu au niveau du serveur pour une base de données SQL Azure à laquelle vous pourrez vous connecter à partir d’une ressource locale.

## <a name="prerequisites"></a>Prérequis

Ce tutoriel utilise comme point de départ les ressources créées dans le guide de démarrage rapide [Créer une base de données SQL Azure dans le portail Azure](sql-database-get-started-portal.md).

## <a name="log-in-to-the-azure-portal"></a>Se connecter au portail Azure.

Connectez-vous au [Portail Azure](https://portal.azure.com/).

## <a name="create-a-server-level-firewall-rule"></a>Créer une règle de pare-feu au niveau du serveur

Le service SQL Database crée un pare-feu au niveau du serveur qui empêche les applications et les outils de se connecter au serveur ou à toute base de données sur le serveur, sauf si une règle de pare-feu est créée pour ouvrir le pare-feu. Pour établir une connexion à partir d’une adresse IP hors Azure, créez une règle de pare-feu pour une adresse IP spécifique ou une plage d’adresses. Suivez ces étapes pour créer une [règle de pare-feu au niveau du serveur de base de données SQL](sql-database-firewall-configure.md) pour l’adresse IP de votre client afin de permettre la connectivité externe via le pare-feu de base de données SQL pour votre adresse IP uniquement.

> [!NOTE]
> SQL Database communique par le biais du port 1433. Si vous essayez de vous connecter à partir d’un réseau d’entreprise, le trafic sortant sur le port 1433 peut ne pas être autorisé par le pare-feu de votre réseau. Dans ce cas, vous ne pouvez pas vous connecter à votre serveur Azure SQL Database, sauf si votre service informatique ouvre le port 1433.
>

1. Une fois le déploiement terminé, cliquez sur **Bases de données SQL** dans le menu de gauche, puis cliquez sur **mySampleDatabase** sur la page **Bases de données SQL**. La page de présentation de votre base de données s’ouvre, elle affiche le nom de serveur complet (tel que **mynewserver-20170824.database.windows.net**) et fournit des options pour poursuivre la configuration.

2. Copiez le nom complet du serveur pour vous connecter à votre serveur et à ses bases de données dans les démarrages rapides suivants.

   ![nom du serveur](./media/sql-database-get-started-portal/server-name.png)

3. Cliquez sur **Définir le pare-feu du serveur** dans la barre d’outils, comme illustré sur l’image précédente. La page **Paramètres de pare-feu** du serveur de base de données SQL s’ouvre.

   ![règle de pare-feu de serveur](./media/sql-database-get-started-portal/server-firewall-rule.png)

4. Dans la barre d’outils, cliquez sur **Ajouter une adresse IP cliente** afin d’ajouter votre adresse IP actuelle à une nouvelle règle de pare-feu. Une règle de pare-feu peut ouvrir le port 1433 pour une seule adresse IP ou une plage d’adresses IP.

5. Cliquez sur **Enregistrer**. Une règle de pare-feu au niveau du serveur est créée pour votre adresse IP actuelle et ouvre le port 1433 sur le serveur logique.

6. Cliquez sur **OK**, puis fermez la page **Paramètres de pare-feu**.

Vous pouvez maintenant vous connecter au serveur SQL Database et à ses bases de données à l’aide de SQL Server Management Studio ou de tout autre outil de votre choix à partir de cette adresse IP à l’aide du compte Administrateur de serveur créé au préalable.

> [!IMPORTANT]
> Par défaut, l’accès via le pare-feu SQL Database est activé pour tous les services Azure. Cliquez sur **ÉTEINT** sur cette page pour le désactiver pour tous les services Azure.
>

## <a name="clean-up-resources"></a>Supprimer des ressources

Enregistrez les ressources si vous souhaitez passer aux [Étapes suivantes](#next-steps) et découvrir comment vous connecter à votre base de données et les différentes méthodes à votre disposition pour l’interroger. Toutefois, si vous souhaitez supprimer les ressources créées dans ce démarrage rapide, procédez comme suit.


1. Dans le menu de gauche du portail Azure, cliquez sur **Groupes de ressources**, puis sur **myResourceGroup**.
2. Sur la page de votre groupe de ressources, cliquez sur **Supprimer**, tapez **myResourceGroup** dans la zone de texte, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

- Maintenant que vous disposez d’une base de données, vous pouvez [vous y connecter et l’interroger](sql-database-connect-query.md) à l’aide d’un de vos outils ou langages préférés. Par exemple :
  - [Se connecter et interroger à l’aide de SQL Server Management Studio](sql-database-connect-query-ssms.md)
  - [Se connecter et interroger à l’aide d’Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Pour apprendre à concevoir votre première base de données, créer des tables et insérer des données, consultez un de ces didacticiels :
 - [Concevoir votre première base de données SQL Azure](sql-database-design-first-database.md)
 - [Concevoir une base de données SQL Azure et se connecter avec C# et ADO.NET](sql-database-design-first-database-csharp.md)
