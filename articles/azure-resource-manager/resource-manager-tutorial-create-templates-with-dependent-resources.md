---
title: Créer des modèles Azure Resource Manager avec des ressources dépendantes | Microsoft Docs
description: Découvrez comment créer votre premier modèle Azure Resource Manager avec plusieurs ressources, et comment le déployer dans le portail Azure
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 07/20/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: bd559cb9f0140706a4b9735c642367e03616a14d
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188163"
---
# <a name="tutorial-create-azure-resource-manager-templates-with-dependent-resources"></a>Didacticiel : créer des modèles Azure Resource Manager avec des ressources dépendantes

Découvrez comment créer un modèle Azure Resource Manager pour déployer plusieurs ressources.  Après avoir créé le modèle, vous déployez le modèle à l’aide du Cloud Shell dans le portail Azure.

Certaines ressources ne peuvent pas être déployées avant qu’une autre ressource n’existe. Par exemple, vous ne pouvez pas créer la machine virtuelle avant que son compte de stockage et son interface réseau n’existent. Vous définissez cette relation en rendant une seule ressource dépendante des autres ressources. Resource Manager évalue les dépendances entre les ressources et les déploie dans leur ordre dépendant. Quand les ressources ne dépendent pas les unes des autres, Resource Manager les déploie en parallèle. Pour plus d’informations, consultez [Définir l’ordre de déploiement des ressources dans les modèles Azure Resource Manager](./resource-group-define-dependencies.md).

> [!div class="checklist"]
> * Ouvrir un modèle de démarrage rapide
> * Explorer le modèle
> * Déployer le modèle
> * Supprimer les ressources

Les instructions fournies dans ce didacticiel créent une machine virtuelle, un réseau virtuel, et d’autres ressources dépendantes. 

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce qui est décrit dans cet article, vous avez besoin des éléments suivants :

