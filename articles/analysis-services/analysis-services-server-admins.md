---
title: Gérer les administrateurs de serveur dans Azure Analysis Services | Microsoft Docs
description: Apprenez à gérer des administrateurs de serveur pour un serveur Analysis Services dans Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 25eb111871bbe3b18f59b0d6c123c72f3e55c859
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2019
ms.locfileid: "56737108"
---
# <a name="manage-server-administrators"></a>Gérer des administrateurs de serveur

Un administrateur de serveur doit être un utilisateur ou un groupe valide présent dans Azure Active Directory (Azure AD) pour le tenant disposant du serveur. Pour gérer les administrateurs de serveur, vous pouvez utiliser **Administrateurs Analysis Services** pour votre serveur dans le portail Microsoft Azure, ou Propriétés du serveur dans SSMS, PowerShell ou l’API REST. 

> [!NOTE]
> La propriété `MailEnabled` des groupes de sécurité doit être définie sur `True`.

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Pour ajouter des administrateurs de serveur à l’aide du portail Azure

1. Dans le portail, cliquez sur **Administrateurs Analysis Services** pour votre serveur.
2. Dans **\<NomServeur> - Administrateurs Analysis Services**, cliquez sur **Ajouter**.
3. Dans **Ajouter des administrateurs de serveur**, sélectionnez des comptes d’utilisateur à partir d’Azure AD ou invitez des utilisateurs externes par le biais de leur adresse e-mail.

    ![Administrateurs de serveur dans le portail Azure](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Pour ajouter des administrateurs de serveur à l’aide de SSMS

1. Faites un clic droit sur le serveur > **Propriétés**.
2. Dans **Propriétés de Analysis Server**, cliquez sur **Sécurité**.
3. Cliquez sur **Ajouter**, puis entrez l’adresse e-mail d’un utilisateur ou d’un groupe dans Azure AD.
   
    ![Ajouter des administrateurs de serveur dans SSMS](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Utilisez [New-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/new-azanalysisservicesserver) applet de commande pour spécifier le paramètre de l’administrateur lors de la création d’un nouveau serveur. <br>
Utilisez [Set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver) applet de commande pour modifier le paramètre de l’administrateur d’un serveur existant.

## <a name="rest-api"></a>API REST

Utilisez [Créer](https://docs.microsoft.com/rest/api/analysisservices/servers/create) pour spécifier la propriété asAdministrator au moment de créer un nouveau serveur. <br>
Utilisez [Mettre à jour](https://docs.microsoft.com/rest/api/analysisservices/servers/update) pour spécifier la propriété asAdministrator au moment de modifier un serveur existant. <br>



## <a name="next-steps"></a>Étapes suivantes 

[Authentification et autorisations utilisateur](analysis-services-manage-users.md)  
[Gérer les utilisateurs et rôles de base de données](analysis-services-database-users.md)  
[Contrôle d’accès en fonction du rôle](../role-based-access-control/overview.md)  

