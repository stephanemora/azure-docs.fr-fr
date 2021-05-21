---
title: Sécurisation des principaux de service dans Azure Active Directory
description: Recherchez, évaluez et sécurisez les principaux de service.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 2/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: d8a4b97ef095f5860ddf99751186ca04926e1698
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108761738"
---
# <a name="securing-service-principals"></a>Sécurisation des principaux de service

Un [principal de service](../develop/app-objects-and-service-principals.md) Azure Active Directory (Azure AD) est la représentation locale d’un objet d’application dans un seul locataire ou répertoire.  Il fonctionne en tant qu’identité d’instance de l’application. Les principaux de service définissent qui peut accéder à l’application, ainsi que les ressources auxquelles l’application peut accéder. Un principal de service est créé dans chaque locataire dans lequel l’application est utilisée, et fait référence à l’objet application global unique. Le locataire sécurise la connexion du principal du service et l’accès aux ressources.  

### <a name="tenant-service-principal-relationships"></a>Relations du principal du service et du locataire
Une application à locataire unique dispose d’un seul principal de service dans son locataire de base. Une application web ou une API multi-locataire requiert un principal de service dans chaque locataire. Un principal de service est créé lorsqu’un utilisateur de ce locataire a consenti à l’utilisation de l’application ou de l’API. Ce consentement crée une relation un-à-plusieurs entre l’application multi-locataire et ses principaux de service associés.

Une application multi-locataire est hébergée dans un seul locataire et est conçue pour disposer d’instances dans d’autres locataires. La plupart des applications SaaS (Software-as-a-service) sont conçues pour une architecture multi-locataire. Utilisez des principaux de service pour garantir la bonne posture de sécurité de l’application et de ses utilisateurs dans les cas d’usage à un ou plusieurs locataires.

## <a name="applicationid-and-objectid"></a>ApplicationID et ObjectID

Une instance d’application donnée dispose de deux propriétés distinctes : ApplicationID (également appelée ClientID) et ObjectID.

> [!NOTE] 
> Vous constaterez peut-être que les termes application et principal du service sont utilisés de manière interchangeable lorsque vous faites référence à une application dans le contexte des tâches liées à l’authentification. Il s’agit cependant de deux représentations différentes d’applications dans Azure AD.
 

La propriété ApplicationID représente l’application globale et est la même pour toutes les instances d’application au sein des locataires. La propriété ObjectID est une valeur unique pour un objet d’application. Comme pour les utilisateurs, groupes et autres ressources, ObjectID permet d’identifier de manière unique une instance d’application dans Azure AD.

Pour plus d’informations sur ce sujet, consultez [Relation entre l’application et le principal du service](../develop/app-objects-and-service-principals.md).

Vous pouvez également créer une application et son objet principal de service (ObjectID) dans un locataire à l’aide d’Azure PowerShell, de l’interface Azure CLI, de Microsoft Graph, du portail Azure et d’autres outils. 

![Capture d’écran montrant une nouvelle inscription d’application, avec les champs ApplicationID et ObjectID en surbrillance.](./media/securing-service-accounts/secure-principal-image-1.png)

## <a name="service-principal-authentication"></a>Authentification d’un principal du service

Il existe deux mécanismes d’authentification utilisant les principaux de service : les certificats clients et les secrets clients. 

![ Capture d’écran de la page Nouvelle application montrant les zones Certificats et secrets clients en surbrillance.](./media/securing-service-accounts/secure-principal-certificates.png)

Les certificats sont plus sécurisés : si possible, utilisez des certificats clients. Contrairement aux secrets clients, les certificats clients ne peuvent pas être incorporés par inadvertance dans le code. Si possible, utilisez Azure Key Vault pour gérer les certificats, les secrets et chiffrer les ressources suivantes à l’aide de clés protégées par des modules de sécurité matériels :

* Clés d’authentification

* Clés de compte de stockage

* Clés de chiffrement des données

* Fichiers .pfx

* mots de passe 

Pour plus d’informations sur Azure Key Vault et son utilisation afin de gérer les certificats et secrets, consultez [À propos d’Azure Key Vault](../../key-vault/general/overview.md) et [Attribuer une stratégie d’accès Key Vault à l’aide du portail Azure](../../key-vault/general/assign-access-policy-portal.md). 

 ### <a name="challenges-and-mitigations"></a>Défis et atténuations
Le tableau suivant présente les mesures d’atténuation destinées aux défis susceptibles de se présenter lors de l’utilisation de principaux de service.


