---
title: Activer l’authentification Azure Active Directory pour Azure-SSIS Integration Runtime | Microsoft Docs
description: Cet article décrit comment activer l’authentification Azure Active Directory avec l’identité managée pour Azure Data Factory afin de créer un Azure-SSIS Integration Runtime.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 3/11/2019
author: swinarko
ms.author: sawinark
manager: craigg
ms.openlocfilehash: 58bdc0e698fc28929c2080b1737770275b1164ad
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57848726"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>Activer l’authentification Azure Active Directory pour Azure-SSIS Integration Runtime

Cet article vous montre comment activer l’authentification Azure Active Directory (Azure AD) avec l’identité gérée pour votre Azure Data Factory (ADF) et l’utiliser au lieu de l’authentification SQL pour créer un Runtime d’intégration Azure-SSIS (IR) qui met en service à son tour Catalogue base de données SSIS (SSISDB) dans la base de données Azure SQL server/Managed Instance à votre place.

Pour plus d’informations sur l’identité gérée pour votre ADF, consultez [identiy géré de fabrique de données](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity).

> [!NOTE]
>-  Dans ce scénario, l’authentification Azure AD avec l’identité gérée pour votre ADF est uniquement utilisée dans la création et les opérations suivantes à partir de votre runtime d’intégration SSIS qui seront dans activer l’approvisionnement et se connecter à SSISDB. Pour les exécutions de package SSIS, votre runtime d’intégration SSIS sera toujours se connecter à SSISDB à l’aide de l’authentification SQL avec des comptes entièrement gérés qui sont créés lors de l’approvisionnement de SSISDB.
>-  Si vous avez déjà créé votre runtime d’intégration SSIS à l’aide de l’authentification SQL, vous ne pouvez pas reconfigurer pour qu’il utilise l’authentification Azure AD via PowerShell pour l’instant, mais vous pouvez le faire via l’application/ADF du portail Azure. 

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

3.  Ajoutez au groupe l’identité managée pour votre service Azure Data Factory. Vous pouvez suivre l’article [identiy géré de fabrique de données](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) pour obtenir l’ID d’identité de SERVICE principal (par exemple, 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc, mais n’utilisez pas d’ID d’APPLICATION DE SERVICE identité à cet effet).

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

5.  Sélectionnez un compte d’utilisateur Azure AD pour devenir administrateur du serveur, puis sélectionnez **sélectionner.**

6.  Dans la barre de commandes, sélectionnez **Enregistrer**.

### <a name="create-a-contained-user-in-azure-sql-database-server-representing-the-azure-ad-group"></a>Créer un utilisateur de base de données autonome sur le serveur Azure SQL Database représentant le groupe Azure AD

Pour cette nouvelle étape, vous avez besoin de  [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)  (SSMS).

1. Démarrez SSMS.

2. Dans le **se connecter au serveur** boîte de dialogue, entrez le nom de votre serveur de base de données SQL Azure dans le **nom du serveur** champ.

3. Dans le **authentification** champ, sélectionnez **Active Directory - authentification universelle avec prise en charge MFA** (vous pouvez également utiliser les autres deux types d’authentification Active Directory, consultez [configurer et gérer L’authentification Azure AD avec SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)).

4. Dans le **nom d’utilisateur** , entrez le nom du compte Azure AD que vous définissez en tant que l’administrateur du serveur, par exemple, testuser@xxxonline.com.

5. Sélectionnez **Connect** et terminer le processus de connexion.

6. Dans l’**Explorateur d’objets**, développez le dossier **Bases de données** -> **Bases de données système**.

7. Cliquez avec le bouton droit sur la base de données **master** et sélectionnez **Nouvelle requête**.

8. Dans la fenêtre de requête, entrez la commande T-SQL suivante, puis sélectionnez **Execute** sur la barre d’outils.

   ```sql
   CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
   ```

   La commande doit s’exécuter correctement, en créant l’utilisateur de base de données autonome pour représenter le groupe.

