---
title: 'Tutoriel : Bien démarrer avec Always Encrypted avec enclaves sécurisées dans Azure SQL Database'
description: Ce tutoriel vous montre comment créer un environnement simple pour Always Encrypted avec enclaves sécurisées de base dans Azure SQL Database, comment chiffrer les données sur place et comment émettre des requêtes confidentielles riches sur des colonnes chiffrées à l’aide de SQL Server Management Studio (SSMS).
keywords: chiffrer les données, chiffrement sql, chiffrement de base de données, données sensibles, Always Encrypted, enclaves sécurisées, SGX, attestation
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: tutorial
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: 809ac72977b670faff984ad39effb1c70767e141
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102120944"
---
# <a name="tutorial-getting-started-with-always-encrypted-with-secure-enclaves-in-azure-sql-database"></a>Tutoriel : Bien démarrer avec Always Encrypted avec enclaves sécurisées dans Azure SQL Database

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Always Encrypted avec des enclaves sécurisées pour Azure SQL Database est actuellement en **préversion publique**.

Ce tutoriel explique comment démarrer avec [Always Encrypted avec enclaves sécurisées](/sql/relational-databases/security/encryption/always-encrypted-enclaves) dans Azure SQL Database. Il vous montre comment :

> [!div class="checklist"]
> - Créer un environnement pour tester et évaluer Always Encrypted avec enclaves sécurisées.
> - Chiffrer des données sur place et émettre des requêtes confidentielles complexes sur des colonnes chiffrées en utilisant SQL Server Management Studio (SSMS).

## <a name="prerequisites"></a>Prérequis

Ce tutoriel nécessite Azure PowerShell et [SSMS](/sql/ssms/download-sql-server-management-studio-ssms).

### <a name="powershell-requirements"></a>Configuration requise pour PowerShell

Pour plus d’informations sur l’installation et l’exécution d’Azure PowerShell, consultez [Vue d’ensemble d’Azure PowerShell](/powershell/azure). 

Version minimale des modules Az nécessaires pour prendre en charge les opérations d’attestation :

- Az 4.5.0
- Az.Accounts 1.9.2
- Az.Attestation 0.1.8

Exécutez la commande ci-dessous pour vérifier la version installée de tous les modules Az :

```powershell
Get-InstalledModule
```

Si les versions ne correspondent pas à la configuration minimale requise, exécutez la commande `Update-Module`.

PowerShell Gallery a déprécié les versions 1.0 et 1.1 du protocole TLS (Transport Layer Security). TLS 1.2 ou une version ultérieure est recommandé. Si vous utilisez une version TLS antérieure à 1.2, vous pouvez recevoir les erreurs suivantes :

- `WARNING: Unable to resolve package source 'https://www.powershellgallery.com/api/v2'`
- `PackageManagement\Install-Package: No match was found for the specified search criteria and module name.`

Pour continuer à interagir avec PowerShell Gallery, exécutez la commande suivante avant les commandes Install-Module

```powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
```

### <a name="ssms-requirements"></a>Configuration requise pour SSMS

Consultez [Télécharger SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) pour obtenir des informations sur la façon de télécharger SSMS.

La version minimale requise de SSMS est 18.8.


## <a name="step-1-create-and-configure-a-server-and-a-dc-series-database"></a>Étape 1 : Créer et configurer un serveur et une base de données de série DC

