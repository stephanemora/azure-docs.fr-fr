---
title: Activer l’authentification Azure Active Directory pour le runtime d’intégration Azure-SSIS | Microsoft Docs
description: Cet article décrit comment configurer le runtime d’intégration Azure-SSIS pour activer les connexions qui utilisent l’authentification Azure Active Directory.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 06/21/2018
ms.author: douglasl
ms.openlocfilehash: 3c829819748309ecbca248afe35cd59f54b202a6
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50085408"
---
# <a name="enable-azure-active-directory-authentication-for-the-azure-ssis-integration-runtime"></a>Activer l’authentification Azure Active Directory pour le runtime d’intégration Azure-SSIS

Cet article vous montre comment créer un runtime d’intégration Azure-SSIS avec l’identité de service Azure Data Factory. Vous pouvez utiliser l’authentification Azure Active Directory (Azure AD) avec l’identité managée de votre instance Azure Data Factory au lieu de l’authentification SQL pour créer une instance Azure-SSIS Integration Runtime.

Pour plus d’informations sur l’identité managée de votre instance Azure Data Factory, consultez [Identité de service Azure Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity).

> [!NOTE]
> Si vous avez déjà créé un runtime d’intégration Azure-SSIS avec l’authentification SQL, vous ne pouvez pas pour l’instant reconfigurer le runtime d’intégration pour utiliser l’authentification Azure AD avec PowerShell.

## <a name="create-a-group-in-azure-ad-and-make-the-managed-identity-for-your-adf-a-member-of-the-group"></a>Créer un groupe dans Azure AD et faire de l’identité managée de votre service Azure Data Factory un membre du groupe

Vous pouvez utiliser un groupe Azure AD existant ou en créer un à l’aide d’Azure AD PowerShell.

1.  Installez le module [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2).

2.  Connectez-vous avec  `Connect-AzureAD`, puis exécutez la commande suivante pour créer le groupe et l’enregistrer dans une variable :

    ```powershell
    $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                              -MailEnabled $false `
                              -SecurityEnabled $true `
                              -MailNickName "NotSet"
    ```

    La sortie se présente comme dans l’exemple suivant, qui examine également la valeur de la variable :

    ```powershell
    $Group

    ObjectId DisplayName Description
    -------- ----------- -----------
    6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
    ```

3.  Ajoutez au groupe l’identité managée pour votre service Azure Data Factory. Vous pouvez suivre [Identité de service Azure Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) pour obtenir l’ID d’IDENTITÉ DE SERVICE principal (par exemple 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc, mais n’utilisez pas l’ID d’APPLICATION d’IDENTITÉ DE SERVICE à cette fin).

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    Vous pouvez aussi examiner l’appartenance au groupe par la suite.

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

## <a name="enable-azure-ad-on-azure-sql-database"></a>Activer Azure AD sur Azure SQL Database

Azure SQL Database prend en charge la création d’une base de données avec un utilisateur Azure AD. Ainsi, vous pouvez définir un utilisateur Azure AD comme l’administrateur Active Directory, puis vous connecter à SSMS avec l’utilisateur Azure AD. Vous pouvez ensuite créer un utilisateur contenu pour que le groupe Azure AD permette à votre runtime d’intégration de créer le catalogue SQL Server Integration Services (SSIS) sur le serveur.

### <a name="enable-azure-ad-authentication-for-the-azure-sql-database"></a>Activer l’authentification Azure AD pour Azure SQL Database

Vous pouvez  [configurer l’authentification Azure AD pour l’instance SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure) en suivant ces étapes :

1.  Sur le portail Azure, dans le volet de navigation gauche, sélectionnez  **Tous les services** -> **Serveurs SQL** .

2.  Cliquez sur la base de données SQL pour laquelle activer l’authentification Azure AD.

3.  Dans la section  **Paramètres**  du panneau, sélectionnez  **Administrateur Active Directory**.

4.  Dans la barre de commandes, sélectionnez  **Définir l’administrateur**.

5.  Sélectionnez un compte d’utilisateur Azure AD à désigner comme administrateur du serveur, puis choisissez  **Sélectionner**.

6.  Dans la barre de commandes, sélectionnez  **Enregistrer**.

### <a name="create-a-contained-user-in-the-database-that-represents-the-azure-ad-group"></a>Créer un utilisateur contenu dans la base de données représentant le groupe Azure AD

