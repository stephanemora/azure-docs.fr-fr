---
title: Importer des fichiers SQL BACPAC avec des modèles
description: Découvrez comment utiliser l’extension SQL Database pour importer des fichiers SQL BACPAC avec des modèles Azure Resource Manager.
author: mumian
ms.date: 11/21/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 741521551335712400e5f61822d7dda31199d3df
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422174"
---
# <a name="tutorial-import-sql-bacpac-files-with-azure-resource-manager-templates"></a>Tutoriel : Importer des fichiers SQL BACPAC avec des modèles Azure Resource Manager

Découvrez comment utiliser les extensions Azure SQL Database pour importer un fichier SQL BACPAC avec des modèles Azure Resource Manager. Les artefacts de déploiement correspondent à tous les fichiers, en plus des principaux fichiers modèles requis pour effectuer un déploiement. Le fichier BACPAC est un artefact. Dans ce tutoriel, vous créez un modèle pour déployer un SQL Server Azure, une SQL Database et importer un fichier BACPAC. Pour plus d’informations sur le déploiement des extensions de machines virtuelles Azure à l’aide de modèles Azure Resource Manager, consultez [Tutoriel : Déployer des extensions de machines virtuelles avec des modèles Azure Resource Manager](./resource-manager-tutorial-deploy-vm-extensions.md).

Ce tutoriel décrit les tâches suivantes :

> [!div class="checklist"]
> * Préparer un fichier BACPAC
> * Ouvrir un modèle de démarrage rapide
> * Modifier le modèle
> * Déployer le modèle
> * Vérifier le déploiement

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce qui est décrit dans cet article, vous avez besoin des éléments suivants :

* Visual Studio Code avec l’extension Outils Resource Manager. Consultez [Utiliser Visual Studio Code pour créer des modèles Azure Resource Manager](./resource-manager-tools-vs-code.md).
* Pour une sécurité optimale, utilisez un mot de passe généré pour le compte administrateur de SQL Server. Voici un exemple pour générer un mot de passe :

    ```azurecli-interactive
    openssl rand -base64 32
    ```

    Azure Key Vault a été conçu pour protéger les clés et autres secrets de chiffrement. Pour plus d’informations, consultez [Tutoriel : Intégrer Azure Key Vault à un déploiement de modèle Resource Manager](./resource-manager-tutorial-use-key-vault.md). Nous vous recommandons également de mettre à jour votre mot de passe tous les trois mois.

## <a name="prepare-a-bacpac-file"></a>Préparer un fichier BACPAC

Un fichier BACPAC est partagé dans [GitHub](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac). Pour créer le vôtre, consultez [Exporter une base de données Azure SQL dans un fichier BACPAC](../sql-database/sql-database-export.md). Si vous choisissez de publier le fichier sur votre propre emplacement, vous devez mettre à jour le modèle plus tard dans ce didacticiel.

Le fichier BACPAC doit être stocké dans un compte de stockage Azure avant de pouvoir être importé à l’aide d’un modèle Resource Manager.

