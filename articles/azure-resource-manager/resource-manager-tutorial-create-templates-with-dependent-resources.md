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
ms.date: 10/09/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 50f1c81f08787181de2fe3a9f6fb97a96a2bd882
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114310"
---
# <a name="tutorial-create-azure-resource-manager-templates-with-dependent-resources"></a>Didacticiel : créer des modèles Azure Resource Manager avec des ressources dépendantes

Découvrez comment créer un modèle Azure Resource Manager pour déployer plusieurs ressources.  Après avoir créé le modèle, vous déployez le modèle à l’aide du Cloud Shell dans le portail Azure.

Dans ce didacticiel, vous créez un compte de stockage, une machine virtuelle, un réseau virtuel et d’autres ressources dépendantes. Certaines ressources ne peuvent pas être déployées avant qu’une autre ressource n’existe. Par exemple, vous ne pouvez pas créer la machine virtuelle avant que son compte de stockage et son interface réseau n’existent. Vous définissez cette relation en rendant une seule ressource dépendante des autres ressources. Resource Manager évalue les dépendances entre les ressources et les déploie dans leur ordre dépendant. Quand les ressources ne dépendent pas les unes des autres, Resource Manager les déploie en parallèle. Pour plus d’informations, consultez [Définir l’ordre de déploiement des ressources dans les modèles Azure Resource Manager](./resource-group-define-dependencies.md).

Ce tutoriel décrit les tâches suivantes :

> [!div class="checklist"]
> * Préparer le coffre Key Vault
> * Ouvrir un modèle de démarrage rapide
> * Explorer le modèle
> * Modifier le fichier de paramètres
> * Déployer le modèle

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce qui est décrit dans cet article, vous avez besoin des éléments suivants :

