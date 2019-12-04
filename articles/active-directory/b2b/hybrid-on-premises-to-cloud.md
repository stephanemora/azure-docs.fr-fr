---
title: Synchroniser des comptes de partenaires locaux vers le cloud en tant qu’utilisateurs B2B – Azure AD
description: Octroyez aux partenaires externes gérés localement un accès à la fois aux ressources locales et aux ressources cloud à l’aide des mêmes informations d’identification avec Azure AD B2B Collaboration.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/24/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: dcc8c0538bb3362818a4172dd42905fd72b19812
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74272618"
---
# <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources-using-azure-ad-b2b-collaboration"></a>Octroyer aux comptes de partenaires gérés localement un accès aux ressources cloud à l’aide d’Azure AD B2B Collaboration

Avant Azure Active Directory (Azure AD), les organisations dotées de systèmes d’identité locaux géraient généralement les comptes de partenaires dans leur répertoire local. Dans ce type d’organisation, lorsque vous commencez à déplacer des applications vers Azure AD, vous souhaitez vous assurer que vos partenaires peuvent accéder aux ressources dont ils ont besoin. Le fait que les ressources soient en local ou dans le cloud importe peu. En outre, vous souhaitez que vos utilisateurs partenaires soient en mesure d’utiliser les mêmes informations de connexion pour les ressources locales et les ressources Azure AD. 

Si vous créez des comptes pour vos partenaires externes dans votre répertoire local (par exemple, vous créez un compte avec le nom de connexion « wmoran » pour un utilisateur externe nommé Wendy Moran dans votre domaine partners.contoso.com), vous pouvez maintenant synchroniser ces comptes dans le cloud. Plus précisément, vous pouvez utiliser Azure AD Connect pour synchroniser les comptes de partenaires dans le cloud en tant qu’utilisateurs Azure AD B2B (autrement dit, en tant qu’utilisateurs définis avec UserType = Invité). Cela permet à vos utilisateurs partenaires d’accéder aux ressources cloud en utilisant les mêmes informations d’identification que leurs comptes locaux, sans leur donner plus d’accès que nécessaire. 

## <a name="identify-unique-attributes-for-usertype"></a>Identifier les attributs uniques de UserType

Avant d’activer la synchronisation de l’attribut UserType, vous devez d’abord déterminer comment dériver l’attribut UserType de l’Active Directory local. En d’autres termes, vous devez définir les paramètres de votre environnement local qui doivent être uniques pour vos collaborateurs externes. Déterminez un paramètre qui différencie ces collaborateurs externes des membres de votre organisation.

Pour ce faire, les deux approches communes sont les suivantes :

- Vous pouvez désigner un attribut Active Directory local inutilisé (par exemple, extensionAttribute1) à utiliser en tant qu’attribut source. 
- Vous pouvez également dériver la valeur de l’attribut de UserType à partir d’autres propriétés. Par exemple, vous souhaitez synchroniser tous les utilisateurs en tant qu’Invité si leur attribut UserPrincipalName Active Directory local se termine par le domaine *\@partners.contoso.com*.
 
Pour plus d’informations sur les conditions requises pour les attributs, consultez [Activer la synchronisation de UserType](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype). 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>Configurer Azure AD Connect pour synchroniser les utilisateurs avec le cloud

Après avoir identifié l’attribut unique, vous pouvez configurer Azure AD Connect pour synchroniser ces utilisateurs avec le cloud en tant qu’utilisateurs Azure AD B2B (autrement dit, en tant qu’utilisateurs avec UserType = Invité). D’un point de vue des autorisations, ces utilisateurs ne se distinguent pas des utilisateurs B2B créés via le processus d’invitation d’Azure AD B2B Collaboration.

Pour connaître les instructions d’implémentation, consultez [Activer la synchronisation de UserType](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype).

## <a name="next-steps"></a>Étapes suivantes

- [Azure Active Directory B2B Collaboration pour les organisations hybrides](hybrid-organizations.md)
- [Grant B2B users in Azure AD access to your on-premises applications](hybrid-cloud-to-on-premises.md) (Accorder aux utilisateurs B2B dans Azure AD l’accès à vos applications locales)
- Pour obtenir une vue d’ensemble d’Azure AD Connect, consultez [Intégrer des répertoires locaux à Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

