---
title: 'Didacticiel : Ajouter une base de données unique Azure SQL Database à un groupe de basculement | Microsoft Docs'
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
ms.openlocfilehash: d11dd72c65ea32fb5a262f325bdcad0b5a8ab863
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566645"
---
# <a name="tutorial-add-an-azure-sql-database-single-database-to-a-failover-group"></a>Didacticiel : Ajouter une base de données unique Azure SQL Database à un groupe de basculement

Configurez un groupe de basculement pour une base de données unique Azure SQL Database et testez le basculement en utilisant le portail Azure, PowerShell ou Azure CLI.  Ce didacticiel vous apprendra à effectuer les opérations suivantes :

> [!div class="checklist"]
> - Créer une base de données unique Azure SQL Database.
> - Créer un [groupe de basculement](sql-database-auto-failover-group.md) pour une base de données unique entre deux serveurs SQL logiques.
> - Tester le basculement.

## <a name="prerequisites"></a>Prérequis

# <a name="azure-portaltabazure-portal"></a>[Portail Azure](#tab/azure-portal)
Pour suivre ce tutoriel, veillez à disposer des éléments suivants : 

- Un abonnement Azure. [Créez un compte gratuit](https://azure.microsoft.com/free/) si vous n’en avez pas déjà un.


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Pour suivre le tutoriel, vérifiez que les prérequis ci-dessous sont remplis :

- Un abonnement Azure. [Créez un compte gratuit](https://azure.microsoft.com/free/) si vous n’en avez pas déjà un.
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)


# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)
Pour suivre le tutoriel, vérifiez que les prérequis ci-dessous sont remplis :

- Un abonnement Azure. [Créez un compte gratuit](https://azure.microsoft.com/free/) si vous n’en avez pas déjà un.
- La dernière version d’[Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). 

---

## <a name="1---create-a-single-database"></a>1 - Créer une base de données unique 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---create-the-failover-group"></a>2 - Créer le groupe de basculement 
Dans de cette étape, vous allez créer un [groupe de basculement](sql-database-auto-failover-group.md) entre un serveur SQL Azure existant et un nouveau serveur SQL Azure dans une autre région. Ajoutez ensuite l’exemple de base de données au groupe de basculement. 

# <a name="azure-portaltabazure-portal"></a>[Portail Azure](#tab/azure-portal)
Créez votre groupe de basculement et ajoutez-y votre base de données en utilisant le portail Azure. 


1. En haut à gauche du [portail Azure](https://portal.azure.com), sélectionnez **Tous les services**. 
1. Tapez `sql servers` dans la zone de recherche. 
1. (Facultatif) Sélectionnez l’icône d’étoile en regard de Serveurs SQL pour mettre **Serveurs SQL** dans les Favoris, puis ajoutez-le à votre volet de navigation gauche. 
    
    ![Rechercher les serveurs SQL](media/sql-database-single-database-create-failover-group-tutorial/all-services-sql-servers.png)

1. Sélectionnez **Serveurs SQL**, puis choisissez le serveur créé dans la section 1, comme `mysqlserver`.
1. Sélectionnez **Groupes de basculement** dans le volet **Paramètres**, puis sélectionnez **Ajouter un groupe** pour créer un groupe de basculement. 

    ![Ajouter un nouveau groupe de basculement](media/sql-database-single-database-create-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. Dans la page **Groupe de basculement**, entrez ou sélectionnez les valeurs suivantes, puis sélectionnez **Créer** :
    - **Nom du groupe de basculement** : Tapez un nom de groupe de basculement unique, par exemple `failovergrouptutorial`. 
    - **Serveur secondaire** : Sélectionnez l’option permettant de *configurer les paramètres requis*, puis choisissez de **Créer un serveur**. Vous pouvez également choisir un serveur existant en tant que serveur secondaire. Après avoir entré les valeurs suivantes, sélectionnez **Sélectionner**. 
        - **Nom du serveur** : Tapez un nom unique pour le serveur secondaire, par exemple `mysqlsecondary`. 
        - **Connexion administrateur au serveur** : Tapez `azureuser`.
        - **Mot de passe** : tapez un mot de passe complexe qui répond aux exigences de mot de passe.
        - **Emplacement** : Choisissez un emplacement dans la liste déroulante, par exemple USA Est 2. Cet emplacement ne peut pas être le même que celui de votre serveur principal.

    > [!NOTE]
    > La connexion au serveur et les paramètres de pare-feu doivent correspondre à ceux de votre serveur principal. 
    
      ![Créer un serveur secondaire pour le groupe de basculement](media/sql-database-single-database-create-failover-group-tutorial/create-secondary-failover-server.png)

   - **Base de données dans le groupe** : Une fois qu’un serveur secondaire est sélectionné, cette option est déverrouillée. Sélectionnez-la pour **Sélectionner les bases de données à ajouter**, puis choisissez la base de données créée dans la section 1. L’ajout de la base de données au groupe de basculement démarre automatiquement le processus de géoréplication. 
        
    ![Ajouter la base de données SQL au groupe de basculement](media/sql-database-single-database-create-failover-group-tutorial/add-sqldb-to-failover-group.png)
        

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Créez votre groupe de basculement et ajoutez-y votre base de données unique en utilisant PowerShell. 

   > [!NOTE]
   > La connexion au serveur et les paramètres de pare-feu doivent correspondre à ceux de votre serveur principal. 

   ```powershell-interactive
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "West US 2"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $databaseName = "mySampleDatabase"
   $drLocation = "East US 2"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

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

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)
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
   
   # Create a failover group between the servers and add the database
   echo "Creating a failover group between the two servers..."
   az sql failover-group create \
      --name $failoverGroupName  \
      --partner-server $drServerName \
      --resource-group $resourceGroupName \
      --server $serverName \
      --failover-policy Automatic
   ```

---

## <a name="3---test-failover"></a>3 - Tester le basculement 
Dans cette étape, vous allez faire basculer votre groupe de basculement sur le serveur secondaire, puis effectuer une restauration automatique en utilisant le portail Azure. 

# <a name="azure-portaltabazure-portal"></a>[Portail Azure](#tab/azure-portal)
Testez le basculement en utilisant le portail Azure. 

1. Accédez à votre serveur **SQL** dans le [portail Azure](https://portal.azure.com). 
1. Sélectionnez **Groupes de basculement** dans le volet **Paramètres**, puis choisissez le groupe de basculement créé dans la section 2. 
  
   ![Sélectionner le groupe de basculement à partir du portail](media/sql-database-single-database-create-failover-group-tutorial/select-failover-group.png)

1. Vérifiez quel est le serveur principal et quel est le serveur secondaire. 
1. Sélectionnez **Basculer** dans le volet des tâches pour faire basculer votre groupe de basculement contenant votre exemple de base de données unique. 
1. Sélectionnez **Oui** en réponse à l’avertissement qui signale que les sessions TDS seront déconnectées. 

   ![Basculer votre groupe de basculement contenant votre base de données SQL](media/sql-database-single-database-create-failover-group-tutorial/failover-sql-db.png)

1. Vérifiez quel est maintenant le serveur principal et quel est le serveur secondaire. Si le basculement a réussi, les deux serveurs doivent avoir échangé leur rôle. 
1. Resélectionnez **Basculer** pour rétablir les serveurs dans leur rôle d’origine. 

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
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
   Write-host "Failed failover group to sucessfully to" $drServerName 
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
   Write-host "Failed failover group to successfully to back to" $serverName
   ```

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)
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

---

## <a name="clean-up-resources"></a>Supprimer des ressources 
Nettoyez les ressources en supprimant le groupe de ressources. 

# <a name="azure-portaltabazure-portal"></a>[Portail Azure](#tab/azure-portal)
Supprimez le groupe de ressources en utilisant le portail Azure. 


1. Accédez à votre groupe de ressources sur le [portail Azure](https://portal.azure.com).
1. Sélectionnez **Supprimer le groupe de ressources** pour supprimer toutes les ressources du groupe ainsi que le groupe de ressources lui-même. 
1. Tapez le nom du groupe de ressources, `myResourceGroup`, dans la zone de texte, puis sélectionnez **Supprimer** pour supprimer le groupe de ressources.  

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Supprimez le groupe de ressources en utilisant PowerShell. 


   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Remove the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)
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

---


## <a name="full-scripts"></a>Scripts complets

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add single database to a failover group")]

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

[!code-azurecli-interactive[main](../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Create SQL Database")]

# <a name="azure-portaltabazure-portal"></a>[Portail Azure](#tab/azure-portal)
Aucun script n’est disponible pour le portail Azure.
 
---

Vous trouverez d’autres scripts Azure SQL Database ici : [Azure PowerShell](sql-database-powershell-samples.md) et [Azure CLI](sql-database-cli-samples.md). 

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez ajouté une base de données unique Azure SQL Database à un groupe de basculement et vous avez testé le basculement. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> - Créer une base de données unique Azure SQL Database. 
> - Créer un [groupe de basculement](sql-database-auto-failover-group.md) pour une base de données unique entre deux serveurs SQL logiques.
> - Tester le basculement.

Passez au didacticiel suivant pour découvrir comment ajouter votre pool élastique à un groupe de basculement. 

> [!div class="nextstepaction"]
> [Tutoriel : Ajouter un pool élastique Azure SQL Database à un groupe de basculement](sql-database-elastic-pool-failover-group-tutorial.md)