1. Ouvrez l’invite de commandes [Cloud Shell](https://shell.azure.com).
1. Sélectionnez **Charger/Télécharger des fichiers**, puis **Charger**.
1. Spécifiez l’URL suivante, puis sélectionnez **Ouvrir**.

    ```url
    https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac
    ```

1. Copiez et collez le script PowerShell suivant dans la fenêtre de l’interpréteur de commandes.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

    $resourceGroupName = "${projectName}rg"
    $storageAccountName = "${projectName}store"
    $containerName = "bacpacfiles"
    $bacpacFile = "$HOME/SQLDatabaseExtension.bacpac"
    $blobName = "SQLDatabaseExtension.bacpac"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
                                           -Name $storageAccountName `
                                           -SkuName Standard_LRS `
                                           -Location $location
    $storageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName `
                                                  -Name $storageAccountName).Value[0]

    New-AzStorageContainer -Name $containerName -Context $storageAccount.Context

    Set-AzStorageBlobContent -File $bacpacFile `
                             -Container $containerName `
                             -Blob $blobName `
                             -Context $storageAccount.Context

    Write-Host "The storage account key is $storageAccountKey"
    Write-Host "The BACPAC file URL is https://$storageAccountName.blob.core.windows.net/$containerName/$blobName"
    Write-Host "Press [ENTER] to continue ..."
    ```

1. Notez la clé du compte de stockage et l’URL du fichier BACPAC. Vous avez besoin de ces valeurs lorsque vous déployez le modèle.

## <a name="open-a-quickstart-template"></a>Ouvrir un modèle de démarrage rapide

Le modèle utilisé dans ce tutoriel est stocké dans [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. À partir de Visual Studio Code, sélectionnez **Fichier**>**Ouvrir un fichier**.
2. Collez l’URL suivante dans **Nom de fichier** :

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```

3. Sélectionnez **Ouvrir** pour ouvrir le fichier.

    Trois ressources sont définies dans le modèle :

   * `Microsoft.Sql/servers`. Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/microsoft.sql/servers).
   * `Microsoft.SQL/servers/securityAlertPolicies`. Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/securityalertpolicies).
   * `Microsoft.SQL.servers/databases`.  Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).

     Il est préférable de comprendre quelques notions basiques du modèle avant de le personnaliser.
4. Sélectionnez **Fichier**>**Enregistrer sous** pour enregistrer une copie du fichier sur votre ordinateur local avec le nom **azuredeploy.json**.

## <a name="edit-the-template"></a>Modifier le modèle

