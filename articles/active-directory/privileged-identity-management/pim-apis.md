---
title: API Microsoft Graph pour Azure AD Privileged Identity Management (PIM) (préversion) | Microsoft Docs
description: Fournit des informations sur l’utilisation de l’API Microsoft Graph pour Azure Active Directory Privileged Identity Management (PIM) (préversion).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: pim
ms.topic: overview
ms.date: 11/13/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: e27f4c937a97b955d4f0b879ad0199cc35dcadce
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52500195"
---
# <a name="microsoft-graph-apis-for-pim-preview"></a>API Microsoft Graph pour PIM (préversion)

La plupart des tâches que vous pouvez effectuer dans Azure AD Privileged Identity Management (PIM) à l’aide du portail Azure peuvent également être effectuées à l’aide de l’[API Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/overview). Cet article décrit certains concepts importants en cas d’utilisation des API Microsoft Graph pour PIM. Pour plus d’informations sur les API Microsoft Graph, consultez la [référence sur les API Azure AD Privileged Identity Management](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root).

> [!IMPORTANT]
> Les API sous la version /beta dans Microsoft Graph sont en préversion et susceptibles de changer. L’utilisation de ces API dans les applications de production n’est pas prise en charge.

## <a name="required-permissions"></a>Autorisations requises

Pour appeler les API Microsoft Graph pour PIM, vous devez avoir **une ou plusieurs** des autorisations suivantes :

- `Directory.AccessAsUser.All`
- `Directory.Read.All`
- `Directory.ReadWrite.All`
- `PrivilegedAccess.ReadWrite.AzureAD`

### <a name="set-permissions"></a>Définir des autorisations

Pour pouvoir appeler les API Microsoft Graph pour PIM, les applications doivent avoir les autorisations requises. Pour spécifier les autorisations requises, le plus simple consiste à utiliser le [framework de consentement Azure AD](../develop/consent-framework.md).

### <a name="set-permissions-in-graph-explorer"></a>Définir des autorisations dans l’Afficheur Graph

Si vous utilisez l’Afficheur Graph pour tester vos appels, vous pouvez spécifier les autorisations dans l’outil.

1. Connectez-vous à l’[Afficheur Graph](https://developer.microsoft.com/graph/graph-explorer) en tant qu’Administrateur général.

1. Cliquez sur **Modifier les autorisations**.

    ![Afficheur Graph - Modifier les autorisations](./media/pim-apis/graph-explorer.png)

1. Ajouter des coches en regard des autorisations que vous souhaitez inclure. `PrivilegedAccess.ReadWrite.AzureAD` n’est pas encore disponible dans l’Afficheur Graph.

    ![Afficheur Graph - Modifier les autorisations](./media/pim-apis/graph-explorer-modify-permissions.png)

1. Cliquez sur **Modifier les autorisations** pour appliquer les modifications d’autorisation.

## <a name="next-steps"></a>Étapes suivantes

- [Référence sur les API Azure AD Privileged Identity Management](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root)
