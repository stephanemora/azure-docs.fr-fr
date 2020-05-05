---
title: Création et configuration d’un coffre de clés pour Azure Disk Encryption
description: Cet article décrit les étapes de création et de configuration d’un coffre de clés à utiliser avec Azure Disk Encryption.
ms.service: virtual-machines-linux
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 0038d5fdb38fdcfd4130a710f51d764e0cf9d907
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81459812"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Création et configuration d’un coffre de clés pour Azure Disk Encryption

Azure Disk Encryption utilise Azure Key Vault pour contrôler et gérer les clés et les secrets de chiffrement de disque.  Pour plus d’informations sur les coffres de clés, consultez les articles [Prise en main du coffre de clés Azure](../../key-vault/key-vault-get-started.md) et [Sécuriser votre coffre de clés](../../key-vault/general/secure-your-key-vault.md). 

> [!WARNING]
> - Si vous avez déjà utilisé Azure Disk Encryption avec Azure AD pour chiffrer une machine virtuelle, vous devez continuer à utiliser cette option pour chiffrer votre machine virtuelle. Pour plus d’informations, consultez [Création et configuration d’un coffre de clés pour Azure Disk Encryption avec Azure AD (version précédente)](disk-encryption-key-vault-aad.md).

La création et la configuration d’un coffre de clés à utiliser avec Azure Disk Encryption impliquent trois étapes :

1. Création d’un groupe de ressources, si nécessaire
2. Création d’un coffre de clés 
3. Définition de stratégies d’accès avancé au coffre de clés

Ces étapes sont illustrées dans les guides de démarrage rapide suivants :

- [Créer et chiffrer une machine virtuelle Linux avec Azure CLI](disk-encryption-cli-quickstart.md)
- [Créer et chiffrer une machine virtuelle Linux avec Azure PowerShell](disk-encryption-cli-quickstart.md)

Si vous le souhaitez, vous pouvez également générer ou importer une clé de chiffrement principale (KEK, key encryption key).

> [!Note]
> Les étapes de cet article sont automatisées dans le [script d’interface CLI des prérequis Azure Disk Encryption](https://github.com/ejarvi/ade-cli-getting-started) et le [script PowerShell des prérequis Azure Disk Encryption](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).

## <a name="install-tools-and-connect-to-azure"></a>Installer les outils et se connecter à Azure

Les étapes décrites dans cet article peuvent être effectuées avec [Azure CLI](/cli/azure/), le [module Az Azure PowerShell](/powershell/azure/overview) ou le [portail Azure](https://portal.azure.com). 

Le portail est accessible par le biais de votre navigateur, alors qu’Azure CLI et Azure PowerShell nécessitent une installation locale. Pour plus d’informations, consultez [Azure Disk Encryption pour Linux : Installer les outils](disk-encryption-linux.md#install-tools-and-connect-to-azure).

### <a name="connect-to-your-azure-account"></a>Se connecter au compte Azure

Avant d’utiliser Azure CLI ou Azure PowerShell, vous devez vous connecter à votre abonnement Azure. Pour cela, vous devez [vous connecter avec Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), [vous connecter avec Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-2.5.0) ou fournir vos informations d’identification dans le portail Azure quand vous y êtes invité.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 ```
 
## Next steps

- [Azure Disk Encryption prerequisites CLI script](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption prerequisites PowerShell script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- Learn [Azure Disk Encryption scenarios on Linux VMs](disk-encryption-linux.md)
- Learn how to [troubleshoot Azure Disk Encryption](disk-encryption-troubleshooting.md)
- Read the [Azure Disk Encryption sample scripts](disk-encryption-sample-scripts.md)
