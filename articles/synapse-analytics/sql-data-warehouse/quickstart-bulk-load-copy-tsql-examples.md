---
title: Mécanismes d’authentification avec l’instruction COPY
description: Décrit les mécanismes d’authentification pour charger des données en bloc
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 07/10/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.custom: subject-rbac-steps
ms.openlocfilehash: 3873ae1dd4ab230e5e0c3424341722e76aeb48fb
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113216222"
---
# <a name="securely-load-data-using-synapse-sql"></a>Charger des données de façon sécurisée à l’aide de SQL Synapse

Cet article met en évidence et fournit des exemples sur les mécanismes d’authentification sécurisée pour l’instruction [COPY](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true). L’instruction COPY est la manière la plus flexible et sécurisée de charger des données en bloc dans SQL Synapse.
## <a name="supported-authentication-mechanisms"></a>Mécanismes d’authentification pris en charge

La matrice suivante décrit les méthodes d’authentification prises en charge pour chaque type de fichier et compte de stockage. Elle s’applique à l’emplacement de stockage source et à l’emplacement du fichier d’erreur.

|                          |                CSV                |                      Parquet                       |                        ORC                         |
| :----------------------: | :-------------------------------: | :------------------------------------------------: | :------------------------------------------------: |
|  **Stockage Blob Azure**  | SAS/MSI/SERVICE PRINCIPAL/KEY/AAD |                      SAS/KEY                       |                      SAS/KEY                       |
| **Azure Data Lake Gen2** | SAS/MSI/SERVICE PRINCIPAL/KEY/AAD | SAS (blob<sup>1</sup>)/MSI (dfs<sup>2</sup>)/SERVICE PRINCIPAL/KEY/AAD | SAS (blob<sup>1</sup>)/MSI (dfs<sup>2</sup>)/SERVICE PRINCIPAL/KEY/AAD |

1 : Le point de terminaison .blob ( **.blob**.core.windows.net) dans le chemin de votre emplacement externe est nécessaire pour cette méthode d’authentification.

2 : Le point de terminaison .dfs ( **.dfs**.core.windows.net) dans le chemin de votre emplacement externe est nécessaire pour cette méthode d’authentification.

## <a name="a-storage-account-key-with-lf-as-the-row-terminator-unix-style-new-line"></a>R. Clé de compte de stockage avec LF comme indicateur de fin de ligne (nouvelle ligne de style UNIX)


```sql
--Note when specifying the column list, input field numbers start from 1
COPY INTO target_table (Col_one default 'myStringDefault' 1, Col_two default 1 3)
FROM 'https://adlsgen2account.dfs.core.windows.net/myblobcontainer/folder1/'
WITH (
    FILE_TYPE = 'CSV'
    ,CREDENTIAL=(IDENTITY= 'Storage Account Key', SECRET='<Your_Account_Key>')
    --CREDENTIAL should look something like this:
    --CREDENTIAL=(IDENTITY= 'Storage Account Key', SECRET='x6RWv4It5F2msnjelv3H4DA80n0QW0daPdw43jM0nyetx4c6CpDkdj3986DX5AHFMIf/YN4y6kkCnU8lb+Wx0Pj+6MDw=='),
    ,ROWTERMINATOR='0x0A' --0x0A specifies to use the Line Feed character (Unix based systems)
)
```
> [!IMPORTANT]
>
> - Utilisez la valeur hexadécimale (0x0A) pour spécifier le caractère de saut de ligne/nouvelle ligne. Remarque : l’instruction COPY interprète la chaîne « \n » comme « \r\n » (retour chariot avec nouvelle ligne).

## <a name="b-shared-access-signatures-sas-with-crlf-as-the-row-terminator-windows-style-new-line"></a>B. Signatures d’accès partagé (SAP) avec CRLF comme indicateur de fin de ligne (nouvelle ligne de style Windows)
```sql
COPY INTO target_table
FROM 'https://adlsgen2account.dfs.core.windows.net/myblobcontainer/folder1/'
WITH (
    FILE_TYPE = 'CSV'
    ,CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='<Your_SAS_Token>')
    --CREDENTIAL should look something like this:
    --CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='?sv=2018-03-28&ss=bfqt&srt=sco&sp=rl&st=2016-10-17T20%3A14%3A55Z&se=2021-10-18T20%3A19%3A00Z&sig=IEoOdmeYnE9%2FKiJDSFSYsz4AkNa%2F%2BTx61FuQ%2FfKHefqoBE%3D'),
    ,ROWTERMINATOR='\n'-- COPY command automatically prefixes the \r character when \n (newline) is specified. This results in carriage return newline (\r\n) for Windows based systems.
)
```

