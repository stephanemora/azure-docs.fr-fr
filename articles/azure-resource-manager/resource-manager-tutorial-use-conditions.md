---
title: Utiliser une condition dans des modèles Azure Resource Manager | Microsoft Docs
description: Découvrez comment déployer des ressources Azure en fonction des conditions.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/02/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 216e474f519e57352b017dc3e6bcdd74d48b03de
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48238644"
---
# <a name="tutorial-use-condition-in-azure-resource-manager-templates"></a>Didacticiel : utiliser une condition dans des modèles Azure Resource Manager

Découvrez comment déployer des ressources Azure en fonction des conditions. 

Le scénario utilisé dans ce didacticiel est similaire à celui utilisé dans [Didacticiel : créer des modèles Azure Resource Manager avec des ressources dépendantes](./resource-manager-tutorial-create-templates-with-dependent-resources.md). Dans ce didacticiel, vous créez un compte de stockage, une machine virtuelle, un réseau virtuel et d’autres ressources dépendantes. Plutôt que de créer un compte de stockage, vous laissez le choix aux utilisateurs de créer un compte de stockage ou d’utiliser un compte de stockage existant. Vous définissez pour cela un paramètre supplémentaire. Si la valeur du paramètre est « nouveau », un nouveau compte de stockage est créé.

Ce tutoriel décrit les tâches suivantes :

> [!div class="checklist"]
> * Ouvrir un modèle de démarrage rapide
> * Modifier le modèle
> * Déployer le modèle
> * Supprimer des ressources

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce qui est décrit dans cet article, vous avez besoin des éléments suivants :

* [Visual Studio Code](https://code.visualstudio.com/) avec l’[extension Outils Resource Manager](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)

## <a name="open-a-quickstart-template"></a>Ouvrir un modèle de démarrage rapide

Modèles de démarrage rapide Azure est un référentiel pour les modèles Resource Manager. Au lieu de créer un modèle à partir de zéro, vous pouvez chercher un exemple de modèle et le personnaliser. Le modèle utilisé dans ce didacticiel se nomme [Déployer une machine virtuelle Windows simple](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. À partir de Visual Studio Code, sélectionnez **Fichier**>**Ouvrir un fichier**.
2. Collez l’URL suivante dans **Nom de fichier** :

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Sélectionnez **Ouvrir** pour ouvrir le fichier.
4. Sélectionnez **Fichier**>**Enregistrer sous** pour enregistrer une copie du fichier sur votre ordinateur local avec le nom **azuredeploy.json**.

## <a name="modify-the-template"></a>Modifier le modèle

Apportez deux modifications au modèle existant :

* Ajoutez un paramètre utilisé pour fournir un nom de compte de stockage. Ce paramètre permet à l’utilisateur de spécifier un nom de compte de stockage existant. Il peut également être utilisé comme nouveau nom de compte de stockage.
* Ajoutez un nouveau paramètre appelé **newOrExisting**. Le déploiement utilise ce paramètre pour déterminer où créer un compte de stockage ou utiliser un compte de stockage existant.

1. Ouvrez **azuredeploy.json** dans Visual Studio Code.
2. Remplacez **variables('storageAccountName')** par **parameters('storageAccountName')** dans le modèle entier.  **variables('storageAccountName')** peut avoir trois apparences.
3. Supprimez la définition de variable suivante :

    ```json
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]",
    ```
4. Ajoutez les deux paramètres suivants au modèle :

    ```json
    "newOrExisting": {
      "type": "string"
    },
    "storageAccountName": {
      "type": "string"
    },
    ```
    La définition de paramètres mise à jour ressemble à :

    ![Condition d’utilisation de Resource Manager](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-parameters.png)

5. Ajoutez la ligne suivante au début de la définition du compte de stockage.

    ```json
    "condition": "[equals(parameters('newOrExisting'),'yes')]",
    ```

    La condition vérifie la valeur d’un paramètre appelé **newOrExisting**. Si la valeur du paramètre est **nouveau**, le déploiement crée le compte de stockage.

    La définition du compte de stockage mise à jour ressemble à :

    ![Condition d’utilisation de Resource Manager](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template.png)

6. Enregistrez les modifications.

## <a name="deploy-the-template"></a>Déployer le modèle

Suivez les instructions de la section [Déployer le modèle](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) pour déployer le modèle.

Lorsque vous déployez le modèle à l’aide d’Azure PowerShell, vous devez spécifier un paramètre supplémentaire :

```powershell
$resourceGroupName = "<Enter the resource group name>"
$storageAccountName = "Enter the storage account name>"
$location = "<Enter the Azure location>"
$vmAdmin = "<Enter the admin username>"
$vmPassword = "<Enter the password>"
$dnsLabelPrefix = "<Enter the prefix>"

New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
$vmPW = ConvertTo-SecureString -String $vmPassword -AsPlainText -Force
New-AzureRmResourceGroupDeployment -Name mydeployment0710 -ResourceGroupName $resourceGroupName `
    -TemplateFile azuredeploy.json -adminUsername $vmAdmin -adminPassword $vmPW `
    -dnsLabelPrefix $dnsLabelPrefix -storageAccountName $storageAccountName -newOrExisting "new"
```

> [!NOTE]
> Le déploiement échoue si **newOrExisting** est **nouveau**, mais le compte de stockage avec le nom de compte de stockage spécifié déjà existe.

Essayez d’effectuer un autre déploiement avec **newOrExisting** défini sur « existant » et spécifiez un compte de stockage existant. Pour créer un compte de stockage au préalable, consultez [Créer un compte de stockage](../storage/common/storage-quickstart-create-account.md).

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, nettoyez les ressources Azure que vous avez déployées en supprimant le groupe de ressources.

1. Dans le portail Azure, sélectionnez **Groupe de ressources** dans le menu de gauche.
2. Entrez le nom du groupe de ressources dans le champ **Filtrer par nom**.
3. Sélectionnez le nom du groupe de ressources.  Vous devriez voir six ressources au total dans le groupe de ressources.
4. Sélectionnez **Supprimer le groupe de ressources** dans le menu supérieur.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous développez un modèle qui permet aux utilisateurs de choisir de créer un compte de stockage ou d’utiliser un compte de stockage existant. La machine virtuelle créée dans ce didacticiel requiert un nom d’utilisateur et un mot de passe administrateur. Plutôt que de transmettre le mot de passe pendant le déploiement, vous pouvez stocker au préalable le mot de passe à l’aide d’Azure Key Vault et récupérer le mot de passe pendant le déploiement. Pour savoir comment récupérer des secrets dans Azure Key Vault et utiliser les secrets dans le déploiement de modèle, consultez :

> [!div class="nextstepaction"]
> [Intégrer Key Vault dans le déploiement de modèle](./resource-manager-tutorial-use-key-vault.md)
