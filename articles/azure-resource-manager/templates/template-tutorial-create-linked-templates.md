---
title: Créer des modèles liés
description: Découvrez comment créer des modèles Azure Resource Manager liés pour la création de machine virtuelle
author: mumian
ms.date: 12/03/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: a77f32620d4e3a694806cdf6c247928fa3c7ede4
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045293"
---
# <a name="tutorial-create-linked-azure-resource-manager-templates"></a>Tutoriel : Créer des modèles Azure Resource Manager liés

Découvrez comment créer des modèles Azure Resource Manager liés. Avec des modèles liés, un modèle peut appeler un autre modèle. C’est idéal pour la modularisation de modèles. Dans ce tutoriel, vous utilisez le même modèle que celui utilisé dans le [Tutoriel : Créer des modèles Azure Resource Manager avec des ressources dépendantes](./template-tutorial-create-templates-with-dependent-resources.md), qui crée une machine virtuelle, un réseau virtuel et d’autres ressources dépendantes, notamment un compte de stockage. Vous séparez la création de la ressource de compte de stockage à un modèle lié.

Appeler un modèle lié équivaut à effectuer un appel de fonction.  Vous allez également découvrir comment passer des valeurs de paramètre au modèle lié et comment obtenir des « valeurs de retour » à partir de celui-ci.

Ce tutoriel décrit les tâches suivantes :

> [!div class="checklist"]
> * Ouvrir un modèle de démarrage rapide
> * Créer le modèle lié
> * Charger le modèle lié
> * Lien vers le modèle lié
> * Configurer la dépendance
> * Déployer le modèle
> * Pratiques supplémentaires

Pour plus d’informations, consultez [Utiliser des modèles liés et imbriqués lors du déploiement de ressources Azure](./linked-templates.md).

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Conditions préalables requises

Pour effectuer ce qui est décrit dans cet article, vous avez besoin des éléments suivants :

* Visual Studio Code avec l’extension Outils Resource Manager. Consultez [Utiliser Visual Studio Code pour créer des modèles Azure Resource Manager](use-vs-code-to-create-template.md).
* Pour une sécurité optimale, utilisez un mot de passe généré pour le compte administrateur de la machine virtuelle. Voici un exemple pour générer un mot de passe :

    ```azurecli-interactive
    openssl rand -base64 32
    ```

    Azure Key Vault a été conçu pour protéger les clés et autres secrets de chiffrement. Pour plus d’informations, consultez [Didacticiel : Intégrer Azure Key Vault à un déploiement de modèle Resource Manager](./template-tutorial-use-key-vault.md). Nous vous recommandons également de mettre à jour votre mot de passe tous les trois mois.

## <a name="open-a-quickstart-template"></a>Ouvrir un modèle de démarrage rapide

Modèles de démarrage rapide Azure est un référentiel pour les modèles Resource Manager. Au lieu de créer un modèle à partir de zéro, vous pouvez chercher un exemple de modèle et le personnaliser. Le modèle utilisé dans ce didacticiel se nomme [Déployer une machine virtuelle Windows simple](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/). Il s’agit du même modèle que celui utilisé dans le [Tutoriel : Créer des modèles Azure Resource Manager avec des ressources dépendantes](./template-tutorial-create-templates-with-dependent-resources.md). Vous enregistrez deux copies du même modèle à utiliser en tant que :

* **Modèle principal** : créez toutes les ressources à l’exception du compte de stockage.
* **Modèle lié** : créez le compte de stockage.

