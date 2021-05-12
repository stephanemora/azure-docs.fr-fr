---
title: Sécurisation des identités managées dans Azure Active Directory
description: Document expliquant comment rechercher, évaluer et accroître la sécurité des identités managées.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 3/1/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a08c464e5c6a1fb5d4337e0902c9bd84b3b7772
ms.sourcegitcommit: eda26a142f1d3b5a9253176e16b5cbaefe3e31b3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2021
ms.locfileid: "109732917"
---
# <a name="securing-managed-identities"></a>Sécurisation des identités managées

La gestion des secrets et des informations d’identification utilisés pour sécuriser la communication entre les différents services relève souvent du défi pour les développeurs. Les identités managées sont des identités Azure Active Directory (Azure AD) sécurisées et créées pour fournir une identité aux ressources Azure.

## <a name="benefits-of-using-managed-identities-for-azure-resources"></a>Avantages de l'utilisation des identités managées pour les ressources Azure

L'utilisation des identités managées présente notamment les avantages suivants :

* Vous n’avez pas besoin de gérer les informations d’identification. Avec les identités managées, les informations d'identification sont complètement managées et protégées par Azure, qui assure également leur rotation. Les identités sont automatiquement fournies et supprimées avec les ressources Azure. Les identités managées permettent aux ressources Azure de communiquer avec tous les services qui prennent en charge l'authentification Azure AD.

* Personne (pas même les administrateurs généraux) n'a accès aux informations d'identification ; celles-ci ne peuvent donc pas être divulguées accidentellement, par exemple en étant incluses dans le code.

## <a name="when-to-use-managed-identities"></a>Quand utiliser les identités managées ?

Les identités managées sont idéales pour les communications entre les services qui prennent en charge l'authentification Azure AD. 

Un système source demande l'accès à un service cible. N'importe quelle ressource Azure peut être un système source. Par exemple, les machines virtuelles Azure, les instances d'Azure Function et les instances d'Azure App Service prennent en charge les identités managées.

   > [!VIDEO https://www.youtube.com/embed/5lqayO_oeEo]

### <a name="how-authentication-and-authorization-work"></a>Fonctionnement de l'authentification et de l'autorisation

Avec les identités managées, le système source peut obtenir un jeton auprès d'Azure AD sans que le propriétaire de la source ait à gérer les informations d'identification. Les informations d'identification sont gérées par Azure. Le jeton obtenu par le système source est présenté au système cible à des fins d'authentification. 

Le système cible doit s'authentifier (s'identifier) et autoriser le système source avant d'autoriser l'accès. Lorsque le service cible prend en charge l'authentification Azure AD, il accepte un jeton d'accès émis par Azure AD. 

Azure dispose d'un plan de contrôle et d'un plan de données. Le plan de contrôle vous permet de créer des ressources, tandis que le plan de données vous permet d'y accéder. Par exemple, vous créez une base de données Cosmos dans le plan de contrôle, mais vous l'interrogez dans le plan de données.

Une fois que le système cible accepte le jeton pour l'authentification, il peut prendre en charge différents mécanismes d'autorisation pour son plan de contrôle et son plan de données.

Toutes les opérations du plan de contrôle d'Azure sont gérées par [Azure Resource Manager](../../azure-resource-manager/management/overview.md) et utilisent le [contrôle d'accès en fonction du rôle Azure](../../role-based-access-control/overview.md). Dans le plan de données, chaque système cible dispose de son propre mécanisme d'autorisation. Le service Stockage Azure prend en charge Azure RBAC sur le plan de données. Par exemple, les applications qui utilisent Azure App Services peuvent lire les données à partir de Stockage Azure, et les applications qui utilisent Azure Kubernetes Service peuvent lire les secrets stockés dans Azure Key Vault.

Pour plus d'informations sur le plan de contrôle et le plan de données, consultez [Opérations du plan de contrôle et du plan de données - Azure Resource Manager](../../azure-resource-manager/management/control-plane-and-data-plane.md).

À terme, tous les services Azure prendront en charge les identités managées. Pour plus d'informations, consultez [Services prenant en charge les identités managées pour les ressources Azure](../managed-identities-azure-resources/services-support-managed-identities.md).

## <a name="types-of-managed-identities"></a>Types d'identités managées