* [Visual Studio Code](https://code.visualstudio.com/).
* Extension Outils Azure Resource Manager.  Consultez [Installer l’extension ](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)

## <a name="open-a-quickstart-template"></a>Ouvrir un modèle de démarrage rapide

Modèles de démarrage rapide Azure est un référentiel pour les modèles Resource Manager. Au lieu de créer un modèle à partir de zéro, vous pouvez chercher un exemple de modèle et le personnaliser. Le modèle utilisé dans ce didacticiel se nomme [Déployer une machine virtuelle Windows simple](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. À partir de Visual Studio Code, sélectionnez **Fichier**>**Ouvrir un fichier**.
2. Collez l’URL suivante dans **Nom de fichier** :

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Sélectionnez **Ouvrir** pour ouvrir le fichier.
4. Sélectionnez **Fichier**>**Enregistrer sous** pour enregistrer une copie du fichier sur votre ordinateur local avec le nom **azuredeploy.json**.

## <a name="explore-the-template"></a>Explorer le modèle

1. À partir de Visual Studio Code, réduisez les éléments jusqu'à ce que vous voyiez uniquement les éléments de premier niveau et les éléments de second niveau à l’intérieur des **ressources** :

    ![Modèles Azure Resource Manager Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    Il existe cinq ressources définies par le modèle.
2. Développez le quatrième élément :

    ![Élément dependson des modèles Azure Resource Manager Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependson.png)

    L’élément dependsOn vous permet de définir une ressource comme une dépendance sur une ou plusieurs ressources. Dans cet exemple, cette ressource est une networkInterface.  Elle dépend de deux autres ressources :

    * publicIpAddress
    * virtualNetwork

3. Développez le cinquième élément. Cette ressource est une machine virtuelle. Elle dépend de deux autres ressources :

    * storageAccount
    * networkInterface

Le diagramme suivant illustre les ressources et les informations de dépendance pour ce modèle :

![Diagramme de dépendance des modèles Azure Resource Manager Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

En spécifiant les dépendances, Resource Manager déploie efficacement la solution. Il déploie le compte de stockage, l’adresse IP publique et le réseau virtuel en parallèle car ils n’ont aucune dépendance. Après que l’adresse IP publique et le réseau virtuel sont déployés, l’interface réseau est créée. Lorsque toutes les autres ressources sont déployées, Resource Manager déploie la machine virtuelle.

## <a name="deploy-the-template"></a>Déployer le modèle

Il existe de nombreuses méthodes pour déployer des modèles.  Dans ce didacticiel, vous utilisez Cloud Shell dans le portail Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com)
2. Sélectionnez **Cloud Shell** à partir de l’angle supérieur droit, comme indiqué dans l’image suivante :

    ![Cloud shell du portail Azure](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell.png)
3. Sélectionnez **PowerShell** à partir de l’angle supérieur gauche de Cloud Shell.  Vous utiliserez PowerShell dans ce didacticiel.
4. Sélectionnez **Redémarrer**
5. Sélectionnez **Charger le fichier** à partir de Cloud Shell :

    ![Fichier de chargement du Cloud Shell du portail Azure](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-upload-file.png)
6. Sélectionnez le fichier que vous avez enregistré précédemment dans ce didacticiel. Le nom par défaut est **azuredeploy.json**.  Si vous avez un fichier portant le même nom de fichier, l’ancien fichier est remplacé sans notification.
7. Dans le Cloud Shell, exécutez la commande suivante pour vérifier que le fichier est chargé avec succès. 

    ```shell
    ls
    ```

    ![Fichier de liste du Cloud Shell du portail Azure](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-list-file.png)

    Le nom de fichier affiché sur la capture d’écran est azuredeploy.json.

8. Dans le Cloud Shell, exécutez la commande suivante pour vérifier le contenu du fichier JSON :

    ```shell
    cat azuredeploy.json
    ```
9. Dans le Cloud Shell, exécutez les commandes PowerShell suivantes :

    ```powershell
    $resourceGroupName = "<Enter the resource group name>"
    $location = "<Enter the Azure location>"
    $vmAdmin = "<Enter the admin username>"
    $vmPassword = "<Enter the password>"
    $dnsLabelPrefix = "<Enter the prefix>"

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    $vmPW = ConvertTo-SecureString -String $vmPassword -AsPlainText -Force
    New-AzureRmResourceGroupDeployment -Name mydeployment0710 -ResourceGroupName $resourceGroupName `
        -TemplateFile azuredeploy.json -adminUsername $vmAdmin -adminPassword $vmPW `
        -dnsLabelPrefix $dnsLabelPrefix
    ```
    Voici une capture d’écran pour un exemple de déploiement :

    ![déploiement du modèle du Cloud shell du portail Azure](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-deploy-template.png)

    Sur la capture d’écran, les valeurs suivantes sont utilisées :

    * **$resourceGroupName** : myresourcegroup0710. 
    * **$location** : eastus2
    * **&lt;DeployName>**  : mydeployment0710
    * **&lt;TemplateFile>** : azuredeploy.json
    * **Paramètres du modèle** :

        * **adminUsername** : JohnDole
        * **adminPassword** : Pass@word123
        * **dnsLabelPrefix** : myvm0710

10. Exécutez la commande PowerShell suivante pour lister les machines virtuelles nouvellement créées :

    ```powershell
    Get-AzureRmVM -Name SimpleWinVM -ResourceGroupName <ResourceGroupName>
    ```

    Le nom de la machine virtuelle est codé en dur en tant que **SimpleWinVM** à l’intérieur du modèle.

## <a name="clean-up-resources"></a>Supprimer les ressources

Lorsque vous n’en avez plus besoin, nettoyez les ressources Azure que vous avez déployées en supprimant le groupe de ressources.

1. Dans le portail Azure, sélectionnez **Groupe de ressources** dans le menu de gauche.
2. Entrez le nom du groupe de ressources dans le champ **Filtrer par nom**.
3. Sélectionnez le nom du groupe de ressources.  Vous devriez voir six ressources au total dans le groupe de ressources.
4. Sélectionnez **Supprimer le groupe de ressources** dans le menu supérieur.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous allez développer et déployer un modèle pour créer une machine virtuelle, un réseau virtuel et les ressources dépendantes. Pour apprendre à créer des modèles, consultez [Comprendre la structure et la syntaxe des modèles Azure Resource Manager](./resource-group-authoring-templates.md).