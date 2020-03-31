---
title: Création et configuration d’un coffre de clés pour Azure Disk Encryption
description: Cet article décrit les étapes de création et de configuration d’un coffre de clés à utiliser avec Azure Disk Encryption.
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: eec5b42da709ab5e79da42f11600f6ffc81d247e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279013"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Création et configuration d’un coffre de clés pour Azure Disk Encryption

Azure Disk Encryption utilise Azure Key Vault pour contrôler et gérer les clés et les secrets de chiffrement de disque.  Pour plus d’informations sur les coffres de clés, consultez les articles [Prise en main du coffre de clés Azure](../key-vault/key-vault-get-started.md) et [Sécuriser votre coffre de clés](../key-vault/key-vault-secure-your-key-vault.md).

La création et la configuration d’un coffre de clés à utiliser avec Azure Disk Encryption impliquent trois étapes :

1. Création d’un groupe de ressources, si nécessaire
2. Création d’un coffre de clés 
3. Définition de stratégies d’accès avancé au coffre de clés

Ces étapes sont illustrées dans les guides de démarrage rapide suivants :

Si vous le souhaitez, vous pouvez également générer ou importer une clé de chiffrement principale (KEK, key encryption key).

## <a name="install-tools-and-connect-to-azure"></a>Installer les outils et se connecter à Azure

Les étapes décrites dans cet article peuvent être effectuées avec [Azure CLI](/cli/azure/), le [module Az Azure PowerShell](/powershell/azure/overview) ou le [portail Azure](https://portal.azure.com).

### <a name="connect-to-your-azure-account"></a>Se connecter au compte Azure

Avant d’utiliser Azure CLI ou Azure PowerShell, vous devez vous connecter à votre abonnement Azure. Pour cela, vous devez [vous connecter avec Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), [vous connecter avec Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-2.5.0) ou fournir vos informations d’identification dans le portail Azure quand vous y êtes invité.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble d’Azure Disk Encryption](disk-encryption-overview.md)
- [Chiffrer un groupe de machines virtuelles identiques à l’aide d’Azure CLI](disk-encryption-cli.md)
- [Chiffrer un groupe de machines virtuelles identiques à l’aide d’Azure PowerShell](disk-encryption-powershell.md)
