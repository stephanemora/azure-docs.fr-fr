---
title: Activer AAD pour Azure-SSIS Integration Runtime
description: Cet article décrit comment activer l’authentification Azure Active Directory avec l’identité managée pour Azure Data Factory afin de créer un Azure-SSIS Integration Runtime.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: powershell
ms.topic: conceptual
author: swinarko
ms.author: sawinark
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 5/14/2019
ms.openlocfilehash: 70367a38fbf7b59486e2eaaf6c05634aa7575869
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79230001"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>Activer l’authentification Azure Active Directory pour Azure-SSIS Integration Runtime

Cet article vous explique comment activer l’authentification Azure Active Directory (Azure AD) avec l’identité managée pour votre Azure Data Factory (ADF) et l’utiliser à la place de méthodes d’authentification conventionnelles (telles que l’authentification SQL) pour :

- Créer un Azure-SSIS Integration Runtime (IR) qui, à son tour, approvisionnera la base de données du catalogue SSIS (SSISDB) dans le serveur/l’instance gérée Azure SQL Database en votre nom.

- Connectez-vous à différentes ressources Azure lors de l’exécution de packages SSIS sur Azure-SSIS IR.

Pour plus d’informations sur l’identité managée de votre ADF, consultez [Managed identiy for Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) (Identité managée pour Data Factory).

> [!NOTE]
>-  Dans ce scénario, l’authentification Azure AD avec l’identité managée pour votre ADF est utilisée uniquement pour la création et les opérations de démarrage suivantes de votre SSIS IR qui, à son tour, approvisionnera et se connectera à la base de données SSISDB. Pour les exécutions de package SSIS, votre SSIS IR se connectera toujours à la base de données SSISDB à l’aide de l’authentification SQL avec des comptes entièrement gérés, créés lors de l’approvisionnement de la base de données SSISDB.
>-  Si vous avez déjà créé un SSIS IR à l’aide de l’authentification SQL, vous ne pouvez pas pour l’instant le reconfigurer pour utiliser l’authentification Azure AD avec PowerShell, mais vous pouvez le faire dans l’application ADF/le Portail Azure. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="enable-azure-ad-on-azure-sql-database"></a>Activer Azure AD sur Azure SQL Database

Le serveur Azure SQL Database prend en charge la création d’une base de données avec un utilisateur Azure AD. Tout d’abord, vous devez créer un groupe Azure AD avec l’identité managée pour votre ADF en tant que membre. Ensuite, vous devez définir un utilisateur Azure AD comme administrateur Active Directory pour votre serveur Azure SQL Database, puis vous y connecter sur SQL Server Management Studio (SSMS) à l’aide de cet utilisateur. Pour finir, vous devez créer un utilisateur de base de données autonome représentant le groupe Azure AD, afin que l’identité managée pour votre ADF soit utilisable par Azure-SSIS IR pour créer SSISDB à votre place.

### <a name="create-an-azure-ad-group-with-the-managed-identity-for-your-adf-as-a-member"></a>Créer un groupe Azure AD avec l’identité managée pour votre ADF en tant que membre

Vous pouvez utiliser un groupe Azure AD existant ou en créer un à l’aide d’Azure AD PowerShell.

1.  Installez le module [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2).

2.  Connectez-vous avec  `Connect-AzureAD`, puis exécutez l’applet de commande suivante pour créer un groupe et l’enregistrer dans une variable :

    ```powershell
    $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                              -MailEnabled $false `
                              -SecurityEnabled $true `
                              -MailNickName "NotSet"
    ```

    Le résultat ressemble à l’exemple suivant, qui affiche également la valeur de la variable :

    ```powershell
    $Group

    ObjectId DisplayName Description
    -------- ----------- -----------
    6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
    ```

3.  Ajoutez au groupe l’identité managée pour votre service Azure Data Factory. Vous pouvez suivre l’article [Managed identiy for Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) (Identité managée pour Data Factory) pour obtenir l’ID d’objet principal de l’identité managée (par exemple 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc), mais n’utilisez pas l’ID d’application de l’identité managée à cette fin.

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    Vous pouvez également vérifier l’appartenance au groupe par la suite.

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

### <a name="configure-azure-ad-authentication-for-azure-sql-database-server"></a>Configurer l’authentification Azure AD pour le serveur Azure SQL Database

Vous pouvez [configurer et gérer l’authentification Azure AD avec SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure) en effectuant les étapes suivantes :

1.  Dans le portail Azure, dans le volet de navigation gauche, sélectionnez **Tous les services** -> **Serveurs SQL**.

