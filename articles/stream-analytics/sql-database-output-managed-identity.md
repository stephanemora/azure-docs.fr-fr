---
title: Utiliser les identités managées pour accéder à Azure SQL Database ou Azure Synapse Analytics - Azure Stream Analytics
description: Cet article explique comment utiliser les identités managées afin d’authentifier votre tâche Azure Stream Analytics pour la sortie Azure SQL Database ou Azure Synapse Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 11/30/2020
ms.openlocfilehash: e491c421f4af256b2e74fa61eb442d269bdb9e34
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102487914"
---
# <a name="use-managed-identities-to-access-azure-sql-database-or-azure-synapse-analytics-from-an-azure-stream-analytics-job-preview"></a>Utiliser les identités managées pour accéder à Azure SQL Database ou Azure Synapse Analytics à partir d’une tâche Azure Stream Analytics (préversion)

Azure Stream Analytics prend en charge l’[authentification des identités managées](../active-directory/managed-identities-azure-resources/overview.md) pour les récepteurs de sortie Azure SQL Database et Azure Synapse Analytics. Les identités managées n’ont pas les limitations des méthodes d’authentification basée sur l’utilisateur, comme la réauthentification obligatoire après un changement de mot de passe ou l’expiration du jeton d’utilisateur tous les 90 jours. En l'absence d'authentification manuelle, vos déploiements Stream Analytics peuvent être entièrement automatisés.

Une identité managée est une application managée inscrite auprès d’Azure Active Directory qui représente un travail Stream Analytics. L’application managée est utilisée à des fins d'authentification auprès d’une ressource ciblée. Cet article vous montre comment activer une identité managée pour une ou plusieurs sorties Azure SQL Database ou Azure Synapse Analytics d'une tâche Stream Analytics via le portail Azure.

## <a name="overview"></a>Vue d’ensemble

Cet article présente les étapes nécessaires pour connecter votre tâche Stream Analytics à votre pool Azure Synapse Analytics SQL ou Azure SQL Database à l’aide du mode d’authentification Managed Identity. 

- Vous devez d’abord créer une identité managée affectée par le système pour votre tâche Stream Analytics. Il s’agit de l’identité de votre tâche dans Azure Active Directory.  

- Ajoutez un administrateur Active Directory à votre espace de travail SQL Server ou Synapse, ce qui active l’authentification Azure AD (Managed Identity) pour cette ressource.

- Ensuite, créez un utilisateur contenu représentant l’identité de la tâche Stream Analytics dans la base de données. Chaque fois que la tâche Stream Analytics interagit avec votre ressource SQL DB ou Synapse SQL DB, il s’agit de l’identité à laquelle elle fera référence pour vérifier les autorisations dont dispose votre tâche Stream Analytics.

- Accordez à votre tâche Stream Analytics les autorisations nécessaires pour accéder à vos pools Synapse SQL ou SQL Database.

- Pour finir, ajoutez votre Azure SQL Database/Azure Synapse Analytics en tant que sortie dans la tâche Stream Analytics.

## <a name="prerequisites"></a>Prérequis

#### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/azure-sql)

Les éléments suivants sont requis pour utiliser cette fonctionnalité :

- Tâche Azure Stream Analytics.

- Ressource Azure SQL Database.

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

Les éléments suivants sont requis pour utiliser cette fonctionnalité :

- Tâche Azure Stream Analytics.

- Un pool SQL Azure Synapse Analytics.

- Un compte de stockage Azure [configuré pour votre tâche Stream Analytics](azure-synapse-analytics-output.md).

- Remarque : L’identité de service managée (MSI) de stockage de compte Stream Analytics intégrée à l’identité MSI Synapse SQL n’est pas disponible actuellement.

---

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

1. Accédez à votre ressource de pool SQL Azure Synapse Analytics ou Azure SQL Database et sélectionnez le serveur SQL ou l’espace de travail Synapse sous lequel se trouve la ressource. Vous trouverez le lien vers ces éléments dans la page de vue d’ensemble de ressource en regard de *Nom du serveur* ou *Nom de l’espace de travail*.

1. Sélectionnez **Administrateur Active Directory** ou **Administrateur SQL Active Directory** sous **Paramètres**, respectivement pour SQL Server et Espace de travail Synapse. Sélectionnez ensuite **Définir l’administrateur**.

   ![Page Administrateur Active Directory](./media/sql-db-output-managed-identity/active-directory-admin-page.png)

