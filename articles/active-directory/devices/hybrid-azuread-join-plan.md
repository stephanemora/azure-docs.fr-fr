---
title: Guide pratique pour planifier l’implémentation de la jonction Azure AD Hybride dans Azure Active Directory (Azure AD) | Microsoft Docs
description: Découvrez comment configurer des appareils hybrides joints à Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2019
ms.author: joflore
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9fc0105fa2e56760554946b4e871a2dc3ff4d8bc
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58518879"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Procédure : Planifier l’implémentation de la jonction Azure AD Hybride

À l’instar d’un utilisateur, un appareil devient une autre identité que vous souhaitez protéger et aussi utiliser pour protéger vos ressources à tout moment et en tout lieu. Vous pouvez atteindre cet objectif en intégrant les identités de vos appareils à Azure AD suivant l’une des méthodes ci-dessous :

- jointure Azure AD ;
- jointure Azure AD hybride ;
- inscription Azure AD.

En mettant vos appareils sur Azure AD, vous optimisez la productivité de vos utilisateurs par le biais de l’authentification unique (SSO) sur vos ressources cloud et locales. Dans le même temps, vous pouvez sécuriser l’accès à ces ressources avec [l’accès conditionnel](../active-directory-conditional-access-azure-portal.md).

Si vous disposez d’un environnement Active Directory local et que vous souhaitez lier à Azure AD vos appareils joints à un domaine, vous pouvez y parvenir en configurant simplement des appareils hybrides joints à Azure AD. Cet article présente les étapes à suivre pour implémenter une jointure Azure AD hybride dans un environnement. 


## <a name="prerequisites"></a>Conditions préalables

Cet article suppose que vous avez lu la [Présentation de la gestion des appareils dans Azure Active Directory](../device-management-introduction.md).

>[!NOTE]
>  Le minimum requis fonctionnel de domaine et les niveaux fonctionnels de forêt pour une jointure hybrid Azure AD Windows 10 est Windows Server 2008 R2. Sur des versions inférieures, l’utilisateur peut ne pas obtenir un principal jeton d’actualisation au cours d’ouverture de session Windows en raison de problèmes LSA 

## <a name="plan-your-implementation"></a>Planifier l’implémentation

Pour planifier votre implémentation Azure AD hybride, prenez connaissance de ces étapes :

|   |   |
|---|---|
|![Vérification][1]|Vérifier les appareils pris en charge|
|![Vérification][1]|Connaître les points à savoir|
|![Vérification][1]|Guide pratique pour contrôler la jointure Azure AD hybride de vos appareils|
|![Vérification][1]|Sélectionner un scénario|


 

## <a name="review-supported-devices"></a>Vérifier les appareils pris en charge 

La jointure Azure AD hybride prend en charge un large éventail d’appareils Windows. Dans la mesure où la configuration des appareils fonctionnant sous des versions antérieures de Windows implique des étapes supplémentaires ou différentes, les appareils pris en charge sont divisés en deux catégories :

**Appareils Windows actuels**

- Windows 10
    
- Windows Server 2016


Pour les appareils qui fonctionnent avec le système d’exploitation d’ordinateur Windows, la version prise en charge est la Mise à jour anniversaire Windows 10 (version 1607) ou une version ultérieure. La meilleure pratique consiste à effectuer une mise à niveau vers la dernière version de Windows 10.



 **Appareils Windows de bas niveau**

- Windows 8.1
 
- Windows 7

- Windows Server 2012 R2
 
- Windows Server 2012 
 
- Windows Server 2008 R2 


La première étape de la planification consiste à examiner l’environnement et à déterminer s’il est nécessaire de prendre en charge les appareils Windows de bas niveau.



## <a name="review-things-you-should-know"></a>Connaître les points à savoir

Vous ne pouvez pas utiliser de jointure Azure AD hybride si votre environnement se compose d’une seule forêt qui synchronise les données d’identité sur plusieurs locataires Azure AD.

Si vous comptez sur l’outil de préparation système (Sysprep), assurez-vous qu’images créées à partir d’une installation de Windows 10 1803 ou précédemment n'ont pas été configurées pour une jointure hybrid Azure AD.

Si vous utilisez une capture instantanée de machine virtuelle pour créer des machines virtuelles supplémentaires, vérifiez qu’elle n’a pas été configurée pour la jointure Azure AD hybride.

La jonction Azure AD hybride des appareils Windows de bas niveau :

- **Est** prise en charge dans les environnements non fédérés via l’[authentification unique transparente d’Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start). 

- **N’est pas** prise en charge lors de l’utilisation de l’authentification directe Azure AD sans l’authentification unique transparente.

- **N’est pas** prise en charge lors de l’utilisation de l’itinérance des informations d’identification ou du profil utilisateur, ni lors de l’utilisation de l’infrastructure de bureau virtuel (VDI).


L’inscription de Windows Server avec le rôle de contrôleur de domaine n’est pas prise en charge.

Si votre organisation a besoin d’accéder à Internet via un proxy sortant authentifié, assurez-vous que vos ordinateurs Windows 10 parviennent à s’authentifier sur le proxy sortant. Les ordinateurs Windows 10 procèdent à l’inscription des appareils suivant le contexte ordinateur ; il est donc indispensable de configurer l’authentification du proxy sortant selon ce contexte.


