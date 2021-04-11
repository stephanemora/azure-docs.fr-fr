---
title: Stocker un secret multiligne dans Azure Key Vault
description: Tutoriel montrant comment définir des secrets multilignes à partir d’Azure Key Vault avec Azure CLI et PowerShell
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurecli
ms.date: 03/17/2021
ms.author: mbaldwin
ms.openlocfilehash: e320795d5e8623dea9b97ac6371a0ffc6e6117f1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608480"
---
# <a name="store-a-multi-line-secret-in-azure-key-vault"></a>Stocker un secret multiligne dans Azure Key Vault

Le [guide de démarrage rapide sur Azure CLI](quick-create-cli.md) et le [guide de démarrage rapide sur Azure PowerShell](quick-create-powershell.md) montrent comment stocker un secret monoligne.   Vous pouvez également utiliser Key Vault pour stocker un secret multiligne, tel qu’un fichier JSON ou une clé privée RSA.

Les secrets multilignes ne peuvent pas être passés à la commande Azure CLI [az keyvault secret set](/cli/azure/keyvault/secret#az_keyvault_secret_set) ou à l’applet de commande Azure PowerShell [Set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret) par le biais de la ligne de commande. Au lieu de cela, vous devez d’abord stocker le secret multiligne en tant que fichier texte. 

Par exemple, vous pouvez créer un fichier texte appelé « secretfile.txt » contenant les lignes suivantes :

```bash
This is my
multi-line
secret
```

Vous pouvez ensuite passer ce fichier à la commande Azure CLI [az keyvault secret set](/cli/azure/keyvault/secret#az_keyvault_secret_set) avec le paramètre `--file`.

```azurecli-interactive
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "MultilineSecret" --file "secretfile.txt"
```

Avec Azure PowerShell, vous devez d’abord lire le fichier avec l’applet de commande [Get-Content](/powershell/module/microsoft.powershell.management/get-content), puis le convertir en une chaîne sécurisée en utilisant [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring). 

```azurepowershell-interactive
$RawSecret =  Get-Content "secretfile.txt" -Raw
$SecureSecret = ConvertTo-SecureString -String $RawSecret -AsPlainText -Force
```

Enfin, vous stockez le secret avec l’applet de commande [Set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret).

```azurepowershell-interactive
$secret = Set-AzKeyVaultSecret -VaultName "<your-unique-keyvault-name>" -Name "MultilineSecret" -SecretValue $SecureSecret
```

Dans les deux cas, vous pouvez afficher le secret stocké avec la commande Azure CLI [az keyvault secret show](/cli/azure/keyvault/secret#az_keyvault_secret_show) ou l’applet de commande Azure PowerShell [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret).

```azurecli-interactive
az keyvault secret show --name "MultilineSecret" --vault-name "<your-unique-keyvault-name>" --query "value"
```

Le secret est retourné avec les nouvelles lignes incorporées :

```bash
"This is\nmy multi-line\nsecret"
```

## <a name="next-steps"></a>Étapes suivantes

- Lire la [vue d’ensemble Azure Key Vault](../general/overview.md)
- Consulter le [guide de démarrage rapide sur Azure CLI](quick-create-cli.md)
- Consulter les [commandes az keyvault Azure CLI](/cli/azure/keyvault)
- Consulter le [guide de démarrage rapide sur Azure PowerShell](quick-create-powershell.md)
- Consulter les [applets de commande Azure PowerShell Az.KeyVault](/powershell/module/az.keyvault#key-vault)
