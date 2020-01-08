---
title: Créer une base de données unique
description: Créez et interrogez une base de données unique dans Azure SQL Database à l’aide du portail Azure, de PowerShell et d’Azure CLI.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: ninarn
ms.reviewer: carlrab, sstein
ms.date: 09/09/2019
ms.openlocfilehash: 5cfccd6e79b7289b6fd59abcabba05abab6ab09b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443840"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-portal-powershell-and-azure-cli"></a>Démarrage rapide : Créer une base de données unique dans Azure SQL Database à l’aide du portail Azure, de PowerShell et d’Azure CLI

La création d’une [base de données unique](sql-database-single-database.md) est l’option de déploiement la plus rapide et la plus simple pour créer une base de données dans Azure SQL Database. Ce guide de démarrage rapide vous montre comment créer et interroger une base de données unique à l’aide du portail Azure.

Si vous n’avez pas d’abonnement Azure, [créez un compte gratuit](https://azure.microsoft.com/free/). 

Pour toutes les étapes de ce guide de démarrage rapide, connectez-vous au [Portail Azure](https://portal.azure.com/).

## <a name="create-a-single-database"></a>Créer une base de données unique

Une base de données unique peut être créée au niveau du calcul approvisionné ou serverless.

- Une base de données unique dans le niveau de calcul provisionné a une quantité définie de ressources de calcul allouées au préalable, notamment en termes de mémoire et de processeur, selon l’un des deux [modèles d’achat](sql-database-purchase-models.md).
- Une base de données unique dans le niveau de calcul serverless a une gamme de ressources de calcul automatiquement mises à l’échelle, notamment en termes de mémoire et de processeur, et est uniquement disponible avec les [modèles d’achat vCore](sql-database-service-tiers-vcore.md).

Quand vous créez une base de données unique, vous définissez également un [serveur SQL Database](sql-database-servers.md) pour la gérer et la placer dans le [groupe de ressources Azure](../azure-resource-manager/management/overview.md) d’une région spécifiée.

> [!NOTE]
> Ce guide de démarrage rapide utilise le [modèle d’achat vCore](sql-database-service-tiers-vcore.md), mais le [modèle d’achat DTU](sql-database-service-tiers-DTU.md) est également disponible.

Pour créer une base de données unique contenant les exemples de données AdventureWorksLT :

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="query-the-database"></a>Interroger la base de données

Maintenant que vous avez créé la base de données, utilisez l’outil de requête intégré au portail Azure pour vous connecter à la base de données et interroger les données.

1. Sur la page **SQL Database** de votre base de données, sélectionnez **Éditeur de requête (préversion)** dans le menu de gauche.

   ![Se connecter à l’Éditeur de requêtes](./media/sql-database-get-started-portal/query-editor-login.png)

2. Entrez vos informations de connexion, puis sélectionnez **OK**.
3. Entrez la requête suivante dans le volet **Éditeur de requêtes**.

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

4. Sélectionnez **Exécuter**, puis passez en revue les résultats de la requête dans le volet **Résultats**.

   ![Interroger les résultats de l’Éditeur](./media/sql-database-get-started-portal/query-editor-results.png)

5. Fermez la page **Éditeur de requêtes**, puis sélectionnez **OK** à l’invite pour ignorer les modifications que vous n’avez pas enregistrées.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Conservez ce groupe de ressources, ce serveur SQL et cette base de données unique pour accéder aux [étapes suivantes](#next-steps). Les étapes suivantes vous montrent comment vous connecter à votre base de données et comment l’interroger via différentes méthodes.

Une fois que vous avez fini d’utiliser ces ressources, vous pouvez les supprimer comme suit :

1. Dans le menu de gauche du Portail Azure, cliquez sur **Groupes de ressources**, puis sur **myResourceGroup**.
2. Dans la page de votre groupe de ressources, sélectionnez **Supprimer le groupe de ressources**.
3. Entrez *myResourceGroup* dans le champ, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

- Créez une règle de pare-feu au niveau du serveur pour vous connecter à la base de données unique à l’aide d’outils locaux ou à distance. Pour plus d’informations, consultez [Créer une règle de pare-feu au niveau du serveur](sql-database-server-level-firewall-rule.md).
- Après avoir créé une règle de pare-feu au niveau du serveur, [connectez et interrogez](sql-database-connect-query.md) votre base de données à l’aide de plusieurs outils et langues différents.
  - [Se connecter et effectuer des requêtes à l’aide de SQL Server Management Studio](sql-database-connect-query-ssms.md)
  - [Se connecter et effectuer des requêtes à l’aide d’Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Pour créer une base de données unique dans la couche de calcul approvisionnée à l’aide d’Azure CLI, consultez [Exemples Azure CLI](sql-database-cli-samples.md).
- Pour créer une base de données unique dans la couche de calcul approvisionnée à l’aide d’Azure PowerShell, consultez [Exemples Azure PowerShell](sql-database-powershell-samples.md).
- Pour créer une base de données unique dans la couche de calcul serverless à l’aide d’Azure PowerShell, consultez [Créer une base de données serverless](sql-database-serverless.md#create-new-database-in-serverless-compute-tier).
