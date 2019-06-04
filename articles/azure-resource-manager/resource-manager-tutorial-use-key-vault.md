---
title: Intégrer Azure Key Vault à un déploiement de modèle Resource Manager | Microsoft Docs
description: Découvrez comment utiliser Azure Key Vault pour transmettre des valeurs de paramètre sécurisées lors d’un déploiement de modèle Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 05/23/2019
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 0d78e6eaca708073c3a216507b320fe8783a25b6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239240"
---
# <a name="tutorial-integrate-azure-key-vault-in-resource-manager-template-deployment"></a>Tutoriel : Intégrer Azure Key Vault à un déploiement de modèle Resource Manager

Découvrez comment récupérer des secrets auprès d’Azure Key Vault et comment les passer en tant que paramètres lors d’un déploiement Resource Manager. La valeur n’est jamais exposée, car vous référencez uniquement son ID de coffre de clés. Pour plus d’informations, consultez l’article [Utiliser Azure Key Vault pour transmettre une valeur de paramètre sécurisée pendant le déploiement](./resource-manager-keyvault-parameter.md).

Dans le tutoriel [Définir l’ordre de déploiement des ressources](./resource-manager-tutorial-create-templates-with-dependent-resources.md), vous créez une machine virtuelle. Vous devez fournir le nom d’utilisateur et le mot de passe de l’administrateur de la machine virtuelle. Au lieu de fournir le mot de passe, vous pouvez stocker au préalable le mot de passe dans le coffre de clés Azure Key Vault, puis personnalisez le modèle pour récupérer le mot de passe dans le coffre de clés lors du déploiement.

![Diagramme d’intégration de Key Vault à un modèle Resource Manager](./media/resource-manager-tutorial-use-key-vault/resource-manager-template-key-vault-diagram.png)

Ce tutoriel décrit les tâches suivantes :

> [!div class="checklist"]
> * Préparer un coffre de clés
> * Ouvrir un modèle de démarrage rapide
> * Modifier le fichier de paramètres
> * Déployer le modèle
> * Valider le déploiement
> * Supprimer des ressources

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce qui est décrit dans cet article, vous avez besoin des éléments suivants :

