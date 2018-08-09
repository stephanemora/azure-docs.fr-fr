---
title: Comment configurer des appareils hybrides joints à Azure Active Directory | Microsoft Docs
description: Découvrez comment configurer des appareils hybrides joints à Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2018
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: 27068c1154a4cc5776bbcc74922ca31c4f28ced6
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399915"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Guide pratique pour planifier l’implémentation de la jointure Azure Active Directory hybride

De façon comparable à un utilisateur, un appareil devient une autre identité qu’il faut protéger et également utiliser pour protéger les ressources partout et à tout instant. Vous pouvez atteindre cet objectif en intégrant les identités de vos appareils à Azure AD suivant l’une des méthodes ci-dessous :

- jointure Azure AD ;
- jointure Azure AD hybride ;
- inscription Azure AD.

En mettant vos appareils sur Azure AD, vous optimisez la productivité de vos utilisateurs par le biais de l’authentification unique (SSO) sur vos ressources cloud et locales. Dans le même temps, vous pouvez sécuriser l’accès à ces ressources avec [l’accès conditionnel](../active-directory-conditional-access-azure-portal.md).

Si vous disposez d’un environnement Active Directory local et que vous souhaitez lier à Azure AD vos appareils joints à un domaine, vous pouvez y parvenir en configurant simplement des appareils hybrides joints à Azure AD. Cet article présente les étapes à suivre pour implémenter une jointure Azure AD hybride dans un environnement. 


## <a name="prerequisites"></a>Prérequis

Cet article suppose que vous avez lu la [Présentation de la gestion des appareils dans Azure Active Directory](../device-management-introduction.md).


## <a name="plan-your-implementation"></a>Planifier l’implémentation

Pour planifier votre implémentation Azure AD hybride, prenez connaissance de ces étapes :

|   |   |
|---|---|
|![Vérification][1]|Vérifier les appareils pris en charge|
|![Vérification][1]|Connaître les points à savoir|
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

En cas de dépendance vis-à-vis de l’Outil de préparation du système (Sysprep), veillez à créer des images à partir d’une installation de Windows sur laquelle la jointure Azure AD hybride n’a pas été configurée.

Si vous utilisez une capture instantanée de machine virtuelle pour créer des machines virtuelles supplémentaires, vérifiez qu’elle n’a pas été configurée pour la jointure Azure AD hybride.

L’inscription des appareils Windows de bas niveau n’est pas prise en charge pour les appareils ayant pour configuration l’itinérance du profil utilisateur ou des informations d’identification. Si vous vous appuyez sur l’itinérance de profils ou de paramètres, utilisez Windows 10.

L’inscription de Windows Server avec le rôle de contrôleur de domaine n’est pas prise en charge.

Si votre organisation a besoin d’accéder à Internet via un proxy sortant authentifié, assurez-vous que vos ordinateurs Windows 10 parviennent à s’authentifier sur le proxy sortant. Les ordinateurs Windows 10 procèdent à l’inscription des appareils suivant le contexte ordinateur ; il est donc indispensable de configurer l’authentification du proxy sortant selon ce contexte.


La jointure Azure AD hybride est un processus qui consiste à inscrire automatiquement auprès d’Azure AD des appareils joints au domaine local. Si vous ne souhaitez pas que tous vos appareils s’inscrivent automatiquement, voir [Guide pratique pour contrôler la jointure Azure AD hybride de vos appareils](hybrid-azuread-join-control.md).



## <a name="select-your-scenario"></a>Sélectionner un scénario

La jointure Azure AD hybride peut être configurée pour les scénarios suivants :

- les domaines managés ;
- les domaines fédérés.  



Si votre environnement comporte des domaines managés, la jointure Azure AD hybride prend en charge :

- la synchronisation directe avec authentification unique transparente (SSO) ; 

- la synchronisation de hachage de mot de passe avec authentification unique transparente (SSO). 

Depuis la version 1.1.819.0, Azure AD Connect comporte un Assistant permettant de configurer la jointure Azure AD hybride. Il simplifie considérablement le processus de configuration. Pour plus d'informations, consultez les pages suivantes :

- [Configurer la jointure hybride Azure Active Directory pour des domaines fédérés](hybrid-azuread-join-federated-domains.md)

- [Configurer la jointure hybride Azure Active Directory pour des domaines managés](hybrid-azuread-join-managed-domains.md)


 Si vous n’avez pas la possibilité d’installer la version requise d’Azure AD Connect, consultez le [Guide pratique pour configurer manuellement l’inscription des appareils](../device-management-hybrid-azuread-joined-devices-setup.md). 






## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Configurer la jointure Azure Active Directory hybride pour les domaines fédérés](hybrid-azuread-join-federated-domains.md)
> [Configurer la jointure Azure Active Directory hybride pour les domaines managés](hybrid-azuread-join-managed-domains.md)




<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
