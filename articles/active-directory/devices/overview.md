---
title: En quoi consiste la gestion d’identité dans Azure Active Directory ?
description: Identités des appareils et leurs cas d’usage
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 06/09/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo, ravenn, spunukol, jogro, jploegert
ms.collection: M365-identity-device-management
ms.openlocfilehash: d5115b8ce7d28e7fe3c254a1942ea035aa9db473
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122514885"
---
# <a name="what-is-a-device-identity"></a>Qu’est-ce qu’une identité d’appareil ?

Une [identité d’appareil](/graph/api/resources/device?view=graph-rest-1.0&preserve-view=true) est un objet dans Azure Active Directory (Azure AD). Cet objet d’appareil est semblable aux utilisateurs, aux groupes ou aux applications. Une identité d’appareil fournit aux administrateurs des informations qu’ils peuvent utiliser pour prendre des décisions concernant l’accès ou la configuration.

![Appareils affichés dans le panneau Appareils Azure AD](./media/overview/azure-active-directory-devices-all-devices.png)

Il existe trois façons d’obtenir une identité d’appareil :

- inscription Azure AD.
- jointure Azure AD ;
- jointure Azure AD hybride ;

Les identités des appareils sont un prérequis pour les scénarios comme les [stratégies d’accès conditionnel basées sur les appareils](../conditional-access/require-managed-devices.md) et la [gestion des périphériques mobiles avec Microsoft Endpoint Manager](/mem/endpoint-manager-overview).

## <a name="modern-device-scenario"></a>Scénario d’appareil moderne

Le scénario d’appareil moderne se concentre sur deux de ces méthodes : 

- [inscription Azure AD.](concept-azure-ad-register.md) 
   - Apportez votre propre appareil (BYOD)
   - Appareil mobile (téléphone portable et tablette)
- [Jonction Azure AD](concept-azure-ad-register.md)
   - Appareils Windows 10 appartenant à votre organisation
   - [Windows Serveur 2019 et serveurs plus récents de votre organisation s’exécutant en tant que machines virtuelles dans Azure](howto-vm-sign-in-azure-ad-windows.md)

La [jonction Azure AD Hybride](concept-azure-ad-join-hybrid.md) est considérée comme une étape intermédiaire sur la voie vers la jonction Azure AD. La jonction Azure AD Hybride offre aux organisations la prise en charge des versions de Windows de niveau inférieur, jusqu’à Windows 7 et Server 2008. Les trois scénarios peuvent coexister dans une seule organisation.

## <a name="resource-access"></a>Accès aux ressources

L’inscription et la jonction d’appareils à Azure AD permet aux utilisateurs de se connecter aux ressources basées sur le cloud à l’aide de l’authentification unique (SSO) fluide.

Les appareils avec jointure Azure AD bénéficient de [l’authentification unique aux ressources locales de votre organisation](azuread-join-sso.md).

## <a name="provisioning"></a>Approvisionnement

L’ajout d’appareils à Azure AD est possible en libre-service ou par un processus contrôlé managé par des administrateurs.

## <a name="license-requirements"></a>Conditions de licence :

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [appareils inscrits Azure AD](concept-azure-ad-register.md)
- En savoir plus sur les [appareils joints à Azure AD](concept-azure-ad-join.md)
- En savoir plus sur les [appareils hybrides joints à Azure AD](concept-azure-ad-join-hybrid.md)
- Pour obtenir une vue d’ensemble de la gestion des identités dans le portail Azure, consultez [Gestion des identités d’appareils via le portail Azure](device-management-azure-portal.md).
- Pour plus d’informations sur l’accès conditionnel basé sur les appareils, consultez [Configurer les stratégies d’accès conditionnel basé sur les appareils Azure Active Directory](../conditional-access/require-managed-devices.md).
