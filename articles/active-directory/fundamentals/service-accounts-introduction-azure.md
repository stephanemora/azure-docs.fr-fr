---
title: Introduction à la sécurisation des comptes de service Azure Active Directory
description: Description des types de comptes de service disponibles dans Azure Active Directory.
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
ms.openlocfilehash: 27353e6086085aadae622e007a38f97e15cbcb7f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104587863"
---
# <a name="introduction-to-securing-azure-service-accounts"></a>Introduction à la sécurisation des comptes de service Azure

Il existe trois types de comptes de service natifs dans Azure Active Directory : les identités managées, les principaux de service et les comptes de service basés sur les utilisateurs. Les comptes de service sont un type particulier de compte destiné à représenter une entité non humaine telle qu'une application, une API ou un autre service. Ces entités opèrent dans le contexte de sécurité fourni par le compte de service. 

## <a name="types-of-azure-active-directory-service-accounts"></a>Types de comptes de service Azure Active Directory

Pour les services hébergés dans Azure, nous recommandons d'utiliser une identité managée, si possible, et un service principal si ce n'est pas le cas. Les identités managées ne peuvent pas être utilisées pour les services hébergés en dehors d'Azure. Dans ce cas, nous vous recommandons d'utiliser un principal de service. Si vous pouvez utiliser une identité managée ou un principal de service, faites-le. Nous vous recommandons de ne pas utiliser un compte d'utilisateur Azure Active Directory en tant que principal de service. Le tableau suivant fournit un récapitulatif.
 

| Hébergement du service| Identité managée| Principal du service| Compte d’utilisateur Azure |
| - | - | - | - |
|Le service est hébergé dans Azure.| Oui. <br>Recommandé si le service <br>prend en charge une identité managée.| Oui.| Non recommandé. |
| Le service n'est pas hébergé dans Azure.| Non| Oui. Recommandé.| Non recommandé. |
| Il s'agit d'un service multi-locataire| Non| Oui. Recommandé.| Non. |


## <a name="managed-identities"></a>Identités managées

Les identités managées sont des identités Azure Active Directory (Azure AD) sécurisées et créées pour fournir une identité aux ressources Azure. Il existe [deux types d'identités managées](../managed-identities-azure-resources/overview.md#managed-identity-types) : 
 
* Les identités managées attribuées par le système peuvent être attribuées directement à une instance d'un service. 

* Les identités managées attribuées par l'utilisateur peuvent être créées en tant que ressources Azure autonomes. 

Pour plus d'informations, consultez [Sécurisation des identités managées](service-accounts-managed-identities.md). Pour en savoir plus sur les identités managées, consultez la section [Que sont les identités managées pour les ressources Azure ?](../managed-identities-azure-resources/overview.md)

## <a name="service-principals"></a>Principaux de service

Si vous n'êtes pas en mesure d'utiliser une identité managée pour représenter votre application, utilisez un principal de service. Les principaux de service peuvent être utilisés avec des applications à locataire unique et à plusieurs locataires. 

Un principal de service est la représentation locale d'un objet d'application dans un locataire Azure AD. Il constitue l'identité de l'instance d'application, détermine qui peut accéder à l'application et à quelles ressources l'application a accès. Un principal de service est créé dans (local à) chaque locataire dans lequel l'application est utilisée, et fait référence à l'objet d'application global unique. Le locataire sécurise la connexion du principal de service et l'accès aux ressources.

Il existe deux mécanismes pour l'authentification à l'aide de principaux de service : les certificats client et les clés secrètes client. Les certificats sont plus sécurisés : si possible, utilisez des certificats client. Contrairement aux clés secrètes client, les certificats client ne peuvent pas être accidentellement incorporés dans le code.

Pour plus d'informations sur la sécurisation des principaux de service, consultez Sécurisation des principaux de service.

 
## <a name="next-steps"></a>Étapes suivantes


Pour plus d'informations sur la sécurisation des comptes de service Azure, consultez les rubriques suivantes :

[Sécurisation des identités managées](service-accounts-managed-identities.md)

[Sécurisation des principaux de service](service-accounts-principal.md)

[Administration des comptes de service Azure](service-accounts-governing-azure.md)