Au cours de cette étape, vous allez créer un nouveau serveur logique Azure SQL Database et une nouvelle base de données à l’aide de la génération matérielle de série DC, requise pour Always Encrypted avec enclaves sécurisées. Pour plus d’informations, consultez [Série DC](service-tiers-vcore.md#dc-series).

1. Ouvrez une console PowerShell et importez la version requise d’Az.

  ```PowerShell
  Import-Module "Az" -MinimumVersion "4.5.0"
  ```
  
2. Connectez-vous à Azure. Si nécessaire, [basculez vers l’abonnement](/powershell/azure/manage-subscriptions-azureps) que vous utilisez pour ce tutoriel.

  ```PowerShell
  Connect-AzAccount
  $subscriptionId = "<your subscription ID>"
  Set-AzContext -Subscription $subscriptionId
  ```

3. Créer un groupe de ressources 

  > [!IMPORTANT]
  > Vous devez créer votre groupe de ressources dans une région (emplacement) qui prend en charge à la fois la génération matérielle de série DC et Microsoft Azure Attestation. Pour obtenir la liste des régions prenant en charge la série DC, consultez [Disponibilité de la série DC](service-tiers-vcore.md#dc-series-1). [Voici](https://azure.microsoft.com/global-infrastructure/services/?products=azure-attestation) la disponibilité régionale de Microsoft Azure Attestation.

  ```powershell
  $resourceGroupName = "<your new resource group name>"
  $location = "<Azure region supporting DC-series and Microsoft Azure Attestation>"
  New-AzResourceGroup -Name $resourceGroupName -Location $location
  ```

4. Créez un serveur logique Azure SQL. Lorsque vous y êtes invité, entrez le nom de l’administrateur du serveur et un mot de passe. Veillez à mémoriser le nom d’administrateur et le mot de passe : vous en aurez besoin ultérieurement pour vous connecter au serveur.

  ```powershell
  $serverName = "<your server name>" 
  New-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName -Location $location 
  ```

5. Créez une règle de pare-feu de serveur qui autorise l’accès à partir de la plage d’adresses IP spécifiée.
  
  ```powershell
  $startIp = "<start of IP range>"
  $endIp = "<end of IP range>"
  $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
    -ServerName $serverName `
    -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
  ```

6. Affectez une identité de système managé à votre serveur. 

  ```PowerShell
  $server = Set-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName -AssignIdentity
  $serverObjectId = $server.Identity.PrincipalId
  ```

7. Créez une base de données de série DC.

  ```powershell
  $databaseName = "ContosoHR"
  $edition = "GeneralPurpose"
  $vCore = 2
  $generation = "DC"
  New-AzSqlDatabase -ResourceGroupName $resourceGroupName `
    -ServerName $serverName `
    -DatabaseName $databaseName `
    -Edition $edition `
    -Vcore $vCore `
    -ComputeGeneration $generation
  ```

8. Récupérez et enregistrez les informations relatives à votre serveur et à la base de données. Vous aurez besoin de ces informations, ainsi que du nom d’administrateur et du mot de passe de l’étape 4 de cette section, dans les sections suivantes.

  ```powershell
  Write-Host 
  Write-Host "Fully qualified server name: $($server.FullyQualifiedDomainName)" 
  Write-Host "Server Object Id: $serverObjectId"
  Write-Host "Database name: $databaseName"
  ```
  
## <a name="step-2-configure-an-attestation-provider"></a>Étape 2 : Configurer un fournisseur d’attestation 

Dans cette étape, vous allez créer et configurer un fournisseur d’attestation dans Microsoft Azure Attestation. Ceci est nécessaire pour attester l’enclave sécurisée que votre base de données utilise.

1. Copiez la stratégie d’attestation ci-dessous et enregistrez-la dans un fichier texte (txt). Pour obtenir des informations sur la stratégie ci-dessous, consultez [Créer et configurer un fournisseur d’attestation](always-encrypted-enclaves-configure-attestation.md#create-and-configure-an-attestation-provider).

  ```output
  version= 1.0;
  authorizationrules 
  {
       [ type=="x-ms-sgx-is-debuggable", value==false ]
        && [ type=="x-ms-sgx-product-id", value==4639 ]
        && [ type=="x-ms-sgx-svn", value>= 0 ]
        && [ type=="x-ms-sgx-mrsigner", value=="e31c9e505f37a58de09335075fc8591254313eb20bb1a27e5443cc450b6e33e5"] 
    => permit();
  };
  ```

2. Importez la version requise de `Az.Attestation`.  

  ```powershell
  Import-Module "Az.Attestation" -MinimumVersion "0.1.8"
  ```
  
3. Créez un fournisseur d’attestation. 

  ```powershell
  $attestationProviderName = "<your attestation provider name>" 
  New-AzAttestation -Name $attestationProviderName -ResourceGroupName $resourceGroupName -Location $location
  ```

4. Configurez la stratégie d’attestation.
  
  ```powershell
  $policyFile = "<the pathname of the file from step 1 in this section>"
  $teeType = "SgxEnclave"
  $policyFormat = "Text"
  $policy=Get-Content -path $policyFile -Raw
  Set-AzAttestationPolicy -Name $attestationProviderName `
    -ResourceGroupName $resourceGroupName `
    -Tee $teeType `
    -Policy $policy `
    -PolicyFormat  $policyFormat
  ```

5. Accordez à votre serveur logique Azure SQL l’accès à votre fournisseur d’attestation. Dans cette étape, vous utilisez l’ID d’objet de l’identité de service géré que vous avez attribuée précédemment à votre serveur.

  ```powershell
  New-AzRoleAssignment -ObjectId $serverObjectId `
    -RoleDefinitionName "Attestation Reader" `
    -ResourceName $attestationProviderName `
    -ResourceType "Microsoft.Attestation/attestationProviders" `
    -ResourceGroupName $resourceGroupName  
  ```

6. Récupérez l’URL d’attestation qui pointe vers une stratégie d’attestation que vous avez configurée pour l’enclave SGX. Enregistrez l’URL, car vous en aurez besoin ultérieurement.

  ```powershell
  $attestationProvider = Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $resourceGroupName 
  $attestationUrl = $attestationProvider.AttestUri + “/attest/SgxEnclave”
  Write-Host
  Write-Host "Your attestation URL is: $attestationUrl"
  ```
  
  L’URL d’attestation doit ressembler à ceci : `https://contososqlattestation.uks.attest.azure.net/attest/SgxEnclave`

## <a name="step-3-populate-your-database"></a>Étape 3 : Remplir votre base de données

Dans cette étape, vous allez créer une table et la remplir avec des données que vous chiffrerez et interrogerez ultérieurement.

1. Ouvrez SSMS et connectez-vous à la base de données **ContosoHR** sur le serveur logique Azure SQL que vous avez créé **sans** Always Encrypted activé dans la connexion de base de données.
    1. Dans la boîte de dialogue **Se connecter au serveur**, spécifiez le nom complet de votre serveur (par exemple, *myserver123.database.windows.net*), puis entrez le nom d’utilisateur de l’administrateur et le mot de passe que vous avez spécifié lors de la création du serveur.
    2. Cliquez sur **Options >>** et sélectionnez l’onglet **Propriétés de connexion**. Veillez à sélectionner la base de données **ContosoHR** (pas la base de données master par défaut). 
    3. Sélectionnez l’onglet **Always Encrypted**.
    4. Assurez-vous que la case **Activer Always Encrypted (chiffrement de colonne)** n’est **pas** cochée.

        ![Se connecter sans Always Encrypted](media/always-encrypted-enclaves/connect-without-always-encrypted-ssms.png)

    5. Cliquez sur **Connecter**.

2. Créez une nouvelle table nommée **Employés**.

    ```sql
    CREATE SCHEMA [HR];
    GO

    CREATE TABLE [HR].[Employees]
    (
        [EmployeeID] [int] IDENTITY(1,1) NOT NULL,
        [SSN] [char](11) NOT NULL,
        [FirstName] [nvarchar](50) NOT NULL,
        [LastName] [nvarchar](50) NOT NULL,
        [Salary] [money] NOT NULL
    ) ON [PRIMARY];
    GO
    ```

3. Ajoutez quelques enregistrements d’employés à la table **Employés**.

    ```sql
    INSERT INTO [HR].[Employees]
            ([SSN]
            ,[FirstName]
            ,[LastName]
            ,[Salary])
        VALUES
            ('795-73-9838'
            , N'Catherine'
            , N'Abel'
            , $31692);

    INSERT INTO [HR].[Employees]
            ([SSN]
            ,[FirstName]
            ,[LastName]
            ,[Salary])
        VALUES
            ('990-00-6818'
            , N'Kim'
            , N'Abercrombie'
            , $55415);
    ```


## <a name="step-4-provision-enclave-enabled-keys"></a>Étape 4 : Approvisionner des clés prenant en charge les enclaves

Dans cette étape, vous allez créer une clé principale de colonne et une clé de chiffrement de colonne qui permettent les calculs d’enclave.

1. À l’aide de l’instance SSMS dans l’étape précédente, dans **Explorateur d’objets**, développez votre base de données et accédez à **Sécurité** > **Clés Always Encrypted**.
1. Provisionnez une nouvelle clé principale de colonne prenant en charge les enclaves :
    1. Cliquez avec le bouton droit sur **Clés Always Encrypted** et sélectionnez **Nouvelle clé principale de colonne...** .
    2. Sélectionnez le nom de votre clé principale de colonne : **CMK1**.
    3. Veillez à sélectionnez **Magasin de certificats Windows (utilisateur actuel ou ordinateur local)** ou **Azure Key Vault**.
    4. Sélectionnez **Autoriser les calculs d’enclave**.
    5. Si vous avez sélectionné Azure Key Vault, connectez-vous à Azure et sélectionnez votre coffre de clés. Pour plus d’informations sur la création d’un coffre de clés pour Always Encrypted, consultez [Gérer vos coffres de clés à partir du portail Azure](/archive/blogs/kv/manage-your-key-vaults-from-new-azure-portal).
    6. Sélectionnez votre certificat ou votre clé Valeur de clé Azure, ou cliquez sur le bouton **Générer un certificat** pour en créer.
    7. Sélectionnez **OK**.

        ![Autoriser les calculs d’enclave](media/always-encrypted-enclaves/allow-enclave-computations.png)

1. Créez une clé de chiffrement de colonne prenant en charge les enclaves :

    1. Cliquez avec le bouton droit sur **Clés Always Encrypted** et sélectionnez **Nouvelle clé de chiffrement de colonne**.
    2. Entrez un nom pour la nouvelle clé de chiffrement de colonne : **CEK1**.
    3. Dans le menu déroulant **Clé principale de colonne**, sélectionnez la clé principale de colonne que vous avez créée aux étapes précédentes.
    4. Sélectionnez **OK**.

## <a name="step-5-encrypt-some-columns-in-place"></a>Étape 5 : Chiffrer des colonnes sur place

Dans cette étape, vous allez chiffrer les données stockées dans les colonnes **SSN** et **Salaire** à l’intérieur de l’enclave côté serveur, puis vous testerez une requête SELECT sur les données.

1. Ouvrez une nouvelle instance SSMS et connectez-vous à votre base de données **avec** Always Encrypted activé pour la connexion de base de données.
    1. Démarrez une nouvelle instance SSMS.
    2. Dans la boîte de dialogue **Se connecter au serveur**, spécifiez le nom complet de votre serveur (par exemple, *myserver123.database.windows.net*), puis entrez le nom d’utilisateur de l’administrateur et le mot de passe que vous avez spécifié lors de la création du serveur.
    3. Cliquez sur **Options >>** et sélectionnez l’onglet **Propriétés de connexion**. Veillez à sélectionner la base de données **ContosoHR** (pas la base de données master par défaut). 
    4. Sélectionnez l’onglet **Always Encrypted**.
    5. Veillez à ce que la case **Activer Always Encrypted (chiffrement de colonne)** soit cochée.
    6. Spécifiez votre URL d’attestation d’enclave que vous avez obtenue en suivant les étapes décrites dans [Étape 2 : Configurer un fournisseur d’attestation](#step-2-configure-an-attestation-provider). Consultez la capture d’écran ci-dessous.

        ![Se connecter avec une attestation](media/always-encrypted-enclaves/connect-to-server-configure-attestation.png)

    7. Sélectionnez **Se connecter**.
    8. Si vous êtes invité à activer les requêtes Paramétrage pour Always Encrypted, sélectionnez **Activer**.



1. En utilisant la même instance SSMS (avec Always Encrypted activé), ouvrez une nouvelle fenêtre de requête et chiffrez les colonnes **SSN** et **Salaire** en exécutant les instructions ci-dessous.

    ```sql
    ALTER TABLE [HR].[Employees]
    ALTER COLUMN [SSN] [char] (11) COLLATE Latin1_General_BIN2
    ENCRYPTED WITH (COLUMN_ENCRYPTION_KEY = [CEK1], ENCRYPTION_TYPE = Randomized, ALGORITHM = 'AEAD_AES_256_CBC_HMAC_SHA_256') NOT NULL
    WITH
    (ONLINE = ON);

    ALTER TABLE [HR].[Employees]
    ALTER COLUMN [Salary] [money]
    ENCRYPTED WITH (COLUMN_ENCRYPTION_KEY = [CEK1], ENCRYPTION_TYPE = Randomized, ALGORITHM = 'AEAD_AES_256_CBC_HMAC_SHA_256') NOT NULL
    WITH
    (ONLINE = ON);

    ALTER DATABASE SCOPED CONFIGURATION CLEAR PROCEDURE_CACHE;
    ```

    > [!NOTE]
    > Notez l’instruction ALTER DATABASE SCOPED CONFIGURATION CLEAR PROCEDURE_CACHE destinée à effacer le cache du plan de requête de la base de données dans le script ci-dessus. Une fois que vous avez modifié la table, vous devez effacer les plans pour l’ensemble des lots et des procédures stockées qui accèdent à la table, afin d’actualiser les informations de chiffrement des paramètres. 

1. Pour vérifier que les colonnes **SSN** et **Salaire** sont maintenant chiffrées, ouvrez une nouvelle fenêtre de requête dans l’instance SSMS **sans** Always Encrypted activé pour la connexion de base de données et exécutez l’instruction ci-dessous. La fenêtre de requête doit retourner des valeurs chiffrées dans les colonnes **SSN** et **Salaire**. Si vous exécutez la même requête à l’aide de l’instance SSMS avec Always Encrypted activé, vous devez voir les données déchiffrées.

    ```sql
    SELECT * FROM [HR].[Employees];
    ```

## <a name="step-6-run-rich-queries-against-encrypted-columns"></a>Étape 6 : Exécuter des requêtes complexes sur les colonnes chiffrées

Vous pouvez exécuter des requêtes complexes sur les colonnes chiffrées. Un traitement de requête se produit à l’intérieur de votre enclave côté serveur. 

1. Dans l’instance SSMS **avec** Always Encrypted activé, vérifiez que Paramétrage pour Always Encrypted est également activé.
    1. Sélectionnez **Outils** dans le menu principal de SSMS.
    2. Sélectionnez **Options...** .
    3. Accédez à **Exécution de la requête** > **SQL Server** > **Avancé**.
    4. Vérifiez que la case **Activer Paramétrage pour Always Encrypted** est cochée.
    5. Sélectionnez **OK**.
2. Ouvrez une nouvelle fenêtre de requête, collez-y la requête ci-dessous et exécutez-la. La requête doit retourner des valeurs de texte en clair et des lignes correspondant aux critères de recherche spécifiés.

    ```sql
    DECLARE @SSNPattern [char](11) = '%6818';
    DECLARE @MinSalary [money] = $1000;
    SELECT * FROM [HR].[Employees]
    WHERE SSN LIKE @SSNPattern AND [Salary] >= @MinSalary;
    ```

3. Essayez à nouveau la même requête dans l’instance SSMS pour laquelle Always Encrypted n’est pas activé. Un échec doit survenir.
 
## <a name="next-steps"></a>Étapes suivantes

À l’issue de ce tutoriel, vous pouvez accéder à l’un des tutoriels suivants :
- [Tutoriel : Développer une application .NET en utilisant Always Encrypted avec enclaves sécurisées](/sql/connect/ado-net/sql/tutorial-always-encrypted-enclaves-develop-net-apps)
- [Tutoriel : Développer une application .NET Framework avec Always Encrypted avec enclaves sécurisées](/sql/relational-databases/security/tutorial-always-encrypted-enclaves-develop-net-framework-apps)
- [Tutoriel : Création et utilisation des index sur des colonnes prenant en charge les enclaves à l’aide d’un chiffrement aléatoire](/sql/relational-databases/security/tutorial-creating-using-indexes-on-enclave-enabled-columns-using-randomized-encryption)

## <a name="see-also"></a>Voir aussi

- [Configurer et utiliser Always Encrypted avec enclaves sécurisées](/sql/relational-databases/security/encryption/configure-always-encrypted-enclaves)
