---
title: Guide pratique pour gérer une identité managée affectée par l’utilisateur Azure à l’aide de REST
description: Instructions étape par étape pour créer, lister et supprimer une identité managée affectée par l’utilisateur afin d’effectuer des appels d’API REST.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/26/2018
ms.author: daveba
ms.openlocfilehash: a6241c105019f04df09080a89e8fe3b77b5f9385
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42888761"
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-rest-api-calls"></a>Créer, lister ou supprimer une identité affectée par l’utilisateur à l’aide d’appels d’API REST

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

L’identité managée offre aux services Azure la possibilité de s’authentifier auprès de services prenant en charge l’authentification Azure AD, sans avoir recours à des informations d’identification dans votre code. 

Dans cet article, découvrez comment créer, lister et supprimer une identité managée affectée par l'utilisateur à l’aide de CURL pour effectuer des appels d’API REST.

## <a name="prerequisites"></a>Prérequis

- Si vous ne connaissez pas Managed Service Identity, consultez la [section Vue d’ensemble](overview.md). **Veillez à consulter [la différence entre les identité affectées par le système et celles affectées par l’utilisateur](overview.md#how-does-it-work)**.
- Si vous n’avez pas encore de compte Azure, [inscrivez-vous à un essai gratuit](https://azure.microsoft.com/free/) avant de continuer.
- Si vous utilisez Windows, installez le [sous-système Windows pour Linux](https://msdn.microsoft.com/commandline/wsl/about) ou utilisez [Azure Cloud Shell](../../cloud-shell/overview.md) dans le portail Azure.
- Si vous utilisez le [sous-système Windows pour Linux](https://msdn.microsoft.com/commandline/wsl/about) ou un [système d’exploitation de distribution Linux](/cli/azure/install-azure-cli-apt?view=azure-cli-latest), [installez la console locale Azure CLI](/cli/azure/install-azure-cli).
- Si vous utilisez la console locale Azure CLI, connectez-vous à Azure à l’aide de `az login` avec un compte associé à l’abonnement Azure sous lequel vous souhaitez déployer ou récupérer des informations sur les identités managées affectées par l’utilisateur.
- Pour effectuer les opérations de gestion dans cet article, votre compte doit disposer des attributions des rôles suivants :
    - [Contributeur d’identité managée](/azure/role-based-access-control/built-in-roles#managed-identity-contributor), pour créer, lire (lister), mettre à jour et supprimer une identité affectée par l’utilisateur.
    - [Opérateur d’identité managée](/azure/role-based-access-control/built-in-roles#managed-identity-operator) pour lire (lister) les propriétés d’une identité affectée par l’utilisateur.
- Récupérez un jeton d’accès du porteur en utilisant `az account get-access-token` pour effectuer les opérations suivantes relatives aux identités managées affectées par l’utilisateur.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Créer une identité managée attribuée à l’utilisateur 

Pour créer une identité managée affectée par l’utilisateur, utilisez la requête CURL suivante dans l’API Azure Resource Manager. Remplacez les valeurs `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, `<USER ASSIGNED IDENTITY NAME>`, `<LOCATION>` et `<ACCESS TOKEN>` par vos propres valeurs :

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X PUT -d '{"loc
ation": "<LOCATION>"}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
```

## <a name="list-user-assigned-managed-identities"></a>Lister les identités managées affectées par l’utilisateur

Pour lister les identités managées affectées par l’utilisateur, utilisez la requête CURL suivante dans l’API Azure Resource Manager. Remplacez les valeurs `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` et `<ACCESS TOKEN>` par vos propres valeurs :

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```
## <a name="delete-a-user-assigned-managed-identity"></a>Supprimer une identité managée affectée par l’utilisateur

Pour supprimer une identité managée affectée par l’utilisateur, utilisez la requête CURL suivante dans l’API Azure Resource Manager. Remplacez les paramètres `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` et `<ACCESS TOKEN>` par vos propres valeurs :

> [!NOTE]
> La suppression d’une identité affectée par l’utilisateur ne supprime pas les références des ressources auxquelles elle a été affectée. Pour supprimer une identité affectée par l’utilisateur d’une machine virtuelle à l’aide de CURL, consultez [Supprimer une identité affectée par l’utilisateur d’une machine virtuelle Azure](qs-configure-rest-vm.md#remove-a-user-assigned identity-from-an-azure-vm).

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X DELETE -H "Authorization: Bearer <ACCESS TOKEN>"
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la façon d’attribuer une identité affectée par l’utilisateur à une machine virtuelle ou à un groupe de machines virtuelles identiques Azure à l’aide de CURL, consultez [Configurer une identité managée sur une machine virtuelle Azure à l’aide de CURL](qs-configure-rest-vm.md#user-assigned-identity) et [Configurer une identité managée sur un groupe de machines virtuelles identiques à l’aide de CURL](qs-configure-rest-vmss.md#user-assigned-identity).


