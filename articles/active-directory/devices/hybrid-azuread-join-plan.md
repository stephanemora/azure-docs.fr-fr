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
ms.date: 04/10/2019
ms.author: joflore
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64dd8067654246f7c9a077d027c068df820f439d
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2019
ms.locfileid: "66688705"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Procédure : Planifier l’implémentation de la jonction Azure AD Hybride

De manière similaire à un utilisateur, un appareil est une autre identité core que vous souhaitez protéger et l’utiliser pour protéger vos ressources à tout moment et à partir de n’importe quel emplacement. Vous pouvez atteindre cet objectif en ouvrant et la gestion des identités des appareils dans Azure AD en utilisant l’une des méthodes suivantes :

- jointure Azure AD ;
- jointure Azure AD hybride ;
- inscription Azure AD.

En mettant vos appareils sur Azure AD, vous optimisez la productivité de vos utilisateurs par le biais de l’authentification unique (SSO) sur vos ressources cloud et locales. Dans le même temps, vous pouvez sécuriser l’accès à ces ressources avec [l’accès conditionnel](../active-directory-conditional-access-azure-portal.md).

Si votre environnement Active Directory (AD) en local et que vous souhaitez joindre vos ordinateurs joints au domaine d’Active Directory à Azure AD, vous pouvez le faire en effectuant une jointure hybrid Azure AD. Cet article présente les étapes à suivre pour implémenter une jointure Azure AD hybride dans un environnement. 

## <a name="prerequisites"></a>Conditions préalables

Cet article suppose que vous êtes familiarisé avec la [Introduction à la gestion d’identité de périphérique dans Azure Active Directory](../device-management-introduction.md).

> [!NOTE]
> Le minimum requis fonctionnel de domaine et les niveaux fonctionnels de forêt pour une jointure hybrid Azure AD Windows 10 est Windows Server 2008 R2.

## <a name="plan-your-implementation"></a>Planifier l’implémentation

Pour planifier votre implémentation Azure AD hybride, prenez connaissance de ces étapes :

|   |   |
| --- | --- |
| ![Vérification][1] | Vérifier les appareils pris en charge |
| ![Vérification][1] | Connaître les points à savoir |
| ![Vérification][1] | Passez en revue la validation contrôlée de jonction hybride Azure AD |
| ![Vérification][1] | Sélectionnez votre scénario en fonction de votre infrastructure d’identité |
| ![Vérification][1] | Révision local UPN AD prise en charge des hybrides d’Azure AD join |

## <a name="review-supported-devices"></a>Vérifier les appareils pris en charge

La jointure Azure AD hybride prend en charge un large éventail d’appareils Windows. Dans la mesure où la configuration des appareils fonctionnant sous des versions antérieures de Windows implique des étapes supplémentaires ou différentes, les appareils pris en charge sont divisés en deux catégories :

### <a name="windows-current-devices"></a>Appareils Windows actuels

- Windows 10
- Windows Server 2016
- Windows Server 2019

