---
title: Connectez Active Directory à Azure Active Directory. | Microsoft Docs
description: Azure AD Connect intègre vos répertoires locaux à Azure Active Directory. Cela vous permet de fournir une identité commune pour les applications Office 365, Azure et SaaS intégrées à Azure AD.
keywords: introduction à Azure AD Connect, présentation d’Azure AD Connect, qu’est-ce qu’Azure AD Connect, installation d’active directory
services: active-directory
author: billmath
manager: daveba
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 11/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: df5d42135c538ca3d0a63252805efb44cef7e8b0
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56190464"
---
# <a name="what-is-hybrid-identity"></a>L’Identité hybride 

Les entreprises et les organisations utilisent aujourd’hui de plus en plus souvent une combinaison d’applications locales et cloud.  Les utilisateurs doivent avoir accès à ces applications en local et dans le cloud, ce qui constitue un véritable défi. 

Les solutions d'identité de Microsoft regroupent des fonctionnalités, locales et cloud.  Ces solutions créent une identité d'utilisateur unique commune pour l'authentification et l'autorisation d'accès à toutes les ressources, indépendamment de leur l'emplacement. Nous appelons cette identité **identité hybride**.

Pour obtenir l’identité hybride, l'une des trois méthodes d’authentification peut être utilisée, selon vos scénarios.   Ces trois méthodes sont les suivantes : 

- **[Synchronisation de hachage de mot de passe (PHS)](whatis-phs.md)**  
- **[Authentification directe (PTA)](how-to-connect-pta.md)**  
- **[Fédération](whatis-fed.md)** 

Ces méthodes d’authentification s'accompagnent également de fonctionnalités d'[authentification unique](how-to-connect-sso.md).  L’authentification unique connecte automatiquement les utilisateurs lorsque leurs appareils d’entreprise sont connectés au réseau de l’entreprise.

Pour plus d'informations, consultez [Choisir la méthode d’authentification adaptée à votre solution d’identité hybride Azure Active Directory](https://docs.microsoft.com/azure/security/azure-ad-choose-authn). 

## <a name="common-scenarios-and-recommendations"></a>Scénarios et recommandations courants 

Voici quelques scénarios courants de gestion des identités hybrides et des accès avec des recommandations concernant l’option (ou les options) d’identité hybride la ou les mieux appropriées dans chaque cas. 

|J’ai besoin de :|PHS et SSO<sup>1</sup>| PTA et SSO<sup>2</sup> | AD FS<sup>3</sup>| 
|-----|-----|-----|-----| 
|Synchroniser de nouveaux comptes d’utilisateurs, de contacts et de groupes créés automatiquement dans mon Active Directory local vers le cloud.|![Recommandé](./media/whatis-hybrid-identity/ic195031.png)| ![Recommandé](./media/whatis-hybrid-identity/ic195031.png) |![Recommandé](./media/whatis-hybrid-identity/ic195031.png)| 
|Configurer mon client pour des scénarios hybrides Office 365.|![Recommandé](./media/whatis-hybrid-identity/ic195031.png)| ![Recommandé](./media/whatis-hybrid-identity/ic195031.png) |![Recommandé](./media/whatis-hybrid-identity/ic195031.png)| 
|Permettre à mes utilisateurs de se connecter et d’accéder aux services cloud à l’aide de leur mot de passe local.|![Recommandé](./media/whatis-hybrid-identity/ic195031.png)| ![Recommandé](./media/whatis-hybrid-identity/ic195031.png) |![Recommandé](./media/whatis-hybrid-identity/ic195031.png)| 
|Implémenter l’authentification unique à l’aide des informations d’identification d’entreprise.|![Recommandé](./media/whatis-hybrid-identity/ic195031.png)| ![Recommandé](./media/whatis-hybrid-identity/ic195031.png) |![Recommandé](./media/whatis-hybrid-identity/ic195031.png)|  
|M’assurer qu’aucun hachage du mot de passe n’est stocké dans le cloud.| |![Recommandé](./media/whatis-hybrid-identity/ic195031.png)|![Recommandé](./media/whatis-hybrid-identity/ic195031.png)| 
|Activer des solutions d’authentification multifacteur cloud.| |![Recommandé](./media/whatis-hybrid-identity/ic195031.png)|![Recommandé](./media/whatis-hybrid-identity/ic195031.png)| 
|Activer des solutions d’authentification multifacteur locales.| | |![Recommandé](./media/whatis-hybrid-identity/ic195031.png)| 
|Prendre en charge l’authentification par carte à puce pour mes utilisateurs.<sup>4</sup>| | |![Recommandé](./media/whatis-hybrid-identity/ic195031.png)| 
|Afficher les notifications d’expiration du mot de passe dans le portail Office et sur le bureau Windows 10.| | |![Recommandé](./media/whatis-hybrid-identity/ic195031.png)| 

> <sup>1</sup> Synchronisation de hachage de mot de passe avec authentification unique (SSO). 
> 
> <sup>2</sup> Authentification directe et authentification unique.  
> 
> <sup>3</sup> Authentification unique fédérée avec AD FS.  
>  
> <sup>4</sup> AD FS peut être intégré à l’infrastructure de clé publique (PKI) de votre entreprise pour permettre l’authentification à l’aide de certificats. Ces certificats peuvent être des certificats logiciels déployés via des canaux d’approvisionnement approuvés tels que les certificats de gestion des périphériques mobiles (GPM), d’objet de stratégie de groupe (GPO), de carte à puce (y compris les cartes PIV/CAC) ou Hello for Business (approbation de certificat). Pour plus d’informations sur la prise en charge de l’authentification par carte à puce, consultez [ce  blog](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/). 
> 

## <a name="next-steps"></a>Étapes suivantes 

- [Présentation d’Azure AD Connect et Connect Health](whatis-azure-ad-connect.md) 
- [Qu’est-ce que la synchronisation de hachage de mot de passe (PHS) ?](whatis-phs.md) 
- [Qu’est-ce que l’authentification directe (PTA) ?](how-to-connect-pta.md) 
- [Qu’est-ce que la fédération ?](whatis-fed.md) 
- [Qu’est-ce que l’authentification unique ?](how-to-connect-sso.md) 

