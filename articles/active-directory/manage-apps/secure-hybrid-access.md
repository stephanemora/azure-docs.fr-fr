---
title: Accès hybride sécurisé à Azure AD | Microsoft Docs
description: Cet article décrit les solutions de partenaires pour intégrer vos applications locales, de cloud public ou de cloud privé héritées à Azure AD. Sécurisez vos applications héritées en connectant des contrôleurs ou des réseaux de livraison d’application à Azure AD.
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 9/10/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d64f7423d537958b6d3c388cb12f23bd2e30e36
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90087078"
---
# <a name="secure-hybrid-access-secure-legacy-apps-with-azure-active-directory"></a>Sécuriser l’accès hybride : Sécuriser des applications héritées avec Azure Active Directory

Vous pouvez désormais protéger vos applications d’authentification héritées locales et dans le cloud en les connectant à Azure Active Directory (AD) avec :

- [Proxy d’application Azure AD](#secure-hybrid-access-sha-through-azure-ad-application-proxy)

- [Vos contrôleurs et réseaux de remise d’application existants](#sha-through-networking-and-delivery-controllers)

- [Les applications de réseau privé virtuel (VPN) et de périmètre défini par logiciel (SDP)](#sha-through-vpn-and-sdp-applications)

Vous pouvez mener à bien vos transitions et renforcer la sécurité de l’ensemble de vos applications grâce à des fonctionnalités de Azure AD telles que Azure AD [Conditional Access](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) et Azure AD [Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection).

## <a name="secure-hybrid-access-sha-through-azure-ad-application-proxy"></a>Sécurisation de l’accès hybride (SHA) via le proxy d’application Azure AD
  
À l’aide du [Proxy d'application](https://aka.ms/whyappproxy), vous pouvez fournir un [accès à distance sécurisé](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) à vos applications web locales. Vos utilisateurs n’ont pas besoin d’utiliser un VPN. Les utilisateurs bénéficient d’une connexion facile à leurs applications à partir de n’importe quel appareil après une [authentification unique](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal-setup-sso). Le proxy d’application fournit l’accès à distance en tant que service et vous permet de [facilement publier vos applications locales](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application) à des utilisateurs en dehors du réseau d’entreprise. Il vous permet de mettre à l’échelle votre gestion des accès cloud sans avoir à modifier vos applications locales. [Planifiez le déploiement d’un proxy d’application Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-deployment-plan) comme étape suivante.

## <a name="azure-ad-partner-integrations"></a>Intégrations de partenaires Azure AD

### <a name="sha-through-networking-and-delivery-controllers"></a>SHA via les contrôleurs de livraison et de mise en réseau

En plus du [Proxy d’application Azure AD](https://aka.ms/whyappproxy), pour vous permettre d’utiliser le [framework de Confiance Zéro](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/), les partenaires Microsoft avec des fournisseurs tiers. Vous pouvez utiliser votre contrôleur de livraison et de mise en réseau et protéger facilement vos applications héritées, qui sont critiques pour vos processus d’entreprise, mais que vous ne pouviez pas protéger avant avec Azure AD. Il est probable que vous disposiez déjà de tout ce dont vous avez besoin pour commencer à protéger ces applications.

![L’image montre un accès hybride sécurisé avec les partenaires de mise en réseau et le proxy d’application](./media/secure-hybrid-access/secure-hybrid-access.png)

Les fournisseurs réseau suivants proposent des solutions prédéfinies et des conseils détaillés pour l’intégration avec Azure AD.

- [Akamai Enterprise Application Access (EAA)](https://docs.microsoft.com/azure/active-directory/saas-apps/akamai-tutorial)

- [Citrix Application Delivery Controller (ADC)](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial)

- [F5 Big-IP APM](https://docs.microsoft.com/azure/active-directory/saas-apps/headerf5-tutorial)

- [Kemp](https://docs.microsoft.com/azure/active-directory/saas-apps/kemp-tutorial)

### <a name="sha-through-vpn-and-sdp-applications"></a>SHA par le biais d’applications VPN et SDP

À l’aide de solutions VPN et SDP, vous pouvez fournir un accès sécurisé à votre réseau d’entreprise à partir de n’importe quel appareil, à tout moment et à tout endroit, tout en protégeant les données de votre organisation. En ayant Azure AD comme fournisseur d’identité (IDP), vous pouvez utiliser des méthodes d’authentification et d’autorisation modernes comme [l’authentification unique](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) et [l’authentification multifacteur](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) Azure AD pour sécuriser vos applications héritées locales.  

![L’image montre un accès hybride sécurisé avec les partenaires VPN et le proxy d’application ](./media/secure-hybrid-access/app-proxy-vpn.png)

Les fournisseurs de VPN et SDP suivants proposent des solutions prédéfinies et des conseils détaillés pour l’intégration avec Azure AD.

• [Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect)

• [Fortinet](https://docs.microsoft.com/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial)

• [F5 Big-IP APM](https://aka.ms/f5-hybridaccessguide)

• [Palo Alto Networks Global Protect](https://docs.microsoft.com/azure/active-directory/saas-apps/paloaltoadmin-tutorial)

• [Zscaler Private Access (ZPA)](https://aka.ms/zscaler-hybridaccessguide)