9. Effacez la fenêtre de requête, entrez la commande T-SQL suivante, puis sélectionnez **Exécuter** dans la barre d’outils.

   ```sql
   ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
   ```

   La commande doit s’exécuter correctement, et octroyer à l’utilisateur contenu la capacité des créer une base de données (SSISDB).

10. Si votre SSISDB a été créé à l’aide de l’authentification SQL et que vous souhaitez basculer pour utiliser l’authentification Azure AD pour votre IR Azure-SSIS à y accéder, avec le bouton droit sur **SSISDB** de base de données et sélectionnez **nouvelle requête**.

11. Dans la fenêtre de requête, entrez la commande T-SQL suivante, puis sélectionnez **Execute** sur la barre d’outils.

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

1.   Dans le portail Azure, dans le volet de navigation gauche, sélectionnez **Tous les services** -> **Serveurs SQL**.

2.   Sélectionnez votre Managed Instance à configurer avec l’authentification Azure AD.

3.   Dans la section **Paramètres** du panneau, sélectionnez **Administrateur Active Directory**.

4.   Dans la barre de commandes, sélectionnez **Définir l’administrateur**.

5.   Sélectionnez un compte d’utilisateur Azure AD à désigner comme administrateur du serveur, puis choisissez **Sélectionner**.

6.   Dans la barre de commandes, sélectionnez **Enregistrer**.

### <a name="add-the-managed-identity-for-your-adf-as-a-user-in-azure-sql-database-managed-instance"></a>Ajouter l’identité managée pour votre ADF comme utilisateur dans Azure SQL Database Managed Instance

Pour cette nouvelle étape, vous avez besoin de  [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)  (SSMS).

1.  Démarrez SSMS.

2.  Connectez-vous à votre Managed Instance à l’aide de votre compte d’administrateur SQL/Active Directory.

3.  Dans l’**Explorateur d’objets**, développez le dossier **Bases de données** -> **Bases de données système**.

4.  Cliquez avec le bouton droit sur la base de données **master** et sélectionnez **Nouvelle requête**.

5.  Obtenez l’identité managée pour votre ADF. Vous pouvez suivre l’article [identiy géré de fabrique de données](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) pour obtenir l’ID d’APPLICATION du principal du SERVICE IDENTITY (mais n’utilisez pas d’ID d’identité de SERVICE à cet effet).

6.  Dans la fenêtre de requête, exécutez le script T-SQL suivant pour convertir l’identité managée pour votre ADF en type binaire :

    ```sql
    DECLARE @applicationId uniqueidentifier = '{your SERVICE IDENTITY APPLICATION ID}'
    select CAST(@applicationId AS varbinary)
    ```
    
    La commande doit s’exécuter avec succès et afficher l’identité managée pour votre ADF sous forme binaire.

7.  Effacez la fenêtre de requête et exécutez le script T-SQL suivant pour ajouter l’identité managée pour votre ADF comme utilisateur

    ```sql
    CREATE LOGIN [{a name for the managed identity}] FROM EXTERNAL PROVIDER with SID = {your SERVICE IDENTITY APPLICATION ID as binary}, TYPE = E
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{the managed identity name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{the managed identity name}]
    ```
    
    La commande doit s’exécuter correctement et octroyer à l’identité managée pour votre ADF la capacité de créer une base de données.

8.  Si votre SSISDB a été créé à l’aide de l’authentification SQL et que vous souhaitez basculer pour utiliser l’authentification Azure AD pour votre IR Azure-SSIS à y accéder, avec le bouton droit sur **SSISDB** de base de données et sélectionnez **nouvelle requête**.

9.  Dans la fenêtre de requête, entrez la commande T-SQL suivante, puis sélectionnez **Execute** sur la barre d’outils.

    ```sql
    CREATE USER [{the managed identity name}] FOR LOGIN [{the managed identity name}] WITH DEFAULT_SCHEMA = dbo
    ALTER ROLE db_owner ADD MEMBER [{the managed identity name}]
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
