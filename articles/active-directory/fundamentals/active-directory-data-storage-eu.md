---
title: Stockage de données d’identité pour les clients européens - Azure Active Directory | Microsoft Docs
description: Découvrez où Azure Active Directory stocke les données relatives à l’identité de ses clients européens.
services: active-directory
author: eross-msft
manager: daveba
ms.author: lizross
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 03/04/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b21f82dc0a1eb8edf571da13e0d34fecae5f401b
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57337683"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Stockage de données d’identité pour les clients européens dans Azure Active Directory
Azure Active Directory (Azure AD) vous aide à gérer les identités des utilisateurs et à créer des stratégies d’accès basées sur des fonctionnalités d’Intelligence pour sécuriser les ressources de votre organisation. Les données d’identité sont stockées dans un emplacement basé sur l’adresse fournie par votre organisation lors de la souscription à l’abonnement au service. Par exemple, lorsque vous vous êtes inscrit à Office 365 ou à Azure. Pour obtenir des informations précises sur le lieu de stockage des données d’identité, vous pouvez utiliser la section [Où se trouvent vos données ?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) de Microsoft Trust Center.

Alors que les données liées à AD d’identité européenne plus Azure sont conservées dans des centres de données européen, il existe des données opérationnelles, spécifiques au service qui sont requis pour normal opération Azure AD, qui sont stockés dans le fuseau horaire et n’inclut pas toutes les données personnelles.

## <a name="data-stored-outside-of-european-datacenters-for-european-customers"></a>Données stockées en dehors des centres de données européens pour les clients européens

Une grande partie des données d’identité européennes concernant Azure AD, pour les organisations disposant d’adresses en Europe, restent dans des centres de données européens. Les données Azure AD qui sont stockées dans des centres de données européens et qui sont aussi répliquées vers des centres de données aux États-Unis incluent :

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
Pour plus d’informations sur les fonctions et fonctionnalités décrites ci-dessus, consultez les articles suivants :
- [Présentation d’authentification multifacteur](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)

- [Réinitialisation de mot de passe en libre-service Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview)

- [Qu’est-ce qu’Azure Active Directory B2C ?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)

- [Qu'est-ce que la collaboration B2B d'Azure AD ?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)

- [Services de domaine Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)
