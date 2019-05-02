---
title: Importer des fichiers SQL BACPAC avec des modèles Azure Resource Manager | Microsoft Docs
description: Découvrez comment utiliser l’extension SQL Database pour importer des fichiers SQL BACPAC avec des modèles Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 04/08/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 08befabfbd14651475fa56dec95bdf4c2fe54c9c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60390304"
---
# <a name="tutorial-import-sql-bacpac-files-with-azure-resource-manager-templates"></a>Tutoriel : Importer des fichiers SQL BACPAC avec des modèles Azure Resource Manager

Découvrez comment utiliser les extensions Azure SQL Database pour importer un fichier SQL BACPAC avec des modèles Azure Resource Manager. Les artefacts de déploiement correspondent à tous les fichiers, en plus du fichier de modèle principal, requis pour effectuer un déploiement. Le fichier BACPAC est un artefact. Dans ce tutoriel, vous créez un modèle pour déployer un SQL Server Azure, une SQL Database et importer un fichier BACPAC. Pour plus d’informations sur le déploiement des extensions de machines virtuelles Azure à l’aide de modèles Azure Resource Manager, consultez [Tutoriel : Déployer des extensions de machines virtuelles avec des modèles Azure Resource Manager](./resource-manager-tutorial-deploy-vm-extensions.md).

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

* [Visual Studio Code](https://code.visualstudio.com/) avec l’extension Outils Resource Manager. Consultez [Installer l’extension ](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Pour une sécurité optimale, utilisez un mot de passe généré pour le compte administrateur de SQL Server. Voici un exemple pour générer un mot de passe :

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Azure Key Vault a été conçu pour protéger les clés et autres secrets de chiffrement. Pour plus d’informations, consultez [Tutoriel : Intégrer Azure Key Vault à un déploiement de modèle Resource Manager](./resource-manager-tutorial-use-key-vault.md). Nous vous recommandons également de mettre à jour votre mot de passe tous les trois mois.

## <a name="prepare-a-bacpac-file"></a>Préparer un fichier BACPAC

Un fichier BACPAC est partagé sur un [compte de Stockage Azure](https://armtutorials.blob.core.windows.net/sqlextensionbacpac/SQLDatabaseExtension.bacpac) avec l’accès public. Pour créer le vôtre, consultez [Exporter une base de données SQL Azure dans un fichier BACPAC](../sql-database/sql-database-export.md). Si vous choisissez de publier le fichier sur votre propre emplacement, vous devez mettre à jour le modèle plus tard dans ce didacticiel.

## <a name="open-a-quickstart-template"></a>Ouvrir un modèle de démarrage rapide

Le modèle utilisé dans ce tutoriel est stocké dans un [compte de Stockage Azure](https://armtutorials.blob.core.windows.net/createsql/azuredeploy.json). 

1. À partir de Visual Studio Code, sélectionnez **Fichier**>**Ouvrir un fichier**.
2. Collez l’URL suivante dans **Nom de fichier** :

    ```url
    https://armtutorials.blob.core.windows.net/createsql/azuredeploy.json
    ```
3. Sélectionnez **Ouvrir** pour ouvrir le fichier.

    Trois ressources sont définies dans le modèle :

   * `Microsoft.Sql/servers`. Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/microsoft.sql/servers).
   * `Microsoft.SQL/servers/securityAlertPolicies`. Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/securityalertpolicies).
   * `Microsoft.SQL.servers/databases`.  Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).

     Il est préférable de comprendre quelques notions basiques du modèle avant de le personnaliser.
4. Sélectionnez **Fichier**>**Enregistrer sous** pour enregistrer une copie du fichier sur votre ordinateur local avec le nom **azuredeploy.json**.

## <a name="edit-the-template"></a>Modifier le modèle

Ajoutez deux ressources supplémentaires au modèle.

* Pour permettre à l’extension de base de données SQL d’importer des fichiers BACPAC, vous devez autoriser l’accès aux services Azure. Ajoutez le code JSON suivant à la définition du serveur SQL :

    ```json
    {
        "type": "firewallrules",
        "name": "AllowAllAzureIps",
        "location": "[parameters('location')]",
        "apiVersion": "2015-05-01-preview",
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
            "name": "Import",
            "type": "extensions",
            "apiVersion": "2014-04-01",
            "dependsOn": [
                "[resourceId('Microsoft.Sql/servers/databases', variables('databaseServerName'), variables('databaseName'))]"
            ],
            "properties": {
                "storageKeyType": "SharedAccessKey",
                "storageKey": "?",
                "storageUri": "https://armtutorials.blob.core.windows.net/sqlextensionbacpac/SQLDatabaseExtension.bacpac",
                "administratorLogin": "[variables('databaseServerAdminLogin')]",
                "administratorLoginPassword": "[variables('databaseServerAdminLoginPassword')]",
                "operationMode": "Import",
            }
        }
    ]
    ```

    Le modèle doit ressembler à ce qui suit :

    ![Azure Resource Manager deploy sql extensions BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png)

    Pour comprendre la définition de ressource, consultez la [référence de l’extension SQL Database](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases/extensions). Voici quelques éléments importants :

    * **dependsOn** : la ressource d’extension doit être créée une fois que la base de données SQL a été créée.
    * **storageKeyType** : type de clé de stockage à utiliser. La valeur peut être `StorageAccessKey` ou `SharedAccessKey`. Étant donné que le fichier BACPAC fourni est partagé sur un compte de Stockage Azure avec un accès public, « SharedAccessKey » est utilisé ici.
    * **storageKey** : clé de stockage à utiliser. Si le type de clé de stockage est SharedAccessKey, il doit être précédé de « ? ».
    * **storageUri** : l'URI de stockage à utiliser. Si vous choisissez de ne pas utiliser le fichier BACPAC fourni, vous devez mettre à jour les valeurs.
    * **administratorLoginPassword** : mot de passe de l'administrateur SQL. Utilisez un mot de passe généré. Consultez les [Conditions préalables](#prerequisites).

## <a name="deploy-the-template"></a>Déployer le modèle

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Reportez-vous à la section [Déployer le modèle](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) pour connaître la procédure de déploiement. Utilisez plutôt le script de déploiement PowerShell suivant :

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the SQL admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -TemplateFile "$HOME/azuredeploy.json"
```

Utilisez un mot de passe généré. Consultez les [Conditions préalables](#prerequisites).

## <a name="verify-the-deployment"></a>Vérifier le déploiement

Dans le portail, sélectionnez la base de données SQL dans le nouveau groupe de ressources déployé. Sélectionnez l’**éditeur de requêtes (préversion)**, puis entrez les informations d’identification d’administrateur. Vous devez voir deux tables importées dans la base de données :

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