* [Visual Studio Code](https://code.visualstudio.com/) avec l’extension Outils Resource Manager.  Consultez [Installer l’extension ](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)

## <a name="prepare-key-vault"></a>Préparer Key Vault

Pour empêcher les attaques par pulvérisation de mot de passe, il est recommandé d’utiliser un mot de passe généré automatiquement pour le compte d’administrateur de machine virtuelle et de le stocker dans Key Vault. La procédure suivante crée un coffre Key Vault et un secret pour stocker le mot de passe. Elle permet aussi de configurer les autorisations nécessaires pour le déploiement du modèle permettant d’accéder au secret stocké dans le coffre Key Vault. Des stratégies d’accès supplémentaires sont nécessaires si le coffre Key Vault se trouve dans un abonnement Azure différent. Pour obtenir des informations, consultez [Utiliser Azure Key Vault pour transmettre une valeur de paramètre sécurisée pendant le déploiement](./resource-manager-keyvault-parameter.md).

1. Connectez-vous à [Azure Cloud Shell](https://shell.azure.com).
2. Basculez sur votre environnement préféré, **PowerShell** ou **Bash** depuis le coin supérieur gauche.
3. Exécutez la commande Azure PowerShell ou Azure CLI suivante.  

    ```azurecli-interactive
    keyVaultName='<your-unique-vault-name>'
    resourceGroupName='<your-resource-group-name>'
    location='Central US'
    userPrincipalName='<your-email-address-associated-with-your-subscription>'
    
    # Create a resource group
    az group create --name $resourceGroupName --location $location
    
    # Create a Key Vault
    keyVault=$(az keyvault create \
      --name $keyVaultName \
      --resource-group $resourceGroupName \
      --location $location \
      --enabled-for-template-deployment true)
    keyVaultId=$(echo $keyVault | jq -r '.id')
    az keyvault set-policy --upn $userPrincipalName --name $keyVaultName --secret-permissions set delete get list

    # Create a secret
    password=$(openssl rand -base64 32)
    az keyvault secret set --vault-name $keyVaultName --name 'vmAdminPassword' --value $password
    
    # Print the useful property values
    echo "You need the following values for the virtual machine deployment:"
    echo "Resource group name is: $resourceGroupName."
    echo "The admin password is: $password."
    echo "The Key Vault resource ID is: $keyVaultId."
    ```

    ```azurepowershell-interactive
    $keyVaultName = "<your-unique-vault-name>"
    $resourceGroupName="<your-resource-group-name>"
    $location='Central US'
    $userPrincipalName="<your-email-address-associated-with-your-subscription>"
    
    # Create a resource group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
        
    # Create a Key Vault
    $keyVault = New-AzureRmKeyVault `
      -VaultName $keyVaultName `
      -resourceGroupName $resourceGroupName `
      -Location $location `
      -EnabledForTemplateDeployment
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userPrincipalName -PermissionsToSecrets set,delete,get,list
      
    # Create a secret
    $password = openssl rand -base64 32
    
    $secretValue = ConvertTo-SecureString $password -AsPlainText -Force
    Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name "vmAdminPassword" -SecretValue $secretValue
    
    # Print the useful property values
    echo "You need the following values for the virtual machine deployment:"
    echo "Resource group name is: $resourceGroupName."
    echo "The admin password is: $password."
    echo "The Key Vault resource ID is: " $keyVault.ResourceID
    ```
4. Notez les valeurs de sortie. Vous en aurez besoin plus loin dans le didacticiel

> [!NOTE]
> Chaque service Azure présente des exigences de mot de passe spécifiques. Par exemple, les exigences de la machine virtuelle Azure sont disponibles dans Quelles sont les exigences en matière de mot de passe lors de la création d’une machine virtuelle ?

## <a name="open-a-quickstart-template"></a>Ouvrir un modèle de démarrage rapide

Modèles de démarrage rapide Azure est un référentiel pour les modèles Resource Manager. Au lieu de créer un modèle à partir de zéro, vous pouvez chercher un exemple de modèle et le personnaliser. Le modèle utilisé dans ce didacticiel se nomme [Déployer une machine virtuelle Windows simple](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. À partir de Visual Studio Code, sélectionnez **Fichier**>**Ouvrir un fichier**.
2. Collez l’URL suivante dans **Nom de fichier** :

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Sélectionnez **Ouvrir** pour ouvrir le fichier.
4. Sélectionnez **Fichier**>**Enregistrer sous** pour enregistrer une copie du fichier sur votre ordinateur local avec le nom **azuredeploy.json**.
5. Répétez les étapes 1 à 4 pour ouvrir **https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json**, puis enregistrez le fichier sous le nom **azuredeploy.parameters.json**.

## <a name="explore-the-template"></a>Explorer le modèle

Lorsque vous explorez le modèle dans cette section, essayez de répondre aux questions suivantes :

- Combien de ressources Azure sont-elles définies dans ce modèle ?
- L’une des ressources est un compte de stockage Azure.  La définition est-elle semblable à celle utilisée dans le dernier didacticiel ?
- Trouvez-vous les références de modèle pour les ressources définies dans ce modèle ?
- Trouvez-vous les dépendances des ressources ?

1. À partir de Visual Studio Code, réduisez les éléments jusqu'à ce que vous voyiez uniquement les éléments de premier niveau et les éléments de second niveau à l’intérieur des **ressources** :

    ![Modèles Azure Resource Manager Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    Il existe cinq ressources définies par le modèle.
2. Développez la première ressource. Il s’agit d’un compte de stockage. La définition doit être identique à celle utilisée au début du dernier didacticiel.

    ![Définition du compte de stockage des modèles Azure Resource Manager Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-storage-account-definition.png)

3. Développez la deuxième ressource. Le type de ressource est **Microsoft.Network/publicIPAddresses**. Pour rechercher la référence de modèle, accédez à [référence de modèle](https://docs.microsoft.com/azure/templates/), saisissez **adresse ip publique** ou **adresses ip publiques** dans le champ **Filtrer par titre**. Comparez la définition de ressource avec la référence de modèle.

    ![Définition de l’adresse IP publique des modèles Azure Resource Manager Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-public-ip-address-definition.png)
4. Répétez la dernière étape pour rechercher les références de modèle pour les autres ressources définies dans ce modèle.  Comparez les définitions de ressource avec les références.
5. Développez la quatrième ressource :

    ![Élément dependson des modèles Azure Resource Manager Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependson.png)

    L’élément dependsOn vous permet de définir une ressource comme une dépendance sur une ou plusieurs ressources. Dans cet exemple, cette ressource est une networkInterface.  Elle dépend de deux autres ressources :

    * publicIpAddress
    * virtualNetwork

6. Développez la cinquième ressource. Cette ressource est une machine virtuelle. Elle dépend de deux autres ressources :

    * storageAccount
    * networkInterface

Le diagramme suivant illustre les ressources et les informations de dépendance pour ce modèle :

![Diagramme de dépendance des modèles Azure Resource Manager Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

En spécifiant les dépendances, Resource Manager déploie efficacement la solution. Il déploie le compte de stockage, l’adresse IP publique et le réseau virtuel en parallèle car ils n’ont aucune dépendance. Après que l’adresse IP publique et le réseau virtuel sont déployés, l’interface réseau est créée. Lorsque toutes les autres ressources sont déployées, Resource Manager déploie la machine virtuelle.

## <a name="edit-the-parameters-file"></a>Modifier le fichier de paramètres

Aucune modification de ce fichier de modèle n’est nécessaire. Mais vous devez modifier le fichier de paramètres pour récupérer le mot de passe administrateur de Key Vault.

1. Ouvrez le fichier **azuredeploy.parameters.json** dans Visual Studio Code s’il n’est pas déjà ouvert.
2. Mettez à jour le paramètre **adminPassword** comme suit :

    ```json
    "adminPassword": {
        "reference": {
            "keyVault": {
            "id": "/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>"
            },
            "secretName": "vmAdminPassword"
        }
    },
    ```
    Remplacez la valeur de l’élément **id** par l’ID de ressource du coffre Key Vault que vous avez créé au cours de la dernière procédure. Il s’agit de l’une des sorties. 

    ![Fichier de paramètres de déploiement de machine virtuelle pour l’intégration d’un coffre Key Vault à un modèle Resource Manager](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)
3. Fournissez les valeurs suivantes :

    - **adminUsername** : nommez le compte administrateur de la machine virtuelle.
    - **dnsLabelPrefix** : nommez l’élément dnsLabelPrefix.
4. Enregistrez les modifications.

## <a name="deploy-the-template"></a>Déployer le modèle

Il existe de nombreuses méthodes pour déployer des modèles.  Dans ce didacticiel, vous utilisez Cloud Shell dans le portail Azure.

1. Connectez-vous à [Cloud Shell](https://shell.azure.com). Vous pouvez également vous connecter au [Portail Azure](https://portal.azure.com), puis sélectionner **Cloud Shell** à partir de l’angle supérieur droit, comme indiqué dans l’image suivante :

    ![Cloud shell du portail Azure](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell.png)
2. Sélectionnez **PowerShell** à partir de l’angle supérieur gauche de Cloud Shell, puis sélectionnez **Confirmer**.  Vous utiliserez PowerShell dans ce didacticiel.
3. Sélectionnez **Charger le fichier** à partir de Cloud Shell :

    ![Fichier de chargement du Cloud Shell du portail Azure](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-upload-file.png)
4. Sélectionnez les fichiers que vous avez enregistrés précédemment dans le didacticiel. Les noms par défaut sont **azuredeploy.json** et **azuredeploy.paraemters.json**.  Si vous avez des fichiers portant les mêmes noms, les fichiers plus anciens sont remplacés sans notification.
5. Dans le Cloud Shell, exécutez la commande suivante pour vérifier que le fichier est chargé avec succès. 

    ```bash
    ls
    ```

    ![Fichier de liste du Cloud Shell du portail Azure](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-list-file.png)

    Le nom de fichier affiché sur la capture d’écran est azuredeploy.json.

6. Dans le Cloud Shell, exécutez la commande suivante pour vérifier le contenu du fichier JSON :

    ```bash
    cat azuredeploy.json
    cat azuredeploy.parameters.json
    ```
7. Dans Cloud Shell, exécutez les commandes PowerShell suivantes. L’exemple de script utilise le même groupe de ressources que celui créé pour Key Vault. Le fait d’utiliser le même groupe simplifie la suppression des ressources.

    ```powershell
    $resourceGroupName = "<Enter the resource group name>"
    $deploymentName = "<Enter a deployment name>"

    New-AzureRmResourceGroupDeployment -Name $deploymentName `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile azuredeploy.json `
        -TemplateparameterFile azuredeploy.parameters.json
    ```
8. Exécutez la commande PowerShell suivante pour lister les machines virtuelles nouvellement créées :

    ```powershell
    Get-AzureRmVM -Name SimpleWinVM -ResourceGroupName $resourceGroupName
    ```

    Le nom de la machine virtuelle est codé en dur en tant que **SimpleWinVM** à l’intérieur du modèle.

9. Connectez-vous à la machine virtuelle pour tester les informations d’identification de l’administrateur. 

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, nettoyez les ressources Azure que vous avez déployées en supprimant le groupe de ressources.

1. Dans le portail Azure, sélectionnez **Groupe de ressources** dans le menu de gauche.
2. Entrez le nom du groupe de ressources dans le champ **Filtrer par nom**.
3. Sélectionnez le nom du groupe de ressources.  Vous devriez voir six ressources au total dans le groupe de ressources.
4. Sélectionnez **Supprimer le groupe de ressources** dans le menu supérieur.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous allez développer et déployer un modèle pour créer une machine virtuelle, un réseau virtuel et les ressources dépendantes. Pour savoir comment déployer des ressources Azure en fonction des conditions, consultez :


> [!div class="nextstepaction"]
> [Utiliser des conditions](./resource-manager-tutorial-use-conditions.md)

