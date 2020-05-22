---
title: Utiliser les identités managées pour accéder à Azure SQL Database - Azure Stream Analytics
description: Cet article explique comment utiliser les identités managées afin d’authentifier votre tâche Azure Stream Analytics pour la sortie de stockage SQL DB.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 70ad69c1a34f656347b0cf53b28a1c35ac6ad043
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595838"
---
# <a name="use-managed-identities-to-access-azure-sql-database-from-an-azure-stream-analytics-job-preview"></a>Utiliser les identités managées pour accéder à Azure SQL Database à partir d’une tâche Azure Stream Analytics (préversion)

Azure Stream Analytics prend en charge l’[authentification des identités managées](../active-directory/managed-identities-azure-resources/overview.md) pour les récepteurs de sortie Azure SQL Database. Les identités managées n’ont pas les limitations des méthodes d’authentification basée sur l’utilisateur, comme la réauthentification obligatoire après un changement de mot de passe ou l’expiration du jeton d’utilisateur tous les 90 jours. En l'absence d'authentification manuelle, vos déploiements Stream Analytics peuvent être entièrement automatisés.

Une identité managée est une application managée inscrite auprès d’Azure Active Directory qui représente un travail Stream Analytics. L’application managée est utilisée à des fins d'authentification auprès d’une ressource ciblée. Cet article vous montre comment activer une identité managée pour une ou plusieurs sorties Azure SQL Database d'une tâche Azure Stream Analytics via le portail Azure.

## <a name="prerequisites"></a>Prérequis

Les éléments suivants sont requis pour cette fonctionnalité :

- Tâche Azure Stream Analytics.

- Ressource Azure SQL Database.

## <a name="create-a-managed-identity"></a>Créer une identité managée

Commencez par créer une identité managée pour votre tâche Azure Stream Analytics.

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre tâche Azure Stream Analytics.