2.  Sélectionnez votre serveur Azure SQL Database à configurer avec l’authentification Azure AD.

3.  Dans la section **Paramètres** du panneau, sélectionnez **Administrateur Active Directory**.

4.  Dans la barre de commandes, sélectionnez **Définir l’administrateur**.

5.  Sélectionnez un compte d’utilisateur Azure AD à désigner comme administrateur du serveur, puis choisissez **Sélectionner**.

6.  Dans la barre de commandes, sélectionnez **Enregistrer**.

### <a name="create-a-contained-user-in-azure-sql-database-server-representing-the-azure-ad-group"></a>Créer un utilisateur de base de données autonome sur le serveur Azure SQL Database représentant le groupe Azure AD

Pour cette nouvelle étape, vous avez besoin de  [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)  (SSMS).

1. Démarrer SSMS.

2. Dans la boîte de dialogue **Se connecter au serveur**, dans le champ **Nom du serveur**, entrez le nom de votre serveur Azure SQL Database.

3. Dans le champ **Authentification**, sélectionnez **Active Directory - Authentification universelle avec prise en charge de MFA** (vous pouvez également utiliser les deux autres types d’authentification Active Directory ; consultez [Configurer et gérer l’authentification Azure AD avec SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)).

4. Dans le champ **Nom d’utilisateur**, entrez le nom du compte Azure AD que vous avez défini comme administrateur du serveur, par exemple testuser@xxxonline.com.

5. Sélectionnez **Se connecter** et terminez le processus de connexion.

6. Dans l’**Explorateur d’objets**, développez le dossier **Bases de données** -> **Bases de données système**.

7. Cliquez avec le bouton droit sur la base de données **master** et sélectionnez **Nouvelle requête**.

8. Dans la fenêtre de requête, entrez la commande T-SQL suivante, puis sélectionnez **Exécuter** dans la barre d’outils.

   ```sql
   CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
   ```

   La commande doit s’exécuter correctement, en créant l’utilisateur de base de données autonome pour représenter le groupe.

9. Effacez la fenêtre de requête, entrez la commande T-SQL suivante, puis sélectionnez **Exécuter** dans la barre d’outils.

   ```sql
   ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
   ```

   La commande doit s’exécuter correctement, et octroyer à l’utilisateur contenu la capacité des créer une base de données (SSISDB).

10. Si votre base de données SSISDB a été créée à l’aide d’une authentification SQL et que vous souhaitez basculer vers une authentification Azure AD pour votre Azure-SSIS IR pour y accéder, cliquez avec le bouton droit sur la base de données **SSISDB**, puis sélectionnez**Nouvelle requête**.

11. Dans la fenêtre de requête, entrez la commande T-SQL suivante, puis sélectionnez **Exécuter** dans la barre d’outils.

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    La commande doit s’exécuter correctement, en créant l’utilisateur de base de données autonome pour représenter le groupe.

12. Effacez la fenêtre de requête, entrez la commande T-SQL suivante, puis sélectionnez **Exécuter** dans la barre d’outils.

    ```sql
    ALTER ROLE db_owner ADD MEMBER [SSISIrGroup]
    ```

    La commande doit s’exécuter correctement et octroyer à l’utilisateur contenu la capacité d’accéder à la base de données SSISDB.

## <a name="enable-azure-ad-on-azure-sql-database-managed-instance"></a>Activer Azure AD sur Azure SQL Database Managed Instance

Azure SQL Database Managed Instance prend en charge la création directe d’une base de données avec l’identité managée pour votre ADF. Vous n’avez pas besoin de joindre l’identité managée pour votre ADF à un groupe Azure AD, ni de créer un utilisateur de base de données autonome représentant ce groupe dans votre Managed Instance.

### <a name="configure-azure-ad-authentication-for-azure-sql-database-managed-instance"></a>Configurer l’authentification Azure AD pour Azure SQL Database Managed Instance

