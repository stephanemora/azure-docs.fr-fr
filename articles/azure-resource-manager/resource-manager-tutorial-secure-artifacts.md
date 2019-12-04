---
title: Sécuriser les artefacts dans les modèles
description: Apprenez à sécuriser les artefacts utilisés dans vos modèles Azure Resource Manager.
author: mumian
ms.date: 10/08/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: b37f7e284b655a362c5a4231a7c1da3719762644
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326437"
---
# <a name="tutorial-secure-artifacts-in-azure-resource-manager-template-deployments"></a>Tutoriel : Sécuriser des artefacts dans les déploiements de modèles Azure Resource Manager

Apprenez à sécuriser les artefacts utilisés dans vos modèles Azure Resource Manager à l’aide d'un compte de stockage Azure avec signatures d’accès partagé (SAP). Les artefacts de déploiement correspondent à tous les fichiers, en plus du fichier de modèle principal, requis pour effectuer un déploiement. Par exemple, dans [Tutoriel : Importer des fichiers SQL BACPAC avec des modèles Azure Resource Manager](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md), le modèle principal crée une base de données Azure SQL. Il appelle également un fichier BACPAC pour créer des tables et insérer des données. Le fichier BACPAC est un artefact. L’artefact est stocké dans un compte de stockage Azure avec accès public. Dans ce tutoriel, vous utilisez les signatures d'accès partagé pour accorder un accès limité au fichier BACPAC de votre propre compte de stockage Azure. Pour plus d’informations sur les signatures d’accès partagé, consultez [Utilisation des signatures d’accès partagé (SAP)](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

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

Pour automatiser ces étapes à l’aide d’un script PowerShell, consultez le script dans [Charger le modèle lié](./resource-manager-tutorial-create-linked-templates.md#upload-the-linked-template).

### <a name="download-the-bacpac-file"></a>Télécharger le fichier BACPAC

Téléchargez le [fichier BACPAC](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac) et enregistrez-le sur votre ordinateur local avec le même nom, **SQLDatabaseExtension.bacpac**.

### <a name="create-a-storage-account"></a>Créez un compte de stockage.

1. Sélectionnez l’image suivante pour ouvrir un modèle Resource Manager dans le portail Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-storage-account-create%2fazuredeploy.json" target="_blank"><img src="./media/resource-manager-tutorial-secure-artifacts/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Entrez les propriétés suivantes :

    * **Abonnement**: Sélectionnez votre abonnement Azure.
    * **Groupe de ressources** : Sélectionnez **Créer** et donnez-lui un nom. Un groupe de ressources est un conteneur utilisé à des fins de gestion des ressources Azure. Dans ce tutoriel, vous pouvez utiliser le même groupe de ressources pour le compte de stockage et la base de données Azure SQL. Notez le nom de ce groupe de ressources. Vous en aurez besoin pour créer la base de données Azure SQL plus loin dans les tutoriels.
    * **Emplacement** : Sélectionnez une région. Par exemple, **USA Centre**.
    * **Type de compte de stockage** : Utilisez la valeur par défaut, à savoir **Standard_LRS**.
    * **Emplacement** : Utilisez la valeur par défaut, à savoir **[resourceGroup () .location]** . Cela signifie que vous utilisez l’emplacement du groupe de ressources pour le compte de stockage.
    * **J’accepte les termes et conditions mentionnés ci-dessus** : (case cochée)
3. Sélectionnez **Achat**.
4. Sélectionnez l’icône de notification (icône de cloche) dans le coin supérieur droit du portail pour consulter l’état du déploiement.

    ![Volet de notifications du portail du tutoriel Resource Manager](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-portal-notifications-pane.png)
5. Une fois le compte de stockage correctement déployé, sélectionnez **Accéder au groupe de ressources** à partir du volet de notification pour ouvrir le groupe de ressources.

### <a name="create-a-blob-container"></a>Créer un conteneur d’objets blob

Un conteneur d’objets blob est requis avant de télécharger tous les fichiers.

1. Sélectionnez le compte de stockage pour l’ouvrir. Vous devriez voir un seul compte de stockage dans le groupe de ressources. Le nom du compte de stockage est différent de celui indiqué dans la capture d’écran suivante.

    ![Compte de stockage du tutoriel Resource Manager](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-storage-account.png)

2. Sélectionnez la vignette **Objets blob** .

    ![Objets blob du tutoriel Resource Manager](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-blobs.png)
3. Sélectionnez **+ Conteneur**, en haut, pour créer un conteneur.
4. Saisissez les valeurs suivantes :

    * **Nom** : entrez **sqlbacpac**.
    * **Niveau d’accès public** : utilisez la valeur par défaut, **Privé (aucun accès anonyme)** .
5. Sélectionnez **OK**.
6. Sélectionnez **sqlbacpac** pour ouvrir le conteneur nouvellement créé.

### <a name="upload-the-bacpac-file-to-the-container"></a>Télécharger le fichier BACPAC dans le conteneur

1. Sélectionnez **Télécharger**.
2. Saisissez les valeurs suivantes :

    * **Fichiers** : Suivez les instructions pour sélectionner le fichier BACPAC téléchargé précédemment. Le nom par défaut est **SQLDatabaseExtension.bacpac**.
    * **Type d'authentification** : Sélectionnez **SAS**.  *SAS* correspond à la valeur par défaut.
3. Sélectionnez **Télécharger**.  Une fois le fichier téléchargé, son nom doit être répertorié dans le conteneur.

### <a name="a-namegenerate-a-sas-token-generate-a-sas-token"></a><a name="generate-a-sas-token" />Générer un jeton SAS

1. Dans le conteneur, cliquez avec le bouton droit sur **SQLDatabaseExtension.bacpac**, puis sélectionnez **Générer un jeton SAS**.
2. Saisissez les valeurs suivantes :

    * **Permission** : Utiliser la valeur par défaut, **Lecture**.
    * **Date/heure de début et d'expiration** : La valeur par défaut vous permet de disposer de huit heures pour utiliser le jeton SAS. Si vous avez besoin de plus de temps pour suivre ce tutoriel, mettez à jour **Expiration**.
    * **Adresses IP autorisées** : Laissez ce champ vide.
    * **Protocoles autorisés** : Utilisez la valeur par défaut : **HTTPS**.
    * **Clé de signature** : Utilisez la valeur par défaut : **Clé 1**.
3. Sélectionnez **Générer un jeton et une URL SAP d’objet blob**.
4. Faites une copie de l'**URL SAP d'objet blob**. Au milieu de l’URL se trouve le nom de fichier **SQLDatabaseExtension.bacpac**.  Le nom de fichier divise l’URL en trois parties :

   - **Emplacement de l’artefact** : https://xxxxxxxxxxxxxx.blob.core.windows.net/sqlbacpac/. Assurez-vous que l’emplacement se termine par un « / ».
   - **Nom du fichier BACPAC** : SQLDatabaseExtension.bacpac.
   - **Jeton SAS de l’emplacement des artefacts** : Assurez-vous que le jeton est précédé d'un « ? ».

     Vous avez besoin de ces trois valeurs dans [Déployer le modèle](#deploy-the-template).

## <a name="open-an-existing-template"></a>Ouvrir un modèle existant

Dans cette session, vous modifiez le modèle que vous avez créé dans [Tutoriel : Importer des fichiers SQL BACPAC avec des modèles Azure Resource Manager](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md) pour appeler le fichier BACPAC avec un jeton SAS.  Le modèle développé dans le tutoriel de l’extension SQL est partagé dans [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. À partir de Visual Studio Code, sélectionnez **Fichier**>**Ouvrir un fichier**.
2. Collez l’URL suivante dans **Nom de fichier** :

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```
3. Sélectionnez **Ouvrir** pour ouvrir le fichier.

    Il existe cinq ressources définies dans le modèle :

   * `Microsoft.Sql/servers`. Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers).
   * `Microsoft.SQL/servers/securityAlertPolicies`. Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/securityalertpolicies).
   * `Microsoft.SQL/servers/filewallRules`. Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers/firewallrules).
   * `Microsoft.SQL/servers/databases`.  Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).
   * `Microsoft.SQL/server/databases/extensions`.  Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions).

     Il est préférable de comprendre quelques notions basiques du modèle avant de le personnaliser.
4. Sélectionnez **Fichier**>**Enregistrer sous** pour enregistrer une copie du fichier sur votre ordinateur local avec le nom **azuredeploy.json**.

## <a name="edit-the-template"></a>Modifier le modèle

Ajoutez les paramètres supplémentaires suivants :

```json
"_artifactsLocation": {
    "type": "string",
    "metadata": {
        "description": "The base URI where artifacts required by this template are located."
    }
},
"_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
        "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
},
"bacpacFileName": {
    "type": "string",
    "defaultValue": "SQLDatabaseExtension.bacpac",
    "metadata": {
        "description": "The bacpac for configure the database and tables."
    }
}
```

![Paramètres des artefacts sécurisés du tutoriel Resource Manager](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-secure-artifacts-parameters.png)

Mettez à jour la valeur des deux éléments suivants :

```json
"storageKey": "[parameters('_artifactsLocationSasToken')]",
"storageUri": "[uri(parameters('_artifactsLocation'), parameters('bacpacFileName'))]",
```

## <a name="deploy-the-template"></a>Déployer le modèle

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Reportez-vous à la section [Déployer le modèle](./resource-manager-tutorial-create-multiple-instances.md#deploy-the-template) pour connaître la procédure de déploiement. Utilisez plutôt le script de déploiement PowerShell suivant :

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$artifactsLocation = Read-Host -Prompt "Enter the artifacts location"
$artifactsLocationSasToken = Read-Host -Prompt "Enter the artifacts location SAS token" -AsSecureString
$bacpacFileName = Read-Host -Prompt "Enter the BACPAC file name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -_artifactsLocation $artifactsLocation `
    -_artifactsLocationSasToken $artifactsLocationSasToken `
    -bacpacFileName $bacpacFileName `
    -TemplateFile "$HOME/azuredeploy.json"
```

Utilisez un mot de passe généré. Consultez les [Conditions préalables](#prerequisites).
Pour les valeurs de _artifactsLocation, _artifactsLocationSasToken et bacpacFileName, consultez [Générer un jeton SAS](#generate-a-sas-token).

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
