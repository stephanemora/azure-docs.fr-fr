---
title: Utiliser les identités managées pour accéder à Azure SQL Database - Azure Stream Analytics
description: Cet article explique comment utiliser les identités managées afin d’authentifier votre tâche Azure Stream Analytics pour la sortie de stockage SQL DB.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 05/08/2020
ms.openlocfilehash: 906311452598d592b73a263ce25d0c8c51cc1cc7
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88870185"
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

   La page Administrateur Active Directory affiche tous les membres et groupes présents dans Active Directory. Les utilisateurs ou groupes grisés ne peuvent pas être sélectionnés, car ils ne sont pas pris en charge en tant qu’administrateurs Azure AD. Consultez la liste des administrateurs de prise en charge dans la section  **Fonctionnalités et limitations d’Azure AD** de l’article  [Utiliser l’authentification Azure Active Directory pour l’authentification auprès de SQL Database ou d’Azure Synapse](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations). Le contrôle d'accès basé sur les rôles (RBAC) s'applique uniquement au portail et n'est pas propagé vers SQL Server. Par ailleurs, l’utilisateur ou groupe sélectionné est l’utilisateur qui sera en mesure de créer l’**Utilisateur de base de données autonome** dans la section suivante.

1. Sur la page **Administrateur Active Directory**, sélectionnez **Enregistrer**. Le processus de modification de l’administrateur prend quelques minutes.

   Lors de la configuration de l’administrateur Azure AD, le nom du nouvel administrateur (utilisateur ou groupe) ne peut pas être présent dans la base de données MASTER virtuelle en tant qu’utilisateur de l’authentification SQL Server. S’il est présent, la configuration de l’administrateur d’Azure AD échoue et annule sa création, indiquant qu’il existe déjà un administrateur (nom). Dans la mesure où l’utilisateur de l’authentification SQL Server ne fait pas partie d’Azure AD, tout effort pour se connecter au serveur en utilisant l’authentification Azure AD échoue. 

## <a name="create-a-contained-database-user"></a>Créer un utilisateur de base de données autonome

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

1. Pour que l’Azure Active Directory de Microsoft vérifie si la tâche Stream Analytics a accès à SQL Database, nous devons autoriser Azure Active Directory à communiquer avec la base de données. Pour ce faire, accédez de nouveau à la page « Pare-feu et réseau virtuel » dans le Portail Azure, puis activez l’option « Autoriser les services et les ressources Azure à accéder à ce serveur ». 

   ![Pare-feu et réseau virtuel](./media/sql-db-output-managed-identity/allow-access.png)

## <a name="grant-stream-analytics-job-permissions"></a>Octroyer des autorisations de tâche Stream Analytics

Une fois que vous avez créé un utilisateur de base de données autonome et accordé un accès aux services Azure du portail, comme décrit dans la section précédente, votre tâche Stream Analytics est autorisée par Managed Identity à **se connecter** (autorisation CONNECT) à votre ressource SQL Database via une identité. Nous vous recommandons d’accorder à la tâche Stream Analytics les autorisations SELECT et INSERT, car celles-ci seront nécessaires plus tard dans le flux de travail Stream Analytics. L’autorisation **SELECT** permet à la tâche de tester sa connexion à la table dans SQL Database. L’autorisation **INSERT** permet de tester des requêtes Stream Analytics de bout en bout une fois que vous avez configuré une entrée et la sortie SQL Database. Vous pouvez accorder ces autorisations à la tâche Stream Analytics en utilisant SQL Server Management Studio. Pour plus d’informations, consultez la référence GRANT (Transact-SQL).

Pour n’accorder une autorisation qu’à une table ou à un objet spécifiques dans la base de données, utilisez la syntaxe T-SQL suivante, puis exécutez la requête. 

```sql
GRANT SELECT, INSERT ON OBJECT::TABLE_NAME TO ASA_JOB_NAME; 
```

Vous pouvez également cliquer avec le bouton droit sur votre base de données SQL dans SQL Server Management Studio, puis sélectionner **Propriétés > Autorisations**. La tâche Stream Analytics que vous avez ajoutée précédemment s'affiche dans le menu Autorisations, et vous pouvez manuellement octroyer ou refuser des autorisations comme vous le souhaitez.

## <a name="create-an-azure-sql-database-output"></a>Créer une sortie Azure SQL Database

Maintenant que votre identité gérée est configurée, vous êtes prêt à ajouter la base de données Azure SQL Database en tant que sortie à votre tâche Stream Analytics.

Assurez-vous d’avoir créé une table dans votre SQL Database avec le schéma de sortie approprié. Le nom de cette table est l’une des propriétés requises qui doit être spécifiée lorsque vous ajoutez la sortie SQL Database à la tâche Stream Analytics. En outre, assurez-vous que la tâche dispose des autorisations **SELECT** et **INSERT** pour tester la connexion et exécuter des requêtes Stream Analytics. Si ce n’est déjà fait, consultez la section [Octroyer des autorisations de tâche Stream Analytics](#grant-stream-analytics-job-permissions). 

1. Revenez à votre tâche Stream Analytics, puis accédez à la page **Sorties** sous **Topologie de la tâche**. 

1. Sélectionnez **Ajouter > SQL Database**. Dans la fenêtre de propriétés de la sortie du récepteur de sortie SQL Database, sélectionnez **Identité managée** dans la liste déroulante Mode d’authentification.

1. Renseignez le reste des propriétés. Pour en savoir plus sur la création d’une sortie SQL Database, consultez [Créer une sortie SQL Database avec Stream Analytics](sql-database-output.md). Lorsque vous avez terminé, sélectionnez **Enregistrer**. 

## <a name="next-steps"></a>Étapes suivantes

* [Comprendre les sorties d’Azure Stream Analytics](stream-analytics-define-outputs.md)
* [Sortie Azure Stream Analytics dans Azure SQL Database](stream-analytics-sql-output-perf.md)