1. À partir de Visual Studio Code, sélectionnez **Fichier**>**Ouvrir un fichier**.
1. Collez l’URL suivante dans **Nom de fichier** :

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Sélectionnez **Ouvrir** pour ouvrir le fichier.
1. Il existe six ressources définies par le modèle :

   * [`Microsoft.Storage/storageAccounts`](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts)
   * [`Microsoft.Network/publicIPAddresses`](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)
   * [`Microsoft.Network/networkSecurityGroups`](https://docs.microsoft.com/azure/templates/microsoft.network/networksecuritygroups)
   * [`Microsoft.Network/virtualNetworks`](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)
   * [`Microsoft.Network/networkInterfaces`](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)
   * [`Microsoft.Compute/virtualMachines`](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines)

     Il est utile de comprendre certaines notions de base du schéma de modèle avant de personnaliser le modèle.
1. Sélectionnez **Fichier**>**Enregistrer sous** pour enregistrer une copie du fichier sur votre ordinateur local avec le nom **azuredeploy.json**.
1. Sélectionnez **Fichier**>**Enregistrer sous** pour créer une autre copie du fichier avec le nom **linkedTemplate.json**.

## <a name="create-the-linked-template"></a>Créer le modèle lié

Le modèle lié crée un compte de stockage. Le modèle lié peut être utilisé comme un modèle autonome pour créer un compte de stockage. Dans ce tutoriel, le modèle lié prend deux paramètres et passe une valeur au modèle principal. Cette valeur de « retour » est définie dans l’élément `outputs`.

1. Ouvrez le fichier **linkedTemplate.json** dans Visual Studio Code s’il n’est pas ouvert.
1. Effectuez les modifications suivantes :

    * Supprimez tous les paramètres autres que **location**.
    * Ajoutez un paramètre appelé **storageAccountName**.

      ```json
      "storageAccountName":{
        "type": "string",
        "metadata": {
            "description": "Azure Storage account name."
        }
      },
      ```

      Le nom et l’emplacement du compte de stockage sont passés du modèle principal au modèle lié en tant que paramètres.

    * Supprimez l’élément **variables** et toutes les définitions de variable.
    * Supprimez toutes les ressources autres que le compte de stockage. Vous supprimez quatre ressources au total.
    * Mettez à jour la valeur de l’élément **name** de la ressource de compte de stockage comme suit :

        ```json
          "name": "[parameters('storageAccountName')]",
        ```

    * Mettez à jour l’élément **outputs**, de sorte qu’il ressemble à ce qui suit :

        ```json
        "outputs": {
          "storageUri": {
              "type": "string",
              "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
        }
        ```

       **storageUri** est requis par la définition de ressource de machine virtuelle dans le modèle principal.  Vous passez la valeur au modèle principal en tant que valeur de sortie.

        Lorsque vous avez terminé, le modèle doit ressembler à ce qui suit :

        ```json
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "storageAccountName": {
              "type": "string",
              "metadata": {
                "description": "Azure Storage account name."
              }
            },
            "location": {
              "type": "string",
              "defaultValue": "[resourceGroup().location]",
              "metadata": {
                "description": "Location for all resources."
              }
            }
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2018-11-01",
              "name": "[parameters('storageAccountName')]",
              "location": "[parameters('location')]",
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "Storage",
              "properties": {}
            }
          ],
          "outputs": {
            "storageUri": {
              "type": "string",
              "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
          }
        }
        ```

1. Enregistrez les modifications.

## <a name="upload-the-linked-template"></a>Charger le modèle lié

Le modèle principal et le modèle lié doivent être accessibles à partir de l’emplacement où vous exécutez le déploiement. Dans ce tutoriel, vous utilisez la même méthode de déploiement Cloud Shell que celle utilisée dans le [Tutoriel : Créer des modèles Azure Resource Manager avec des ressources dépendantes](./template-tutorial-create-templates-with-dependent-resources.md). Le modèle principal (azuredeploy.json) est chargé dans l’interpréteur de commandes (shell). Le modèle lié (linkedTemplate.json) doit être partagé quelque part de manière sécurisée. Le script PowerShell suivant crée un compte Stockage Azure, charge le modèle sur le compte de stockage, puis génère un jeton SAS pour accorder un accès limité au fichier de modèle. Pour simplifier le tutoriel, le script télécharge un modèle lié terminé à partir d’un dépôt Github. Si vous souhaitez utiliser le modèle lié que vous avez créé, vous pouvez utiliser [Cloud Shell](https://shell.azure.com) pour charger votre modèle lié, puis modifier le script afin d’utiliser votre propre modèle lié.

> [!NOTE]
> Le script limite la durée d’utilisation du jeton SAS à huit heures. Si vous avez besoin de plus de temps pour suivre ce tutoriel, augmentez le délai d’expiration.

```azurepowershell-interactive
$projectNamePrefix = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectNamePrefix + "rg"
$storageAccountName = $projectNamePrefix + "store"
$containerName = "linkedtemplates" # The name of the Blob container to be created.

$linkedTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-linked-templates/linkedStorageAccount.json" # A completed linked template used in this tutorial.
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

# Download the tutorial linked template
Invoke-WebRequest -Uri $linkedTemplateURL -OutFile "$home/$fileName"

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

# Upload the linked template
Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$fileName" `
    -Blob $fileName `
    -Context $context

# Generate a SAS token
$templateURI = New-AzStorageBlobSASToken `
    -Context $context `
    -Container $containerName `
    -Blob $fileName `
    -Permission r `
    -ExpiryTime (Get-Date).AddHours(8.0) `
    -FullUri

Write-Host "You need the following values later in the tutorial:"
Write-Host "Resource Group Name: $resourceGroupName"
Write-Host "Linked template URI with SAS token: $templateURI"
Write-Host "Press [ENTER] to continue ..."
```

1. Sélectionnez le bouton vert **Essayer** pour ouvrir le volet Azure Cloud Shell.
2. Sélectionnez **Copier** pour copier le script PowerShell.
3. Cliquez avec le bouton droit dans le volet de l’interpréteur de commandes (la partie bleu foncé), puis sélectionnez **Coller**.
4. Prenez note des deux valeurs (nom de groupe de ressources et URI du modèle lié) à la fin du volet de l’interpréteur de commandes. Vous aurez besoin de ces valeurs plus loin dans le didacticiel.
5. Sélectionnez **Quitter le mode focus** pour fermer le volet de l’interpréteur de commandes.

Dans la pratique, vous générez un jeton SAS quand vous déployez le modèle principal et affectez à l’expiration du jeton SAS une fenêtre plus petite pour renforcer la sécurité. Pour plus d’informations, consultez [Fournir un jeton SAS au cours du déploiement](./secure-template-with-sas-token.md#provide-sas-token-during-deployment).

## <a name="call-the-linked-template"></a>Appeler le modèle lié

Le modèle principal se nomme azuredeploy.json.

1. Ouvrez **azuredeploy.json** dans Visual Studio Code s’il n’est pas ouvert.
1. Remplacez la définition de ressource de compte de stockage par l’extrait JSON suivant :

    ```json
    {
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri":""
          },
          "parameters": {
              "storageAccountName":{"value": "[variables('storageAccountName')]"},
              "location":{"value": "[parameters('location')]"}
          }
      }
    },
    ```

    Prenez note des détails suivants :

    * Une ressource `Microsoft.Resources/deployments` dans le modèle principal est utilisée pour créer le lien avec un autre modèle.
    * La ressource `deployments` se nomme `linkedTemplate`. Ce nom est utilisé pour [configurer la dépendance](#configure-dependency).
    * Vous pouvez uniquement utiliser le mode de déploiement [Incremental](./deployment-modes.md) lors de l’appel de modèles liés.
    * `templateLink/uri` contient l’URI du modèle lié. Mettez à jour la valeur avec l’URI que vous obtenez quand vous chargez le modèle lié (celui avec un jeton SAS).
    * Pour passer des valeurs du modèle principal au modèle lié, utilisez `parameters`.
1. Vérifiez que vous avez mis à jour la valeur de l’élément `uri` avec la valeur obtenue quand vous chargez le modèle lié (celui avec un jeton SAS). Dans la pratique, vous souhaitez fournir l’URI avec un paramètre.
1. Enregistrer le modèle modifié

## <a name="configure-dependency"></a>Configurer la dépendance

Nous avons vu dans le [Tutoriel : Créer des modèles Azure Resource Manager avec des ressources dépendantes](./template-tutorial-create-templates-with-dependent-resources.md) que la ressource de machine virtuelle dépendait du compte de stockage :

![Diagramme de dépendance des modèles Azure Resource Manager](./media/template-tutorial-create-linked-templates/resource-manager-template-visual-studio-code-dependency-diagram.png)

Étant donné que le compte de stockage est maintenant défini dans le modèle lié, vous devez mettre à jour les deux éléments suivants de la ressource `Microsoft.Compute/virtualMachines`.

* Reconfigurez l’élément `dependsOn`. La définition du compte de stockage est déplacée vers le modèle lié.
* Reconfigurez l’élément `properties/diagnosticsProfile/bootDiagnostics/storageUri`. Dans [Créer le modèle lié](#create-the-linked-template), vous avez ajouté une valeur de sortie :

    ```json
    "outputs": {
        "storageUri": {
            "type": "string",
            "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
    }
    ```

    Cette valeur est requise par le modèle principal.

1. Ouvrez azuredeploy.json dans Visual Studio Code s’il n’est pas ouvert.
2. Développez la définition de ressource de machine virtuelle, mettez à jour **dependsOn** comme indiqué dans la capture d’écran suivante :

    ![Dépendance configurée par les modèles liés Azure Resource Manager](./media/template-tutorial-create-linked-templates/resource-manager-template-linked-templates-configure-dependency.png)

    Le nom de la ressource de déploiement *linkedTemplate*.
3. Mettez à jour **properties/diagnosticsProfile/bootDiagnostics/storageUri** comme indiqué dans la capture d’écran précédente.
4. Enregistrez le modèle modifié.

## <a name="deploy-the-template"></a>Déployer le modèle

Reportez-vous à la section [Déployer le modèle](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) pour connaître la procédure de déploiement. Utilisez le même nom de groupe de ressources que le compte de stockage pour stocker le modèle lié. Cela facilite le nettoyage des ressources dans la section suivante. Pour une sécurité optimale, utilisez un mot de passe généré pour le compte administrateur de la machine virtuelle. Consultez les [Conditions préalables](#prerequisites).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’en avez plus besoin, nettoyez les ressources Azure que vous avez déployées en supprimant le groupe de ressources.

1. Dans le portail Azure, sélectionnez **Groupe de ressources** dans le menu de gauche.
2. Entrez le nom du groupe de ressources dans le champ **Filtrer par nom**.
3. Sélectionnez le nom du groupe de ressources.  Vous devriez voir six ressources au total dans le groupe de ressources.
4. Sélectionnez **Supprimer le groupe de ressources** dans le menu supérieur.

## <a name="additional-practice"></a>Pratique supplémentaire

Pour améliorer le projet, apportez les modifications supplémentaires suivantes au projet terminé :

1. Modifiez le modèle principal (azuredeploy.json) afin qu’il accepte la valeur d’URI du modèle lié par le biais d’un paramètre.
2. Au lieu de générer un jeton SAS quand vous chargez le modèle lié, générez le jeton quand vous déployez le modèle principal. Pour plus d’informations, consultez [Fournir un jeton SAS au cours du déploiement](./secure-template-with-sas-token.md#provide-sas-token-during-deployment).

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez modularisé un modèle en un modèle principal et un modèle lié. Pour découvrir comment utiliser les extensions de machine virtuelle afin d’exécuter les tâches post-déploiement, consultez :

> [!div class="nextstepaction"]
> [Déployer des extensions de machine virtuelle](./template-tutorial-deploy-vm-extensions.md)
