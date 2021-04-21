---
title: Gérer les administrateurs de serveur dans Azure Analysis Services | Microsoft Docs
description: Cet article explique comment gérer les administrateurs d’un serveur Azure Analysis Services avec le Portail Azure, PowerShell ou les API REST.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 2/4/2021
ms.author: owend
ms.reviewer: minewiskan
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c0986b8508a7e21dee7c7c87e535eb2726944de8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769190"
---
# <a name="manage-server-administrators"></a>Gérer des administrateurs de serveur

Un administrateur de serveur doit être un utilisateur, un principal du service ou un groupe valide présent dans Azure Active Directory (Azure AD) pour le tenant disposant du serveur. Pour gérer les administrateurs de serveur, vous pouvez utiliser **Administrateurs Analysis Services** pour votre serveur dans le portail Microsoft Azure, ou Propriétés du serveur dans SSMS, PowerShell ou l’API REST. 

Lors de l'ajout d'un **groupe de sécurité**, utilisez `obj:groupid@tenantid`. Les principaux de service ne sont pas pris en charge dans les groupes de sécurité ajoutés au rôle Administrateur du serveur.

Pour en savoir plus sur l’ajout d’un principal de service au rôle d’administrateur de serveur, consultez [Ajouter un principal de service au rôle d’administrateur de serveur](analysis-services-addservprinc-admins.md).

Si le pare-feu de serveur est activé, les adresses IP des ordinateurs clients de l’administrateur du serveur doivent être incluses dans une règle de pare-feu. Pour en savoir plus, consultez [Configuration d’un pare-feu de serveur](analysis-services-qs-firewall.md).

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Pour ajouter des administrateurs de serveur à l’aide du portail Azure

1. Dans le portail, cliquez sur **Administrateurs Analysis Services** pour votre serveur.
2. Dans **\<servername>– Administrateurs Analysis Services**, cliquez sur **Ajouter**.
3. Dans **Ajouter des administrateurs de serveur**, sélectionnez des comptes d’utilisateur à partir d’Azure AD ou invitez des utilisateurs externes par le biais de leur adresse e-mail.

    ![Administrateurs de serveur dans le portail Azure](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Pour ajouter des administrateurs de serveur à l’aide de SSMS

1. Faites un clic droit sur le serveur > **Propriétés**.
2. Dans **Propriétés de Analysis Server**, cliquez sur **Sécurité**.
3. Cliquez sur **Ajouter**, puis entrez l’adresse e-mail d’un utilisateur ou d’un groupe dans Azure AD.
   
    ![Ajouter des administrateurs de serveur dans SSMS](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Utilisez l’applet de commande [New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver) pour spécifier le paramètre Administrateur au moment de la création d’un serveur. <br>
Utilisez l’applet de commande [Set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver) pour modifier le paramètre Administrateur d’un serveur existant.

## <a name="rest-api"></a>API REST

Utilisez [Créer](/rest/api/analysisservices/servers/create) pour spécifier la propriété asAdministrator au moment de créer un nouveau serveur. <br>
Utilisez [Mettre à jour](/rest/api/analysisservices/servers/update) pour spécifier la propriété asAdministrator au moment de modifier un serveur existant. <br>



## <a name="next-steps"></a>Étapes suivantes 

[Authentification et autorisations utilisateur](analysis-services-manage-users.md)  
[Gérer les utilisateurs et rôles de bases de données](analysis-services-database-users.md)  
[Contrôle d’accès en fonction du rôle Azure (Azure RBAC)](../role-based-access-control/overview.md)