Suivez les étapes décrites dans [Approvisionner un administrateur d’Azure Active Directory pour votre instance gérée](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure#provision-an-azure-active-directory-administrator-for-your-managed-instance).

### <a name="add-the-managed-identity-for-your-adf-as-a-user-in-azure-sql-database-managed-instance"></a>Ajouter l’identité managée pour votre ADF comme utilisateur dans Azure SQL Database Managed Instance

Pour cette nouvelle étape, vous avez besoin de  [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)  (SSMS).

1.  Démarrer SSMS.

2.  Connectez-vous à votre instance Managed Instance à l’aide d’un compte SQL Server qui est un **administrateur système**. Il s’agit d’une limitation temporaire qui sera levée une fois que les principaux de serveur (connexions) Azure AD d’Azure SQL Database Managed Instance deviendront des comptes en disponibilité générale. L’erreur suivante s’affiche si vous essayez d’utiliser un compte d’administrateur Azure AD pour créer la connexion : Msg 15247, Niveau 16, État 1, Ligne 1 L’utilisateur n’est pas autorisé à effectuer cette action.

3.  Dans l’**Explorateur d’objets**, développez le dossier **Bases de données** -> **Bases de données système**.

4.  Cliquez avec le bouton droit sur la base de données **master** et sélectionnez **Nouvelle requête**.

5.  Dans la fenêtre de requête, exécutez le script T-SQL suivant pour ajouter l’identité managée pour votre ADF comme utilisateur.

    ```sql
    CREATE LOGIN [{your ADF name}] FROM EXTERNAL PROVIDER
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{your ADF name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{your ADF name}]
    ```
    
    La commande doit s’exécuter correctement et octroyer à l’identité managée pour votre ADF la capacité de créer une base de données.

6.  Si votre base de données SSISDB a été créée à l’aide d’une authentification SQL et que vous souhaitez basculer vers une authentification Azure AD pour votre Azure-SSIS IR pour y accéder, cliquez avec le bouton droit sur la base de données **SSISDB**, puis sélectionnez**Nouvelle requête**.

7.  Dans la fenêtre de requête, entrez la commande T-SQL suivante, puis sélectionnez **Exécuter** dans la barre d’outils.

    ```sql
    CREATE USER [{your ADF name}] FOR LOGIN [{your ADF name}] WITH DEFAULT_SCHEMA = dbo
    ALTER ROLE db_owner ADD MEMBER [{your ADF name}]
    ```

    La commande doit s’exécuter correctement et octroyer à l’identité managée pour votre ADF la capacité d’accéder à la base de données SSISDB.

## <a name="provision-azure-ssis-ir-in-azure-portaladf-app"></a>Provisionner Azure-SSIS IR dans le portail Azure/l’application ADF

Quand vous provisionnez votre Azure-SSIS IR dans le portail Azure/l’application ADF, dans la page **Paramètres SQL**, sélectionnez l’option **Utiliser l’authentification AAD avec l’identité managée pour votre ADF**. La capture d’écran suivante montre les paramètres pour IR avec le serveur Azure SQL Database hébergeant SSISDB. Pour IR avec Managed Instance hébergeant SSISDB, les paramètres **Niveau de service de base de données de catalogue** et **Autoriser les services Azure à accéder au serveur** ne sont pas applicables, tandis que les autres paramètres sont identiques.

Pour plus d’informations sur la création d’un Azure-SSIS IR, consultez [Créer un runtime d’intégration Azure-SSIS dans Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

![Paramètres pour le runtime d’intégration Azure-SSIS](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-azure-ssis-ir-with-powershell"></a>Provisionner Azure-SSIS IR avec PowerShell

Pour provisionner votre runtime d’intégration Azure-SSIS avec PowerShell, procédez comme suit :

1.  Installez le module [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) .

2.  Dans votre script, ne définissez pas le paramètre `CatalogAdminCredential`. Par exemple :

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -Description $AzureSSISDescription `
                                               -Type Managed `
                                               -Location $AzureSSISLocation `
                                               -NodeSize $AzureSSISNodeSize `
                                               -NodeCount $AzureSSISNodeNumber `
                                               -Edition $AzureSSISEdition `
                                               -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                 -DataFactoryName $DataFactoryName `
                                                 -Name $AzureSSISName
    ```

## <a name="run-ssis-packages-with-managed-identity-authentication"></a>Exécuter des packages SSIS avec l’authentification d’identité managée

Lorsque vous exécutez des packages SSIS sur Azure-SSIS Integration Runtime (IR), vous pouvez utiliser l’authentification d’identité managée pour vous connecter à différentes ressources Azure. Actuellement, nous prenons déjà en charge l’authentification d’identité managée dans les gestionnaires de connexions suivants.

- [Gestionnaire de connexions OLE DB](https://docs.microsoft.com/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication)

- [Gestionnaire de connexions ADO.NET](https://docs.microsoft.com/sql/integration-services/connection-manager/ado-net-connection-manager#managed-identities-for-azure-resources-authentication)

- [Gestionnaire de connexions Stockage Azure](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
