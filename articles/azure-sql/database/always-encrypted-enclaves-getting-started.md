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
ms.date: 05/01/2021
ms.openlocfilehash: 71e90e0afc3bc976ed65eb0ef59c76781490bdc1
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110457129"
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

- Un abonnement Azure actif. Si vous n’en avez pas, [créez un compte gratuit](https://azure.microsoft.com/free/). Pour pouvoir créer des ressources et configurer une stratégie d’attestation, vous devez être membre du rôle contributeur ou du rôle propriétaire de l’abonnement.

- SQL Server Management Studio (SSMS), version 18.9.1 ou ultérieure. Consultez [Télécharger SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) pour obtenir des informations sur la façon de télécharger SSMS.

### <a name="powershell-requirements"></a>Configuration requise pour PowerShell

> [!NOTE]
> La configuration requise indiquée dans cette section s’applique uniquement si vous choisissez d’utiliser PowerShell pour certaines des étapes de ce tutoriel. Si vous comptez utiliser le portail Azure à la place, vous pouvez ignorer cette section.

Assurez-vous que les modules PowerShell suivants sont installés sur votre ordinateur.

1. Az version 5.6 ou ultérieure. Pour plus d’informations sur l’installation du module PowerShell Az, consultez [Installer le module PowerShell Azure Az](/powershell/azure/install-az-ps). Pour déterminer la version du module Az installée sur votre ordinateur, exécutez la commande suivante à partir d’une session PowerShell.

    ```powershell
    Get-InstalledModule -Name Az
    ```

1. Az.Attestation 0.1.8 ou ultérieure. Pour plus d’informations sur la façon d’installer le module PowerShell Az.Attestation, consultez [Installer le module PowerShell Az.Attestation](../../attestation/quickstart-powershell.md#install-azattestation-powershell-module). Pour déterminer la version du module Az.Attestation installée sur votre ordinateur, exécutez la commande suivante à partir d’une session PowerShell.

    ```powershell
    Get-InstalledModule -Name Az.Attestation
    ```

Si les versions ne correspondent pas à la configuration minimale requise, exécutez la commande `Update-Module`.

PowerShell Gallery a déprécié les versions 1.0 et 1.1 du protocole TLS (Transport Layer Security). TLS 1.2 ou une version ultérieure est recommandé. Si vous utilisez une version TLS antérieure à 1.2, vous pouvez recevoir les erreurs suivantes :

- `WARNING: Unable to resolve package source 'https://www.powershellgallery.com/api/v2'`
- `PackageManagement\Install-Package: No match was found for the specified search criteria and module name.`

Pour continuer à interagir avec PowerShell Gallery, exécutez la commande suivante avant les commandes Install-Module

```powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
```

## <a name="step-1-create-and-configure-a-server-and-a-dc-series-database"></a>Étape 1 : Créer et configurer un serveur et une base de données de série DC

Au cours de cette étape, vous allez créer un nouveau serveur logique Azure SQL Database et une nouvelle base de données à l’aide de la génération matérielle de série DC, requise pour Always Encrypted avec enclaves sécurisées. Pour plus d’informations, consultez [Série DC](service-tiers-vcore.md#dc-series).

# <a name="portal"></a>[Portail](#tab/azure-portal)

1. Accédez à la page [Sélectionner l’option de déploiement SQL](https://portal.azure.com/#create/Microsoft.AzureSQL).
1. Si vous n’êtes pas déjà connecté au portail Azure, connectez-vous lorsque vous y êtes invité.
1. Sous **Bases de données SQL**, laissez **Type de ressource** défini sur **Base de données unique**, puis sélectionnez **Créer**.

   :::image type="content" source="./media/single-database-create-quickstart/select-deployment.png" alt-text="Ajouter à Azure SQL":::

1. Sous l’onglet **De base** du formulaire **Créer une base de données SQL**, sous **Détails du projet**, sélectionnez l’**Abonnement** Azure souhaité.
1. Pour **Groupe de ressources**, sélectionnez **Créer**, entrez un nom pour votre groupe de ressources, puis sélectionnez **OK**.
1. Pour **Nom de la base de données**, entrez *ContosoHR*.
1. Pour **Serveur**, sélectionnez **Créer**, puis remplissez le formulaire **Nouveau serveur** avec les valeurs suivantes :
   - **Nom du serveur** : entrez *mysqlserver* et ajoutez quelques caractères pour l’unicité. Nous ne pouvons pas fournir un nom de serveur exact à utiliser, car les noms de serveur doivent être globalement uniques pour tous les serveurs dans Azure, et non pas seulement dans un abonnement. Par conséquent, entrez un nom tel que mysqlserver135 et le portail vous indique s’il est disponible ou non.
   - **Nom de connexion d’administrateur serveur** : entrez le nom de connexion de l’administrateur, par exemple : *azureuser*.
   - **Mot de passe** : entrez un mot de passe qui répond aux exigences, puis réentrez-le dans le champ **Confirmer le mot de passe**.
   - **Emplacement** : sélectionnez un emplacement dans la liste déroulante.
      > [!IMPORTANT]
      > Vous devez sélectionner un emplacement (une région Azure) qui prend en charge à la fois la génération matérielle de série DC et Microsoft Azure Attestation. Pour obtenir la liste des régions prenant en charge la série DC, consultez [Disponibilité de la série DC](service-tiers-vcore.md#dc-series-1). [Voici](https://azure.microsoft.com/global-infrastructure/services/?products=azure-attestation) la disponibilité régionale de Microsoft Azure Attestation.

   Sélectionnez **OK**.
1. Laissez **Vous souhaitez utiliser un pool élastique SQL ?** avec la valeur **Non**.
1. Sous **Calcul + stockage**, sélectionnez **Configurer la base de données**, puis cliquez sur **Changer la configuration**.

   :::image type="content" source="./media/always-encrypted-enclaves/portal-configure-database.png" alt-text="Configurer la base de données" lightbox="./media/always-encrypted-enclaves/portal-configure-database.png":::

1. Sélectionnez la configuration matérielle **Série DC**, puis choisissez **OK**.

   :::image type="content" source="./media/always-encrypted-enclaves/portal-configure-dc-series-database.png" alt-text="Configurer une base de données Série DC":::

1. Sélectionnez **Appliquer**. 
1. De retour dans l’onglet **Informations de base** , vérifiez que **Calcul + stockage** est défini sur **Usage général**, **DC, 2 vCores, 32 Go de stockage**.
1. Sélectionnez **Suivant : Réseau** en bas de la page.

   :::image type="content" source="./media/always-encrypted-enclaves/portal-configure-dc-series-database-basics.png" alt-text="Configurer une base de données Série DC - informations de base":::

1. Sous l’onglet **Réseau**, pour **Méthode de connectivité**, sélectionnez **Point de terminaison public**.
1. Pour **Règles de pare-feu**, affectez la valeur **Oui** à **Ajouter l’adresse IP actuelle du client**. Laissez **Autoriser les services et les ressources Azure à accéder à ce serveur** avec la valeur **Non**.
1. Au bas de la page, sélectionnez **Examiner et créer**.

   :::image type="content" source="./media/always-encrypted-enclaves/portal-configure-database-networking.png" alt-text="Nouvelle base de données SQL - mise en réseau":::

1. Dans la page **Vérifier + créer**, après vérification, sélectionnez **Créer**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Ouvrez une console PowerShell et importez la version requise d’Az.

   ```PowerShell
   Import-Module "Az" -MinimumVersion "5.6.0"
   ```

1. Connectez-vous à Azure. Si nécessaire, [basculez vers l’abonnement](/powershell/azure/manage-subscriptions-azureps) que vous utilisez pour ce tutoriel.

   ```PowerShell
   Connect-AzAccount
   $subscriptionId = "<your subscription ID>"
   Set-AzContext -Subscription $subscriptionId
   ```

1. Créer un groupe de ressources

   > [!IMPORTANT]
   > Vous devez créer votre groupe de ressources dans une région (emplacement) qui prend en charge à la fois la génération matérielle de série DC et Microsoft Azure Attestation. Pour obtenir la liste des régions prenant en charge la série DC, consultez [Disponibilité de la série DC](service-tiers-vcore.md#dc-series-1). [Voici](https://azure.microsoft.com/global-infrastructure/services/?products=azure-attestation) la disponibilité régionale de Microsoft Azure Attestation.

   ```powershell
   $resourceGroupName = "<your new resource group name>"
   $location = "<Azure region supporting DC-series and Microsoft Azure Attestation>"
   New-AzResourceGroup -Name $resourceGroupName -Location $location
   ```

1. Créez un serveur logique Azure SQL. Lorsque vous y êtes invité, entrez le nom de l’administrateur du serveur et un mot de passe. Veillez à mémoriser le nom d’administrateur et le mot de passe : vous en aurez besoin ultérieurement pour vous connecter au serveur. 

   ```powershell
   $serverName = "<your server name>" 
   New-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName -Location $location 
   ```

1. Créez une règle de pare-feu de serveur qui autorise l’accès à partir de la plage d’adresses IP spécifiée.
  
   ```powershell
   $startIp = "<start of IP range>"
   $endIp = "<end of IP range>"
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
    -ServerName $serverName `
    -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   ```

1. Créez une base de données de série DC.

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

---

## <a name="step-2-configure-an-attestation-provider"></a>Étape 2 : Configurer un fournisseur d’attestation

Dans cette étape, vous allez créer et configurer un fournisseur d’attestation dans Microsoft Azure Attestation. Ceci est nécessaire pour attester l’enclave sécurisée que votre base de données utilise.

# <a name="portal"></a>[Portail](#tab/azure-portal)

1. Accédez à la page [Créer un fournisseur d’attestation](https://ms.portal.azure.com/#create/Microsoft.Attestation).
1. Dans la page **Créer un fournisseur d’attestation**, fournissez les entrées suivantes :

   - **Abonnement** : choisissez le même abonnement que celui dans lequel vous avez créé le serveur logique Azure SQL.
   - **Groupe de ressources** : choisissez le groupe de ressources dans lequel vous avez créé le serveur logique Azure SQL.
   - **Nom** : entrez *myattestprovider* et ajoutez quelques caractères pour l’unicité. Nous ne pouvons pas fournir un nom de fournisseur d’attestation exact, car les noms doivent être globalement uniques. Par conséquent, entrez un nom tel que myattestprovider12345 et le portail vous indique s’il est disponible ou non.
   - **Emplacement** : choisissez l’emplacement dans lequel vous avez créé le serveur logique Azure SQL.
   - **Fichier des certificats de signataire de stratégie** : laissez ce champ vide, car vous allez configurer une stratégie non signée.

1. Une fois que vous avez fourni les entrées nécessaires, sélectionnez **Vérifier + créer**.

   :::image type="content" source="./media/always-encrypted-enclaves/portal-create-attestation-provider-basics.png" alt-text="Créer un fournisseur d’attestation":::

1. Sélectionnez **Create** (Créer).
1. Une fois le fournisseur d’attestation créé, cliquez sur **Accéder à la ressource**.
1. Dans l’onglet **Vue d’ensemble** du fournisseur d’attestation, copiez la valeur de la propriété **URI d’attestation** dans le Presse-papiers, puis enregistrez-la dans un fichier. Il s’agit de l’URL d’attestation, dont vous aurez besoin dans les étapes ultérieures.  

   :::image type="content" source="./media/always-encrypted-enclaves/portal-attest-uri.png" alt-text="URL d’attestation":::

1. Sélectionnez **Stratégie** dans le menu de la ressource sur le côté gauche de la fenêtre ou dans le volet inférieur.
1. Définissez **Type d’attestation** sur **SGX-IntelSDK**.
1. Sélectionnez **Configurer** dans le menu supérieur.

   :::image type="content" source="./media/always-encrypted-enclaves/portal-configure-attestation-policy.png" alt-text="Configurer la stratégie d’attestation":::

1. Définissez **Format de stratégie** sur **Texte**. Laissez le paramètre **Options de la stratégie** défini sur **Entrer la stratégie**.
1. Dans le champ **Texte de la stratégie**, remplacez la stratégie par défaut par la stratégie ci-dessous. Pour obtenir des informations sur la stratégie ci-dessous, consultez [Créer et configurer un fournisseur d’attestation](always-encrypted-enclaves-configure-attestation.md#create-and-configure-an-attestation-provider).

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

1. Cliquez sur **Enregistrer**.

   :::image type="content" source="./media/always-encrypted-enclaves/portal-edit-attestation-policy.png" alt-text="Modifier la stratégie d’attestation":::

1. Cliquez sur **Actualiser** dans le menu supérieur pour afficher la stratégie configurée.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

1. Importez la version requise de `Az.Attestation`.  

   ```powershell
   Import-Module "Az.Attestation" -MinimumVersion "0.1.8"
   ```
  
1. Créez un fournisseur d’attestation.

   ```powershell
   $attestationProviderName = "<your attestation provider name>" 
   New-AzAttestation -Name $attestationProviderName -ResourceGroupName $resourceGroupName -Location $location
   ```

1. Configurez la stratégie d’attestation.
  
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

1. Récupérez l’URL d’attestation (l’URI d’attestation de votre fournisseur d’attestation).

   ```powershell
   $attestationUrl = (Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $resourceGroupName).AttestUri
   Write-Host "Your attestation URL is: $attestationUrl"
   ```

   L’URL d’attestation doit ressembler à ceci : `https://myattestprovider12345.eus.attest.azure.net`

---


## <a name="step-3-populate-your-database"></a>Étape 3 : Remplir votre base de données

Dans cette étape, vous allez créer une table et la remplir avec des données que vous chiffrerez et interrogerez ultérieurement.

1. Ouvrez SSMS et connectez-vous à la base de données **ContosoHR** sur le serveur logique Azure SQL que vous avez créé **sans** Always Encrypted activé dans la connexion de base de données.
    1. Dans la boîte de dialogue **Se connecter au serveur**, spécifiez le nom complet de votre serveur (par exemple, *myserver135.database.windows.net*), puis entrez le nom d’utilisateur de l’administrateur et le mot de passe que vous avez spécifié lors de la création du serveur.
    2. Cliquez sur **Options >>** et sélectionnez l’onglet **Propriétés de connexion**. Veillez à sélectionner la base de données **ContosoHR** (pas la base de données master par défaut). 
    3. Sélectionnez l’onglet **Always Encrypted**.
    4. Assurez-vous que la case **Activer Always Encrypted (chiffrement de colonne)** n’est **pas** cochée.

        :::image type="content" source="./media/always-encrypted-enclaves/connect-without-always-encrypted-ssms.png" alt-text="Se connecter sans Always Encrypted":::

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

        :::image type="content" source="./media/always-encrypted-enclaves/allow-enclave-computations.png" alt-text="Autoriser les calculs d’enclave":::

1. Créez une clé de chiffrement de colonne prenant en charge les enclaves :

    1. Cliquez avec le bouton droit sur **Clés Always Encrypted** et sélectionnez **Nouvelle clé de chiffrement de colonne**.
    2. Entrez un nom pour la nouvelle clé de chiffrement de colonne : **CEK1**.
    3. Dans le menu déroulant **Clé principale de colonne**, sélectionnez la clé principale de colonne que vous avez créée aux étapes précédentes.
    4. Sélectionnez **OK**.

## <a name="step-5-encrypt-some-columns-in-place"></a>Étape 5 : Chiffrer des colonnes sur place

Dans cette étape, vous allez chiffrer les données stockées dans les colonnes **SSN** et **Salaire** à l’intérieur de l’enclave côté serveur, puis vous testerez une requête SELECT sur les données.

1. Ouvrez une nouvelle instance SSMS et connectez-vous à votre base de données **avec** Always Encrypted activé pour la connexion de base de données.
    1. Démarrez une nouvelle instance SSMS.
    2. Dans la boîte de dialogue **Se connecter au serveur**, spécifiez le nom complet de votre serveur (par exemple, *myserver135.database.windows.net*), puis entrez le nom d’utilisateur de l’administrateur et le mot de passe que vous avez spécifié lors de la création du serveur.
    3. Cliquez sur **Options >>** et sélectionnez l’onglet **Propriétés de connexion**. Veillez à sélectionner la base de données **ContosoHR** (pas la base de données master par défaut). 
    4. Sélectionnez l’onglet **Always Encrypted**.
    5. Veillez à ce que la case **Activer Always Encrypted (chiffrement de colonne)** **soit** cochée.
    6. Spécifiez votre URL d’attestation d’enclave que vous avez obtenue en suivant les étapes décrites dans [Étape 2 : Configurer un fournisseur d’attestation](#step-2-configure-an-attestation-provider). Consultez la capture d’écran ci-dessous.

        :::image type="content" source="./media/always-encrypted-enclaves/connect-to-server-configure-attestation.png" alt-text="Se connecter avec une attestation":::

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
