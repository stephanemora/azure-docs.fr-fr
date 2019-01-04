---
title: Stockage de données d’identité pour les clients européens - Azure Active Directory | Microsoft Docs
description: Découvrez où Azure Active Directory stocke les données relatives à l’identité de ses clients européens.
services: active-directory
author: eross-msft
manager: mtillman
ms.author: lizross
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/17/2018
ms.custom: it-pro, seodec18
ms.openlocfilehash: 371c7b2eb2f2e0e34dc80cf8fe6771620fa76765
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53099934"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Stockage de données d’identité pour les clients européens dans Azure Active Directory
Azure Active Directory (Azure AD) vous aide à gérer les identités des utilisateurs et à créer des stratégies d’accès basées sur des fonctionnalités d’Intelligence pour sécuriser les ressources de votre organisation. Les données d’identité sont stockées dans un emplacement basé sur l’adresse fournie par votre organisation lors de la souscription à l’abonnement au service. Par exemple, lorsque vous vous êtes inscrit à Office 365 ou à Azure. Pour obtenir des informations précises sur le lieu de stockage des données d’identité, vous pouvez utiliser la section [Où se trouvent vos données ?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) de Microsoft Trust Center.

Tandis que la plupart des données d’identité européennes liées à Azure AD restent dans des centres de données européens, il existe cinq attributs relatifs à l’utilisateur qui sont généralement stockés dans des centres de données aux États-Unis. Ces attributs sont GivenName, Surname, userPrincipalName, Domain et PasswordHash. L’attribut PasswordHash peut être une exception et il est possible qu’il ne soit pas stocké aux États-Unis, si une personne utilise une méthode d’authentification fédérée et locale qui empêche la synchronisation de la valeur PasswordHash avec Azure AD. En outre, certaines données opérationnelles et relatives au service, nécessaires au fonctionnement normal d’Azure AD, sont stockées aux États-Unis et n’incluent aucune donnée personnelle.

## <a name="data-stored-outside-of-european-datacenters-for-european-customers"></a>Données stockées en dehors des centres de données européens pour les clients européens

Une grande partie des données d’identité européennes concernant Azure AD, pour les organisations disposant d’adresses en Europe, restent dans des centres de données européens. Les données Azure AD qui sont stockées dans des centres de données européens et qui sont aussi répliquées vers des centres de données aux États-Unis incluent :

- **Attributs d’identité**

    Les attributs d’identité suivants seront répliqués vers les États-Unis :

    - GivenName
    - Surname
    - userPrincipalName
    - Domaine
    - PasswordHash
    - SourceAnchor
    - AccountEnabled
    - PasswordPolicies
    - StrongAuthenticationRequirement
    - ApplicationPassword
    - PUID

- **L’authentification multifacteur Microsoft Azure (MFA) et la réinitialisation de mot de passe en libre-service Azure AD (SSPR)**
    
    MFA stocke toutes les données utilisateur au repos dans des centres de données européens. Toutefois, certaines données spécifiques au service MFA se trouvent aux États-Unis, y compris :
    
    - L’authentification à deux facteurs et les données personnelles associées peuvent être stockées aux États-Unis, si vous utilisez le service MFA ou SSPR.
        - Toutes les authentification à deux facteurs comprenant des appels téléphoniques ou des SMS peuvent être effectuées par les opérateurs aux États-Unis.
        - Les notifications Push à l’aide de l’application Microsoft Authenticator requièrent des notifications de la part du service de notification du fabricant (Apple ou Google), qui peut se situer hors de l’Europe.
        - Les codes OATH sont toujours validés aux États-Unis. 
    - Les journaux MFA ou SSPR sont stockés aux États-Unis pendant 30 jours, quel que soit le type d’authentification.

- **Microsoft Azure Active Directory B2C (Azure AD B2C)**

    Azure AD B2C stocke toutes les données utilisateur au repos dans des centres de données européens. Toutefois, les journaux des opérations (avec les données personnelles supprimées) restent à l’emplacement d’où la personne accède aux services. Par exemple, si un utilisateur B2C accède au service depuis les États-Unis, les journaux des opérations restent aux États-Unis. En outre, toutes les données de configuration de stratégie ne contenant aucune donnée personnelle sont uniquement stockées aux États-Unis. Pour plus d’informations sur les configurations de stratégie, consultez l’article [Azure Active Directory B2C : stratégies intégrées](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies).

- **Microsoft Azure Active Directory B2B (Azure AD B2B)** 
    
    Azure AD B2B stocke toutes les données utilisateur au repos dans des centres de données européens. Toutefois, B2B stocke ses métadonnées non personnelles dans des tables au sein de centres de données aux États-Unis. Ces tables incluent des champs comme redeemUrl, invitationTicket, resource tenant Id, InviteRedirectUrl et InviterAppId.

- **Microsoft Azure Active Directory Domain Services (Azure AD DS)**

    Azure AD DS stocke les données utilisateur au même emplacement que le réseau virtuel Azure choisi par le client. Par conséquent, si le réseau se situe en dehors de l’Europe, les données sont répliquées et stockées en dehors de l’Europe.

- **Services et applications intégrés avec Azure AD**

    Tous les services et applications s’intégrant à Azure AD ont accès aux données d’identité. Évaluez chaque service et application pour déterminer comment ils traitent les données d’identité, et s’ils répondent aux besoins de stockage de données de votre entreprise.

    Pour plus d’informations sur la résidence des données des services de Microsoft, consultez la section [Où se trouvent vos données ?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) du Microsoft Trust Center.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les fonctionnalités décrites ci-dessus, consultez les articles suivants.
- [Présentation d’authentification multifacteur](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)
- [Réinitialisation de mot de passe en libre-service Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview)
- [Qu’est-ce qu’Azure Active Directory B2C ?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
- [Qu'est-ce que la collaboration B2B d'Azure AD ?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
- [Services de domaine Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)
