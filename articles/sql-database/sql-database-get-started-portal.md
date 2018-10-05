---
title: 'Portail Azure : Créer une base de données SQL | Microsoft Docs'
description: Créez un serveur logique SQL Database, une règle de pare-feu au niveau du serveur et une base de données dans le Portail Azure, puis interrogez cette dernière.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: carlrab
manager: craigg
ms.date: 09/07/2018
ms.openlocfilehash: 0e7ea33fa775bfba934d68d7cbcdd754880c3e55
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47165005"
---
# <a name="create-an-azure-sql-database-in-the-azure-portal"></a>Création d’une base de données SQL Azure dans le portail Azure

Ce démarrage rapide décrit comment créer une base de données SQL dans Azure à l’aide du [modèle d’achat DTU](sql-database-service-tiers-dtu.md). Azure SQL Database est une offre de type « base de données en tant que service » qui vous permet d’exécuter et de mettre à l’échelle des bases de données SQL Server hautement disponibles dans le cloud. Ce guide de démarrage rapide vous montre comment commencer en créant une base de données SQL à l’aide du portail Azure, puis en effectuant des requêtes.

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

  >[!NOTE]
  >Ce didacticiel utilise le modèle d’achat DTU, mais le [modèle d’achat vCore](sql-database-service-tiers-vcore.md) est également disponible.

## <a name="log-in-to-the-azure-portal"></a>Se connecter au portail Azure.

