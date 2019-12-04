---
title: Utiliser une condition dans des modèles
description: Découvrez comment déployer des ressources Azure en fonction des conditions. Montre comment déployer une nouvelle ressource ou utiliser une ressource existante.
author: mumian
ms.date: 05/21/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 7aecc25daed8f73e903b181d782e53c0ea5acd74
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325344"
---
# <a name="tutorial-use-condition-in-azure-resource-manager-templates"></a>Didacticiel : Utiliser une condition dans des modèles Azure Resource Manager

Découvrez comment déployer des ressources Azure en fonction des conditions.

Dans le didacticiel [Définir l’ordre de déploiement des ressources](./resource-manager-tutorial-create-templates-with-dependent-resources.md), vous créez une machine virtuelle, un réseau virtuel et d’autres ressources dépendantes, y compris un compte de stockage. Plutôt que de créer un compte de stockage à chaque fois, vous laissez le choix aux utilisateurs de créer un compte de stockage ou d’utiliser un compte de stockage existant. Vous définissez pour cela un paramètre supplémentaire. Si la valeur du paramètre est « nouveau », un nouveau compte de stockage est créé. Sinon, un compte de stockage existant avec le nom fourni est utilisé.

![Diagramme de condition d’utilisation d’un modèle Resource Manager](./media/resource-manager-tutorial-use-conditions/resource-manager-template-use-condition-diagram.png)

Ce tutoriel décrit les tâches suivantes :

> [!div class="checklist"]
> * Ouvrir un modèle de démarrage rapide
> * Modifier le modèle
> * Déployer le modèle
> * Supprimer des ressources

Ce tutoriel traite uniquement d’un scénario de base d’utilisation de conditions. Pour plus d'informations, consultez les pages suivantes :

