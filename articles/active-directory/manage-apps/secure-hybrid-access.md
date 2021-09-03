---
title: Accès hybride sécurisé à Azure AD | Microsoft Docs
description: Cet article décrit les solutions de partenaires pour intégrer vos applications locales, de cloud public ou de cloud privé héritées à Azure AD.
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 8/17/2021
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2dcba2b9435cb20cc112da1355cc31cbcb8dff2
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123029809"
---
# <a name="secure-hybrid-access-secure-legacy-apps-with-azure-active-directory"></a>Sécuriser l’accès hybride : Sécuriser des applications héritées avec Azure Active Directory

Vous pouvez désormais protéger vos applications d’authentification héritées locales et dans le cloud en les connectant à Azure Active Directory (AD) avec :

- [Proxy d’application Azure AD](#secure-hybrid-access-through-azure-ad-application-proxy)

- [Partenaires sécurisés d’accès hybride](#secure-hybrid-access-through-azure-ad-partner-integrations)

Vous pouvez mener à bien vos transitions et renforcer la sécurité de l’ensemble de vos applications grâce à des fonctionnalités de Azure AD telles que Azure AD [Conditional Access](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) et [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection). En ayant Azure AD comme fournisseur d’identité (IDP), vous pouvez utiliser des méthodes d’authentification et d’autorisation modernes comme [l’authentification unique (SSO)](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) et [l’authentification multifacteur (MFA)](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) pour sécuriser vos applications héritées locales.

## <a name="secure-hybrid-access-through-azure-ad-application-proxy"></a>Sécurisation de l’accès hybride via le proxy d’application Azure AD
  
À l’aide du [Proxy d'application](https://docs.microsoft.com/azure/active-directory/app-proxy/what-is-application-proxy), vous pouvez fournir un [accès à distance sécurisé](https://docs.microsoft.com/azure/active-directory/app-proxy/application-proxy-add-on-premises-application) à vos applications web locales. Vos utilisateurs n’ont pas besoin d’utiliser un VPN. Les utilisateurs bénéficient d’une connexion facile à leurs applications à partir de n’importe quel appareil après une [authentification unique](https://docs.microsoft.com/azure/active-directory/app-proxy/application-proxy-config-sso-how-to#how-to-configure-single-sign-on). Le proxy d’application fournit l’accès à distance en tant que service et vous permet de [facilement publier vos applications locales](https://docs.microsoft.com/azure/active-directory/app-proxy/application-proxy-add-on-premises-application) à des utilisateurs en dehors du réseau d’entreprise. Il vous permet de mettre à l’échelle votre gestion des accès cloud sans avoir à modifier vos applications locales. [Planifiez le déploiement d’un proxy d’application Azure AD](https://docs.microsoft.com/azure/active-directory/app-proxy/application-proxy-deployment-plan) comme étape suivante.

## <a name="secure-hybrid-access-through-azure-ad-partner-integrations"></a>Sécuriser l’accès hybride via des intégrations de partenaires Azure AD  

En plus du [proxy d’application Azure AD](https://aka.ms/whyappproxy), les partenaires Microsoft avec des fournisseurs tiers pour activer l’accès sécurisé à vos applications et applications locales qui utilisent l’authentification héritée.

![L’image montre un accès hybride sécurisé avec le proxy d’application et les partenaires](./media/secure-hybrid-access/secure-hybrid-access.png)

Les partenaires suivants proposent des solutions prédéfinies pour prendre en charge les stratégies d’accès conditionnel par application et fournissent des instructions détaillées pour l’intégration à Azure AD. 

- [Akamai Enterprise Application Access](https://docs.microsoft.com/azure/active-directory/saas-apps/akamai-tutorial)

- [Citrix Application Delivery Controller (ADC)](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial)  

- [Répartiteur d’accès Datawiza](datawiza-with-azure-ad.md)Datawiza Access Broker

- [F5 Big-IP APM ADC](https://docs.microsoft.com/azure/active-directory/manage-apps/f5-aad-integration)

- [VPN F5 Big-IP APM](https://docs.microsoft.com/azure/active-directory/manage-apps/f5-aad-password-less-vpn)

- [Kemp](https://docs.microsoft.com/azure/active-directory/saas-apps/kemp-tutorial)

- [Perimeter 81](https://docs.microsoft.com/azure/active-directory/saas-apps/perimeter-81-tutorial)

- [Plateforme d’authentification Silverfort](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal-setup-oidc-sso)

- [Strata](https://docs.microsoft.com/azure/active-directory/saas-apps/maverics-identity-orchestrator-saml-connector-tutorial)

Les partenaires suivants proposent des solutions prédéfinies et des conseils détaillés pour l’intégration avec Azure AD. 

- [Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect)

- [Fortinet](https://docs.microsoft.com/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial)

- [Palo Alto Networks Global Protect](https://docs.microsoft.com/azure/active-directory/saas-apps/paloaltoadmin-tutorial)

- [Pulse Secure Pulse Connect Secure (PCS)](https://docs.microsoft.com/azure/active-directory/saas-apps/pulse-secure-pcs-tutorial)

- [Pulse Secure Virtual Traffic Manager (VTM)](https://docs.microsoft.com/azure/active-directory/saas-apps/pulse-secure-virtual-traffic-manager-tutorial)

- [Zscaler Private Access (ZPA)](https://docs.microsoft.com/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial)
