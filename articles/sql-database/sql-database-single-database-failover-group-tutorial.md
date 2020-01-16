---
title: 'Tutoriel : Ajouter une base de données unique à un groupe de basculement'
description: Ajoutez une base de données unique Azure SQL Database à un groupe de basculement en utilisant le portail Azure, PowerShell ou Azure CLI.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 06/19/2019
ms.openlocfilehash: 8c4c346dd004e435846aff5592a20cd747c45df7
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552625"
---
# <a name="tutorial-add-an-azure-sql-database-single-database-to-a-failover-group"></a>Tutoriel : Ajouter une base de données unique Azure SQL Database à un groupe de basculement

Configurez un groupe de basculement pour une base de données unique Azure SQL Database et testez le basculement en utilisant le portail Azure, PowerShell ou Azure CLI.  Dans ce didacticiel, vous apprendrez à :

> [!div class="checklist"]
> - Créer une base de données unique Azure SQL Database.
> - Créer un [groupe de basculement](sql-database-auto-failover-group.md) pour une base de données unique entre deux serveurs SQL logiques.
> - Tester le basculement.

## <a name="prerequisites"></a>Conditions préalables requises

# <a name="portaltabazure-portal"></a>[Portail](#tab/azure-portal)
Pour suivre ce tutoriel, veillez à disposer des éléments suivants : 

