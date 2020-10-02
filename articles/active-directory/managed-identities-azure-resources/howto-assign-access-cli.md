---
title: Attribuer à une identité managée l’accès à une ressource à l’aide d’Azure CLI - Azure AD
description: Instructions détaillées pour affecter à une identité managée sur une ressource l’accès à une autre ressource à l’aide d’Azure CLI.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2017
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: 13c69dda1e300bcff95b6a017fdeb308a6bbf3a4
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90969252"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-azure-cli"></a>Affecter à une identité managée l’accès à une ressource à l’aide d’Azure CLI

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Après avoir configuré une ressource Azure avec une identité managée, vous pouvez accorder à cette dernière un accès à une autre ressource, tout comme n’importe quel principal de sécurité. Cet exemple montre comment accorder à l’identité managée d’une machine virtuelle ou d’un groupe de machines virtuelles identiques Azure l’accès à un compte de stockage Azure, à l’aide d’Azure CLI.

## <a name="prerequisites"></a>Prérequis

- Si vous n’êtes pas familiarisé avec les identités managées pour ressources Azure, consultez la [section Vue d’ensemble](overview.md). **Veillez à consulter la [différence entre les identités managées affectées par le système et celles affectées par l’utilisateur](overview.md#managed-identity-types)** .
- Si vous n’avez pas encore de compte Azure, [inscrivez-vous à un essai gratuit](https://azure.microsoft.com/free/) avant de continuer.
- Pour exécuter les exemples de scripts, vous avez deux options :
    - Utiliser [Azure Cloud Shell](../../cloud-shell/overview.md), que vous pouvez ouvrir à l’aide du bouton **Essayer** dans le coin supérieur droit des blocs de code.
    - Exécuter les scripts localement en installant la dernière version d’[Azure CLI](/cli/azure/install-azure-cli), puis en vous connectant à Azure à l’aide d’[az login](/cli/azure/reference-index#az-login). Utilisez un compte associé à l’abonnement Azure dans lequel vous souhaitez créer des ressources.

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Utiliser Azure RBAC pour affecter à une identité managée l’accès à une autre ressource

Après avoir activé l’identité managée sur une ressource Azure, comme une [machine virtuelle Azure](qs-configure-cli-windows-vm.md) ou un [groupe de machines virtuelles identiques Azure](qs-configure-cli-windows-vmss.md) : 

1. Dans cet exemple, nous accordons à une machine virtuelle Azure l’accès à un compte de stockage. Tout d’abord, nous utilisons [az resource list](/cli/azure/resource/#az-resource-list) pour obtenir le principal du service pour la machine virtuelle nommée myVM :

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   Pour un groupe de machines virtuelles identiques, la commande est la même à l’exception que, ici, vous obtenez le principal du service pour le groupe de machines virtuelles identiques nommé « DevTestVMSS » :
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

1. Une fois l’ID de principal du service obtenu, utilisez [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) pour accorder au « Lecteur » de la machine virtuelle ou du groupe de machines virtuelles identiques l’accès à un compte de stockage appelé « myStorageAcct » :

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble des identités managées pour les ressources Azure](overview.md)
- Pour activer l’identité managée sur une machine virtuelle Azure, consultez [Configurer des identités managées pour ressources Azure sur une machine virtuelle Azure en utilisant Azure CLI](qs-configure-cli-windows-vm.md).
- Pour activer l’identité managée sur un groupe de machines virtuelles identiques Azure, consultez [Configurer des identités managées pour ressources Azure sur un groupe de machines virtuelles identiques en utilisant Azure CLI](qs-configure-cli-windows-vmss.md).