> [!IMPORTANT]
>
> - Ne spécifiez pas l’indicateur ROWTERMINATOR comme « \r\n », qui sera interprété comme « \r\r\n » et peut entraîner des problèmes d’analyse

## <a name="c-managed-identity"></a>C. Identité managée

L’authentification de l’identité managée est requise lorsque votre compte de stockage est joint à un réseau virtuel. 

### <a name="prerequisites"></a>Prérequis

1. Installez Azure PowerShell en vous aidant de ce [guide](/powershell/azure/install-az-ps?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Si vous disposez d’un compte de stockage d’objets blob ou v1 universel, vous devez commencer par le mettre à niveau avec un compte v2 universel en vous aidant de ce [guide](../../storage/common/storage-account-upgrade.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
3. Vous devez avoir activé **Autoriser les services Microsoft approuvés à accéder à ce compte de stockage** sous le menu de paramètres **Pare-feux et réseaux virtuels** du compte Stockage Azure. Pour plus d’informations, consultez ce [guide](../../storage/common/storage-network-security.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#exceptions).

#### <a name="steps"></a>Étapes

1. Si vous disposez d’un pool SQL dédié autonome, inscrivez votre serveur SQL Server auprès d’Azure Active Directory (AAD) à l’aide de PowerShell : 

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

   Cette étape n’est pas nécessaire pour les pools SQL dédiés qui se trouvent dans un espace de travail Synapse.

1. Si vous disposez d’un espace de travail Synapse, inscrivez l’identité gérée par le système de votre espace de travail :

   1. Accédez à votre espace de travail Synapse sur le portail Azure
   2. Accédez au panneau Identités managées 
   3. Vérifiez que l’option « Autoriser les pipelines » est activée
   
   ![Inscrire le fichier msi du système de l’espace de travail](./media/quickstart-bulk-load-copy-tsql-examples/msi-register-example.png)

1. Créez un **compte de stockage v2 universel** en vous aidant de ce [guide](../../storage/common/storage-account-create.md).

   > [!NOTE]
   >
   > - Si vous disposez d’un compte de stockage d’objets blob ou v1 universel, vous devez **d’abord le mettre à niveau avec v2** en vous aidant de ce [guide](../../storage/common/storage-account-upgrade.md).
   > - Pour examiner les problèmes connus liés à Azure Data Lake Storage Gen2, consultez ce [guide](../../storage/blobs/data-lake-storage-known-issues.md).

1. Sous votre compte de stockage, sélectionnez **Contrôle d’accès (IAM)** .

1. Sélectionnez **Ajouter** > **Ajouter une attribution de rôle** pour ouvrir la page Ajouter une attribution de rôle.

1. Attribuez le rôle suivant. Pour connaître les étapes détaillées, consultez [Attribuer des rôles Azure à l’aide du portail Azure](../../role-based-access-control/role-assignments-portal.md).
    
    | Paramètre | Valeur |
    | --- | --- |
    | Role | Contributeur aux données Blob du stockage |
    | Attribuer l’accès à | SERVICEPRINCIPAL |
    | Membres | Serveur ou espace de travail hébergeant votre pool SQL dédié que vous avez inscrit auprès d’Azure Active Directory (AAD)  |

    ![Page Ajouter une attribution de rôle dans le portail Azure.](../../../includes/role-based-access-control/media/add-role-assignment-page.png)


   > [!NOTE]
   > Seuls les membres dotés du privilège Propriétaire peuvent effectuer cette étape. Pour découvrir les divers rôles intégrés Azure, consultez ce [guide](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
   
    > [!IMPORTANT]
    > Spécifiez le rôle Azure Propriétaire, Contributeur ou Lecteur des **données Blob** de **stockage**. Ces rôles sont différents des rôles Azure intégrés Propriétaire, Collaborateur et Lecteur. 

    ![Octroi d’une autorisation Azure RBAC pour le chargement](./media/quickstart-bulk-load-copy-tsql-examples/rbac-load-permissions.png)

4. Vous pouvez maintenant exécuter l’instruction COPY en spécifiant « Identité managée » :

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder1/*.txt'
    WITH (
        FILE_TYPE = 'CSV',
        CREDENTIAL = (IDENTITY = 'Managed Identity'),
    )
    ```

## <a name="d-azure-active-directory-authentication"></a>D. Authentification Azure Active Directory
#### <a name="steps"></a>Étapes

1. Sous votre compte de stockage, sélectionnez **Contrôle d’accès (IAM)** .

1. Sélectionnez **Ajouter** > **Ajouter une attribution de rôle** pour ouvrir la page Ajouter une attribution de rôle.

1. Attribuez le rôle suivant. Pour connaître les étapes détaillées, consultez [Attribuer des rôles Azure à l’aide du portail Azure](../../role-based-access-control/role-assignments-portal.md).
    
    | Paramètre | Valeur |
    | --- | --- |
    | Role | Propriétaire, contributeur ou lecteur des données blob du stockage |
    | Attribuer l’accès à | Utilisateur |
    | Membres | Utilisateur Azure AD |

    ![Page Ajouter une attribution de rôle dans le portail Azure.](../../../includes/role-based-access-control/media/add-role-assignment-page.png)

    > [!IMPORTANT]
    > Spécifiez le rôle Azure Propriétaire, Contributeur ou Lecteur des **données Blob** de **stockage**. Ces rôles sont différents des rôles Azure intégrés Propriétaire, Collaborateur et Lecteur.

    ![Octroi d’une autorisation Azure RBAC pour le chargement](./media/quickstart-bulk-load-copy-tsql-examples/rbac-load-permissions.png)

1. Configurez l’authentification Azure AD en parcourant la [documentation](../../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell) suivante. 

1. Connectez-vous à votre pool SQL à l’aide d’Active Directory. Vous pouvez maintenant exécuter l’instruction COPY sans spécifier d’informations d’identification :

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder1/*.txt'
    WITH (
        FILE_TYPE = 'CSV'
    )
    ```


## <a name="e-service-principal-authentication"></a>E. Authentification d’un principal de service
#### <a name="steps"></a>Étapes

1. [Créez une application Azure Active Directory](../..//active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal).
2. [Obtenir l’ID de l’application](../..//active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)
3. [Obtenir la clé d'authentification](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options)
4. [Obtenir le point de terminaison de jeton OAuth 2.0 V1](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json#step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications)
5. [Affecter des autorisations de lecture, d’écriture et d’exécution à votre application Azure AD](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json#step-3-assign-the-azure-ad-application-to-the-azure-data-lake-storage-gen1-account-file-or-folder) sur votre compte de stockage
6. Vous pouvez maintenant exécuter l’instruction COPY :

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder0/*.txt'
    WITH (
        FILE_TYPE = 'CSV'
        ,CREDENTIAL=(IDENTITY= '<application_ID>@<OAuth_2.0_Token_EndPoint>' , SECRET= '<authentication_key>')
        --CREDENTIAL should look something like this:
        --,CREDENTIAL=(IDENTITY= '92761aac-12a9-4ec3-89b8-7149aef4c35b@https://login.microsoftonline.com/72f714bf-86f1-41af-91ab-2d7cd011db47/oauth2/token', SECRET='juXi12sZ6gse]woKQNgqwSywYv]7A.M')
    )
    ```

> [!IMPORTANT]
>
> - Utilisez la version **V1** du point de terminaison de jeton OAuth 2.0

## <a name="next-steps"></a>Étapes suivantes

- Consulter l’article sur [l’instruction COPY](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax) pour connaître la syntaxe détaillée
- Consulter l’article sur la [vue d’ensemble du chargement des données](./design-elt-data-loading.md#what-is-elt) pour connaître les bonnes pratiques de chargement
