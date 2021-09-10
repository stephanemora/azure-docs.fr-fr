---
title: Afficher les rôles attribués à un groupe dans Azure Active Directory | Microsoft Docs
description: Découvrez la façon dont les rôles attribués à un groupe peuvent être consultés à l’aide du portail Azure. La consultation des groupes et des rôles attribués est une autorisation utilisateur par défaut.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 05/14/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87e59c7ab9bfc5fa9211d84d2d9a855dea97ec1a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122531808"
---
# <a name="view-roles-assigned-to-a-group-in-azure-active-directory"></a>Afficher les rôles attribués à un groupe dans Azure Active Directory

Cette section décrit la façon dont les rôles attribués à un groupe peuvent être consultés à l’aide du portail Azure. La consultation des groupes et des rôles attribués est une autorisation utilisateur par défaut.

## <a name="prerequisites"></a>Prérequis

- Module AzureAD (avec PowerShell)
- Consentement administrateur (avec l'Afficheur Graph pour l'API Microsoft Graph)

Pour plus d’informations, consultez [Prérequis pour utiliser PowerShell ou de l’Afficheur Graph](prerequisites.md).

## <a name="azure-portal"></a>Portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com) ou au [centre d’administration Azure AD](https://aad.portal.azure.com).

1. Sélectionnez **Azure Active Directory** > **Groupes**.

1. Sélectionnez un groupe assignable à un rôle qui vous intéresse.

1. Sélectionnez **Rôles attribués**. Vous pouvez maintenant voir tous les rôles Azure AD attribués à ce groupe.

   ![Afficher tous les rôles attribués à un groupe sélectionné](./media/groups-view-assignments/view-assignments.png)

## <a name="powershell"></a>PowerShell

### <a name="get-object-id-of-the-group"></a>Obtenir l’ID d’objet du groupe

```powershell
Get-AzureADMSGroup -SearchString "Contoso_Helpdesk_Administrators"
```

### <a name="view-role-assignment-to-a-group"></a>Afficher l’attribution de rôle d’un groupe

```powershell
Get-AzureADMSRoleAssignment -Filter "principalId eq '<object id of group>" 
```

## <a name="microsoft-graph-api"></a>API Microsoft Graph

### <a name="get-object-id-of-the-group"></a>Obtenir l’ID d’objet du groupe

```http
GET https://graph.microsoft.com/beta/groups?$filter=displayName+eq+'Contoso_Helpdesk_Administrator'
```

### <a name="get-role-assignments-to-a-group"></a>Obtenir les attributions de rôles d’un groupe

```http
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments?$filter=principalId eq
```

## <a name="next-steps"></a>Étapes suivantes

- [Utiliser des groupes Azure AD pour gérer les attributions de rôles](groups-concept.md)
- [Résoudre les problèmes de rôles Azure AD attribués aux groupes](groups-faq-troubleshooting.yml)
