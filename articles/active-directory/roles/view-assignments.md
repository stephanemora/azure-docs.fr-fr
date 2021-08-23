---
title: Répertorier les attributions de rôle Azure AD
description: Vous pouvez désormais afficher et gérer les membres d’un rôle d’administrateur Azure Active Directory dans le centre d’administration Azure Active Directory.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 05/14/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f8fe57b5b6df03e135bf77e9064e14f376c57ac9
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110796355"
---
# <a name="list-azure-ad-role-assignments"></a>Répertorier les attributions de rôle Azure AD

Cet article explique comment répertorier les rôles que vous avez attribués dans Azure Active Directory (Azure AD). Dans Azure Active Directory (Azure AD), les rôles peuvent être affectés au niveau de l’organisation ou d’une seule application.

- Les attributions de rôles au niveau de l’étendue de l’organisation sont ajoutées à et peuvent être consultées dans la liste des attributions de rôle d’application unique.
- Les attributions de rôles au niveau d’une application unique ne sont pas ajoutées à et ne peuvent pas être affichées dans la liste des attributions à l’échelle de l’organisation.

## <a name="prerequisites"></a>Prérequis

- Module AzureADPreview (avec PowerShell)
- Consentement administrateur (avec l’Afficheur Graph pour l’API Microsoft Graph)

Pour plus d’informations, consultez [Prérequis pour utiliser PowerShell ou l’Afficheur Graph](prerequisites.md).

## <a name="list-role-assignments-in-the-azure-portal"></a>Répertorier les attributions de rôle dans le portail Azure

Cette procédure décrit comment répertorier les attributions de rôle avec une étendue à l’échelle de l’organisation.

1. Connectez-vous au [Centre d’administration Azure AD](https://aad.portal.azure.com).
1. Sélectionnez **Azure Active Directory**, sélectionnez **Rôles et administrateurs**, puis sélectionnez un rôle pour l’ouvrir et voir ses propriétés.
1. Sélectionnez **Attributions** pour répertorier les attributions de rôle.

    ![Répertorier les attributions de rôle et les autorisations lorsque vous ouvrez un rôle dans la liste](./media/view-assignments/role-assignments.png)

## <a name="list-my-role-assignments"></a>Répertorier mes attributions de rôle

Il est également facile de répertorier vos propres autorisations. Sélectionnez **Votre rôle** dans la page **Rôles et administrateurs** pour afficher les rôles qui sont actuellement attribués.

## <a name="download-role-assignments"></a>Télécharger les attributions de rôle

Pour télécharger toutes les attributions d’un rôle spécifique, dans la page **Rôles et administrateurs**, sélectionnez un rôle, puis sélectionnez **Télécharger les attributions de rôle**. Un fichier CSV qui liste les attributions sur toutes les étendues pour ce rôle est téléchargé.

![Télécharger toutes les attributions d’un rôle](./media/view-assignments/download-role-assignments.png)

## <a name="list-role-assignments-using-powershell"></a>Répertorier les attributions de rôle à l’aide de PowerShell

Cette section décrit l’affichage des attributions d’un rôle avec une étendue à l’échelle de l’organisation. Cet article utilise le module [Azure Active Directory PowerShell Version 2](/powershell/module/azuread/#directory_roles). Pour afficher les attributions pour une application unique à l’aide de PowerShell, vous pouvez utiliser les applets de commande dans [Attribuer des rôles personnalisés avec PowerShell.](custom-assign-powershell.md)

Exemple de liste des attributions de rôle.

``` PowerShell
# Fetch list of all directory roles with object ID
Get-AzureADDirectoryRole

# Fetch a specific directory role by ID
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"

# Fetch role membership for a role
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
```

## <a name="list-role-assignments-using-the-microsoft-graph-api"></a>Répertorier les attributions de rôle à l’aide de l’API Microsoft Graph

Cette section décrit comment répertorier les attributions de rôle avec une étendue à l’échelle de l’organisation.  Pour répertorier les attributions de rôle à l’échelle d’une seule application à l’aide de l’API Graph, vous pouvez utiliser les opérations dans [Attribuer des rôles personnalisés avec l’API Graph](custom-assign-graph.md).

Requête HTTP pour obtenir une attribution de rôle pour une définition de rôle donnée

GET

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments&$filter=roleDefinitionId eq ‘<object-id-or-template-id-of-role-definition>’
```

response

``` HTTP
HTTP/1.1 200 OK
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":["/"]
}
```

## <a name="list-role-assignments-with-single-application-scope"></a>Répertorier les attributions de rôle à l’échelle d’une seule application

Cette section décrit comment répertorier les attributions de rôle à l’échelle d’une seule application. Cette fonctionnalité est actuellement disponible en préversion publique.

1. Connectez-vous au [Centre d’administration Azure AD](https://aad.portal.azure.com).
1. Sélectionnez **Inscriptions d’applications**, puis sélectionnez l’inscription d’application pour afficher ses propriétés. Vous devrez peut-être sélectionner **Toutes les applications** pour afficher la liste complète des inscriptions d’applications dans votre organisation Azure AD.

    ![Créer ou modifier des inscriptions d’applications à partir de la page Inscriptions d’applications](./media/view-assignments/app-reg-all-apps.png)

1. Lors de l’inscription de l’application, sélectionnez **Rôles et administrateurs**, puis sélectionnez un rôle pour voir ses propriétés.

    ![Répertorier les attributions de rôle de l’inscription d’application à partir de la page Inscriptions d’applications](./media/view-assignments/app-reg-assignments.png)

1. Sélectionnez **Attributions** pour répertorier les attributions de rôle. L’ouverture de la page des attributions dans le cadre de l’inscription de l’application vous montre les attributions de rôle à l’échelle de cette ressource Azure AD.

    ![Répertorier les attributions de rôle de l’inscription d’application à partir des propriétés d’une inscription d’application](./media/view-assignments/app-reg-assignments-2.png)

## <a name="next-steps"></a>Étapes suivantes

* N’hésitez pas à nous donner votre avis sur le [forum des rôles d’administrateur Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Pour plus d’informations sur les autorisations associées aux rôles, consultez [Rôles intégrés Azure AD](permissions-reference.md).
* Pour les autorisations d’utilisateur par défaut, consultez une [comparaison des autorisations par défaut d’un utilisateur invité et d’un membre](../fundamentals/users-default-permissions.md).