Pour cette nouvelle étape, vous avez besoin de  [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)  (SSMS).

1.  Exécutez SQL Server Management Studio.

2.  Dans la boîte de dialogue  **Se connecter**  au serveur, dans le champ  **Nom du serveur** , entrez le nom de votre serveur SQL.

3.  Dans le champ  **Authentification** , sélectionnez  **Active Directory - Authentification universelle avec prise en charge de MFA**. (Vous pouvez également utiliser deux autres types d’authentification Active Directory. Consultez [Configurer et gérer l’authentification Azure Active Directory avec SQL Database, Managed Instance ou SQL Data Warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure).)

4.  Dans le champ  **Nom d’utilisateur** , entrez le nom du compte Azure AD que vous avez défini en tant qu’administrateur du serveur, par exemple testuser@xxxonline.com.

5.  Sélectionnez  **Se connecter**. Terminez le processus de connexion.

6.  Dans l’ **Explorateur d’objets**, développez le dossier  **Bases de données** -> Bases de données système.

7.  Cliquez avec le bouton droit sur la base de données **MASTER** et sélectionnez  **Nouvelle requête**.

8.  Dans la fenêtre de requête, entrez la ligne suivante, puis sélectionnez  **Exécuter**  dans la barre d’outils :

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    La commande doit s’exécuter correctement, en créant l’utilisateur contenu pour le groupe.

9.  Effacez la fenêtre de requête, entrez la ligne suivante, puis sélectionnez **Exécuter** dans la barre d’outils :

    ```sql
    ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
    ```

    La commande doit s’exécuter correctement, en octroyant à l’utilisateur contenu la possibilité de créer une base de données.

## <a name="enable-azure-ad-on-azure-sql-database-managed-instance"></a>Activer Azure AD sur Azure SQL Database Managed Instance

Azure SQL Database Managed Instance ne prend pas en charge la création d’une base de données avec un autre utilisateur que l’administrateur Active Directory. Vous devez donc définir le groupe Azure AD comme administrateur Active Directory. Vous n’avez pas besoin de créer l’utilisateur contenu.

Vous pouvez  [configurer l’authentification Azure AD pour le serveur SQL Database Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure) en suivant ces étapes :

7.  Sur le portail Azure, dans le volet de navigation gauche, sélectionnez  **Tous les services** -> **Serveurs SQL** .

8.  Cliquez sur le serveur SQL pour lequel activer l’authentification Azure AD.

9.  Dans la section  **Paramètres**  du panneau, sélectionnez  **Administrateur Active Directory**.

10. Dans la barre de commandes, sélectionnez  **Définir l’administrateur**.

11. Recherchez et sélectionnez le groupe Azure AD (par exemple, SSISIrGroup), puis choisissez  **Sélectionner**.

12. Dans la barre de commandes, sélectionnez  **Enregistrer**.

## <a name="provision-the-azure-ssis-ir-in-the-portal"></a>Provisionner le runtime d’intégration Azure-SSIS dans le portail

Quand vous provisionnez votre instance Azure-SSIS IR avec le portail Azure, dans la page **Paramètres SQL**, cochez l’option « Use AAD authentication with the managed identity for your ADF » (Utiliser l’authentification AAD avec l’identité managée pour votre instance Azure Data Factory). (La capture d’écran suivante montre les paramètres pour le runtime d’intégration avec Azure SQL Database. Pour le runtime d’intégration avec Managed Instance, la propriété « Niveau de service de la base de données du catalogue » n’est pas disponible ; les autres paramètres sont identiques.)

Pour plus d’informations sur la création d’un runtime d’intégration Azure-SSIS, consultez [Créer un runtime d’intégration Azure-SSIS dans Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

![Paramètres pour le runtime d’intégration Azure-SSIS](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-the-azure-ssis-ir-with-powershell"></a>Provisionner le runtime d’intégration Azure-SSIS avec PowerShell

Pour provisionner votre runtime d’intégration Azure-SSIS avec PowerShell, procédez comme suit :

1.  Installez le module [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) .

2.  Dans votre script, ne définissez pas le paramètre *CatalogAdminCredential*. Par exemple : 

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
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

    Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                 -DataFactoryName $DataFactoryName `
                                                 -Name $AzureSSISName
   ```
