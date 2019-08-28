---
author: MashaMSFT
ms.service: sql-database
ms.subservice: single-database
ms.topic: include
ms.date: 07/31/2019
ms.author: mathoma
ms.openlocfilehash: d19e21edd31be461ba30e8985e80c4651bbbb02d
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69894093"
---
Au cours de cette étape, vous allez créer une base de données unique Azure SQL Database. 

> [!IMPORTANT]
> Veillez à configurer des règles de pare-feu pour pouvoir utiliser l’adresse IP publique de l’ordinateur sur lequel vous effectuez les étapes de cet article.
>
> Pour plus d’informations, consultez [Créer une règle de pare-feu au niveau du serveur](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) ou, pour déterminer l’adresse IP utilisée pour la règle de pare-feu au niveau du serveur pour votre ordinateur, consultez [Créer une règle de pare-feu au niveau du serveur](../sql-database-server-level-firewall-rule.md).  

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Créez votre groupe de ressources et votre base de données unique à l’aide du portail Azure.

1. Dans le menu de gauche du portail Azure, sélectionnez **Azure SQL**. Si **Azure SQL** ne figure pas dans la liste, sélectionnez **Tous les services**, puis tapez *Azure SQL* dans la zone de recherche.
2. Sélectionnez **+Ajouter** pour ouvrir la page **Sélectionner l’option de déploiement SQL**. Vous pouvez afficher des informations supplémentaires sur les différentes bases de données en sélectionnant **Afficher les détails** sur la vignette **Bases de données**.
3. Sélectionnez **Créer** :

   ![Créer une base de données unique](../media/sql-database-get-started-portal/create-single-database.png)

3. Sous l’onglet **Bases**, dans la section **Détails du projet**, tapez ou sélectionnez les valeurs suivantes :

   - **Abonnement**: Faites défiler la liste et sélectionnez l’abonnement approprié, s’il n’apparaît pas.
   - **Groupe de ressources** : sélectionnez **Créer**, tapez `myResourceGroup`, puis sélectionnez **OK**.

     ![Nouvelle base de données SQL - Onglet des informations de base](../media/sql-database-get-started-portal/new-sql-database-basics.png)

4. Dans la section formulaire **Détails de la base de données**, tapez ou sélectionnez les valeurs suivantes :

   - **Nom de la base de données** : Entrez `mySampleDatabase`.
   - **Server** (Serveur) : sélectionnez **Créer**, entrez les valeurs suivantes, puis sélectionnez **Sélectionner**.
       - **Nom du serveur** : tapez `mysqlserver` ainsi que des chiffres à des fins d’unicité.
       - **Connexion administrateur au serveur** : Saisissez `azureuser`.
       - **Mot de passe** : tapez un mot de passe complexe qui répond aux exigences de mot de passe.
       - **Emplacement** : choisissez un emplacement dans la liste déroulante, tel que `West US 2`.

         ![Nouveau serveur](../media/sql-database-get-started-portal/new-server.png)

      > [!IMPORTANT]
      > Mémorisez votre nom d’utilisateur et mot de passe de connexion d’administrateur au serveur, car vous en aurez besoin pour vous connecter au serveur et aux bases de données dans le cadre de ce guide ou d’autres guides de démarrage rapide. Si vous oubliez votre mot de passe ou vos identifiants de connexion, vous pouvez obtenir le nom de connexion ou réinitialiser le mot de passe sur la page **SQL Server**. Pour ouvrir la page **SQL Server** , sélectionnez le nom du serveur sur la page **Vue d’ensemble** de la base de données une fois cette dernière créée.

   - **Vous souhaitez utiliser un pool élastique SQL ?**  : sélectionnez l’option **Non**.
   - **Calcul + stockage** : Sélectionnez **Configurer la base de données**. 

     ![Détails de la base de données SQL](../media/sql-database-get-started-portal/sql-db-basic-db-details.png)

   - Sélectionnez **Provisionné** et **Gen5**.

     ![Gen4 provisionné](../media/sql-database-get-started-portal/create-database-provisioned.png)

   - Vérifiez les paramètres pour **vCores max.** , **vCores min.** , **Délai de pause automatique** et **Taille maximale de données**. Modifiez-les comme vous le souhaitez.
   - Acceptez les termes de la préversion et cliquez sur **OK**.
   - Sélectionnez **Appliquer**.

