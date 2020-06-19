---
title: API Microsoft Graph pour PIM (préversion) – Azure AD| Microsoft Docs
description: Fournit des informations sur l’utilisation des API Microsoft Graph pour Azure AD Privileged Identity Management (préversion).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: how-to
ms.date: 01/02/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea3e307df95fe4cb18986ed8e68a7f3381c5fbe7
ms.sourcegitcommit: 24f31287b6a526e23ff5b5469113522d1ccd4467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/12/2020
ms.locfileid: "84742894"
---
# <a name="microsoft-graph-apis-for-privileged-identity-management-preview"></a>API Microsoft Graph pour Privileged Identity Management (préversion)

Vous pouvez effectuer les tâches Privileged Identity Management à l’aide des [API Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/overview) pour Azure Active Directory. Cet article décrit des concepts importants pour l’utilisation des API Microsoft Graph pour Privileged Identity Management.

Pour plus d’informations sur les API Microsoft Graph, consultez la [référence sur les API Azure AD Privileged Identity Management](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root).

> [!IMPORTANT]
> Les API sous la version /beta dans Microsoft Graph sont en préversion et susceptibles de changer. L’utilisation de ces API dans les applications de production n’est pas prise en charge.

## <a name="required-permissions"></a>Autorisations requises

Pour appeler les API Microsoft Graph pour Privileged Identity Management, vous devez avoir **une ou plusieurs** des autorisations suivantes :

- `Directory.AccessAsUser.All`
- `Directory.Read.All`
- `Directory.ReadWrite.All`
- `PrivilegedAccess.ReadWrite.AzureAD`

### <a name="set-permissions"></a>Définir des autorisations

Pour pouvoir appeler les API Microsoft Graph pour Privileged Identity Management, les applications doivent avoir les autorisations requises. Pour spécifier les autorisations requises, le plus simple consiste à utiliser le [framework de consentement Azure AD](../develop/consent-framework.md).

### <a name="set-permissions-in-graph-explorer"></a>Définir des autorisations dans l’Afficheur Graph

Si vous utilisez l’Afficheur Graph pour tester vos appels, vous pouvez spécifier les autorisations dans l’outil.

1. Connectez-vous à l’[Afficheur Graph](https://developer.microsoft.com/graph/graph-explorer) en tant qu’Administrateur général.

1. Cliquez sur **Modifier les autorisations**.

    ![Afficheur Graph - Modifier les autorisations](./media/pim-apis/graph-explorer.png)

1. Cochez les cases en regard des autorisations que vous souhaitez inclure. `PrivilegedAccess.ReadWrite.AzureAD` n’est pas encore disponible dans l’Afficheur Graph.

    ![Afficheur Graph - Modifier les autorisations](./media/pim-apis/graph-explorer-modify-permissions.png)

1. Cliquez sur **Modifier les autorisations** pour appliquer les modifications d’autorisation.

## <a name="next-steps"></a>Étapes suivantes

- [Référence sur les API Azure AD Privileged Identity Management](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root)
