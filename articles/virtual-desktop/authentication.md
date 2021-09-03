---
title: Authentification Azure Virtual Desktop – Azure
description: Méthodes d’authentification pour Azure Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/16/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: c987ad26d28a6c96d187c420b32bd3103391c62d
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2021
ms.locfileid: "122563696"
---
# <a name="supported-authentication-methods"></a>Méthodes d’authentification prises en charge

Dans cet article, nous vous fournirons un aperçu des types d’authentification que vous pouvez utiliser dans Azure Virtual Desktop.

## <a name="identities"></a>Identities

Azure Virtual Desktop prend en charge différents types d’identités, selon la configuration que vous choisissez. Cette section décrit les identités que vous pouvez utiliser pour chaque configuration.

### <a name="on-premise-identity"></a>Identité locale

Étant donné que les utilisateurs doivent pouvoir être découverts via Azure Active Directory (Azure AD) pour accéder à Azure Virtual Desktop, les identités utilisateurs qui existent uniquement dans Active Directory Domain Services (AD DS) ne sont pas prises en charge. Cela comprend les déploiements Active Directory autonomes avec les services de fédération Active Directory (AD FS).

### <a name="hybrid-identity"></a>Identité hybride

Azure Virtual Desktop prend en charge les [identités hybrides](../active-directory/hybrid/whatis-hybrid-identity.md) par le biais d’Azure AD, notamment celles fédérées à l’aide des services de fédération Active Directory (AD FS). Vous pouvez gérer ces identités utilisateurs dans AD DS et les synchroniser avec Azure AD à l’aide d’[Azure AD Connect](../active-directory/hybrid/whatis-azure-ad-connect.md). Vous pouvez également utiliser Azure AD pour gérer ces identités et les synchroniser avec [Azure AD Directory Services (Azure AD DS)](../active-directory-domain-services/overview.md).

Lors de l’accès à Azure Virtual Desktop à l’aide d’identités hybrides, parfois, le nom d’utilisateur principal (UPN) ou l’identificateur de sécurité (SID) de l’utilisateur dans Active Directory (AD) et Azure AD ne correspondent pas. Par exemple, le compte AD user@contoso.local peut correspondre à user@contoso.com dans Azure AD. Azure Virtual Desktop ne prend en charge ce type de configuration que si l’UPN ou le SID de vos comptes AD et Azure AD correspondent. SID fait référence à la propriété d’objet utilisateur « ObjectSID » dans AD et « OnPremisesSecurityIdentifier » dans Azure AD.

### <a name="cloud-only-identity"></a>Identité cloud uniquement

Azure Virtual Desktop prend en charge les identités cloud uniquement lors de l’utilisation de [machines virtuelles jointes à Azure AD](deploy-azure-ad-joined-vm.md).

### <a name="external-identity"></a>Identité externe

Actuellement, Azure Virtual Desktop ne prend pas en charge les [identités externes](../active-directory/external-identities/index.yml).

## <a name="service-authentication"></a>Authentification du service