* [Visual Studio Code](https://code.visualstudio.com/) avec l’[extension Outils Resource Manager](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Pour une sécurité optimale, utilisez un mot de passe généré pour le compte administrateur de la machine virtuelle. Voici un exemple pour générer un mot de passe :

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Assurez-vous que le mot de passe généré répond aux exigences en matière de mot de passe de machine virtuelle. Chaque service Azure présente des exigences de mot de passe spécifiques. Pour connaître les exigences relatives aux mots de passe de machine virtuelle, consultez la section [Quelles sont les exigences en matière de mot de passe lors de la création d’une machine virtuelle ?](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

## <a name="prepare-a-key-vault"></a>Préparer un coffre de clés

Dans cette section, vous créez un coffre de clés et y ajoutez un secret pour pouvoir récupérer la clé secrète lorsque vous déployez votre modèle. Il existe de nombreuses façons de créer un coffre de clés. Dans ce tutoriel, vous utilisez Azure PowerShell pour déployer un [modèle Resource Manager](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json). Ce modèle effectue les opérations suivantes :

* Créez un coffre de clés où la propriété `enabledForTemplateDeployment` est activée. Cette propriété doit être définie sur la valeur True pour que le processus de déploiement de modèle puisse accéder aux secrets définis dans ce coffre de clés.
* Ajoutez un secret au coffre de clés.  Ce secret stocke le mot de passe d’administrateur de la machine virtuelle.

> [!NOTE]
> Si, en votre qualité d’utilisateur déployant le modèle de machine virtuelle, vous n’êtes ni le propriétaire ni un contributeur du coffre de clés, son propriétaire ou l’un des contributeurs doit vous accorder l’accès à l’autorisation Microsoft.KeyVault/vaults/deploy/action pour ce coffre. Pour plus d’informations, consultez l’article [Utiliser Azure Key Vault pour transmettre une valeur de paramètre sécurisée pendant le déploiement](./resource-manager-keyvault-parameter.md).

Pour exécuter le script PowerShell suivant, sélectionnez **Try it** (Essayer) afin d’ouvrir le service Cloud Shell. Pour coller le script, cliquez avec le bouton droit dans volet de l’interpréteur de commandes, puis sélectionnez **Coller**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$upn = Read-Host -Prompt "Enter your user principal name (email address) used to sign in to Azure"
$secretValue = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"
$keyVaultName = $projectName
$adUserId = (Get-AzADUser -UserPrincipalName $upn).Id
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -keyVaultName $keyVaultName -adUserId $adUserId -secretValue $secretValue
```

Voici quelques d’informations importantes :

* Le nom du groupe de ressources est le nom du projet avec **rg** ajouté. Pour faciliter le [nettoyage des ressources créées dans ce tutoriel](#clean-up-resources), utilisez le même nom de projet et le même nom de groupe de ressources lorsque vous [déployez le modèle suivant](#deploy-the-template).
* Le nom par défaut du secret est **vmAdminPassword**. Il est codé en dur dans le modèle.
* Pour permettre au modèle de récupérer le secret, vous devez activer une stratégie d’accès appelée **Activer l'accès à Azure Resource Manager pour le déploiement de modèles** pour le coffre de clés. Cette stratégie est activée dans le modèle. Pour plus d’informations sur cette stratégie d’accès, consultez [Déployer des coffres de clés et des secrets](./resource-manager-keyvault-parameter.md#deploy-key-vaults-and-secrets).

Le modèle contient une seule valeur de sortie appelée **keyVaultId**. Notez la valeur. Vous devez disposer de cet ID lorsque vous déployez la machine virtuelle. Le format d’ID de ressource est le suivant :

```json
/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
```

Lorsque vous copiez et collez l’ID, celui peut apparaître sur plusieurs lignes. Vous devez fusionner les lignes et supprimer les espaces supplémentaires.

Pour valider le déploiement, exécutez la commande PowerShell suivante dans le même volet de l’interpréteur de commandes afin de récupérer le secret en texte clair. La commande fonctionne uniquement dans la même session d’interpréteur de commandes, car elle utilise une variable $keyVaultName définie dans le script PowerShell précédent.

```azurepowershell
(Get-AzKeyVaultSecret -vaultName $keyVaultName  -name "vmAdminPassword").SecretValueText
```

Maintenant que vous avez préparé un coffre de clés et un secret, les sections suivantes vous montrent comment personnaliser un modèle existant afin de récupérer le secret lors du déploiement.

## <a name="open-a-quickstart-template"></a>Ouvrir un modèle de démarrage rapide

Modèles de démarrage rapide Azure est un référentiel pour les modèles Resource Manager. Au lieu de créer un modèle à partir de zéro, vous pouvez chercher un exemple de modèle et le personnaliser. Le modèle utilisé dans ce didacticiel se nomme [Déployer une machine virtuelle Windows simple](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. À partir de Visual Studio Code, sélectionnez **Fichier**>**Ouvrir un fichier**.
2. Collez l’URL suivante dans **Nom de fichier** :

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

3. Sélectionnez **Ouvrir** pour ouvrir le fichier. Il s’agit du même scénario que celui utilisé dans l’article [Didacticiel : créer des modèles Azure Resource Manager avec des ressources dépendantes](./resource-manager-tutorial-create-templates-with-dependent-resources.md).
4. Il existe cinq ressources définies par le modèle :

   * `Microsoft.Storage/storageAccounts`. Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * `Microsoft.Network/publicIPAddresses`. Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * `Microsoft.Network/virtualNetworks`. Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * `Microsoft.Network/networkInterfaces`. Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * `Microsoft.Compute/virtualMachines`. Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

     Il est préférable de comprendre quelques notions basiques du modèle avant de le personnaliser.
5. Sélectionnez **Fichier**>**Enregistrer sous** pour enregistrer une copie du fichier sur votre ordinateur local avec le nom **azuredeploy.json**.
6. Répétez les étapes 1 à 4 pour accéder à l’URL ci-après, puis enregistrez le fichier sous le nom **azuredeploy.parameters.json**.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>Modifier le fichier de paramètres

Aucune modification de ce fichier de modèle n’est nécessaire.

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

    > [!IMPORTANT]
    > Remplacez la valeur **id** par l’ID de ressource du coffre de clés que vous avez créé lors de la dernière procédure.

    ![Fichier de paramètres de déploiement de machine virtuelle pour l’intégration d’un coffre Key Vault à un modèle Resource Manager](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)
3. Fournissez les valeurs suivantes :

    * **adminUsername** : nommez le compte administrateur de la machine virtuelle.
    * **dnsLabelPrefix** : nommez l’élément dnsLabelPrefix.

    Consultez l’exemple présenté dans la capture d’écran précédente.

4. Enregistrez les modifications.

## <a name="deploy-the-template"></a>Déployer le modèle

Suivez les instructions de la section [Déployer le modèle](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) pour déployer le modèle. Vous devez charger les fichiers **azuredeploy.json** et **azuredeploy.parameters.json** dans le service Cloud Shell, puis utiliser le script PowerShell ci-après pour déployer le modèle :

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$location = Read-Host -Prompt "Enter the same location that is used for creating the key vault (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$HOME/azuredeploy.json" `
    -TemplateParameterFile "$HOME/azuredeploy.parameters.json"
```

Lorsque vous déployez le modèle, utilisez le même groupe de ressources que celui du coffre de clés. Cette approche simplifie le nettoyage des ressources. En effet, vous ne devez supprimer qu’un groupe de ressources au lieu de deux.

## <a name="validate-the-deployment"></a>Valider le déploiement

Une fois que la machine virtuelle est déployée, testez la connexion à l’aide du mot de passe stocké dans le coffre de clés.

1. Ouvrez le [portail Azure](https://portal.azure.com).
2. Sélectionnez **Groupes de ressources**/**NomDeVotreGroupeDeRessources>** /**simpleWinVM**.
3. Sélectionnez **Connexion** dans la partie supérieure.
4. Sélectionnez **Télécharger le fichier RDP**, puis suivez les instructions pour vous connecter à la machine virtuelle à l’aide du mot de passe stocké dans le coffre de clés.

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, nettoyez les ressources Azure que vous avez déployées en supprimant le groupe de ressources.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Étapes suivantes

Dans le cadre de ce didacticiel, vous avez récupéré un secret auprès d’Azure Key Vault, puis vous avez utilisé ce secret dans votre déploiement de modèle.  Pour savoir comment créer des modèles liés, consultez l’article suivant :

> [!div class="nextstepaction"]
> [Créer des modèles liés](./resource-manager-tutorial-create-linked-templates.md)