| Défis| Corrections |
| - | - |
| Révisions d’accès pour les principaux de service attribués aux rôles privilégiés.| Proposée en préversion, cette fonctionnalité n’est pas encore largement disponible. |
| Vérifie l’accès aux principaux de service| Vérification manuelle de la liste de contrôle d’accès de la ressource à l’aide du portail Azure. |
| Sur les principaux de service autorisés| Lorsque vous créez des comptes de service Automation ou des principaux de service, fournissez uniquement les autorisations requises pour la tâche. Évaluez les principaux de service existants pour voir s’il vous est possible de réduire les privilèges. |
|Identifier les modifications apportées aux informations d’identification ou méthodes d’authentification des principaux de service |Utilisez le classeur Rapport des opérations sensibles qui permet d’atténuer ce problème. [Consultez l’explication dans ce billet de blog](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/azure-ad-workbook-to-help-you-assess-solorigate-risk/ba-p/2010718).|

## <a name="find-accounts-using-service-principals"></a>Rechercher des comptes à l’aide de principaux de service
Exécutez les commandes suivantes pour rechercher des comptes à l’aide de principaux de service.

Utilisation de l’interface de ligne de commande Azure


`az ad sp list`

Utilisation de PowerShell

`Get-AzureADServicePrincipal -All:$true` 


Pour plus d’informations, consultez [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal).

## <a name="assess-service-principal-security"></a>Évaluer la sécurité des principaux du service

Pour évaluer la sécurité de vos principaux de service, veillez à évaluer les privilèges et le stockage des informations d’identification.

Atténuez les défis susceptibles de se présenter à l’aide des informations suivantes.

|Défis | Corrections|
| - | - |
| Détecter l’utilisateur qui a consenti à une application multi-locataire ainsi que les octrois de consentement illicites à une application multi-locataire | Exécutez la commande PowerShell suivante pour rechercher des applications multi-locataires.<br>`Get-AzureADServicePrincipal -All:$true ? {$_.Tags -eq WindowsAzureActiveDirectoryIntegratedApp"}`<br>Désactivez le consentement de l’utilisateur. <br>Autoriser le consentement de l’utilisateur provenant d'éditeurs vérifiés, pour les autorisations sélectionnées (recommandé) <br> Utilisez l’accès conditionnel pour bloquer les principaux de service à partir d’emplacements non approuvés. Configurez-les dans le contexte de l’utilisateur. Leurs jetons doivent être utilisés pour déclencher le principal du service.|
|Utilisez un secret partagé codé en dur dans un script à l’aide d’un principal de service.|Utilisez un certificat ou Azure Key Vault.|
|Suivi des personnes qui utilisent le certificat ou le secret| Surveillez les connexions du principal de service à l’aide des journaux de connexion Azure AD.|
Impossible de gérer la connexion des principaux de service avec l’accès conditionnel.| Surveiller les connexions à l’aide des journaux de connexion Azure AD
| Le rôle RBAC Azure par défaut est Contributeur. |Évaluez les besoins et appliquez le rôle avec le moins d’autorisations possible pour répondre à ce besoin.|

## <a name="move-from-a-user-account-to-a-service-principal"></a>Passer d’un compte d’utilisateur à un principal de service  
Si vous utilisez un compte d’utilisateur Azure en tant que principal du service, voyez s’il vous est possible d’utiliser une [identité managée](../../app-service/overview-managed-identity.md?tabs=dotnet) ou un principal de service. Si vous ne pouvez pas utiliser d’identité managée, approvisionnez un principal de service doté de suffisamment d’autorisations et d’étendue pour exécuter les tâches requises. Vous pouvez créer un principal de service en [inscrivant une application](../develop/howto-create-service-principal-portal.md) ou à l’aide de [PowerShell](../develop/howto-authenticate-service-principal-powershell.md).

Lorsque vous utilisez Microsoft Graph, consultez la documentation de l’API spécifique, [comme dans cet exemple](/powershell/azure/create-azure-service-principal-azureps), et assurez-vous que le type d’autorisation pour l’application est pris en charge.

## <a name="next-steps"></a>Étapes suivantes

**Pour en savoir plus sur les principaux de service :**

[Créer un principal du service](../develop/howto-create-service-principal-portal.md)

 [Surveiller les connexions de principal de service](../reports-monitoring/concept-sign-ins.md)

**Pour en savoir plus sur la sécurisation des comptes de service :**

[Présentation des comptes de service Azure](service-accounts-introduction-azure.md)

[Sécurisation des identités managées](service-accounts-managed-identities.md)

[Administration des comptes de service Azure](service-accounts-governing-azure.md)

[Présentation des comptes de service locaux](service-accounts-on-premises.md)
