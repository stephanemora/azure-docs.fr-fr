---
title: Qu’est-ce qu’un appareil joint Azure AD hybride ?
description: Découvrez comment la gestion d’identité des appareils peut vous aider à gérer les appareils qui accèdent aux ressources de votre environnement.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/10/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 083dcc0dc5d6ffdf25f6d6f631a8e6f5788ce8b1
ms.sourcegitcommit: 025a2bacab2b41b6d211ea421262a4160ee1c760
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/06/2021
ms.locfileid: "113303037"
---
# <a name="hybrid-azure-ad-joined-devices"></a>Appareils joints Azure AD hybrides

Les organisations avec des implémentations Active Directory existantes peuvent tirer parti de certaines des fonctionnalités fournies par Azure Active Directory (Azure AD) en implémentant des appareils à jointure hybride Azure AD. Ces appareils sont joints à votre Active Directory local et inscrits auprès d’Azure Active Directory.

Les appareils à jointure hybride Azure AD doivent avoir périodiquement une visibilité réseau directe vers vos contrôleurs de domaine locaux. Sans cette connexion, les appareils deviennent inutilisables. Si cette condition pose un problème, pensez [associer Azure AD](concept-azure-ad-join.md) à vos appareils.

| Jonction Azure AD Hybride | Description |
| --- | --- |
| **Définition** | Joint aux systèmes AD local et Azure AD nécessitant un compte professionnel pour se connecter à l’appareil |
| **Public principal** | Convient aux organisations hybrides avec une infrastructure AD locale existante |
|   | S’applique à tous les utilisateurs d’une organisation |
| **Appartenance de l’appareil** | Organisation |
| **Systèmes d’exploitation** | Windows 10, 8.1 et 7 |
|   | Windows Server 2008/R2, 2012/R2, 2016 et 2019 |
| **Approvisionnement** | Windows 10, Windows Server 2016/2019 |
|   | Jonction de domaine par informatique et jointure automatique via une configuration Azure AD Connect ou ADFS |
|   | Jonction de domaine par Windows Autopilot et jointure automatique via une configuration Azure AD Connect ou ADFS |
|   | Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 et Windows Server 2008 R2 : requièrent une identité MSI |
| **Options de connexion de l’appareil** | Comptes professionnels utilisant : |
|   | Mot de passe |
|   | Windows Hello Entreprise pour Win10 |
| **Gestion des appareils** | Stratégie de groupe |
|   | Configuration Manager autonome ou cogestion avec Microsoft Intune |
| **Fonctionnalités clés** | Authentification unique vers les ressources de cloud et locales |
|   | Accès conditionnel via la jonction de domaine ou via Intune si cogéré |
|   | Réinitialisation de mot de passe en libre-service et réinitialisation du code confidentiel Windows Hello sur l’écran de verrouillage |
|   | Enterprise State Roaming entre appareils |

![Appareils joints Azure AD hybrides](./media/concept-azure-ad-join-hybrid/azure-ad-hybrid-joined-device.png)

## <a name="scenarios"></a>Scénarios

Utilisez des appareils joints Azure AD hybrides si :

- Vous prenez en charge des appareils de bas niveau exécutant Windows 7 et 8.1.
- Vous souhaitez continuer à utiliser la stratégie de groupe pour gérer la configuration de l’appareil.
- Vous souhaitez continuer à utiliser des solutions de création d’images existantes pour déployer et configurer des appareils.
- Des applications Win32 sont déployées sur ces appareils qui s’appuient sur l’authentification d’ordinateur Active Directory.

## <a name="next-steps"></a>Étapes suivantes

- [Planifier votre implémentation de la jointure d’Azure AD hybride](hybrid-azuread-join-plan.md)
- [Gérer les identités d’appareil à l’aide du portail Azure](device-management-azure-portal.md)
- [Gérer les appareils obsolètes dans Azure AD](manage-stale-devices.md)
