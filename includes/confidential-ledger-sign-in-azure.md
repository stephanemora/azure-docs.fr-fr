---
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: include
ms.date: 05/05/2021
ms.author: msmbaldwin
ms.openlocfilehash: 985c50f39476c3b1c52670f06d51fde5b3156978
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110385999"
---
Connectez-vous à Azure à l’aide de la commande Azure CLI [az login](/cli/azure/reference-index#az_login) ou de la cmdlet Azure PowerShell [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli-interactive
az login
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell-interactive
Connect-AzAccount
```
---

Si Azure CLI ou PowerShell peut ouvrir votre navigateur par défaut, il le fait et charge une page de connexion Azure. Sinon, visitez [https://aka.ms/devicelogin](https://aka.ms/devicelogin) et entrez le code d’autorisation affiché dans votre terminal.

Si on vous le demande, connectez-vous ensuite avec les informations d’identification de votre compte dans le navigateur.