Il existe deux types d'identités managées : celles qui sont attribuées par le système et celles qui sont attribuées par l'utilisateur.

Les identités managées attribuées par le système possèdent les propriétés suivantes :

* Elles entretiennent une relation 1:1 avec la ressource Azure. Par exemple, une identité managée unique est associée à chaque machine virtuelle.

* Elles sont liées au cycle de vie des ressources Azure. Lorsque la ressource est supprimée, l'identité managée associée à celle-ci est automatiquement supprimée, ce qui élimine le risque lié aux comptes orphelins. 

Les identités managées attribuées par l'utilisateur possèdent les propriétés suivantes :

* Le cycle de vie de ces identités est indépendant d'une ressource Azure, et vous devez gérer le cycle de vie. Lorsque la ressource Azure est supprimée, l'identité managée attribuée par l'utilisateur n'est pas automatiquement supprimée pour vous.

* Les identités managées attribuées par l'utilisateur peuvent être attribuées à zéro ou plusieurs ressources Azure.

* Elles peuvent être créées à l'avance, puis attribuées à une ressource.

## <a name="find-managed-identity-service-principals-in-azure-ad"></a>Rechercher les principaux de service des identités managées dans Azure AD

Vous disposez de plusieurs méthodes pour rechercher des identités managées :

* Via la page Applications d'entreprise du portail Azure

* Via Microsoft Graph

### <a name="using-the-azure-portal"></a>Utilisation du portail Azure

1. Dans Azure Active Directory, sélectionnez Applications d’entreprise.

2. Sélectionnez le filtre « Identités managées ». 

   ![Illustration de l'écran Toutes les applications, avec la liste déroulante Type d'application dans laquelle « Identités managées » est mis en surbrillance.](./media/securing-service-accounts/service-accounts-managed-identities.png)

 

### <a name="using-microsoft-graph"></a>Via Microsoft Graph

Vous pouvez obtenir la liste de toutes les identités managées de votre locataire en adressant la requête GET suivante à Microsoft Graph :

`https://graph.microsoft.com/v1.0/servicePrincipals?$filter=(servicePrincipalType eq 'ManagedIdentity') `

Vous pouvez filtrer ces requêtes. Pour plus d'informations, consultez [GET servicePrincipal](/graph/api/serviceprincipal-get) dans la documentation de Graph.

## <a name="assess-the-security-of-managed-identities"></a>Évaluer la sécurité des identités managées 

Pour évaluer la sécurité des identités managées, procédez comme suit :

* Examinez les privilèges et assurez-vous que le modèle qui possède le moins de privilèges est sélectionné. Utilisez la cmdlet PowerShell suivante pour accéder aux autorisations attribuées à vos identités managées.

   ` Get-AzureADServicePrincipal | % { Get-AzureADServiceAppRoleAssignment -ObjectId $_ }`

 
* Assurez-vous que l'identité managée n'appartient à aucun groupe privilégié, comme un groupe d'administrateurs.  
Pour ce faire, vous pouvez énumérer les membres de vos groupes à privilèges élevés à l'aide de PowerShell.

   `Get-AzureADGroupMember -ObjectId <String> [-All <Boolean>] [-Top <Int32>] [<CommonParameters>]`

* [Vous devez savoir à quelles ressources l'identité managée accède](../../role-based-access-control/role-assignments-list-powershell.md).

## <a name="move-to-managed-identities"></a>Passer aux identités managées

Si vous utilisez un principal de service ou un compte d’utilisateur Azure AD, déterminez si vous pouvez plutôt utiliser une identité managée pour ne pas avoir à protéger, faire tourner et gérer les informations d’identification. 

## <a name="next-steps"></a>Étapes suivantes

**Pour plus d'informations sur la création d'identités managées, consultez :** 

[Créer une identité managée attribuée par l'utilisateur](../managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) 

[Activer une identité managée attribuée par le système lors de la création d'une ressource](../managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

[Activer une identité managée attribuée par le système sur une ressource existante](../managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Pour plus d'informations sur les comptes de service, consultez :**

[Présentation des comptes de service Azure Active Directory](service-accounts-introduction-azure.md)

[Sécurisation des principaux de service](service-accounts-principal.md)

[Administration des comptes de service Azure](service-accounts-governing-azure.md)

[Présentation des comptes de service locaux](service-accounts-on-premises.md)

 

 

