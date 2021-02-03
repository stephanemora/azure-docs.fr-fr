---
title: 'Démarrage rapide : Créer un coffre de clés Azure Key Vault avec Azure PowerShell'
description: Démarrage rapide montrant comment créer un coffre de clés Azure Key Vault avec Azure PowerShell
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: e77493bc73bc2d6f590d9bdcf891171fbd71f74e
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070190"
---
# <a name="quickstart-create-a-key-vault-using-powershell"></a>Démarrage rapide : Créer un coffre de clés à l’aide de PowerShell

Azure Key Vault est un service cloud qui fonctionne comme un magasin sécurisé contenant des [clés](../keys/index.yml), des [secrets](../secrets/index.yml) et des [certificats](../certificates/index.yml). Pour plus d’informations sur Key Vault, consultez [À propos d’Azure Key Vault](overview.md) ; pour plus d’informations sur ce qui peut être stocké dans un coffre de clés, consultez [À propos des clés, des secrets et des certificats](about-keys-secrets-certificates.md).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Dans ce démarrage rapide, vous créez un coffre de clés avec [Azure PowerShell](/powershell/azure/). Si vous choisissez d’installer et d’utiliser PowerShell en local, ce tutoriel nécessite le module Azure PowerShell version 1.0.0 ou ultérieure. Tapez `$PSVersionTable.PSVersion` pour connaître la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-az-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Login-AzAccount` pour créer une connexion avec Azure.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

[!INCLUDE [Create a resource group](../../../includes/key-vault-powershell-rg-creation.md)]

## <a name="create-a-key-vault"></a>Création d’un coffre de clés

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-kv-creation.md)]

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un coffre de clés à l’aide d’Azure PowerShell. Pour en savoir plus sur Key Vault et sur la manière de l’intégrer à vos applications, consultez les articles ci-dessous.

- Lire la [vue d’ensemble Azure Key Vault](overview.md)
- Consulter la référence des [applets de commande Azure PowerShell Key Vault](/powershell/module/az.keyvault/)
- Passer en revue la [Vue d’ensemble de la sécurité d’Azure Key Vault](security-overview.md)

