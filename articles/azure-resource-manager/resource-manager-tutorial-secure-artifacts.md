---
title: Sécuriser les artefacts dans les modèles
description: Apprenez à sécuriser les artefacts utilisés dans vos modèles Azure Resource Manager.
author: mumian
ms.date: 12/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 1a9d209e843d8e9a1735a3c6907b00d85be6580b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74971727"
---
# <a name="tutorial-secure-artifacts-in-azure-resource-manager-template-deployments"></a>Tutoriel : Sécuriser des artefacts dans les déploiements de modèles Azure Resource Manager

Apprenez à sécuriser les artefacts utilisés dans vos modèles Azure Resource Manager à l’aide d'un compte de stockage Azure avec signatures d’accès partagé (SAP). Les artefacts de déploiement correspondent à tous les fichiers, en plus du fichier de modèle principal, requis pour effectuer un déploiement. Par exemple, dans [Tutoriel : Importer des fichiers SQL BACPAC avec des modèles Azure Resource Manager](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md), le modèle principal crée une base de données Azure SQL. Il appelle également un fichier BACPAC pour créer des tables et insérer des données. Le fichier BACPAC est un artefact qui est stocké dans un compte de stockage Azure. La clé de compte de stockage a été utilisée pour accéder à l’artefact. Dans ce tutoriel, vous utilisez les signatures d'accès partagé pour accorder un accès limité au fichier BACPAC de votre propre compte de stockage Azure. Pour plus d’informations sur les signatures d’accès partagé, consultez [Utilisation des signatures d’accès partagé (SAP)](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

Pour savoir comment sécuriser le modèle lié, consultez [Tutoriel : Créer des modèles Azure Resource Manager liés](./resource-manager-tutorial-create-linked-templates.md).

Ce tutoriel décrit les tâches suivantes :

> [!div class="checklist"]
> * Préparer un fichier BACPAC
> * Ouvrir un modèle existant
> * Modifier le modèle
> * Déployer le modèle
> * Vérifier le déploiement

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce qui est décrit dans cet article, vous avez besoin des éléments suivants :

* Visual Studio Code avec l’extension Outils Resource Manager. Consultez [Utiliser Visual Studio Code pour créer des modèles Azure Resource Manager](./resource-manager-tools-vs-code.md).
* Consultez [Tutoriel : Importer des fichiers SQL BACPAC avec des modèles Azure Resource Manager](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md). Le modèle utilisé dans ce tutoriel est celui développé dans ce tutoriel. Un lien de téléchargement du modèle complet est fourni dans cet article.
* Pour une sécurité optimale, utilisez un mot de passe généré pour le compte administrateur de SQL Server. Voici un exemple pour générer un mot de passe :

    ```azurecli-interactive
    openssl rand -base64 32
    ```

    Azure Key Vault a été conçu pour protéger les clés et autres secrets de chiffrement. Pour plus d’informations, consultez [Tutoriel : Intégrer Azure Key Vault à un déploiement de modèle Resource Manager](./resource-manager-tutorial-use-key-vault.md). Nous vous recommandons également de mettre à jour votre mot de passe tous les trois mois.

## <a name="prepare-a-bacpac-file"></a>Préparer un fichier BACPAC

Dans cette section, vous préparez le fichier BACPAC de manière à ce qu'il soit accessible, en toute sécurité, lorsque vous déployez le modèle Resource Manager. Cette section compte cinq procédures :

* Télécharger le fichier BACPAC.
* Création d’un compte Azure Storage.
* Créer un conteneur d’objets blob dans un compte de stockage.
* Télécharger le fichier BACPAC dans le conteneur.
* Récupérer le jeton SAS du fichier BACPAC.

1. Sélectionnez **Try it** afin d’ouvrir Cloud Shell, puis collez le script PowerShell suivant dans la fenêtre de l’interpréteur de commandes.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name"   # This name is used to generate names for Azure resources, such as storage account name.
    $location = Read-Host -Prompt "Enter a location (i.e. centralus)"

    $resourceGroupName = $projectName + "rg"
    $storageAccountName = $projectName + "store"
    $containerName = "bacpacfile" # The name of the Blob container to be created.

    $bacpacURL = "https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac"
    $bacpacFileName = "SQLDatabaseExtension.bacpac" # A file name used for downloading and uploading the BACPAC file.

    # Download the bacpac file
    Invoke-WebRequest -Uri $bacpacURL -OutFile "$home/$bacpacFileName"

    # Create a resource group
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account
    $storageAccount = New-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageAccountName `
        -Location $location `
        -SkuName "Standard_LRS"

    $context = $storageAccount.Context

    # Create a container
    New-AzStorageContainer -Name $containerName -Context $context

    # Upload the bacpac file
    Set-AzStorageBlobContent `
        -Container $containerName `
        -File "$home/$bacpacFileName" `
        -Blob $bacpacFileName `
        -Context $context

    # Generate a SAS token
    $bacpacURI = New-AzStorageBlobSASToken `
        -Context $context `
        -Container $containerName `
        -Blob $bacpacFileName `
        -Permission r `
        -ExpiryTime (Get-Date).AddHours(8.0) `
        -FullUri

    $str = $bacpacURI.split("?")

    Write-Host "You need the blob url and the SAS token later in the tutorial:"
    Write-Host $str[0]
    Write-Host (-join ("?", $str[1]))

    Write-Host "Press [ENTER] to continue ..."
    ```

1. Notez l’URL du fichier BACPAC et le jeton SAS. Vous avez besoin de ces valeurs lorsque vous déployez le modèle.

## <a name="open-an-existing-template"></a>Ouvrir un modèle existant

Dans cette session, vous modifiez le modèle que vous avez créé dans [Tutoriel : Importer des fichiers SQL BACPAC avec des modèles Azure Resource Manager](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md) pour appeler le fichier BACPAC avec un jeton SAS.  Le modèle développé dans le tutoriel de l’extension SQL est partagé dans [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. À partir de Visual Studio Code, sélectionnez **Fichier**>**Ouvrir un fichier**.
2. Collez l’URL suivante dans **Nom de fichier** :

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy2.json
    ```

3. Sélectionnez **Ouvrir** pour ouvrir le fichier.

    Quatre ressources sont définies dans le modèle :

   * `Microsoft.Sql/servers`. Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers).
   * `Microsoft.SQL/servers/firewallRules`. Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers/firewallrules).
   * `Microsoft.SQL/servers/databases`.  Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).
   * `Microsoft.SQL/server/databases/extensions`.  Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions).

     Il est préférable de comprendre quelques notions basiques du modèle avant de le personnaliser.
