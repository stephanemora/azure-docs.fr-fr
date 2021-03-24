---
title: Configurer la fenêtre de maintenance (préversion)
description: Découvrez comment définir l’heure à laquelle la maintenance planifiée doit être effectuée sur vos bases de données Azure SQL, pools élastiques et bases de données d’instances managées.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/04/2021
ms.openlocfilehash: 210f0c52a2b27492bfa2181473043df3537157d2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102183197"
---
# <a name="configure-maintenance-window-preview"></a>Configurer la fenêtre de maintenance (préversion)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]


Vous pouvez configurer la [fenêtre de maintenance (préversion)](maintenance-window.md) d’une base de données Azure SQL, d’un pool élastique ou d’une base de données Azure SQL Managed Instance lors de la création d’une ressource ou à tout moment après la création d’une ressource. 

La fenêtre de maintenance *par défaut du système* est comprise entre 17h00 et 8h00 (heure locale de la région Azure où se trouve la ressource) afin d’éviter toute interruption durant les heures de pointe. Si la fenêtre de maintenance *par défaut du système* n’est pas le meilleur créneau, sélectionnez l’une des autres fenêtres de maintenance disponibles.

La capacité à choisir une autre fenêtre de maintenance n’est pas disponible pour tous les niveaux de service ni toutes les régions. Pour plus d’informations sur la disponibilité, consultez [Disponibilité de la fenêtre de maintenance](maintenance-window.md#availability).

> [!Important]
> La configuration de la fenêtre de maintenance est une opération asynchrone durable, similaire à la modification du niveau de service de la ressource Azure SQL. La ressource est disponible pendant l’opération, à l’exception d’un basculement rapide qui se produit à la fin de l’opération et qui dure généralement jusqu’à 8 secondes, même en cas de transactions durables interrompues. Pour réduire l’impact du basculement, vous devez effectuer l’opération en dehors des heures de pointe.

## <a name="configure-maintenance-window-during-database-creation"></a>Configurer la fenêtre de maintenance lors de la création de la base de données 

# <a name="portal"></a>[Portail](#tab/azure-portal)

Pour configurer la fenêtre de maintenance lors de la création d’une base de données, d’un pool élastique ou d’une instance managée, définissez la **Fenêtre de maintenance** souhaitée dans la page **Paramètres supplémentaires**. 

## <a name="set-the-maintenance-window-while-creating-a-single-database-or-elastic-pool"></a>Définir la fenêtre de maintenance lors de la création d’une base de données unique ou d’un pool élastique

Pour obtenir des informations pas à pas sur la création d’une base de données ou d’un pool, consultez [Créer une base de données unique Azure SQL Database](single-database-create-quickstart.md).

   :::image type="content" source="media/maintenance-window-configure/additional-settings.png" alt-text="Créer une base de données - Onglet Paramètres supplémentaires":::


## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>Définir la fenêtre de maintenance lors de la création d’une instance managée

Pour obtenir des informations pas à pas sur la création d’une instance managée, consultez [Créer une instance managée Azure SQL](../managed-instance/instance-create-quickstart.md).

   :::image type="content" source="media/maintenance-window-configure/additional-settings-mi.png" alt-text="Créer une instance managée - Onglet Paramètres supplémentaires":::




# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Les exemples suivants montrent comment configurer la fenêtre de maintenance à l’aide d’Azure PowerShell. Vous pouvez [installer Azure PowerShell](/powershell/azure/install-az-ps) ou utiliser Azure Cloud Shell.

## <a name="launch-azure-cloud-shell"></a>Lancement d’Azure Cloud Shell

Azure Cloud Shell est un interpréteur de commandes interactif et gratuit que vous pouvez utiliser pour exécuter les étapes de cet article. Il contient des outils Azure courants préinstallés et configurés pour être utilisés avec votre compte. 

Pour ouvrir Cloud Shell, sélectionnez simplement **Essayer** en haut à droite d’un bloc de code. Vous pouvez également lancer Cloud Shell dans un onglet distinct du navigateur en accédant à [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Sélectionnez **Copier** pour copier les blocs de code, collez-les dans Cloud Shell, puis appuyez sur Entrée pour les exécuter.


## <a name="discover-available-maintenance-windows"></a>Découvrir les fenêtres de maintenance disponibles

Lors de la définition de la fenêtre de maintenance, chaque région a ses propres options de fenêtre de maintenance correspondant au fuseau horaire de la région où se trouve la base de données ou le pool. 

### <a name="discover-sql-database-and-elastic-pool-maintenance-windows"></a>Découvrir les fenêtres de maintenance de SQL Database et de pool élastique 

L’exemple suivant retourne les fenêtres de maintenance disponibles pour la région *eastus2* à l’aide de l’applet de commande [Get-AzMaintenancePublicConfiguration](/powershell/module/az.maintenance/get-azmaintenancepublicconfiguration). Pour les bases de données et les pools élastiques, affectez la valeur `SQLDB` à `MaintenanceScope`.

   ```powershell-interactive
   $location = "eastus2"

   Write-Host "Available maintenance schedules in ${location}:"
   $configurations = Get-AzMaintenancePublicConfiguration
   $configurations | ?{ $_.Location -eq $location -and $_.MaintenanceScope -eq "SQLDB"}
   ```

### <a name="discover-sql-managed-instance-maintenance-windows"></a>Découvrir les fenêtres de maintenance de SQL Managed Instance 

L’exemple suivant retourne les fenêtres de maintenance disponibles pour la région *eastus2* à l’aide de l’applet de commande [Get-AzMaintenancePublicConfiguration](/powershell/module/az.maintenance/get-azmaintenancepublicconfiguration). Pour les instances managées, affectez à la valeur `SQLManagedInstance` à `MaintenanceScope`.

   ```powershell-interactive
   $location = "eastus2"

   Write-Host "Available maintenance schedules in ${location}:"
   $configurations = Get-AzMaintenancePublicConfiguration
   $configurations | ?{ $_.Location -eq $location -and $_.MaintenanceScope -eq "SQLManagedInstance"}
   ```


## <a name="set-the-maintenance-window-while-creating-a-single-database"></a>Définir la fenêtre de maintenance lors de la création d’une base de données unique

L’exemple suivant crée une base de données et définit la fenêtre de maintenance à l’aide de l’applet de commande [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase). Vous devez affecter à `-MaintenanceConfigurationId` une valeur valide pour la région de votre base de données. Pour obtenir les valeurs valides pour votre région, consultez [Découvrir les fenêtres de maintenance disponibles](#discover-available-maintenance-windows).


   ```powershell-interactive
    # Set variables for your database
    $resourceGroupName = "your_resource_group_name"
    $serverName = "your_server_name"
    $databaseName = "your_db_name"
    
    # Set selected maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_1"

    Write-host "Creating a gen5 2 vCore database with maintenance window ${maintenanceConfig} ..."
    $database = New-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -Edition GeneralPurpose `
      -ComputeGeneration Gen5 `
      -VCore 2 `
      -MaintenanceConfigurationId $maintenanceConfig
    $database
   ```



## <a name="set-the-maintenance-window-while-creating-an-elastic-pool"></a>Définir la fenêtre de maintenance lors de la création d’un pool élastique

L’exemple suivant crée un pool élastique et définit la fenêtre de maintenance à l’aide de l’applet de commande [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool). La fenêtre de maintenance étant définie sur le pool élastique, toutes les bases de données du pool ont la planification de la fenêtre de maintenance du pool. Vous devez affecter à `-MaintenanceConfigurationId` une valeur valide pour la région de votre pool. Pour obtenir les valeurs valides pour votre région, consultez [Découvrir les fenêtres de maintenance disponibles](#discover-available-maintenance-windows).


   ```powershell-interactive
    # Set variables for your pool
    $resourceGroupName = "your_resource_group_name"
    $serverName = "your_server_name"
    $poolName = "your_pool_name"
    
    # Set selected maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_2"

    Write-host "Creating a Standard 50 pool with maintenance window ${maintenanceConfig} ..."
    $pool = New-AzSqlElasticPool `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -ElasticPoolName $poolName `
      -Edition "Standard" `
      -Dtu 50 `
      -DatabaseDtuMin 10 `
      -DatabaseDtuMax 20 `
      -MaintenanceConfigurationId $maintenanceConfig
    $pool
   ```

## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>Définir la fenêtre de maintenance lors de la création d’une instance managée

L’exemple suivant crée une instance managée et définit la fenêtre de maintenance à l’aide de l’applet de commande [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance). La fenêtre de maintenance étant définie sur l’instance, toutes les bases de données de l’instance ont la planification de la fenêtre de maintenance de l’instance. Pour `-MaintenanceConfigurationId`, *MaintenanceConfigName* doit être une valeur valide pour la région de votre instance. Pour obtenir les valeurs valides pour votre région, consultez [Découvrir les fenêtres de maintenance disponibles](#discover-available-maintenance-windows).


   ```powershell
   New-AzSqlInstance -Name "your_mi_name" `
     -ResourceGroupName "your_resource_group_name" `
     -Location "your_mi_location" `
     -SubnetId /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNETName}/subnets/{SubnetName} `
     -MaintenanceConfigurationId "/subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}" `
     -AsJob
   ```

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/azure-cli)

Les exemples suivants montrent comment configurer la fenêtre de maintenance à l’aide d’Azure CLI. Vous pouvez [installer Azure CLI](/cli/azure/install-azure-cli) ou utiliser Azure Cloud Shell. 

La configuration de la fenêtre de maintenance avec Azure CLI n’est disponible que pour SQL Managed Instance.

## <a name="launch-azure-cloud-shell"></a>Lancement d’Azure Cloud Shell

Azure Cloud Shell est un interpréteur de commandes interactif et gratuit que vous pouvez utiliser pour exécuter les étapes de cet article. Il contient des outils Azure courants préinstallés et configurés pour être utilisés avec votre compte. 

Pour ouvrir Cloud Shell, sélectionnez simplement **Essayer** en haut à droite d’un bloc de code. Vous pouvez également lancer Cloud Shell dans un onglet distinct du navigateur en accédant à [https://shell.azure.com/cli](https://shell.azure.com/cli). Sélectionnez **Copier** pour copier les blocs de code, collez-les dans Cloud Shell, puis appuyez sur Entrée pour les exécuter.

## <a name="discover-available-maintenance-windows"></a>Découvrir les fenêtres de maintenance disponibles

Lors de la définition de la fenêtre de maintenance, chaque région a ses propres options de fenêtre de maintenance correspondant au fuseau horaire de la région où se trouve la base de données ou le pool.

### <a name="discover-sql-database-and-elastic-pool-maintenance-windows"></a>Découvrir les fenêtres de maintenance de SQL Database et de pool élastique

L’exemple suivant retourne les fenêtres de maintenance disponibles pour la région *eastus2* à l’aide de la commande [az maintenance public-configuration list](/cli/azure/ext/maintenance/maintenance/public-configuration#ext_maintenance_az_maintenance_public_configuration_list). Pour les bases de données et les pools élastiques, affectez la valeur `SQLDB` à `maintenanceScope`.

   ```azurecli
   location="eastus2"

   az maintenance public-configuration list --query "[?location=='$location'&&contains(maintenanceScope,'SQLDB')]"
   ```

### <a name="discover-sql-managed-instance-maintenance-windows"></a>Découvrir les fenêtres de maintenance de SQL Managed Instance

L’exemple suivant retourne les fenêtres de maintenance disponibles pour la région *eastus2* à l’aide de la commande [az maintenance public-configuration list](/cli/azure/ext/maintenance/maintenance/public-configuration#ext_maintenance_az_maintenance_public_configuration_list). Pour les instances managées, affectez à la valeur `SQLManagedInstance` à `maintenanceScope`.

   ```azurecli
   az maintenance public-configuration list --query "[?location=='eastus2'&&contains(maintenanceScope,'SQLManagedInstance')]"
   ```

## <a name="set-the-maintenance-window-while-creating-a-single-database"></a>Définir la fenêtre de maintenance lors de la création d’une base de données unique

L’exemple suivant crée une base de données et définit la fenêtre de maintenance à l’aide de la commande [az sql db create](/cli/azure/sql/db#az_sql_db_create). Vous devez affecter à `--maint-config-id` (ou `-m`) une valeur valide pour la région de votre base de données. Pour obtenir les valeurs valides pour votre région, consultez [Découvrir les fenêtres de maintenance disponibles](#discover-available-maintenance-windows).


   ```azurecli
    # Set variables for your database
    resourceGroupName="your_resource_group_name"
    serverName="your_server_name"
    databaseName="your_db_name"

    # Set selected maintenance window
    maintenanceConfig="SQL_EastUS2_DB_1"

    # Create database
    az sql db create \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName \
      --edition GeneralPurpose \
      --family Gen5 \
      --capacity 2 \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-while-creating-an-elastic-pool"></a>Définir la fenêtre de maintenance lors de la création d’un pool élastique

L’exemple suivant crée un pool élastique et définit la fenêtre de maintenance à l’aide de l’applet de commande [az sql elastic-pool create](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_create). La fenêtre de maintenance étant définie sur le pool élastique, toutes les bases de données du pool ont la planification de la fenêtre de maintenance du pool. Vous devez affecter à `--maint-config-id` (ou `-m`) une valeur valide pour la région de votre pool. Pour obtenir les valeurs valides pour votre région, consultez [Découvrir les fenêtres de maintenance disponibles](#discover-available-maintenance-windows).


   ```azurecli
    # Set variables for your pool
    resourceGroupName="your_resource_group_name"
    serverName="your_server_name"
    poolName="your_pool_name"

    # Set selected maintenance window
    maintenanceConfig="SQL_EastUS2_DB_2"

    # Create elastic pool
    az sql elastic-pool create \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $poolName \
      --edition GeneralPurpose \
      --family Gen5 \
      --capacity 2 \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>Définir la fenêtre de maintenance lors de la création d’une instance managée

L’exemple suivant crée une instance managée et définit la fenêtre de maintenance à l’aide de la commande [az sql mi create](/cli/azure/sql/mi#az_sql_mi_create). La fenêtre de maintenance étant définie sur l’instance, toutes les bases de données de l’instance ont la planification de la fenêtre de maintenance de l’instance. *MaintenanceConfigName* doit être une valeur valide pour la région de votre instance. Pour obtenir les valeurs valides pour votre région, consultez [Découvrir les fenêtres de maintenance disponibles](#discover-available-maintenance-windows).

   ```azurecli
   az sql mi create -g mygroup -n myinstance -l mylocation -i -u myusername -p mypassword --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNETName}/subnets/{SubnetName} -m /subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}
   ```

-----

## <a name="configure-maintenance-window-for-existing-databases"></a>Configurer la fenêtre de maintenance pour des bases de données existantes


Lors de l’application d’une sélection de fenêtre de maintenance à une base de données, vous pourrez observer dans certains cas un court basculement (plusieurs secondes), durant lequel Azure applique les modifications requises.

# <a name="portal"></a>[Portail](#tab/azure-portal)

Les étapes suivantes définissent la fenêtre de maintenance sur une base de données, un pool élastique ou une instance managée existant(e) à l’aide du portail Azure :


## <a name="set-the-maintenance-window-for-an-existing-database-or-elastic-pool"></a>Définir la fenêtre de maintenance d’une base de données ou d’un pool élastique existant

1. Accédez à la base de données SQL ou au pool élastique pour lequel vous souhaitez définir la fenêtre de maintenance.
1. Dans le menu **Paramètres**, sélectionnez **Maintenance**, puis sélectionnez la fenêtre de maintenance souhaitée.

   :::image type="content" source="media/maintenance-window-configure/maintenance.png" alt-text="Page Maintenance de base de données SQL":::


## <a name="set-the-maintenance-window-for-an-existing-managed-instance"></a>Définir la fenêtre de maintenance pour une instance managée existante

1. Accédez à l’instance managée pour laquelle vous souhaitez définir la fenêtre de maintenance.
1. Dans le menu **Paramètres**, sélectionnez **Maintenance**, puis sélectionnez la fenêtre de maintenance souhaitée.

   :::image type="content" source="media/maintenance-window-configure/maintenance-mi.png" alt-text="Page Maintenance de l’instance gérée SQL":::



# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="set-the-maintenance-window-for-an-existing-database"></a>Définir la fenêtre de maintenance pour une base de données existante

L’exemple suivant définit la fenêtre de maintenance sur une base de données existante à l’aide de l’applet de commande [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase). Vous devez affecter à `-MaintenanceConfigurationId` une valeur valide pour la région de votre base de données. Pour obtenir les valeurs valides pour votre région, consultez [Découvrir les fenêtres de maintenance disponibles](#discover-available-maintenance-windows).

   ```powershell-interactive
    # Select different maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_2"

    Write-host "Changing database maintenance window to ${maintenanceConfig} ..."
    $database = Set-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -MaintenanceConfigurationId $maintenanceConfig
    $database
   ```

## <a name="set-the-maintenance-window-on-an-existing-elastic-pool"></a>Définir la fenêtre de maintenance sur un pool élastique existant

L’exemple suivant définit la fenêtre de maintenance sur un pool élastique existant à l’aide de l’applet de commande [Set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool). Il est important de vérifier que la valeur `$maintenanceConfig` est une valeur valide pour la région de votre pool.  Pour obtenir les valeurs valides d’une région, consultez [Découvrir les fenêtres de maintenance disponibles](#discover-available-maintenance-windows).

   ```powershell-interactive
    # Select different maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_1"
    
    Write-host "Changing pool maintenance window to ${maintenanceConfig} ..."
    $pool = Set-AzSqlElasticPool `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -ElasticPoolName $poolName `
      -MaintenanceConfigurationId $maintenanceConfig
    $pool
   ```



## <a name="set-the-maintenance-window-on-an-existing-managed-instance"></a>Définir la fenêtre de maintenance sur une instance managée existante

L’exemple suivant définit la fenêtre de maintenance sur une instance managée existante à l’aide de l’applet de commande [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance). Il est important de vérifier que la valeur `$maintenanceConfig` est une valeur valide pour la région de votre instance.  Pour obtenir les valeurs valides d’une région, consultez [Découvrir les fenêtres de maintenance disponibles](#discover-available-maintenance-windows).


   ```powershell-interactive
   Set-AzSqlInstance -Name "your_mi_name" `
     -ResourceGroupName "your_resource_group_name" `
     -MaintenanceConfigurationId "/subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}" `
     -AsJob
   ```

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/azure-cli)

Les exemples suivants montrent comment configurer la fenêtre de maintenance à l’aide d’Azure CLI. Vous pouvez [installer Azure CLI](/cli/azure/install-azure-cli) ou utiliser Azure Cloud Shell.

## <a name="set-the-maintenance-window-for-an-existing-database"></a>Définir la fenêtre de maintenance pour une base de données existante

L’exemple suivant définit la fenêtre de maintenance sur une base de données existante à l’aide de la commande [az sql db update](/cli/azure/sql/db#az_sql_db_update). Vous devez affecter à `--maint-config-id` (ou `-m`) une valeur valide pour la région de votre base de données. Pour obtenir les valeurs valides pour votre région, consultez [Découvrir les fenêtres de maintenance disponibles](#discover-available-maintenance-windows).

   ```azurecli
    # Select different maintenance window
    maintenanceConfig="SQL_EastUS2_DB_2"

    # Update database
    az sql db update \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-on-an-existing-elastic-pool"></a>Définir la fenêtre de maintenance sur un pool élastique existant

L’exemple suivant définit la fenêtre de maintenance sur un pool élastique existant à l’aide de la commande [az sql elastic-pool update](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update). Il est important de vérifier que la valeur `maintenanceConfig` est une valeur valide pour la région de votre pool.  Pour obtenir les valeurs valides d’une région, consultez [Découvrir les fenêtres de maintenance disponibles](#discover-available-maintenance-windows).

   ```azurecli
    # Select different maintenance window
    maintenanceConfig="SQL_EastUS2_DB_1"

    # Update pool
    az sql elastic-pool update \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $poolName \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-on-an-existing-managed-instance"></a>Définir la fenêtre de maintenance sur une instance managée existante

L’exemple suivant définit la fenêtre de maintenance à l’aide de la commande [az sql mi update](/cli/azure/sql/mi#az_sql_mi_update). La fenêtre de maintenance étant définie sur l’instance, toutes les bases de données de l’instance ont la planification de la fenêtre de maintenance de l’instance. Pour `-MaintenanceConfigurationId`, *MaintenanceConfigName* doit être une valeur valide pour la région de votre instance. Pour obtenir les valeurs valides pour votre région, consultez [Découvrir les fenêtres de maintenance disponibles](#discover-available-maintenance-windows).

   ```azurecli
   az sql mi update -g mygroup  -n myinstance -m /subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MainteanceConfigName}
   ```

-----

## <a name="cleanup-resources"></a>Nettoyer les ressources

Veillez à supprimer les ressources inutiles une fois que vous en avez terminé avec elles, afin d’éviter tous frais inutiles.

# <a name="portal"></a>[Portail](#tab/azure-portal)

1. Accédez à la base de données SQL ou au pool élastique dont vous n’avez plus besoin.
1. Dans le menu **Vue d’ensemble**, sélectionnez l’option permettant de supprimer la ressource.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   # Delete database
   Remove-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName

   # Delete elastic pool
   Remove-AzSqlElasticPool `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -ElasticPoolName $poolName
   ```

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/azure-cli)

   ```azurecli
   az sql db delete \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName

   az sql elastic-pool delete \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $poolName
   ```

-----

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la fenêtre de maintenance, consultez [Fenêtre de maintenance (préversion)](maintenance-window.md).
- Pour plus d’informations, consultez [FAQ sur la fenêtre de maintenance](maintenance-window-faq.yml).
- Pour en savoir plus sur l’optimisation des performances, consultez [Supervision et réglage des performances dans Azure SQL Database et Azure SQL Managed Instance](monitor-tune-overview.md).