Pour accéder aux ressources Azure Virtual Desktop, vous devez d’abord vous authentifier auprès du service en vous connectant à un compte Azure AD. L’authentification se produit lors de l’abonnement à un espace de travail pour récupérer vos ressources ou à chaque fois que vous vous connectez à des applications ou à des ordinateurs de bureau. Vous pouvez utiliser des [fournisseurs d’identité tiers](../active-directory/devices/azureadjoin-plan.md#federated-environment) à condition qu’ils soient fédérés à Azure AD.

### <a name="multifactor-authentication"></a>Authentification multifacteur

Suivez les instructions de l’article [Configurer l’authentification multifacteur dans Azure Virtual Desktop](set-up-mfa.md) pour savoir comment activer l’authentification multifacteur (MFA) pour votre déploiement. Cet article vous indique également comment configurer la fréquence à laquelle vos utilisateurs sont invités à entrer leurs informations d’identification. Lors du déploiement de machines virtuelles jointes à Azure AD, suivez le guide de configuration dans [Activation de l’authentification multifacteur pour les machines virtuelles jointes à Azure AD](deploy-azure-ad-joined-vm.md#enabling-mfa-for-azure-ad-joined-vms).

### <a name="smart-card-authentication"></a>Authentification par carte à puce

Pour utiliser une carte à puce à des fins d’authentification auprès d’Azure AD, vous devez d’abord [configurer AD FS pour l’authentification par certificat utilisateur](/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication).

## <a name="session-host-authentication"></a>Authentification de l’hôte de session

Si vous n’avez pas encore activé l’[authentification unique](#single-sign-on-sso) ou enregistré vos informations d’identification localement, vous devez également vous authentifier auprès de l’hôte de session. Voici les méthodes de connexion pour l’hôte de session que les clients Azure Virtual Desktop prennent actuellement en charge :

- Client Windows Desktop
    - Nom d’utilisateur et mot de passe
    - Carte à puce
    - [Approbation de certificat Windows Hello Entreprise](/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust)
    - [Approbation de clé avec certificats Windows Hello Entreprise](/windows/security/identity-protection/hello-for-business/hello-deployment-rdp-certs)
- Client Windows Store
    - Nom d’utilisateur et mot de passe
- Client web
    - Nom d’utilisateur et mot de passe
- Android
    - Nom d’utilisateur et mot de passe
- iOS
    - Nom d’utilisateur et mot de passe
- macOS
    - Nom d’utilisateur et mot de passe

Azure Virtual Desktop prend en charge NT LAN Manager (NTLM) et Kerberos pour l’authentification de l’hôte de session. La carte à puce et Windows Hello Entreprise ne peuvent utiliser que Kerberos pour se connecter. Pour utiliser Kerberos, le client doit obtenir des tickets de sécurité Kerberos auprès d’un service de centre de distribution de clés (KDC, Key Distribution Center) fonctionnant sur un contrôleur de domaine. Pour obtenir des tickets, le client a besoin d’une ligne de vue réseau directe sur le contrôleur de domaine. Vous pouvez obtenir une ligne de vue en vous connectant directement sur votre réseau d’entreprise, à l’aide d’une connexion VPN ou en configurant un [serveur proxy KDC](key-distribution-center-proxy.md).

### <a name="single-sign-on-sso"></a>Authentification unique (SSO)

Azure Virtual Desktop prend en charge [l’authentification unique à l’aide des services de fédération Active Directory (AD FS)](configure-adfs-sso.md) pour les clients Windows et web. L’authentification unique vous permet d’ignorer l’authentification de l’hôte de session.

Sinon, la seule façon d’éviter d’être invité à entrer vos informations d’identification pour l’hôte de la session est de les enregistrer dans le client. Nous vous recommandons de le faire uniquement avec des appareils sécurisés pour empêcher d’autres utilisateurs d’accéder à vos ressources.

## <a name="in-session-authentication"></a>Authentification dans la session

Une fois que vous êtes connecté à votre application ou votre bureau à distance, vous pouvez être invité à vous authentifier dans la session. Cette section explique comment utiliser des informations d’identification autres que le nom d’utilisateur et le mot de passe dans ce scénario.

### <a name="smart-cards"></a>Cartes à puce

Pour utiliser une carte à puce dans votre session, assurez-vous que vous avez installé les pilotes de la carte à puce sur l’hôte de session et que la [redirection de carte à puce](configure-device-redirections.md#smart-card-redirection) est activée. Examinez le [graphique de comparaison client](/windows-server/remote/remote-desktop-services/clients/remote-desktop-app-compare#other-redirection-devices-etc) pour vous assurer que votre client prend en charge la redirection de carte à puce.

### <a name="fido2-and-windows-hello-for-business"></a>FIDO2 et Windows Hello Entreprise

Actuellement, Azure Virtual Desktop ne prend pas en charge l’authentification dans la session avec FIDO2 ou Windows Hello Entreprise.

## <a name="next-steps"></a>Étapes suivantes

- Vous êtes curieux de connaître d’autres moyens de sécuriser votre déploiement ? Consultez les [meilleures pratiques relatives à la sécurité](security-guide.md).
- Vous rencontrez des problèmes pour vous connecter à des machines virtuelles jointes à Azure AD ? [Résolvez les problèmes de connexion aux machines virtuelles jointes à Azure AD](troubleshoot-azure-ad-connections.md).
- Vous souhaitez utiliser des cartes à puce en dehors de votre réseau d’entreprise ? Découvrez comment configurer un [serveur proxy KDC](key-distribution-center-proxy.md).