1. Sur la page Administrateur Active Directory, recherchez un utilisateur ou un groupe à définir en tant qu’administrateur pour SQL Server, puis cliquez sur **Sélectionner**. Il s’agit de l’utilisateur qui sera en mesure de créer l’**Utilisateur de base de données autonome** dans la section suivante.

   ![Ajouter un administrateur Active Directory](./media/sql-db-output-managed-identity/add-admin.png)

   La page Administrateur Active Directory affiche tous les membres et groupes présents dans Active Directory. Les utilisateurs ou groupes grisés ne peuvent pas être sélectionnés, car ils ne sont pas pris en charge en tant qu’administrateurs Azure Active Directory. Consultez la liste des administrateurs pris en charge dans la section  **Fonctionnalités et limitations d'Azure Active Directory** de l'article  [Utiliser l'authentification Azure Active Directory pour l'authentification auprès de SQL Database ou d'Azure Synapse](../azure-sql/database/authentication-aad-overview.md#azure-ad-features-and-limitations).

1. Sur la page **Administrateur Active Directory**, sélectionnez **Enregistrer**. Le processus de modification de l’administrateur prend quelques minutes.

## <a name="create-a-contained-database-user"></a>Créer un utilisateur de base de données autonome

Créez ensuite un utilisateur de base de données autonome dans votre base de données Azure SQL ou Azure Synapse, mappé à l’identité Azure Active Directory. L'utilisateur de base de données autonome ne dispose d'aucune connexion à la base de données primaire, mais correspond à une identité de l'annuaire associé à la base de données. L’identité Azure Active Directory peut être un compte d’utilisateur individuel ou un groupe. Dans ce cas, créez un utilisateur de base de données autonome pour votre tâche Stream Analytics. 

Pour plus d’informations, consultez l’article suivant pour en savoir plus sur l’intégration d’Azure AD : [Authentification universelle avec SQL Database et Azure Synapse Analytics (prise en charge de SSMS pour l’authentification MFA)](../azure-sql/database/authentication-mfa-ssms-overview.md)

1. Connectez-vous à votre base de données Azure SQL ou Azure Synapse via SQL Server Management Studio. Le **nom d'utilisateur** correspond à un utilisateur Azure Active Directory doté de l’autorisation **ALTER ANY USER**. L’administrateur que vous avez défini sur l'instance SQL Server est un exemple. Utilisez **Azure Active Directory - Authentification universelle avec MFA**. 

   ![Se connecter à SQL Server](./media/sql-db-output-managed-identity/connect-sql-server.png)

   Le nom du serveur `<SQL Server name>.database.windows.net` peut être différent selon les régions. Par exemple, la région Chine doit utiliser `<SQL Server name>.database.chinacloudapi.cn`.
 
   Vous pouvez spécifier une base de données Azure SQL ou Azure Synapse spécifique en accédant à **Options > Propriétés de connexion > Se connecter à la base de données**.  

   ![Propriétés de connexion SQL Server](./media/sql-db-output-managed-identity/sql-server-connection-properties.png)

1. Lorsque vous vous connectez pour la première fois, la fenêtre suivante peut s'afficher :

   ![Fenêtre Nouvelle règle de pare-feu](./media/sql-db-output-managed-identity/new-firewall-rule.png)

   1. Si tel est le cas, accédez à votre ressource SQL Server/Espace de travail Synapse dans le portail Azure. Dans la section **Sécurité**, ouvrez la page **Pare-feu et réseau virtuel/Pare-feu**. 
   1. Ajoutez une nouvelle règle avec n’importe quel nom de règle.
   1. Utilisez l'adresse IP *De* de la fenêtre **Nouvelle règle de pare-feu** pour l'*adresse IP de début*.
   1. Utilisez l'adresse IP *À* de la fenêtre **Nouvelle règle de pare-feu** pour l'*adresse IP de fin*. 
   1. Sélectionnez **Enregistrer** et essayez de vous reconnecter à partir de SQL Server Management Studio. 

1. Une fois connecté, créez l’utilisateur de base de données autonome. La commande SQL suivante crée un utilisateur de base de données autonome portant le même nom que votre tâche Stream Analytics. Veillez à mettre *ASA_JOB_NAME* entre crochets. Utilisez la syntaxe T-SQL suivante et exécutez la requête. 

   ```sql
   CREATE USER [ASA_JOB_NAME] FROM EXTERNAL PROVIDER; 
   ```
   
    Pour vérifier si vous avez ajouté correctement l’utilisateur de base de données autonome, exécutez la commande suivante dans SSMS sous la base de données concernée et vérifiez si votre *ASA_JOB_NAME* figure sous la colonne « Nom ».

   ```sql
   SELECT * FROM <SQL_DB_NAME>.sys.database_principals 
   WHERE type_desc = 'EXTERNAL_USER' 
   ```

1. Pour que l’Azure Active Directory de Microsoft vérifie si la tâche Stream Analytics a accès à SQL Database, nous devons autoriser Azure Active Directory à communiquer avec la base de données. Pour ce faire, accédez de nouveau à la page « Pare-feu et réseau virtuel/Pare-feu » dans le portail Azure, puis activez l’option « Autoriser les services et les ressources Azure à accéder à ce serveur/espace de travail ».

   ![Pare-feu et réseau virtuel](./media/sql-db-output-managed-identity/allow-access.png)

## <a name="grant-stream-analytics-job-permissions"></a>Octroyer des autorisations de tâche Stream Analytics

#### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/azure-sql)

Une fois que vous avez créé un utilisateur de base de données autonome et accordé un accès aux services Azure du portail, comme décrit dans la section précédente, votre tâche Stream Analytics est autorisée par Managed Identity à **se connecter** (autorisation CONNECT) à votre ressource Azure SQL Database via une identité. Nous vous recommandons d’accorder à la tâche Stream Analytics les autorisations **SELECT** et **INSERT**, car celles-ci seront nécessaires plus tard dans le flux de travail Stream Analytics. L’autorisation **SELECT** permet à la tâche de tester sa connexion à la table dans Azure SQL Database. L’autorisation **INSERT** permet de tester des requêtes Stream Analytics de bout en bout une fois que vous avez configuré une entrée et la sortie de la base de données SQL Azure.

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

Une fois que vous avez créé un utilisateur de base de données autonome et accordé un accès aux services Azure du portail, comme décrit dans la section précédente, votre tâche Stream Analytics est autorisée par Managed Identity à **se connecter** (autorisation CONNECT) à votre ressource Azure Synapse via une identité. Nous vous recommandons d’accorder à la tâche Stream Analytics les autorisations **SELECT**, **INSERT** et **ADMINISTER DATABASE BULK OPERATIONS**, car celles-ci seront nécessaires plus tard dans le workflow Stream Analytics. L’autorisation **SELECT** permet à la tâche de tester sa connexion à la table dans la base de données Azure Synapse. Les autorisations **INSERT** et **ADMINISTER DATABASE BULK OPERATIONS** permettent de tester des requêtes Stream Analytics de bout en bout une fois que vous avez configuré une entrée et la sortie de la base de données Azure Synapse.

Pour accorder l’autorisation **ADMINISTER DATABASE BULK OPERATIONS**, vous devez accorder toutes les autorisations étiquetées comme **CONTROL** sous [Impliquée par une autorisation de base de données](/sql/t-sql/statements/grant-database-permissions-transact-sql?view=azure-sqldw-latest&preserve-view=true#remarks) à la tâche Stream Analytics. Vous avez besoin de cette autorisation car la tâche Stream Analytics exécute l’instruction **COPY**, qui nécessite [ADMINISTER DATABASE BULK OPERATIONS et INSERT](/sql/t-sql/statements/copy-into-transact-sql).

---

Utilisez SQL Server Management Studio pour octroyer ces autorisations à la tâche Stream Analytics. Pour plus d'informations, consultez la référence GRANT (Transact-SQL).

Pour n’accorder une autorisation qu’à une table ou à un objet spécifiques dans la base de données, utilisez la syntaxe T-SQL suivante, puis exécutez la requête. 

#### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/azure-sql)

```sql
GRANT CONNECT, SELECT, INSERT ON OBJECT::TABLE_NAME TO ASA_JOB_NAME;
```

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

```sql
GRANT CONNECT, SELECT, INSERT, CONTROL, ADMINISTER DATABASE BULK OPERATIONS OBJECT::TABLE_NAME TO ASA_JOB_NAME;
```

---

Vous pouvez également cliquer avec le bouton droit sur votre base de données Azure SQL ou Azure Synapse dans SQL Server Management Studio, puis sélectionner **Propriétés > Autorisations**. La tâche Stream Analytics que vous avez ajoutée précédemment s'affiche dans le menu Autorisations, et vous pouvez manuellement octroyer ou refuser des autorisations comme vous le souhaitez.

Pour consulter toutes les autorisations que vous avez ajoutées à votre utilisateur *ASA_JOB_NAME*, exécutez la commande suivante dans SSMS sous la base de données en question : 

```sql
SELECT dbprin.name, dbprin.type_desc, dbperm.permission_name, dbperm.state_desc, dbperm.class_desc, object_name(dbperm.major_id) 
FROM sys.database_principals dbprin 
LEFT JOIN sys.database_permissions dbperm 
ON dbperm.grantee_principal_id = dbprin.principal_id 
WHERE dbprin.name = '<ASA_JOB_NAME>' 
```

## <a name="create-an-azure-sql-database-or-azure-synapse-output"></a>Créer une sortie Azure SQL Database ou Azure Synapse

#### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/azure-sql)

Maintenant que votre identité gérée est configurée, vous êtes prêt à ajouter une base de données Azure SQL Database ou Azure Synapse à votre tâche Stream Analytics.

Assurez-vous d’avoir créé une table dans votre SQL Database avec le schéma de sortie approprié. Le nom de cette table est l’une des propriétés requises qui doit être spécifiée lorsque vous ajoutez la sortie SQL Database à la tâche Stream Analytics. En outre, assurez-vous que la tâche dispose des autorisations **SELECT** et **INSERT** pour tester la connexion et exécuter des requêtes Stream Analytics. Si ce n’est déjà fait, consultez la section [Octroyer des autorisations de tâche Stream Analytics](#grant-stream-analytics-job-permissions). 

1. Revenez à votre tâche Stream Analytics, puis accédez à la page **Sorties** sous **Topologie de la tâche**. 

1. Sélectionnez **Ajouter > SQL Database**. Dans la fenêtre de propriétés de la sortie du récepteur de sortie SQL Database, sélectionnez **Identité managée** dans la liste déroulante Mode d’authentification.

1. Renseignez le reste des propriétés. Pour en savoir plus sur la création d’une sortie SQL Database, consultez [Créer une sortie SQL Database avec Stream Analytics](sql-database-output.md). Lorsque vous avez terminé, sélectionnez **Enregistrer**.

1. Après un clic sur **Enregistrer**, un test de connexion à votre ressource doit se déclencher automatiquement. Une fois ce test terminé, vous avez correctement configuré votre tâche Stream Analytics pour vous connecter à votre base de données Azure SQL ou Synapse SQL à l’aide du mode d’authentification Managed Identity. 

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

Maintenant que votre identité gérée et votre compte de stockage sont configurés, vous êtes prêt à ajouter la base de données Azure SQL Database en tant que sortie à votre tâche Stream Analytics.

Assurez-vous d’avoir créé une table dans votre base de données Azure Synapse avec le schéma de sortie approprié. Le nom de cette table est l’une des propriétés requises qui doit être spécifiée lorsque vous ajoutez la sortie Azure Synapse à la tâche Stream Analytics. En outre, assurez-vous que la tâche dispose des autorisations **SELECT** et **INSERT** pour tester la connexion et exécuter des requêtes Stream Analytics. Si ce n’est déjà fait, consultez la section [Octroyer des autorisations de tâche Stream Analytics](#grant-stream-analytics-job-permissions).

1. Revenez à votre tâche Stream Analytics, puis accédez à la page **Sorties** sous **Topologie de la tâche**.

1. Sélectionnez **Ajouter > Azure Synapse Analytics**. Dans la fenêtre de propriétés de la sortie du récepteur de sortie SQL Database, sélectionnez **Identité managée** dans la liste déroulante Mode d’authentification.

1. Renseignez le reste des propriétés. Pour en savoir plus sur la création d’une sortie Azure Synapse, consultez [Sortie Azure Synapse Analytics à partir d’Azure Stream Analytics](azure-synapse-analytics-output.md). Lorsque vous avez terminé, sélectionnez **Enregistrer**.

1. Après un clic sur **Enregistrer**, un test de connexion à votre ressource doit se déclencher automatiquement. Une fois cette opération terminée, vous êtes maintenant prêt à utiliser Managed Identity pour votre ressource Azure Synapse Analytics avec Stream Analytics. 

---

## <a name="remove-managed-identity"></a>Supprimer l’identité managée

L’identité gérée créée pour une tâche Stream Analytics est supprimée uniquement lorsque la tâche est supprimée. Il n’existe aucun moyen de supprimer l’identité gérée sans supprimer la tâche. Si vous ne souhaitez plus utiliser l’identité gérée, vous pouvez modifier la méthode d’authentification pour la sortie. L’identité gérée continuera d’exister jusqu’à ce que la tâche soit supprimée, et sera réemployée si vous décidez à nouveau d’utiliser l’authentification d’identité gérée.

## <a name="next-steps"></a>Étapes suivantes

* [Comprendre les sorties d’Azure Stream Analytics](stream-analytics-define-outputs.md)
* [Sortie Azure Stream Analytics dans Azure SQL Database](stream-analytics-sql-output-perf.md)
* [Sortie Azure Synapse Analytics à partir d’Azure Stream Analytics](azure-synapse-analytics-output.md)
