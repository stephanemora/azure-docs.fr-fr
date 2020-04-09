---
title: Utiliser Azure Key Vault dans les modèles
description: Découvrez comment utiliser Azure Key Vault pour transmettre des valeurs de paramètre sécurisées lors d’un déploiement de modèle Resource Manager.
author: mumian
ms.date: 05/23/2019
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: e49fafc2889b98d013d77471f8177fd85a307cc8
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754880"
---
# <a name="tutorial-integrate-azure-key-vault-in-your-arm-template-deployment"></a>Tutoriel : Intégrer Azure Key Vault à votre déploiement de modèle ARM

Découvrez comment récupérer les secrets d’un coffre de clés Azure et les passer en tant que paramètres quand vous déployez un modèle Azure Resource Manager (ARM). La valeur du paramètre n’est jamais exposée, car vous référencez uniquement son ID de coffre de clés. Pour plus d’informations, consultez l’article [Utiliser Azure Key Vault pour transmettre une valeur de paramètre sécurisée pendant le déploiement](./key-vault-parameter.md).

Dans le tutoriel [Définir l’ordre de déploiement des ressources](./template-tutorial-create-templates-with-dependent-resources.md), vous créez une machine virtuelle. Vous devez fournir le nom d’utilisateur et le mot de passe de l’administrateur de la machine virtuelle. Au lieu de fournir le mot de passe, vous pouvez le stocker au préalable dans le coffre de clés Azure Key Vault. Il vous suffit ensuite de personnaliser le modèle pour récupérer le mot de passe à partir du coffre de clés au cours du déploiement.

![Diagramme affichant l’intégration d’un modèle Resource Manager avec un coffre de clés](./media/template-tutorial-use-key-vault/resource-manager-template-key-vault-diagram.png)

Ce tutoriel décrit les tâches suivantes :

> [!div class="checklist"]
> * Préparer un coffre de clés
> * Ouvrir un modèle de démarrage rapide
> * Modifier le fichier de paramètres
> * Déployer le modèle
> * Valider le déploiement
> * Nettoyer les ressources

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce qui est décrit dans cet article, vous avez besoin des éléments suivants :

