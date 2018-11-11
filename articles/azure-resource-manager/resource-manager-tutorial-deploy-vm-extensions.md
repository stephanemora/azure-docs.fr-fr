---
title: Déployer des extensions de machines virtuelles avec des modèles Azure Resource Manager | Microsoft Docs
description: Découvrez comment déployer des extensions de machines virtuelles avec des modèles Azure Resource Manager
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/30/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: ca07f044a423a4ea1b5dee484c56f457c7d785de
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50239729"
---
# <a name="tutorial-deploy-virtual-machine-extensions-with-azure-resource-manager-templates"></a>Didacticiel : Déployer des extensions de machines virtuelles avec des modèles Azure Resource Manager

Découvrez comment utiliser des [extensions de machines virtuelles Azure](../virtual-machines/extensions/features-windows.md) pour exécuter des tâches de configuration et d’automatisation post-déploiement sur des machines virtuelles Azure. De nombreuses extensions de machine virtuelle différentes peuvent être utilisées avec les machines virtuelles Azure. Dans ce didacticiel, vous allez déployer une extension de script personnalisée à partir d’un modèle Resource Manager pour exécuter un script PowerShell sur une machine virtuelle Windows.  Le script installe le serveur Web sur la machine virtuelle.

Ce tutoriel décrit les tâches suivantes :

> [!div class="checklist"]
> * Préparer un script PowerShell
> * Ouvrir un modèle de démarrage rapide
> * Modifier le modèle
> * Déployer le modèle
> * Vérifier le déploiement

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce qui est décrit dans cet article, vous avez besoin des éléments suivants :

* [Visual Studio Code](https://code.visualstudio.com/) avec l’extension Outils Resource Manager.  Consultez [Installer l’extension ](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Pour une sécurité optimale, utilisez un mot de passe généré pour le compte administrateur de la machine virtuelle. Voici un exemple pour générer un mot de passe :

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Azure Key Vault a été conçu pour protéger les clés et autres secrets de chiffrement. Pour plus d’informations, consultez [Didacticiel : Intégrer Azure Key Vault à un déploiement de modèle Resource Manager](./resource-manager-tutorial-use-key-vault.md). Nous vous recommandons également de mettre à jour votre mot de passe tous les trois mois.

## <a name="prepare-a-powershell-script"></a>Préparer un script PowerShell

Un script PowerShell avec le contenu suivant est partagé à partir d’un [compte de stockage Azure muni de l’accès public](https://armtutorials.blob.core.windows.net/usescriptextensions/installWebServer.ps1) :

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Si vous choisissez de publier le fichier sur votre propre emplacement, vous devrez mettre à jour l’élément [fileUri] du modèle plus tard dans ce didacticiel.

## <a name="open-a-quickstart-template"></a>Ouvrir un modèle de démarrage rapide

Modèles de démarrage rapide Azure est un référentiel pour les modèles Resource Manager. Au lieu de créer un modèle à partir de zéro, vous pouvez chercher un exemple de modèle et le personnaliser. Le modèle utilisé dans ce didacticiel se nomme [Déployer une machine virtuelle Windows simple](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. À partir de Visual Studio Code, sélectionnez **Fichier**>**Ouvrir un fichier**.
2. Collez l’URL suivante dans **Nom de fichier** :

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Sélectionnez **Ouvrir** pour ouvrir le fichier.
4. Il existe cinq ressources définies par le modèle :

    * `Microsoft.Storage/storageAccounts`. Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
    * `Microsoft.Network/publicIPAddresses`. Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
    * `Microsoft.Network/virtualNetworks`. Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
    * `Microsoft.Network/networkInterfaces`. Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
    * `Microsoft.Compute/virtualMachines`. Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

    Il est préférable de comprendre quelques notions basiques du modèle avant de le personnaliser.
5. Sélectionnez **Fichier**>**Enregistrer sous** pour enregistrer une copie du fichier sur votre ordinateur local avec le nom **azuredeploy.json**.

## <a name="edit-the-template"></a>Modifier le modèle

Ajoutez une ressource d’extension de machine virtuelle au modèle existant avec le contenu suivant :

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "[concat(variables('vmName'),'/', 'InstallWebServer')]",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/',variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.7",
        "autoUpgradeMinorVersion":true,
        "settings": {
            "fileUris": [
                "https://armtutorials.blob.core.windows.net/usescriptextensions/installWebServer.ps1"
            ],
            "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File installWebServer.ps1"
        }
    }
}
```

Consultez les [informations de référence sur les extensions](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines/extensions) si vous avez besoin d’en savoir plus sur cette définition de ressource. Voici quelques éléments importants :

* **name** : Étant donné que la ressource d’extension est une ressource enfant de l’objet de machine virtuelle, le nom doit être composé du préfixe du nom de la machine virtuelle. Voir [Ressources enfants](./resource-manager-templates-resources.md#child-resources).
* **dependsOn** : La ressource d’extension doit être créée une fois que la machine virtuelle a été créée.
* **fileUris** : Il s’agit des emplacements où sont stockés les fichiers de script. Si vous choisissez de ne pas utiliser celui fourni, vous devez mettre à jour les valeurs.
* **commandToExecute** : Il s’agit de la commande pour appeler le script.  

## <a name="deploy-the-template"></a>Déployer le modèle

Reportez-vous à la section [Déployer le modèle](./resource-manager-tutorial-create-multiple-instances.md#deploy-the-template) pour connaître la procédure de déploiement. Il est recommandé d’utiliser un mot de passe généré pour le compte administrateur de la machine virtuelle. Consultez les [Conditions préalables](#prerequisites).

## <a name="verify-the-deployment"></a>Vérifier le déploiement

Dans le portail, sélectionnez la machine virtuelle. Ensuite, dans la vue d’ensemble de la machine virtuelle, utilisez le bouton **Cliquer pour copier** à droite de l’adresse IP pour la copier-coller dans un onglet de navigateur. La page d’accueil IIS par défaut s’ouvre et devrait ressembler à ceci :

![Azure Resource Manager déploie le serveur Web IIS de script client d’extensions de machine virtuelle](./media/resource-manager-tutorial-deploy-vm-extensions/resource-manager-template-deploy-extensions-customer-script-web-server.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, nettoyez les ressources Azure que vous avez déployées en supprimant le groupe de ressources.

1. Dans le portail Azure, sélectionnez **Groupe de ressources** dans le menu de gauche.
2. Entrez le nom du groupe de ressources dans le champ **Filtrer par nom**.
3. Sélectionnez le nom du groupe de ressources.  Vous devriez voir six ressources au total dans le groupe de ressources.
4. Sélectionnez **Supprimer le groupe de ressources** dans le menu supérieur.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez déployé une machine virtuelle et une extension de machine virtuelle. L’extension a installé le serveur Web IIS sur la machine virtuelle. Pour savoir comment utiliser l’extension SQL Database pour importer un fichier BACPAC, consultez :

> [!div class="nextstepaction"]
> [](./resource-manager-tutorial-deploy-vm-extensions.md)