5. Sélectionnez l’onglet **Paramètres supplémentaires**. 
6. Dans la section **Source de données**, sous **Utiliser des données existantes**, sélectionnez `Sample`.

   ![Paramètres supplémentaires de la base de données SQL](../media/sql-database-get-started-portal/create-sql-database-additional-settings.png)

   > [!IMPORTANT]
   > Veillez à sélectionner les données **Exemple (AdventureWorksLT)** pour pouvoir facilement suivre le présent guide ainsi que d’autres articles dédiés à Azure SQL Database qui utilisent ces données.

7. Laissez le reste des valeurs par défaut, puis sélectionnez **Vérifier + créer** en bas du formulaire.
8. Passez en revue les paramètres finaux et sélectionnez **Créer**.

9. Sur le formulaire **SQL Database**, sélectionnez **Créer** pour déployer et configurer le groupe de ressources, le serveur et la base de données.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Créez votre groupe de ressources et votre base de données unique à l’aide de PowerShell.

   ```powershell-interactive
   # Set variables for your server and database
   $subscriptionId = '<SubscriptionID>'
   $resourceGroupName = "myResourceGroup-$(Get-Random)"
   $location = "West US 2"
   $adminLogin = "azureuser"
   $password = "PWD27!"+(New-Guid).Guid
   $serverName = "mysqlserver-$(Get-Random)"
   $databaseName = "mySampleDatabase"

   # The ip address range that you want to allow to access your server 
   # (leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName 
   Write-host "Password is" $password  
   Write-host "Server name is" $serverName 

   # Connect to Azure
   Connect-AzAccount

   # Set subscription ID
   Set-AzContext -SubscriptionId $subscriptionId 

   # Create a resource group
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup

   # Create a server with a system wide unique server name
   Write-host "Creating primary logical server..."
   $server = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -Location $location `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
      -ArgumentList $adminLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $server

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for primary logical server..."
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   $serverFirewallRule

   # Create General Purpose Gen4 database with 1 vCore
   Write-host "Creating a gen5 2 vCore database..."
   $database = New-AzSqlDatabase  -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -Edition GeneralPurpose `
      -VCore 2 `
      -ComputeGeneration Gen5 `
      -MinimumCapacity 2 `
      -SampleName "AdventureWorksLT"
   $database
   ```

# <a name="azure-clitabazure-cli"></a>[Interface de ligne de commande Azure](#tab/azure-cli)

Créez votre groupe de ressources et votre base de données unique à l’aide de l’interface CLI AZ.

   ```azurecli-interactive
   #!/bin/bash
   # Set variables
   subscriptionID=<SubscriptionID>
   resourceGroupName=myResourceGroup-$RANDOM
   location=SouthCentralUS
   adminLogin=azureuser
   password="PWD27!"+`openssl rand -base64 18`
   serverName=mysqlserver-$RANDOM
   databaseName=mySampleDatabase
   drLocation=NorthEurope
   drServerName=mysqlsecondary-$RANDOM
   failoverGroupName=failovergrouptutorial-$RANDOM

   # The ip address range that you want to allow to access your DB. 
   # Leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB
   startip=0.0.0.0
   endip=0.0.0.0
  
   # Connect to Azure
   az login

   # Set the subscription context for the Azure account
   az account set -s $subscriptionID

   # Create a resource group
   echo "Creating resource group..."
   az group create \
      --name $resourceGroupName \
      --location $location \
      --tags Owner[=SQLDB-Samples]

   # Create a logical server in the resource group
   echo "Creating primary logical server..."
   az sql server create \
      --name $serverName \
      --resource-group $resourceGroupName \
      --location $location  \
      --admin-user $adminLogin \
      --admin-password $password

   # Configure a firewall rule for the server
   echo "Configuring firewall..."
   az sql server firewall-rule create \
      --resource-group $resourceGroupName \
      --server $serverName \
      -n AllowYourIp \
      --start-ip-address $startip \
      --end-ip-address $endip

   # Create a gen5 1vCore database in the server 
   echo "Creating a gen5 2 vCore database..."
   az sql db create \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName \
      --sample-name AdventureWorksLT \
      --edition GeneralPurpose \
      --family Gen5 \
      --capacity 2
   ```

---