- Un abonnement Azure. [Créez un compte gratuit](https://azure.microsoft.com/free/) si vous n’en avez pas déjà un.


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Pour suivre le tutoriel, vérifiez que les prérequis ci-dessous sont remplis :

- Un abonnement Azure. [Créez un compte gratuit](https://azure.microsoft.com/free/) si vous n’en avez pas déjà un.
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)


# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)
Pour suivre le tutoriel, vérifiez que les prérequis ci-dessous sont remplis :

- Un abonnement Azure. [Créez un compte gratuit](https://azure.microsoft.com/free/) si vous n’en avez pas déjà un.
- La dernière version d’[Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). 

---

## <a name="1---create-a-single-database"></a>1 - Créer une base de données unique 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---create-the-failover-group"></a>2 - Créer le groupe de basculement 
Dans de cette étape, vous allez créer un [groupe de basculement](sql-database-auto-failover-group.md) entre un serveur SQL Azure existant et un nouveau serveur SQL Azure dans une autre région. Ajoutez ensuite l’exemple de base de données au groupe de basculement. 

# <a name="portaltabazure-portal"></a>[Portail](#tab/azure-portal)
Créez votre groupe de basculement et ajoutez-y votre base de données en utilisant le portail Azure. 

1. Dans le menu de gauche du **Portail Azure**, sélectionnez [Azure SQL](https://portal.azure.com). Si **Azure SQL** ne figure pas dans la liste, sélectionnez **Tous les services**, puis tapez Azure SQL dans la zone de recherche. (Facultatif) Sélectionnez l’étoile en regard d’**Azure SQL** pour l’ajouter aux favoris et l’ajouter en tant qu’élément dans le volet de navigation de gauche. 
1. Sélectionnez la base de données créée dans la section 1, comme `mySampleDatabase`. 
1. Sélectionnez le nom du serveur sous **Nom du serveur** pour ouvrir les paramètres du serveur.

   ![Ouvrir un serveur pour une base de donnée unique](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Sélectionnez **Groupes de basculement** dans le volet **Paramètres**, puis sélectionnez **Ajouter un groupe** pour créer un groupe de basculement. 

    ![Ajouter un nouveau groupe de basculement](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. Dans la page **Groupe de basculement**, entrez ou sélectionnez les valeurs suivantes, puis sélectionnez **Créer** :
    - **Nom du groupe de basculement** : Tapez un nom de groupe de basculement unique, par exemple `failovergrouptutorial`. 
    - **Serveur secondaire** : Sélectionnez l’option permettant de *configurer les paramètres requis*, puis choisissez de **Créer un serveur**. Vous pouvez également choisir un serveur existant en tant que serveur secondaire. Après avoir entré les valeurs suivantes, sélectionnez **Sélectionner**. 
        - **Nom du serveur** : Tapez un nom unique pour le serveur secondaire, par exemple `mysqlsecondary`. 
        - **Connexion administrateur au serveur** : Tapez `azureuser`.
        - **Mot de passe** : tapez un mot de passe complexe qui répond aux exigences de mot de passe.
        - **Emplacement** : choisissez un emplacement dans la liste déroulante, tel que `East US`. Cet emplacement ne peut pas être le même que celui de votre serveur principal.

    > [!NOTE]
    > La connexion au serveur et les paramètres de pare-feu doivent correspondre à ceux de votre serveur principal. 
    
      ![Créer un serveur secondaire pour le groupe de basculement](media/sql-database-single-database-failover-group-tutorial/create-secondary-failover-server.png)

   - **Base de données dans le groupe** : Une fois qu’un serveur secondaire est sélectionné, cette option est déverrouillée. Sélectionnez-la pour **Sélectionner les bases de données à ajouter**, puis choisissez la base de données créée dans la section 1. L’ajout de la base de données au groupe de basculement démarre automatiquement le processus de géoréplication. 
        
    ![Ajouter la base de données SQL au groupe de basculement](media/sql-database-single-database-failover-group-tutorial/add-sqldb-to-failover-group.png)
        

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Créez votre groupe de basculement et ajoutez-y votre base de données unique en utilisant PowerShell. 

   > [!NOTE]
   > La connexion au serveur et les paramètres de pare-feu doivent correspondre à ceux de votre serveur principal. 

   ```powershell-interactive
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "West US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $databaseName = "mySampleDatabase"
   $drLocation = "East US"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # The ip address range that you want to allow to access your server 
   # (leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "DR Server name is" $drServerName 
   Write-host "Failover group name is" $failoverGroupName
   
   # Create a secondary server in the failover region
   Write-host "Creating a secondary logical server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary logical server..."
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   $serverFirewallRule   
   
   # Create a failover group between the servers
   $failovergroup = Write-host "Creating a failover group between the primary and secondary server..."
   New-AzSqlDatabaseFailoverGroup `
      –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $failovergroup
   
   # Add the database to the failover group
   Write-host "Adding the database to the failover group..." 
   Get-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName | `
   Add-AzSqlDatabaseToFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Successfully added the database to the failover group..." 
   ```

Cette partie du tutoriel utilise les cmdlets PowerShell suivantes :

| Commande | Notes |
|---|---|
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Crée un serveur SQL Database qui héberge des bases de données uniques et des pools élastiques. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Crée une règle de pare-feu pour un serveur logique. | 
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Crée une base de données unique Azure SQL Database. | 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Crée un groupe de basculement. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Obtient une ou plusieurs bases de données SQL. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Ajoute une ou plusieurs bases de données Azure SQL à un groupe de basculement. |

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)
Créez votre groupe de basculement et ajoutez-y votre base de données unique en utilisant Azure CLI. 

   > [!NOTE]
   > La connexion au serveur et les paramètres de pare-feu doivent correspondre à ceux de votre serveur principal. 

   ```azurecli-interactive
   #!/bin/bash
   # Set variables
   # subscriptionID=<SubscriptionID>
   # resourceGroupName=myResourceGroup-$RANDOM
   # location=SouthCentralUS
   # adminLogin=azureuser
   # password="PWD27!"+`openssl rand -base64 18`
   # serverName=mysqlserver-$RANDOM
   # databaseName=mySampleDatabase
   drLocation=NorthEurope
   drServerName=mysqlsecondary-$RANDOM
   failoverGroupName=failovergrouptutorial-$RANDOM

   # Create a secondary server in the failover region
   echo "Creating a secondary logical server in the DR region..."
   az sql server create \
      --name $drServerName \
      --resource-group $resourceGroupName \
      --location $drLocation  \
      --admin-user $adminLogin\
      --admin-password $password

   # Configure a firewall rule for the server
   echo "Configuring firewall..."
   az sql server firewall-rule create \
      --resource-group $resourceGroupName \
      --server $drServerName \
      -n AllowYourIp \
      --start-ip-address $startip \
      --end-ip-address $endip
   
   # Create a failover group between the servers and add the database
   echo "Creating a failover group between the two servers..."
   az sql failover-group create \
      --name $failoverGroupName  \
      --partner-server $drServerName \
      --resource-group $resourceGroupName \
      --server $serverName \
      --add-db $databaseName
      --failover-policy Automatic
   ```

Cette partie du tutoriel utilise les cmdlets CLI Az suivantes :

| Commande | Notes |
|---|---|
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Crée un serveur SQL Database qui héberge des bases de données uniques et des pools élastiques. |
| [az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule) | Crée les règles de pare-feu d’un serveur. | 
| [az sql failover-group create](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Crée un groupe de basculement. | 

---

## <a name="3---test-failover"></a>3 - Tester le basculement 
Dans cette étape, vous allez faire basculer votre groupe de basculement sur le serveur secondaire, puis effectuer une restauration automatique en utilisant le portail Azure. 

# <a name="portaltabazure-portal"></a>[Portail](#tab/azure-portal)
Testez le basculement en utilisant le portail Azure. 

1. Dans le menu de gauche du **Portail Azure**, sélectionnez [Azure SQL](https://portal.azure.com). Si **Azure SQL** ne figure pas dans la liste, sélectionnez **Tous les services**, puis tapez Azure SQL dans la zone de recherche. (Facultatif) Sélectionnez l’étoile en regard d’**Azure SQL** pour l’ajouter aux favoris et l’ajouter en tant qu’élément dans le volet de navigation de gauche. 
1. Sélectionnez la base de données unique créée dans la section 2, comme `mySampleDatbase`. 
1. Sélectionnez le nom du serveur sous **Nom du serveur** pour ouvrir les paramètres du serveur.

   ![Ouvrir un serveur pour une base de donnée unique](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Sélectionnez **Groupes de basculement** dans le volet **Paramètres**, puis choisissez le groupe de basculement créé dans la section 2. 
  
   ![Sélectionner le groupe de basculement à partir du portail](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Vérifiez quel est le serveur principal et quel est le serveur secondaire. 
1. Sélectionnez **Basculer** dans le volet des tâches pour faire basculer votre groupe de basculement contenant votre exemple de base de données unique. 
1. Sélectionnez **Oui** en réponse à l’avertissement qui signale que les sessions TDS seront déconnectées. 

   ![Basculer votre groupe de basculement contenant votre base de données SQL](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Vérifiez quel est maintenant le serveur principal et quel est le serveur secondaire. Si le basculement a réussi, les deux serveurs doivent avoir échangé leur rôle. 
1. Resélectionnez **Basculer** pour rétablir les serveurs dans leur rôle d’origine. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Testez le basculement en utilisant PowerShell. 


Vérifiez le rôle du réplica secondaire : 

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```

Basculez vers le serveur secondaire : 

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully to" $drServerName 
   ```

Rétablir le groupe de basculement sur le serveur principal :

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully back to" $serverName
   ```

Cette partie du tutoriel utilise les cmdlets PowerShell suivantes :

| Commande | Notes |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Obtient ou dresse la liste des groupes de basculement Azure SQL Database. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Exécute le basculement d’un groupe de basculement Azure SQL Database. |



# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)
Testez le basculement en utilisant Azure CLI. 

Vérifiez quel est le serveur secondaire :

   
   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   # serverName=mysqlserver-$RANDOM
   
   # Verify which server is secondary
   echo "Verifying which server is in the secondary role..."
   az sql failover-group list \
      --server $serverName \
      --resource-group $resourceGroupName
   ```

Basculez vers le serveur secondaire : 

   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   # drServerName=mysqlsecondary-$RANDOM
   # failoverGroupName=failovergrouptutorial-$RANDOM

   
   echo "Failing over group to the secondary server..."
   az sql failover-group set-primary \
      --name $failoverGroupName \
      --resource-group $resourceGroupName \
      --server $drServerName
   echo "Successfully failed failover group over to" $drServerName
   ```

Rétablir le groupe de basculement sur le serveur principal :

   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   # serverName=mysqlserver-$RANDOM
   # failoverGroupName=failovergrouptutorial-$RANDOM
   
   echo "Failing over group back to the primary server..."
   az sql failover-group set-primary \
      --name $failoverGroupName \
      --resource-group $resourceGroupName \
      --server $serverName
   echo "Successfully failed failover group back to" $serverName
   ```

Cette partie du tutoriel utilise les cmdlets CLI Az suivantes :

| Commande | Notes |
|---|---|
| [az sql failover-group list](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Répertorie les groupes de basculement d’un serveur. |
| [az sql failover-group set-primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Définit le principal du groupe de basculement en basculant toutes les bases de données à partir du serveur principal actuel. | 

---

## <a name="clean-up-resources"></a>Nettoyer les ressources 
Nettoyez les ressources en supprimant le groupe de ressources. 

# <a name="portaltabazure-portal"></a>[Portail](#tab/azure-portal)
Supprimez le groupe de ressources en utilisant le portail Azure. 

1. Accédez à votre groupe de ressources sur le [portail Azure](https://portal.azure.com).
1. Sélectionnez **Supprimer le groupe de ressources** pour supprimer toutes les ressources du groupe ainsi que le groupe de ressources lui-même. 
1. Tapez le nom du groupe de ressources, `myResourceGroup`, dans la zone de texte, puis sélectionnez **Supprimer** pour supprimer le groupe de ressources.  

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Supprimez le groupe de ressources en utilisant PowerShell. 


   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Remove the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

Cette partie du tutoriel utilise les cmdlets PowerShell suivantes :

| Commande | Notes |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Supprime un groupe de ressources. | 

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Supprimez le groupe de ressources en utilisant Azure CLI. 


   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   
   # Clean up resources by removing the resource group
   echo "Cleaning up resources by removing the resource group..."
   az group delete \
     --name $resourceGroupName
   echo "Successfully removed resource group" $resourceGroupName
   ```

Cette partie du tutoriel utilise les cmdlets CLI Az suivantes :

| Commande | Notes |
|---|---|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

---


> [!IMPORTANT]
> Si vous souhaitez conserver le groupe de ressources tout en supprimant la base de données secondaire, retirez-la du groupe de basculement avant de la supprimer. La suppression d’une base de données secondaire avant son retrait du groupe de basculement peut entraîner un comportement imprévisible. 


## <a name="full-scripts"></a>Scripts complets

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add single database to a failover group")]

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Crée un serveur SQL Database qui héberge des bases de données uniques et des pools élastiques. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Crée une règle de pare-feu pour un serveur logique. | 
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Crée une base de données unique Azure SQL Database. | 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Crée un groupe de basculement. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Obtient une ou plusieurs bases de données SQL. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Ajoute une ou plusieurs bases de données Azure SQL à un groupe de basculement. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Obtient ou dresse la liste des groupes de basculement Azure SQL Database. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Exécute le basculement d’un groupe de basculement Azure SQL Database. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Supprime un groupe de ressources. | 

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

[!code-azurecli-interactive[main](../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add single database to a failover group")]

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az account set](/cli/azure/account?view=azure-cli-latest#az-account-set) | Définit un abonnement en tant qu’abonnement actif. | 
| [az group create](/cli/azure/group#az-group-create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Crée un serveur SQL Database qui héberge des bases de données uniques et des pools élastiques. |
| [az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule) | Crée les règles de pare-feu d’un serveur. | 
| [az sql db create](/cli/azure/sql/db?view=azure-cli-latest) | Crée une base de données. | 
| [az sql failover-group create](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Crée un groupe de basculement. | 
| [az sql failover-group list](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Répertorie les groupes de basculement d’un serveur. |
| [az sql failover-group set-primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Définit le principal du groupe de basculement en basculant toutes les bases de données à partir du serveur principal actuel. | 
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

# <a name="portaltabazure-portal"></a>[Portail](#tab/azure-portal)
Aucun script n’est disponible pour le portail Azure. 
 
---

Vous trouverez d’autres scripts Azure SQL Database ici : [Azure PowerShell](sql-database-powershell-samples.md) et [Azure CLI](sql-database-cli-samples.md). 

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez ajouté une base de données unique Azure SQL Database à un groupe de basculement et vous avez testé le basculement. Vous avez appris à : 

> [!div class="checklist"]
> - Créer une base de données unique Azure SQL Database. 
> - Créer un [groupe de basculement](sql-database-auto-failover-group.md) pour une base de données unique entre deux serveurs SQL logiques.
> - Tester le basculement.

Passez au didacticiel suivant pour découvrir comment ajouter votre pool élastique à un groupe de basculement. 

> [!div class="nextstepaction"]
> [Tutoriel : Ajouter un pool élastique Azure SQL Database à un groupe de basculement](sql-database-elastic-pool-failover-group-tutorial.md)