Connectez-vous au [Portail Azure](https://portal.azure.com/).

## <a name="create-a-sql-database"></a>Créer une base de données SQL

Une base de données SQL Azure est créée avec un ensemble défini de [ressources de calcul et de stockage](sql-database-service-tiers-dtu.md). La base de données est créée dans un [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md) et dans un [serveur logique Azure SQL Database](sql-database-features.md).

Suivez ces étapes pour créer une base de données SQL contenant les exemples de données Adventure Works LT.

1. Cliquez sur **Créer une ressource** en haut à gauche du portail Azure.

2. Dans la page **Nouveau**, sélectionnez **Bases de données**, puis **Créer** sous **SQL Database** dans **cette même** page.

   ![create database-1](./media/sql-database-get-started-portal/create-database-1.png)

3. Remplissez le formulaire de base de données SQL avec les informations suivantes, comme indiqué dans l’illustration précédente :   

   | Paramètre       | Valeur suggérée | DESCRIPTION |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Nom de la base de données** | mySampleDatabase | Pour les noms de base de données valides, consultez [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) (Identificateurs de base de données). |
   | **Abonnement** | Votre abonnement  | Pour plus d’informations sur vos abonnements, consultez [Abonnements](https://account.windowsazure.com/Subscriptions). |
   | **Groupe de ressources**  | myResourceGroup | Pour les noms de groupe de ressources valides, consultez [Naming conventions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Conventions d’affectation de nom). |
   | **Sélectionner une source** | Exemple (AdventureWorksLT) | Charge le schéma et les données AdventureWorksLT schéma dans votre nouvelle base de données |

   > [!IMPORTANT]
   > Vous devez sélectionner l’exemple de base de données sur ce formulaire, car il est utilisé dans le reste de ce démarrage rapide.
   >

4. Sous **Serveur**, cliquez sur **Configurer les paramètres requis** et remplissez le formulaire de serveur SQL (serveur logique) avec les informations suivantes, comme indiqué dans l’illustration précédente :   

   | Paramètre       | Valeur suggérée | DESCRIPTION |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Nom du serveur** | Nom globalement unique | Pour les noms de serveur valides, consultez [Naming conventions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Conventions d’affectation de nom). |
   | **Connexion d’administrateur du serveur** | Nom valide | Pour les noms de connexion valides, consultez [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) (Identificateurs de base de données). |
   | **Mot de passe** | Mot de passe valide | Votre mot de passe doit comporter au moins 8 caractères et contenir des caractères appartenant à trois des catégories suivantes : caractères en majuscules, caractères en minuscules, chiffres et caractères non alphanumériques. |
   | **Abonnement** | Votre abonnement | Pour plus d’informations sur vos abonnements, consultez [Abonnements](https://account.windowsazure.com/Subscriptions). |
   | **Groupe de ressources** | myResourceGroup | Pour les noms de groupe de ressources valides, consultez [Naming conventions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Conventions d’affectation de nom). |
   | **Lieu** | Emplacement valide | Pour plus d’informations sur les régions, consultez [Régions Azure](https://azure.microsoft.com/regions/). |

   > [!IMPORTANT]
   > La connexion d’administrateur serveur et le mot de passe que vous spécifiez ici seront requis plus loin dans ce guide de démarrage rapide pour la connexion au serveur et à ses bases de données. Retenez ou enregistrez ces informations pour une utilisation ultérieure.
   >  

   ![create database-server](./media/sql-database-get-started-portal/create-database-server.png)

5. Une fois le formulaire rempli, cliquez sur **Sélectionner**.

6. Cliquez sur **Niveau tarifaire** pour spécifier le niveau de service, le nombre de DTU et la quantité de stockage. Explorez les options concernant la quantité de DTU et de stockage disponible pour chaque niveau de service.

   > [!IMPORTANT]
   > Le niveau Premium offre actuellement plus de 1 To de stockage dans toutes les régions à l’exception des suivantes : Royaume-Uni Nord, USA Centre-Ouest, Royaume-Uni Sud 2, Chine Est, USDoDCentral, Allemagne - Centre, USDoDEast, US Gov Sud-Ouest, US Gov Centre-Sud, Allemagne - Nord-Est, Chine Nord, US Gov Est. Dans les autres régions, l’espace de stockage maximal au niveau Premium est limité à 1 To. Consultez [Limitations actuelles P11-P15]( sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

7. Pour ce démarrage rapide, sélectionnez le niveau de service **Standard** et utilisez le curseur pour sélectionner **10 DTU (S0)** et **1** Go de stockage.

   ![create database-s1](./media/sql-database-get-started-portal/create-database-s1.png)

8. Acceptez les conditions d’utilisation de la préversion pour pouvoir utiliser l’option **Stockage de composants additionnels**.

   > [!IMPORTANT]
   > Le niveau Premium offre actuellement plus de 1 To de stockage dans toutes les régions à l’exception des suivantes : USA Centre-Ouest, Chine Est, USDoDCentral, US Gov Iowa, Allemagne - Centre, USDoDEast, US Gov Sud-Ouest, Allemagne - Nord-Est, Chine Nord. Dans les autres régions, l’espace de stockage maximal au niveau Premium est limité à 1 To. Consultez [Limitations actuelles P11-P15]( sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

9. Après avoir sélectionné le niveau du serveur, le nombre de DTU et la quantité de stockage, cliquez sur **Appliquer**.  

10. Maintenant que vous avez rempli le formulaire SQL Database, cliquez sur **Créer** pour provisionner la base de données. Le provisionnement prend quelques minutes.

11. Dans la barre d’outils, cliquez sur **Notifications** pour surveiller le processus de déploiement.

     ![notification](./media/sql-database-get-started-portal/notification.png)

## <a name="query-the-sql-database"></a>Interroger la base de données SQL

Maintenant que vous avez créé un exemple de base de données dans Azure, nous allons utiliser l’outil de requête intégré au portail Azure pour vérifier que vous pouvez vous connecter à la base de données et demander des données.

1. Sur la page SQL Database de votre base de données, cliquez sur **Éditeur de requêtes (version préliminaire)** dans le menu de gauche, puis sur **Connexion**.

   ![se connecter](./media/sql-database-get-started-portal/query-editor-login.png)

2. Sélectionnez l’authentification SQL Server, fournissez les informations de connexion requises, puis cliquez sur **OK** pour vous connecter.

3. Après vous être authentifié en tant qu’**Administrateur du serveur**, saisissez la requête suivante dans le volet de l’éditeur de requête.

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

4. Cliquez sur **Exécuter**, puis passez en revue les résultats de la requête dans le volet **Résultats**.

   ![query editor results](./media/sql-database-get-started-portal/query-editor-results.png)

5. Fermez la page **Éditeur de requêtes**, puis cliquez sur **OK** pour ignorer vos modifications non enregistrées.

## <a name="clean-up-resources"></a>Supprimer des ressources

Enregistrez les ressources si vous souhaitez passer aux [Étapes suivantes](#next-steps) et découvrir comment vous connecter à votre base de données et les différentes méthodes à votre disposition pour l’interroger. Toutefois, si vous souhaitez supprimer les ressources créées dans ce démarrage rapide, procédez comme suit.


1. Dans le menu de gauche du portail Azure, cliquez sur **Groupes de ressources**, puis sur **myResourceGroup**.
2. Sur la page de votre groupe de ressources, cliquez sur **Supprimer**, tapez **myResourceGroup** dans la zone de texte, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

- Maintenant que vous avez une base de données, vous devez créer une règle de pare-feu au niveau du serveur pour pouvoir vous y connecter à partir de vos outils locaux. Consultez [Créer une règle de pare-feu au niveau du serveur](sql-database-get-started-portal-firewall.md).
- Si vous créez une règle de pare-feu au niveau du serveur, vous pouvez [vous connecter et effectuer des requêtes](sql-database-connect-query.md) à l’aide d’un de vos outils ou langages préférés. Par exemple :
  - [Se connecter et effectuer des requêtes à l’aide de SQL Server Management Studio](sql-database-connect-query-ssms.md)
  - [Se connecter et effectuer des requêtes à l’aide d’Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Pour créer des bases de données à l’aide d’Azure CLI, consultez [Exemples d’interface de ligne de commande Azure](sql-database-cli-samples.md)
- Pour créer des bases de données à l’aide d’Azure PowerShell, consultez [Exemples Azure PowerShell](sql-database-powershell-samples.md)