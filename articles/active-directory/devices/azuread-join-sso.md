---
title: Fonctionnement de l’authentification unique auprès de ressources locales sur des appareils joints à Azure AD | Microsoft Docs
description: Découvrez comment configurer des appareils hybrides joints à Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: f9d8c0cd803424e117bd4dc7a3382b7b32df2d05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672717"
---
# <a name="how-sso-to-on-premises-resources-works-on-azure-ad-joined-devices"></a>Fonctionnement de l’authentification unique auprès de ressources locales sur des appareils joints à Azure AD

Ce n’est probablement pas une surprise qu’un appareil joint à Azure Active Directory (Azure AD) vous offre une expérience d’authentification unique auprès des applications cloud de votre locataire. Si votre environnement comporte un annuaire Active Directory local, vous pouvez étendre à celui-ci l’expérience d’authentification unique sur ces appareils.

Cet article explique comment cela fonctionne.

## <a name="prerequisites"></a>Conditions préalables requises

 Si les machines jointes à Azure AD ne sont pas connectées au réseau de votre organisation, un VPN ou une autre infrastructure réseau est nécessaire. L’authentification unique locale exige une communication à visibilité directe avec vos contrôleurs de domaine AD DS locaux.

## <a name="how-it-works"></a>Fonctionnement 

Comme vous ne devez mémoriser qu’un seul nom d’utilisateur et un seul mot de passe, l’authentification unique simplifie l’accès à vos ressources et améliore la sécurité de votre environnement. Avec un appareil joint à Azure AD, vos utilisateurs ont déjà une expérience de l’authentification unique après des applications cloud de votre environnement. Si votre environnement comporte un annuaire Azure AD et un annuaire AD local, vous voudrez probablement élargir l’étendue de votre expérience de l’authentification unique à vos applications métier locales, aux partages de fichiers et aux imprimantes.

Les appareils joints à AD Azure n’ont pas connaissance de votre environnement AD local, car ils n’y sont pas joints. Cependant, vous pouvez fournir à ces appareils des informations supplémentaires sur votre annuaire AD local avec Azure AD Connect.

Un environnement qui a à la fois un annuaire Azure AD et un annuaire AD local est également appelé « environnement hybride ». Si vous avez un environnement hybride, il est probable que vous avez déjà déployé Azure AD Connect pour synchroniser vos informations d’identité locales vers le cloud. Dans le cadre du processus de synchronisation, Azure AD Connect synchronise les informations d’utilisateurs locaux sur Azure AD. Quand un utilisateur se connecte à un appareil joint à Azure AD dans un environnement hybride :

1. Azure AD renvoie le nom du domaine local dont l’utilisateur est membre à l’appareil.
1. Le service de l’autorité de sécurité locale active l’authentification Kerberos sur l’appareil.

Lors d’une tentative d’accès à une ressource demandant Kerberos dans l’environnement local de l’utilisateur, l’appareil :

1. Envoie les informations du domaine local et les informations d’identification au contrôleur de domaine localisé pour authentifier l’utilisateur.
1. Reçoit un [ticket TGT (Ticket-Granting Ticket)](/windows/desktop/secauthn/ticket-granting-tickets) Kerberos qui est utilisé pour accéder aux ressources jointes à AD. Si la tentative visant à obtenir le TGT pour le domaine AAD Connect échoue (le délai d’expiration du DCLocator associé peut entraîner un retard), des entrées du gestionnaire d’informations d’identification sont tentées ou l’utilisateur peut recevoir une fenêtre contextuelle d’authentification demandant des informations d’identification pour la ressource cible.

Toutes les applications qui sont configurées pour l’**authentification Windows intégrée** bénéficient automatiquement de l’authentification unique quand un utilisateur tente d’y accéder.

Windows Hello Entreprise nécessite une configuration supplémentaire pour activer l’authentification unique locale depuis un appareil joint à Azure AD. Pour plus d’informations, consultez [Configurer des appareils joints à Azure AD pour l’authentification unique locale avec Windows Hello Entreprise](/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso-base). 

## <a name="what-you-get"></a>Ce que vous obtenez

Avec l’authentification unique, sur un appareil joint à Azure AD, vous pouvez : 

- Accéder à un chemin UNC sur un serveur membre d’AD
- Accéder à un serveur web membre d’AD configuré pour la sécurité intégrée de Windows 

Si vous voulez gérer votre annuaire AD local à partir d’un appareil Windows, installez les [Outils d’administration de serveur distant pour Windows 10](https://www.microsoft.com/download/details.aspx?id=45520).

Vous pouvez utiliser :

- Le composant logiciel enfichable ADUC (Active Directory Users and Computers) pour administrer tous les objets AD. Cependant, vous devez spécifier manuellement le domaine auquel vous voulez vous connecter.
- Le composant logiciel enfichable DHCP pour administrer un serveur DHCP joint à AD. Cependant, il peut être nécessaire de spécifier le nom ou l’adresse du serveur DHCP.
 
## <a name="what-you-should-know"></a>Ce que vous devez savoir

Il peut être nécessaire d’ajuster votre [filtrage par domaine](../hybrid/how-to-connect-sync-configure-filtering.md#domain-based-filtering) dans Azure AD Connect pour que les données sur les domaines nécessaires soient synchronisées.

Les applications et les ressources qui dépendent de l’authentification de la machine Active Directory ne fonctionnent pas, car les appareils joints à Azure AD n’ont pas d’objet ordinateur dans AD. 

Vous ne pouvez pas partager des fichiers avec d’autres utilisateurs sur un appareil joint à Azure AD.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez [Présentation de la gestion des appareils dans Azure Active Directory](overview.md). 
