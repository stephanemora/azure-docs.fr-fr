---
title: Démarrage rapide Azure - Définir et récupérer un secret depuis Key Vault à l’aide de PowerShell | Microsoft Docs
description: ''
services: key-vault
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 1126f665-2e6c-4cca-897e-7d61842e8334
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 08/28/2018
ms.author: barclayn
ms.openlocfilehash: f3ea500c8e48f4a509782657ad2fe750bf3a7ed6
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2018
ms.locfileid: "44025652"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-powershell"></a>Démarrage rapide : définir et récupérer un secret depuis Azure Key Vault à l’aide de PowerShell

Azure Key Vault est un service cloud qui fonctionne comme un magasin des secrets sécurisé. Vous pouvez stocker des clés, des mots de passe, des certificats et d’autres secrets en toute sécurité. Pour plus d’informations sur Key Vault, consultez la [présentation](key-vault-overview.md). Dans ce démarrage rapide, vous allez utiliser PowerShell pour créer un coffre de clés. Ensuite, vous stockerez un secret dans le coffre que vous venez de créer.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, ce didacticiel nécessite le module Azure PowerShell version 5.1.1 ou ultérieure. Exécutez `Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Login-AzureRmAccount` pour créer une connexion avec Azure.

```azurepowershell-interactive
Login-AzureRmAccount
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources Azure avec [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. 

```azurepowershell-interactive
New-AzureRmResourceGroup -Name ContosoResourceGroup -Location EastUS
```

## <a name="create-a-key-vault"></a>Créer un coffre de clés

Vous allez à présent créer un coffre de clés. Lorsque vous effectuez cette opération, vous devez disposer de certaines informations :

Bien que nous utilisions « Contoso KeyVault2 » comme nom pour notre coffre de clés dans ce démarrage rapide, vous devez utiliser un nom unique.

- **Nom du coffre** Contoso-Vault2.
- **Nom du groupe de ressources** ContosoResourceGroup.
- **Emplacement** USA Est.

```azurepowershell-interactive
New-AzureRmKeyVault -VaultName 'Contoso-Vault2' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
```

La sortie de cette cmdlet affiche les propriétés du coffre de clés que vous venez de créer. Notez les deux propriétés ci-dessous :

* **Nom du coffre** : dans l’exemple, il s’agit de **Contoso-Vault2**. Vous allez utiliser ce nom pour les autres applets de commande Key Vault.
* **URI du coffre** : dans l’exemple, il s’agit de https://contosokeyvault.vault.azure.net/. Les applications qui utilisent votre coffre via son API REST doivent utiliser cet URI.

Une fois le coffre créé, votre compte Azure est le seul compte autorisé à exécuter des actions sur ce nouveau coffre.

![Sortie après l’exécution de la commande de création du coffre de clés](./media/quick-create-powershell/output-after-creating-keyvault.png)

## <a name="adding-a-secret-to-key-vault"></a>Ajout d’un secret au coffre de clés

Pour ajouter un secret au coffre, vous devez effectuer deux opérations. Dans ce cas précis, vous ajoutez un mot de passe qu’une application est susceptible d’utiliser. Le mot de passe est appelé **ExamplePassword** et stocke la valeur « **Pa$$w0rd** ».

Commencez par convertir la valeur de Pa$$w0rd en une chaîne sécurisée en tapant la commande suivante :

```azurepowershell-interactive
$secretvalue = ConvertTo-SecureString 'Pa$$w0rd' -AsPlainText -Force
```

Ensuite, tapez les commandes PowerShell ci-après pour créer un secret dans le coffre de clés, appelé **ExamplePassword**, avec la valeur **Pa$$w0rd** :

```azurepowershell-interactive
$secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'ExamplePassword' -SecretValue $secretvalue
```

Pour afficher sous forme de texte brut la valeur contenue dans le secret :

```azurepowershell-interactive
(Get-AzureKeyVaultSecret -vaultName "Contosokeyvault" -name "ExamplePassword").SecretValueText
```

Vous venez de créer un coffre de clés, d’y stocker un secret et de récupérer ce dernier.

## <a name="clean-up-resources"></a>Supprimer les ressources

 D’autres guides de démarrage rapide et didacticiels de cette collection reposent sur ce guide. Si vous prévoyez d’utiliser d’autres démarrages rapides et didacticiels, il peut être utile de conserver ces ressources.

Quand vous n’en avez plus besoin, vous pouvez utiliser la commande [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) pour supprimer le groupe de ressources, le coffre de clés et toutes les ressources associées.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez créé un coffre de clés et vous y avez stocké une clé logicielle. Pour en savoir plus sur Key Vault et sur son utilisation avec vos applications, passez au didacticiel sur les applications web qui utilisent Key Vault.

> [!div class="nextstepaction"]
> Pour apprendre à lire un secret dans un coffre de clés à partir d’une application web en utilisant des identités managées pour les ressources Azure, passez au tutoriel suivant, [Configurer une application web Azure pour lire un secret dans le coffre de clés](quick-create-net.md).