Pour les appareils exécutant le système d’exploitation de bureau Windows, version prise en charge sont répertoriées dans cet article [les informations de version de Windows 10](https://docs.microsoft.com/windows/release-information/). Comme meilleure pratique, Microsoft vous recommande de que vous mettre à niveau vers la dernière version de Windows 10.

### <a name="windows-down-level-devices"></a>Appareils Windows de bas niveau

- Windows 8.1
- Windows 7. Pour plus d’informations sur Windows 7, veuillez consulter cet article [prennent en charge pour Windows 7 se termine.](https://www.microsoft.com/en-us/windowsforbusiness/end-of-windows-7-support)
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2

La première étape de la planification consiste à examiner l’environnement et à déterminer s’il est nécessaire de prendre en charge les appareils Windows de bas niveau.

## <a name="review-things-you-should-know"></a>Connaître les points à savoir

Une jointure Hybrid Azure AD n’est actuellement pas possible si votre environnement se compose d’une seule forêt AD, la synchronisation des données d’identité à plusieurs locataires Azure AD.

Une jointure Hybrid Azure AD est actuellement pas pris en charge à l’aide d’infrastructure de bureau virtuel (VDI).

Azure AD hybride n’est pas pris en charge pour le module de plateforme sécurisée compatible FIPS. Si vos appareils sont dotés de TPM compatible FIPS, vous devez les désactiver avant de procéder à une jointure Hybrid Azure AD. Microsoft ne fournit pas d’outils permettant de désactiver le mode FIPS pour le module de plateforme sécurisée car il dépend du fabricant du TPM. Veuillez contacter votre matériel OEM pour la prise en charge.

Une jointure Hybrid Azure AD n’est pas pris en charge pour Windows Server qui exécute le rôle de contrôleur de domaine (DC).

Jonction hybride Azure AD n’est pas pris en charge sur les appareils de bas niveau Windows lors de l’utilisation des informations d’identification itinérantes ou en itinérance de profil utilisateur.

Si vous comptez sur l’outil de préparation système (Sysprep) et si vous utilisez un **antérieur à Windows 10 1809** de l’image pour l’installation, vérifiez que cette image n’est pas d’un appareil qui est déjà inscrit auprès d’Azure AD en tant que jointure Hybrid Azure AD.

Si vous comptez sur un instantané de Machine virtuelle (VM) pour créer des machines virtuelles supplémentaires, assurez-vous que cet instantané n’est pas d’une machine virtuelle qui est déjà inscrit auprès d’Azure AD en tant que jointure Hybrid Azure AD.

Si vos appareils joints au domaine Windows 10 sont déjà [inscrits auprès d’Azure AD](https://docs.microsoft.com/azure/active-directory/devices/overview#azure-ad-registered-devices) sur votre locataire, nous vous recommandons vivement de supprimer cet état avant d’activer la jonction Azure AD Hybride. À partir de la version 1809 de Windows 10, les modifications suivantes ont été apportées pour éviter ce double état :

- Tout état existant inscrit à Azure AD est automatiquement supprimé dès lors que l’appareil est joint à Azure AD Hybride.
- Vous pouvez empêcher votre appareil joint au domaine Azure AD inscrite en ajoutant cette clé de Registre - HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, « BlockAADWorkplaceJoin » = DWORD : 00000001.
- Cette modification est désormais disponible pour Windows 10 1803 version avec KB4489894 appliquée. Toutefois, si vous avez Windows Hello entreprise configurée, l’utilisateur est obligé de re-le programme d’installation Windows Hello entreprise après l’état double de nettoyage.


## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>Passez en revue la validation contrôlée de jonction hybride Azure AD

Lorsque toutes les conditions préalables sont en place, les appareils Windows seront inscriront automatiquement en tant qu’appareils dans votre locataire Azure AD. L’état de ces identités des appareils dans Azure AD est appelée une jointure hybrid Azure AD. Vous trouverez plus d’informations sur les concepts abordés dans cet article dans les articles [Introduction à la gestion d’identité de périphérique dans Azure Active Directory](overview.md) et [planifier votre jonction d’Azure Active Directory hybride implémentation](hybrid-azuread-join-plan.md).

Organisations peuvent vouloir effectuer une validation contrôlée de jointure Azure AD hybride avant de l’activer dans leur organisation entière à la fois. Consultez l’article [sous contrôle de validation de jonction hybride Azure AD](hybrid-azuread-join-control.md) pour comprendre comment y parvenir.


## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>Sélectionnez votre scénario en fonction de votre infrastructure d’identité

Jonction hybride Azure AD fonctionne avec les environnements à la fois, gérées et fédérées.  

### <a name="managed-environment"></a>Environnement géré

Un environnement géré peut être déployé via [synchronisation de hachage de mot de passe (PHS)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) ou [passer via l’authentification (PTA)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) avec [transparente Single Sign On](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso).

Ces scénarios ne nécessitent pas la configuration d’un serveur de fédération pour l’authentification.

### <a name="federated-environment"></a>Environnement fédéré

Un environnement fédéré doit avoir un fournisseur d’identité qui prend en charge les exigences suivantes :

- **Protocole WS-Trust :** Ce protocole est requis pour l’authentification Windows en cours joints Azure AD des appareils avec Azure AD.
- **Revendication WIAORMULTIAUTHN :** Cette revendication est requise pour effectuer la jointure d’Azure AD hybride pour les appareils de bas niveau Windows.

Si vous avez un environnement fédéré à l’aide d’Active Directory Federation Services (ADFS), puis les conditions ci-dessus sont déjà prises en charge.

> [!NOTE]
> Azure AD ne prend pas en charge les cartes à puce ou les certificats dans les domaines gérés.

Depuis la version 1.1.819.0, Azure AD Connect comporte un Assistant permettant de configurer la jointure Azure AD hybride. Il simplifie considérablement le processus de configuration. Si vous n’avez pas la possibilité d’installer la version requise d’Azure AD Connect, consultez le [Guide pratique pour configurer manuellement l’inscription des appareils](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-manual). 

Selon le scénario qui correspond à votre infrastructure d’identité, consultez :

- [Configurer la jonction d’Azure Active Directory hybride pour un environnement fédéré](hybrid-azuread-join-federated-domains.md)
- [Configurer la jonction d’Azure Active Directory hybride pour un environnement géré](hybrid-azuread-join-managed-domains.md)



## <a name="review-on-premises-ad-upn-support-for-hybrid-azure-ad-join"></a>Révision locale prise en charge de l’UPN AD de jonction hybride Azure AD

Parfois, vos UPN AD locaux peuvent différer de vos UPN AD Azure. Dans ce cas, une jonction Azure AD Hybride Windows 10 offre une prise en charge limitée des UPN AD locaux, variable selon la [méthode d’authentification](https://docs.microsoft.com/azure/security/azure-ad-choose-authn), le type de domaine et la version de Windows 10. Deux types d’UPN AD locaux peuvent exister dans votre environnement :

- UPN routable : un UPN routable possède un domaine vérifié valide, qui est inscrit auprès d’un bureau d’enregistrement de domaines. Par exemple, si contoso.com est le domaine principal dans Azure AD, contoso.org est le domaine principal dans l’AD local appartenant à Contoso et [vérifié dans Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)
- UPN non routable : un UPN non routable ne dispose pas d’un domaine vérifié. Il est applicable uniquement au sein du réseau privé de votre organisation. Par exemple, si contoso.com est le domaine principal dans Azure AD, contoso.local est le domaine principal dans l’AD local, mais n’est pas un domaine vérifiable sur Internet et n’est utilisé qu’à l’intérieur du réseau de Contoso.

Le tableau ci-dessous fournit des détails sur la prise en charge de ces UPN AD locaux dans une jointure Azure AD Hybride Windows 10

| Type d’UPN AD local | Type de domaine | version Windows 10 | Description |
| ----- | ----- | ----- | ----- |
| Routable | Adresses IP fédérées | À partir de la version 1703 | Mise à la disposition générale |
| Non routable | Adresses IP fédérées | À partir de la version 1803 | Mise à la disposition générale |
| Routable | Adresses IP gérées | Non pris en charge | |
| Non routable | Adresses IP gérées | Non pris en charge | |

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Jonction d’Azure Active Directory hybride configurer pour fédérés environnement](hybrid-azuread-join-federated-domains.md)
> [jonction d’Azure Active Directory hybride configurer pour un environnement géré](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