4. Sélectionnez **Fichier**>**Enregistrer sous** pour enregistrer une copie du fichier sur votre ordinateur local avec le nom **azuredeploy.json**.

## <a name="edit-the-template"></a>Modifier le modèle

1. Remplacez la définition de paramètre storageAccountKey par la définition de paramètre suivante :

    ```json
    "_artifactsLocationSasToken": {
      "type": "securestring",
      "metadata": {
        "description": "Specifies the SAS token required to access the artifact location."
      }
    },
    ```

    ![Paramètres des artefacts sécurisés du tutoriel Resource Manager](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-secure-artifacts-parameters.png)

2. Mettez à jour la valeur de ces trois éléments de la ressource d’extension SQL :

    ```json
    "storageKeyType": "SharedAccessKey",
    "storageKey": "[parameters('_artifactsLocationSasToken')]",
    "storageUri": "[parameters('bacpacUrl')]",
    ```

Le modèle complet ressemble à ceci :

[!code-json[](~/resourcemanager-templates/tutorial-sql-extension/azuredeploy3.json?range=1-106&highlight=38-43,95-97)]

## <a name="deploy-the-template"></a>Déployer le modèle

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Reportez-vous à la section [Déployer le modèle](./resource-manager-tutorial-create-multiple-instances.md#deploy-the-template) pour connaître la procédure de déploiement. Utilisez plutôt le script de déploiement PowerShell suivant :

```azurepowershell
$projectName = Read-Host -Prompt "Enter the project name that is used earlier"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the sql database admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$bacpacUrl = Read-Host -Prompt "Enter the BACPAC url"
$artifactsLocationSasToken = Read-Host -Prompt "Enter the artifacts location SAS token" -AsSecureString

$resourceGroupName = $projectName + "rg"

#New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -_artifactsLocationSasToken $artifactsLocationSasToken `
    -bacpacUrl $bacpacUrl `
    -TemplateFile "$HOME/azuredeploy.json"

Write-Host "Press [ENTER] to continue ..."
```

Utilisez un mot de passe généré. Consultez les [Conditions préalables](#prerequisites).
Pour les valeurs de _artifactsLocation, _artifactsLocationSasToken et bacpacFileName, consultez [Préparer un fichier BACPAC](#prepare-a-bacpac-file).

## <a name="verify-the-deployment"></a>Vérifier le déploiement

Dans le portail, sélectionnez la base de données SQL dans le nouveau groupe de ressources déployé. Sélectionnez l’**éditeur de requêtes (préversion)** , puis entrez les informations d’identification d’administrateur. Vous devez voir deux tables importées dans la base de données :

![Azure Resource Manager deploy sql extensions BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, nettoyez les ressources Azure que vous avez déployées en supprimant le groupe de ressources.

1. Dans le portail Azure, sélectionnez **Groupe de ressources** dans le menu de gauche.
2. Entrez le nom du groupe de ressources dans le champ **Filtrer par nom**.
3. Sélectionnez le nom du groupe de ressources.  Vous devriez voir six ressources au total dans le groupe de ressources.
4. Sélectionnez **Supprimer le groupe de ressources** dans le menu supérieur.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez déployé un SQL Server, une SQL Database, et avez importé un fichier BACPAC à l'aide d'un jeton SAS. Pour savoir comment créer un pipeline Azure pour développer et déployer des modèles Resource Manager en continu, consultez

> [!div class="nextstepaction"]
> [Intégration continue avec Azure Pipelines](./resource-manager-tutorial-use-azure-pipelines.md)