1. Dans le menu de navigation gauche, sélectionnez **Identité managée** sous **Configurer**. Activez ensuite la case à cocher située en regard de **Utiliser l'identité managée affectée par le système**, puis sélectionnez **Enregistrer**.

   ![Sélectionner l'identité managée attribuée par le système](./media/sql-db-output-managed-identity/system-assigned-managed-identity.png)


   Un principal de service pour l’identité associée à la tâche Stream Analytics est créé dans Azure Active Directory. Le cycle de vie de la nouvelle identité est géré par Azure. Quand le travail Stream Analytics est supprimé, l’identité associée (autrement dit, le principal de service) est également automatiquement supprimée par Azure. 

1. Lorsque vous enregistrez la configuration, l’ID objet (l’OID) du principal de service s’affiche en tant qu’ID de principal, comme ci-dessous : 

   ![ID d’objet affiché en tant qu’ID de principal](./media/sql-db-output-managed-identity/principal-id.png)

   Le principal de service a le même nom que le travail Stream Analytics. Par exemple, si le nom de votre travail est *MyASAJob*, le nom du principal de service est également *MyASAJob*.

## <a name="select-an-active-directory-admin"></a>Sélectionner un administrateur Active Directory

Une fois l'identité managée créée, sélectionnez un administrateur Active Directory.

1. Accédez à votre ressource Azure SQL Database et sélectionnez l'instance SQL Server hébergeant la base de données. Le nom de l'instance SQL Server se trouve en regard du *nom du serveur* sur la page de vue d’ensemble des ressources. 

1. Sous **Paramètres**, sélectionnez **Administrateur Active Directory**. Sélectionnez ensuite **Définir l’administrateur**. 

   ![Page Administrateur Active Directory](./media/sql-db-output-managed-identity/active-directory-admin-page.png)
 
1. Sur la page Administrateur Active Directory, recherchez un utilisateur ou un groupe à définir en tant qu’administrateur pour SQL Server, puis cliquez sur **Sélectionner**.  

   ![Ajouter un administrateur Active Directory](./media/sql-db-output-managed-identity/add-admin.png)

1. Sur la page **Administrateur Active Directory**, sélectionnez **Enregistrer**. Le processus de modification de l’administrateur prend quelques minutes.  

## <a name="create-a-database-user"></a>Créer un utilisateur de base de données

Créez ensuite un utilisateur de base de données autonome dans votre base de données SQL Database mappé à l’identité Azure Active Directory. L'utilisateur de base de données autonome ne dispose d'aucune connexion à la base de données MASTER, mais correspond à une identité dans l'annuaire associé à la base de données. L’identité Azure Active Directory peut être un compte d’utilisateur individuel ou un groupe. Dans ce cas, créez un utilisateur de base de données autonome pour votre tâche Stream Analytics. 

1. Connectez-vous à la base de données SQL à l'aide de SQL Server Management Studio. Le **nom d'utilisateur** correspond à un utilisateur Azure Active Directory doté de l’autorisation **ALTER ANY USER**. L’administrateur que vous avez défini sur l'instance SQL Server est un exemple. Utilisez **Azure Active Directory - Authentification universelle avec MFA**. 

   ![Se connecter à SQL Server](./media/sql-db-output-managed-identity/connect-sql-server.png)

   Le nom du serveur `<SQL Server name>.database.windows.net` peut être différent selon les régions. Par exemple, la région Chine doit utiliser `<SQL Server name>.database.chinacloudapi.cn`.
 
   Vous pouvez spécifier une base de données SQL Database spécifique en accédant à **Options > Propriétés de connexion > Se connecter à la base de données**.  

   ![Propriétés de connexion SQL Server](./media/sql-db-output-managed-identity/sql-server-connection-properties.png)

1. Lorsque vous vous connectez pour la première fois, la fenêtre suivante peut s'afficher :

   ![Fenêtre Nouvelle règle de pare-feu](./media/sql-db-output-managed-identity/new-firewall-rule.png)

   1. Si tel est le cas, accédez à votre ressource SQL Server dans le portail Azure. Dans la section **Sécurité**, ouvrez la page **Pare-feux et réseaux virtuels**. 
   1. Ajoutez une nouvelle règle avec n’importe quel nom de règle.
   1. Utilisez l'adresse IP *De* de la fenêtre **Nouvelle règle de pare-feu** pour l'*adresse IP de début*.
   1. Utilisez l'adresse IP *À* de la fenêtre **Nouvelle règle de pare-feu** pour l'*adresse IP de fin*. 
   1. Sélectionnez **Enregistrer** et essayez de vous reconnecter à partir de SQL Server Management Studio. 

1. Une fois connecté, créez l’utilisateur de base de données autonome. La commande SQL suivante crée un utilisateur de base de données autonome portant le même nom que votre tâche Stream Analytics. Veillez à mettre *ASA_JOB_NAME* entre crochets. Utilisez la syntaxe T-SQL suivante et exécutez la requête. 

   ```sql
   CREATE USER [ASA_JOB_NAME] FROM EXTERNAL PROVIDER; 
   ```

## <a name="grant-stream-analytics-job-permissions"></a>Octroyer des autorisations de tâche Stream Analytics

L'identité managée autorise la tâche Stream Analytics à **SE CONNECTER** à votre ressource SQL Database. Très probablement, il serait efficace de permettre à la tâche Stream Analytics d'exécuter des commandes telles que **SELECT**. Utilisez SQL Server Management Studio pour octroyer ces autorisations à la tâche Stream Analytics. Pour plus d'informations, consultez la référence [GRANT (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql?view=sql-server-ver15).

Vous pouvez également cliquer avec le bouton droit sur votre base de données SQL dans SQL Server Management Studio, puis sélectionner **Propriétés > Autorisations**. La tâche Stream Analytics que vous avez ajoutée précédemment s'affiche dans le menu Autorisations, et vous pouvez manuellement octroyer ou refuser des autorisations comme vous le souhaitez.

## <a name="create-an-azure-sql-database-output"></a>Créer une sortie Azure SQL Database

Maintenant que votre identité gérée est configurée, vous êtes prêt à ajouter la base de données Azure SQL Database en tant que sortie à votre tâche Stream Analytics.

1. Revenez à votre tâche Stream Analytics, puis accédez à la page **Sorties** sous **Topologie de la tâche**. 

1. Sélectionnez **Ajouter > SQL Database**. Dans la fenêtre de propriétés de la sortie du récepteur de sortie SQL Database, sélectionnez **Identité managée** dans la liste déroulante Mode d’authentification.

1. Renseignez le reste des propriétés. Pour en savoir plus sur la création d’une sortie SQL Database, consultez [Créer une sortie SQL Database avec Stream Analytics](stream-analytics-define-outputs.md#sql-database). Lorsque vous avez terminé, sélectionnez **Enregistrer**. 

## <a name="next-steps"></a>Étapes suivantes

* [Comprendre les sorties d’Azure Stream Analytics](stream-analytics-define-outputs.md)
* [Sortie Azure Stream Analytics dans Azure SQL Database](stream-analytics-sql-output-perf.md)