* [Structure de fichiers de modèle : Condition](conditional-resource-deployment.md).
* [Déployer une ressource de manière conditionnelle dans un modèle Azure Resource Manager](/azure/architecture/building-blocks/extending-templates/conditional-deploy).
* [Fonction de modèle : If](./resource-group-template-functions-logical.md#if).
* [Fonctions de comparaison pour les modèles Azure Resource Manager](./resource-group-template-functions-comparison.md)

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce qui est décrit dans cet article, vous avez besoin des éléments suivants :

* Visual Studio Code avec l’extension Outils Resource Manager. Consultez [Utiliser Visual Studio Code pour créer des modèles Azure Resource Manager](./resource-manager-tools-vs-code.md).
* Pour une sécurité optimale, utilisez un mot de passe généré pour le compte administrateur de la machine virtuelle. Voici un exemple pour générer un mot de passe :

    ```azurecli-interactive
    openssl rand -base64 32
    ```

    Azure Key Vault a été conçu pour protéger les clés et autres secrets de chiffrement. Pour plus d’informations, consultez [Tutoriel : Intégrer Azure Key Vault à un déploiement de modèle Resource Manager](./resource-manager-tutorial-use-key-vault.md). Nous vous recommandons également de mettre à jour votre mot de passe tous les trois mois.

## <a name="open-a-quickstart-template"></a>Ouvrir un modèle de démarrage rapide

Modèles de démarrage rapide Azure est un référentiel pour les modèles Resource Manager. Au lieu de créer un modèle à partir de zéro, vous pouvez chercher un exemple de modèle et le personnaliser. Le modèle utilisé dans ce didacticiel se nomme [Déployer une machine virtuelle Windows simple](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. À partir de Visual Studio Code, sélectionnez **Fichier**>**Ouvrir un fichier**.
2. Collez l’URL suivante dans **Nom de fichier** :

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

3. Sélectionnez **Ouvrir** pour ouvrir le fichier.
4. Il existe cinq ressources définies par le modèle :

   * `Microsoft.Storage/storageAccounts`. Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * `Microsoft.Network/publicIPAddresses`. Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * `Microsoft.Network/virtualNetworks`. Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * `Microsoft.Network/networkInterfaces`. Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * `Microsoft.Compute/virtualMachines`. Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

     Il est préférable de comprendre quelques notions basiques du modèle avant de le personnaliser.
5. Sélectionnez **Fichier**>**Enregistrer sous** pour enregistrer une copie du fichier sur votre ordinateur local avec le nom **azuredeploy.json**.

## <a name="modify-the-template"></a>Modifier le modèle

Apportez deux modifications au modèle existant :

* Ajoutez un paramètre de nom de compte de stockage. Les utilisateurs peuvent spécifier un nouveau nom de compte de stockage ou un nom de compte de stockage existant.
* Ajoutez un nouveau paramètre appelé **newOrExisting**. Le déploiement utilise ce paramètre pour déterminer s’il faut créer un compte de stockage ou utiliser un compte de stockage existant.

Voici la procédure pour apporter les modifications :

1. Ouvrez **azuredeploy.json** dans Visual Studio Code.
2. Remplacez les trois **variables('storageAccountName')** par **parameters('storageAccountName')** dans le modèle entier.
3. Supprimez la définition de variable suivante :

    ![Diagramme de condition d’utilisation d’un modèle Resource Manager](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-remove-storageaccountname.png)

4. Ajoutez les deux paramètres suivants au modèle :

    ```json
    "storageAccountName": {
      "type": "string"
    },
    "newOrExisting": {
      "type": "string",
      "allowedValues": [
        "new",
        "existing"
      ]
    },
    ```

    La définition de paramètres mise à jour ressemble à :

    ![Condition d’utilisation de Resource Manager](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-parameters.png)

5. Ajoutez la ligne suivante au début de la définition du compte de stockage.

    ```json
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    ```

    La condition vérifie la valeur d’un paramètre appelé **newOrExisting**. Si la valeur du paramètre est **nouveau**, le déploiement crée le compte de stockage.

    La définition du compte de stockage mise à jour ressemble à :

    ![Condition d’utilisation de Resource Manager](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template.png)
6. Mettez à jour la propriété **storageUri** de la définition de ressource de machine virtuelle avec la valeur suivante :

    ```json
    "storageUri": "[concat('https://', parameters('storageAccountName'), '.blob.core.windows.net')]"
    ```

    Cette modification est nécessaire lorsque vous utilisez un compte de stockage existant sous un autre groupe de ressources.

7. Enregistrez les modifications.

## <a name="deploy-the-template"></a>Déployer le modèle

Suivez les instructions fournies dans [Déployer le modèle](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) pour ouvrir Cloud Shell et charger le modèle modifié, puis exécutez le script PowerShell suivant pour déployer le modèle.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
$newOrExisting = Read-Host -Prompt "Create new or use existing (Enter new or existing)"
$location = Read-Host -Prompt "Enter the Azure location (i.e. centralus)"
$vmAdmin = Read-Host -Prompt "Enter the admin username"
$vmPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$dnsLabelPrefix = Read-Host -Prompt "Enter the DNS Label prefix"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUsername $vmAdmin `
    -adminPassword $vmPassword `
    -dnsLabelPrefix $dnsLabelPrefix `
    -storageAccountName $storageAccountName `
    -newOrExisting $newOrExisting `
    -TemplateFile "$HOME/azuredeploy.json"
```

> [!NOTE]
> Le déploiement échoue si **newOrExisting** est **nouveau**, mais le compte de stockage avec le nom de compte de stockage spécifié déjà existe.

Essayez d’effectuer un autre déploiement avec **newOrExisting** défini sur « existing » (existant) et spécifiez un compte de stockage existant. Pour créer un compte de stockage au préalable, consultez [Créer un compte de stockage](../storage/common/storage-quickstart-create-account.md).

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, nettoyez les ressources Azure que vous avez déployées en supprimant le groupe de ressources. Pour supprimer le groupe de ressources, sélectionnez **Essayez-le** afin d’ouvrir Cloud Shell. Pour coller le script PowerShell, cliquez sur le volet de l’interpréteur de commandes, puis sélectionnez **Coller**.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the same resource group name you used in the last procedure"
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez développé un modèle qui permet aux utilisateurs de choisir de créer un compte de stockage ou d’utiliser un compte de stockage existant. Pour savoir comment récupérer des secrets dans Azure Key Vault et utiliser les secrets comme mots de passe dans le déploiement de modèle, consultez :

> [!div class="nextstepaction"]
> [Intégrer Key Vault dans le déploiement de modèle](./resource-manager-tutorial-use-key-vault.md)