La jointure Azure AD hybride est un processus qui consiste à inscrire automatiquement auprès d’Azure AD des appareils joints au domaine local. Si vous ne souhaitez pas que tous vos appareils s’inscrivent automatiquement, voir [Guide pratique pour contrôler la jointure Azure AD hybride de vos appareils](hybrid-azuread-join-control.md).

Si vos appareils joints au domaine Windows 10 sont déjà [inscrits auprès d’Azure AD](https://docs.microsoft.com/azure/active-directory/devices/overview#azure-ad-registered-devices) sur votre locataire, nous vous recommandons vivement de supprimer cet état avant d’activer la jonction Azure AD Hybride. À partir de la version 1809 de Windows 10, les modifications suivantes ont été apportées pour éviter ce double état : 
 - Tout état existant inscrit à Azure AD est automatiquement supprimé dès lors que l’appareil est joint à Azure AD Hybride. 
 - Vous pouvez empêcher votre appareil joint au domaine Azure AD inscrite en ajoutant cette clé de Registre - HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, « BlockAADWorkplaceJoin » = DWORD : 00000001.
 - Cette modification est désormais disponible pour Windows 10 1803 version avec KB4489894.

Le module de plateforme sécurisée compatible FIPS ne sont pas pris en charge pour une jointure Hybrid Azure AD. Si vos appareils sont dotés de TPM compatible FIPS, vous devez les désactiver avant de procéder à une jointure Hybrid Azure AD. Microsoft ne fournit pas d’outils permettant de désactiver le mode FIPS pour le module de plateforme sécurisée car il dépend du fabricant du TPM. Veuillez contacter votre matériel OEM pour la prise en charge.

## <a name="review-how-to-control-the-hybrid-azure-ad-join-of-your-devices"></a>Guide pratique pour contrôler la jointure Azure AD hybride de vos appareils

La jointure Azure AD hybride est un processus qui consiste à inscrire automatiquement auprès d’Azure AD des appareils joints au domaine local. Il existe des cas où vous ne souhaitez pas que tous vos appareils s’inscrivent automatiquement. Cela est vrai, par exemple, lors du déploiement initial pour vérifier que tout fonctionne conformément à ce qui est attendu.

Pour plus d’informations, consultez le [Guide pratique pour contrôler la jointure Azure AD hybride de vos appareils](hybrid-azuread-join-control.md)

## <a name="select-your-scenario"></a>Sélectionner un scénario

La jointure Azure AD hybride peut être configurée pour les scénarios suivants :

- les domaines managés ;
- les domaines fédérés.  



Si votre environnement comporte des domaines managés, la jointure Azure AD hybride prend en charge :

- Authentification directe (PTA)

- Synchronisation de hachage de mot de passe (PHS)

Depuis la version 1.1.819.0, Azure AD Connect comporte un Assistant permettant de configurer la jointure Azure AD hybride. Il simplifie considérablement le processus de configuration. Pour plus d'informations, consultez les pages suivantes :

- [Configurer la jointure hybride Azure Active Directory pour des domaines fédérés](hybrid-azuread-join-federated-domains.md)


- [Configurer la jointure hybride Azure Active Directory pour des domaines managés](hybrid-azuread-join-managed-domains.md)


 Si vous n’avez pas la possibilité d’installer la version requise d’Azure AD Connect, consultez le [Guide pratique pour configurer manuellement l’inscription des appareils](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-manual). 


## <a name="on-premises-ad-upn-support-in-hybrid-azure-ad-join"></a>Prise en charge d’un UPN AD local dans une jointure Azure AD Hybride

Parfois, vos UPN AD locaux peuvent différer de vos UPN AD Azure. Dans ce cas, une jonction Azure AD Hybride Windows 10 offre une prise en charge limitée des UPN AD locaux, variable selon la [méthode d’authentification](https://docs.microsoft.com/azure/security/azure-ad-choose-authn), le type de domaine et la version de Windows 10. Deux types d’UPN AD locaux peuvent exister dans votre environnement :

 - UPN routable : un UPN routable possède un domaine vérifié valide, qui est inscrit auprès d’un bureau d’enregistrement de domaines. Par exemple, si contoso.com est le domaine principal dans Azure AD, contoso.org est le domaine principal dans l’AD local appartenant à Contoso et [vérifié dans Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)
 
 - UPN non routable : un UPN non routable ne dispose pas d’un domaine vérifié. Il est applicable uniquement au sein du réseau privé de votre organisation. Par exemple, si contoso.com est le domaine principal dans Azure AD, contoso.local est le domaine principal dans l’AD local, mais n’est pas un domaine vérifiable sur Internet et n’est utilisé qu’à l’intérieur du réseau de Contoso.
 
Le tableau ci-dessous fournit des détails sur la prise en charge de ces UPN AD locaux dans une jointure Azure AD Hybride Windows 10

|Type d’UPN AD local|Type de domaine|version Windows 10|Description|
|-----|-----|-----|-----|
|Routable|Adresses IP fédérées |À partir de la version 1703|Mise à la disposition générale|
|Routable|Adresses IP gérées|À partir de la version 1709|Actuellement en préversion privée. Le SSPR Azure AD n’est pas pris en charge |
|Non routable|Adresses IP fédérées|À partir de la version 1803|Mise à la disposition générale|
|Non routable|Adresses IP gérées|Non pris en charge||



## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Configurer la jointure Azure Active Directory hybride pour les domaines fédérés](hybrid-azuread-join-federated-domains.md)
> [Configurer la jointure Azure Active Directory hybride pour les domaines managés](hybrid-azuread-join-managed-domains.md)




<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
