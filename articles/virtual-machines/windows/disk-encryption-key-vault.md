---
title: Création et configuration d’un coffre de clés pour Azure Disk Encryption
description: Cet article décrit les étapes de création et de configuration d’un coffre de clés à utiliser avec Azure Disk Encryption.
ms.service: virtual-machines
ms.subservice: security
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 0348461e2a58bd8e71b8dff10667c512125d7b03
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83656707"
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

- [Créer et chiffrer une machine virtuelle Windows avec Azure CLI](disk-encryption-cli-quickstart.md)
- [Créer et chiffrer une machine virtuelle Windows avec Azure PowerShell](disk-encryption-powershell-quickstart.md)

Si vous le souhaitez, vous pouvez également générer ou importer une clé de chiffrement principale (KEK, key encryption key).

> [!Note]
> Les étapes de cet article sont automatisées dans le [script d’interface CLI des prérequis Azure Disk Encryption](https://github.com/ejarvi/ade-cli-getting-started) et le [script PowerShell des prérequis Azure Disk Encryption](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).

## <a name="install-tools-and-connect-to-azure"></a>Installer les outils et se connecter à Azure

Les étapes décrites dans cet article peuvent être effectuées avec [Azure CLI](/cli/azure/), le [module Az Azure PowerShell](/powershell/azure/overview) ou le [portail Azure](https://portal.azure.com).

Le portail est accessible par le biais de votre navigateur, alors qu’Azure CLI et Azure PowerShell nécessitent une installation locale. Pour plus d’informations, consultez [Azure Disk Encryption pour Windows : Installer les outils](disk-encryption-windows.md#install-tools-and-connect-to-azure).

### <a name="connect-to-your-azure-account"></a>Se connecter au compte Azure

Avant d’utiliser Azure CLI ou Azure PowerShell, vous devez vous connecter à votre abonnement Azure. Pour cela, vous devez [vous connecter avec Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), [vous connecter avec Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-2.5.0) ou fournir vos informations d’identification dans le portail Azure quand vous y êtes invité.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>Étapes suivantes

- [Script d’interface CLI des prérequis Azure Disk Encryption](https://github.com/ejarvi/ade-cli-getting-started)
- [Script PowerShell des prérequis Azure Disk Encryption](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- En savoir plus sur les [scénarios Azure Disk Encryption sur les machines virtuelles Windows](disk-encryption-windows.md)
- En savoir plus sur la [résolution des problèmes liés à Azure Disk Encryption](disk-encryption-troubleshooting.md)
- Lire les [exemples de scripts Azure Disk Encryption](disk-encryption-sample-scripts.md)