* Visual Studio Code avec l’extension Outils Resource Manager. Consultez [Utiliser Visual Studio Code pour créer des modèles ARM](use-vs-code-to-create-template.md).
* Pour une sécurité optimale, utilisez un mot de passe généré pour le compte administrateur de la machine virtuelle. Voici un exemple de génération de mot de passe :

    ```console
    openssl rand -base64 32
    ```
    Vérifiez que le mot de passe généré répond aux exigences relatives aux mots de passe de machine virtuelle. Chaque service Azure présente des exigences de mot de passe spécifiques. Pour connaître les exigences relatives aux mots de passe de machine virtuelle, consultez [Quelles sont les exigences en matière de mot de passe lors de la création d’une machine virtuelle ?](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

## <a name="prepare-a-key-vault"></a>Préparer un coffre de clés

Dans cette section, vous créez un coffre de clés auquel vous ajoutez un secret pour pouvoir le récupérer quand vous déployez votre modèle. Il existe de nombreuses façons de créer un coffre de clés. Dans ce tutoriel, vous utilisez Azure PowerShell pour déployer un [modèle ARM](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json). Ce modèle effectue les actions suivantes :

* Crée un coffre de clés avec activation de la propriété `enabledForTemplateDeployment`. Cette propriété doit avoir la valeur *true* pour que le processus de déploiement de modèle puisse accéder aux secrets définis dans le coffre de clés.
* Ajoute un secret au coffre de clés. Le secret stocke le mot de passe d’administrateur de la machine virtuelle.

> [!NOTE]
> Si, en tant qu’utilisateur déployant le modèle de machine virtuelle, vous n’êtes ni le propriétaire ni un contributeur du coffre de clés, son propriétaire ou un contributeur doit vous accorder l’accès à l’autorisation *Microsoft.KeyVault/vaults/deploy/action* pour le coffre de clés. Pour plus d’informations, consultez l’article [Utiliser Azure Key Vault pour transmettre une valeur de paramètre sécurisée pendant le déploiement](./key-vault-parameter.md).

Pour exécuter le script Azure PowerShell suivant, sélectionnez **Essayer** afin d’ouvrir Azure Cloud Shell. Pour coller le script, cliquez avec le bouton droit dans volet de l’interpréteur de commandes, puis sélectionnez **Coller**.

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

> [!IMPORTANT]
> * Le nom du groupe de ressources correspond au nom du projet auquel est ajouté **rg**. Pour faciliter le [nettoyage des ressources créées dans ce tutoriel](#clean-up-resources), utilisez le même nom de projet et le même nom de groupe de ressources quand vous [déployez le modèle suivant](#deploy-the-template).
> * Le nom par défaut du secret est **vmAdminPassword**. Il est codé en dur dans le modèle.
> * Pour permettre au modèle de récupérer le secret, vous devez activer une stratégie d’accès appelée « Activer l’accès à Azure Resource Manager pour le déploiement de modèles » pour le coffre de clés. Cette stratégie est activée dans le modèle. Pour plus d’informations sur la stratégie d’accès, consultez [Déployer des coffres de clés et des secrets](./key-vault-parameter.md#deploy-key-vaults-and-secrets).

Le modèle contient une seule valeur de sortie appelée *keyVaultId*. Notez la valeur de l’ID à utiliser plus tard quand vous déploierez la machine virtuelle. Le format d’ID de ressource est le suivant :

```json
/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
```

Quand vous copiez et collez l’ID, il peut figurer sur plusieurs lignes. Fusionnez les lignes et supprimez les espaces supplémentaires.

Pour valider le déploiement, exécutez la commande PowerShell suivante dans le même volet de l’interpréteur de commandes afin de récupérer le secret en texte clair. La commande fonctionne uniquement dans la même session d’interpréteur de commandes, car elle utilise la variable *$keyVaultName* définie dans le script PowerShell précédent.

```azurepowershell
(Get-AzKeyVaultSecret -vaultName $keyVaultName  -name "vmAdminPassword").SecretValueText
```

Vous avez préparé un coffre de clés et un secret. Les sections suivantes expliquent comment personnaliser un modèle existant pour récupérer le secret durant le déploiement.

## <a name="open-a-quickstart-template"></a>Ouvrir un modèle de démarrage rapide

Le dépôt Modèles de démarrage rapide Azure contient les modèles ARM. Au lieu de créer un modèle à partir de zéro, vous pouvez chercher un exemple de modèle et le personnaliser. Le modèle utilisé dans ce tutoriel se nomme [Déployer une machine virtuelle Windows simple](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. Dans Visual Studio Code, sélectionnez **Fichier** > **Ouvrir un fichier**.

1. Dans la zone **Nom de fichier**, collez l’URL suivante :

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Sélectionnez **Ouvrir** pour ouvrir le fichier. Le scénario est identique à celui utilisé dans [Tutoriel : Créer des modèles ARM avec des ressources dépendantes](./template-tutorial-create-templates-with-dependent-resources.md).
   Le modèle définit cinq ressources :

   * `Microsoft.Storage/storageAccounts`. Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * `Microsoft.Network/publicIPAddresses`. Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * `Microsoft.Network/virtualNetworks`. Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * `Microsoft.Network/networkInterfaces`. Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * `Microsoft.Compute/virtualMachines`. Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

   Il est préférable d’avoir des notions de base sur ce modèle avant de le personnaliser.

1. Sélectionnez **Fichier** > **Enregistrer sous**, puis enregistrez une copie du fichier sur votre ordinateur local sous le nom *azuredeploy.json*.

1. Répétez les étapes 1 à 3 pour accéder à l’URL ci-après, puis enregistrez le fichier sous le nom *azuredeploy.parameters.json*.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>Modifier le fichier de paramètres

Aucune modification de ce fichier de modèle n’est nécessaire.

1. Dans Visual Studio Code, ouvrez *azuredeploy.parameters.json*, s’il n’est pas déjà ouvert.
1. Mettez à jour le paramètre `adminPassword` comme suit :

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
    > Remplacez la valeur de **id** par l’ID de ressource du coffre de clés que vous avez créé au cours de la procédure précédente.

    ![Fichier de paramètres de déploiement de machine virtuelle pour l’intégration d’un coffre de clés à un modèle Resource Manager](./media/template-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)

1. Utilisez les valeurs suivantes :

    * **adminUsername** : nom du compte administrateur de la machine virtuelle.
    * **dnsLabelPrefix** : nom de la valeur de dnsLabelPrefix.

    Pour des exemples de noms, consultez l’image précédente.

1. Enregistrez les modifications.

## <a name="deploy-the-template"></a>Déployer le modèle

Suivez les instructions dans [Déployer le modèle](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Chargez *azuredeploy.json* et *azuredeploy.parameters.json* dans Cloud Shell, puis utilisez le script PowerShell suivant pour déployer le modèle :

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$location = Read-Host -Prompt "Enter the same location that is used for creating the key vault (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$HOME/azuredeploy.json" `
    -TemplateParameterFile "$HOME/azuredeploy.parameters.json"
```

Quand vous déployez le modèle, utilisez le même groupe de ressources que celui que vous avez utilisé dans le coffre de clés. Cette approche facilite le nettoyage des ressources, car vous devez supprimer uniquement un seul groupe de ressources au lieu de deux.

## <a name="validate-the-deployment"></a>Valider le déploiement

Une fois que vous avez réussi le déploiement de la machine virtuelle, testez les informations d’identification de connexion à l’aide du mot de passe stocké dans le coffre de clés.

1. Ouvrez le [portail Azure](https://portal.azure.com).

1. Sélectionnez **Groupes de ressources** >  **\<*NomDeVotreGroupeDeRessources*>**  > **MachineVirtuelleWindowsSimple**.
1. Sélectionnez **Se connecter** tout en haut.
1. Sélectionnez **Télécharger le fichier RDP**, puis suivez les instructions pour vous connecter à la machine virtuelle à l’aide du mot de passe stocké dans le coffre de clés.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Une fois que vous n’avez plus besoin de vos ressources Azure, nettoyez les ressources que vous avez déployées en supprimant le groupe de ressources.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez récupéré un secret dans votre coffre de clés Azure. Vous avez ensuite utilisé le secret dans votre déploiement de modèle. Pour découvrir comment utiliser les extensions de machine virtuelle afin d’exécuter les tâches post-déploiement, consultez :

> [!div class="nextstepaction"]
> [Déployer des extensions de machine virtuelle](./template-tutorial-deploy-vm-extensions.md)
