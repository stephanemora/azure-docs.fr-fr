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
ms.openlocfilehash: 93ac5ef5f03f800a8f90259db3e382b3bc5c5e2c
ms.sourcegitcommit: 2c09af866f6cc3b2169e84100daea0aac9fc7fd0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64875635"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Stockage de données d’identité pour les clients européens dans Azure Active Directory
Données d’identité sont stockées par Azure AD dans un emplacement géographique en fonction de l’adresse fournie par votre organisation lors de l’abonnement pour un service Microsoft Online tels qu’Office 365 et Azure. Pour plus d’informations sur le stockage de vos données d’identité, vous pouvez utiliser la [où se trouvent vos données ?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) section de Microsoft Trust Center.

Pour les clients qui a fourni une adresse en Europe, Azure AD conserve la plupart des données d’identité dans des centres de données européen. Ce document fournit des informations sur toutes les données sont stockées en dehors de l’Europe par les services Azure AD.

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Microsoft Azure une authentification multifacteur (MFA)
    
- Authentification à deux facteurs tout à l’aide d’appels téléphoniques ou SMS sont issus de centres de données américains et sont routés également par les fournisseurs globaux.
- Des notifications push à l’aide de Microsoft Authenticator application proviennent de notre part centres de données. En outre, des services spécifiques appareil fournisseur peuvent également entrent en play et ces services peut-être en dehors de l’Europe.
- Les codes OATH sont toujours validés aux États-Unis. 

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure Active Directory B2C (Azure AD B2C)

Azure AD B2C les données de configuration de stratégie et les conteneurs de clé sont stockées dans les centres de données des États-Unis. Ils ne contiennent pas toutes les données personnelles utilisateur. Pour plus d’informations sur les configurations de stratégie, consultez l’article [Azure Active Directory B2C : stratégies intégrées](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies).

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure Active Directory B2B (Azure AD B2B) 
    
Invitations de magasins Azure AD B2B avec remboursement lient et rediriger les informations d’URL dans les centres de données des États-Unis. En outre, adresse de messagerie des utilisateurs qui se désabonner de recevoir des invitations B2B sont également stockés dans les centres de données des États-Unis.

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Microsoft Azure Active Directory Domain Services (Azure AD DS)

Azure AD DS stocke les données utilisateur au même emplacement que le réseau virtuel Azure choisi par le client. Par conséquent, si le réseau se situe en dehors de l’Europe, les données sont répliquées et stockées en dehors de l’Europe.

## <a name="other-considerations"></a>Autres points à considérer

Services et applications qui s’intègrent à Azure AD ont accès aux données d’identité. Évaluez chaque service et l’application que vous utilisez pour déterminer comment les données d’identité sont traitées par ce service spécifique et l’application, et si elles répondent aux besoins de stockage de données de votre entreprise.

Pour plus d’informations sur la résidence des données des services de Microsoft, consultez la section [Où se trouvent vos données ?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) du Microsoft Trust Center.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les fonctions et fonctionnalités décrites ci-dessus, consultez les articles suivants :
- [Présentation d’authentification multifacteur](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)

- [Réinitialisation de mot de passe en libre-service Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview)

- [Qu’est-ce qu’Azure Active Directory B2C ?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)

- [Qu'est-ce que la collaboration B2B d'Azure AD ?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)

- [Services de domaine Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)