1. Ajoutez deux paramètres supplémentaires à la fin de la section des **paramètres** pour définir la clé de compte de stockage et l’URL BACPAC :

    ```json
    "storageAccountKey": {
      "type":"string",
      "metadata":{
        "description": "Specifies the key of the storage account where the BACPAC file is stored."
      }
    },
    "bacpacUrl": {
      "type":"string",
      "metadata":{
        "description": "Specifies the URL of the BACPAC file."
      }
    }
    ```

    Ajoutez une virgule après **adminPassword**. Pour mettre en forme le fichier JSON à partir de VS Code, appuyez sur **[MAJ]+[ALT]+F**.

    Consultez [Préparer un fichier BACPAC](#prepare-a-bacpac-file) sur l’obtention de ces deux valeurs.

1. Ajoutez deux ressources supplémentaires au modèle.

    * Pour permettre à l’extension de base de données SQL d’importer des fichiers BACPAC, vous devez autoriser le trafic à partir des services Azure. Ajoutez la définition de règle de pare-feu suivante sous la définition SQL Server :

        ```json
        {
          "type": "firewallrules",
          "apiVersion": "2015-05-01-preview",
          "name": "AllowAllAzureIps",
          "location": "[parameters('location')]",
          "dependsOn": [
            "[variables('databaseServerName')]"
          ],
          "properties": {
            "startIpAddress": "0.0.0.0",
            "endIpAddress": "0.0.0.0"
          }
        }
        ```

        Le modèle doit ressembler à ce qui suit :

        ![Azure Resource Manager deploy sql extensions BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-firewall.png)

    * Ajoutez une ressource d’extension SQL Database à la définition de base de données avec le code JSON suivant :

        ```json
        "resources": [
            {
              "type": "extensions",
              "apiVersion": "2014-04-01",
              "name": "Import",
              "dependsOn": [
                "[resourceId('Microsoft.Sql/servers/databases', variables('databaseServerName'), variables('databaseName'))]"
              ],
              "properties": {
                "storageKeyType": "StorageAccessKey",
                "storageKey": "[parameters('storageAccountKey')]",
                "storageUri": "[parameters('bacpacUrl')]",
                "administratorLogin": "[variables('databaseServerAdminLogin')]",
                "administratorLoginPassword": "[variables('databaseServerAdminLoginPassword')]",
                "operationMode": "Import"
              }
            }
        ]
        ```

        Le modèle doit ressembler à ce qui suit :

        ![Azure Resource Manager deploy sql extensions BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png)

        Pour comprendre la définition de ressource, consultez la [référence de l’extension SQL Database](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases/extensions). Voici quelques éléments importants :

        * **dependsOn** : la ressource d’extension doit être créée une fois que la base de données SQL a été créée.
        * **storageKeyType** : spécifiez le type de la clé de stockage à utiliser. La valeur peut être `StorageAccessKey` ou `SharedAccessKey`. Utilisez `StorageAccessKey` dans ce tutoriel.
        * **storageKey** : Spécifiez la clé pour le compte de stockage où est stocké le fichier BACPAC. Si le type de clé de stockage est SharedAccessKey, il doit être précédé de « ? »
        * **storageUri** : spécifiez l’URL du fichier BACPAC stocké dans un compte de stockage.
        * **administratorLoginPassword** : mot de passe de l'administrateur SQL. Utilisez un mot de passe généré. Consultez les [Conditions préalables](#prerequisites).

## <a name="deploy-the-template"></a>Déployer le modèle

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Reportez-vous à la section [Déployer le modèle](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) pour connaître la procédure de déploiement. Utilisez plutôt le script de déploiement PowerShell suivant :

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the SQL admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$storageAccountKey = Read-Host -Prompt "Enter the storage account key"
$bacpacUrl = Read-Host -Prompt "Enter the URL of the BACPAC file"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -TemplateFile "$HOME/azuredeploy.json" `
    -storageAccountKey $storageAccountKey `
    -bacpacUrl $bacpacUrl

Write-Host "Press [ENTER] to continue ..."
```

Envisagez d’utiliser le même nom de projet que celui que vous avez utilisé lors de la préparation du fichier bacpac afin que toutes les ressources soient stockées dans le même groupe de ressources.  Il est plus facile de gérer les ressources, comme le nettoyage des ressources. Si vous utilisez le même nom de projet, vous pouvez soit supprimer la commande **New-AzResourceGroup** du script, soit répondre o ou n lorsque vous êtes invité à mettre à jour le groupe de ressources existant.

Utilisez un mot de passe généré. Consultez les [Conditions préalables](#prerequisites).

## <a name="verify-the-deployment"></a>Vérifier le déploiement

Pour accéder à SQL Server à partir de votre ordinateur client, vous devez ajouter une règle de pare-feu supplémentaire. Pour plus d’informations, consultez [Créer et gérer des règles de pare-feu IP](../sql-database/sql-database-firewall-configure.md#create-and-manage-ip-firewall-rules).

Dans le portail, sélectionnez la base de données SQL dans le nouveau groupe de ressources déployé. Sélectionnez l’**éditeur de requêtes (préversion)** , puis entrez les informations d’identification d’administrateur. Vous devez voir deux tables importées dans la base de données :

![Azure Resource Manager deploy sql extensions BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, nettoyez les ressources Azure que vous avez déployées en supprimant le groupe de ressources.

1. Dans le portail Azure, sélectionnez **Groupe de ressources** dans le menu de gauche.
2. Entrez le nom du groupe de ressources dans le champ **Filtrer par nom**.
3. Sélectionnez le nom du groupe de ressources.  Vous devriez voir six ressources au total dans le groupe de ressources.
4. Sélectionnez **Supprimer le groupe de ressources** dans le menu supérieur.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez déployé un SQL Server, une SQL Database, et avez importé un fichier BACPAC. Le fichier BACPAC est stocké dans un compte de stockage Azure. Toute personne en possession de l'URL peut y accéder. Pour savoir comment sécuriser le fichier BACPAC (artefact), consultez

> [!div class="nextstepaction"]
> [Sécuriser des artefacts](./resource-manager-tutorial-secure-artifacts.md)
