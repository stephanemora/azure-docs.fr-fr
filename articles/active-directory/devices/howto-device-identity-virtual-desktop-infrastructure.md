---
title: Identité d’appareil et virtualisation de bureau - Azure Active Directory
description: Découvrez comment les identités d’appareil VDI et Azure AD peuvent être utilisées ensemble
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0dde9d8b50233c3c4033daf618e0e626c0174b0c
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87903151"
---
# <a name="device-identity-and-desktop-virtualization"></a>Identité d’appareil et virtualisation de bureau

Les administrateurs déploient généralement des plateformes VDI (Virtual Desktop Infrastructure) hébergeant des systèmes d’exploitation Windows dans leurs organisations. Les administrateurs déploient la solution VDI pour :

- Simplifier la gestion.
- Réduire les coûts par le regroupement et la centralisation des ressources.
- Dispenser aux utilisateurs finals la mobilité et la liberté d’accéder aux bureaux virtuels, à tout moment, en tous lieux, sur tout appareil.

Il existe deux types principaux de bureaux virtuels :

- Persistante
- Non persistant

Les versions persistantes utilisent une image de bureau unique pour chaque utilisateur ou pool d’utilisateurs. Ces bureaux uniques peuvent être personnalisés et enregistrés pour une utilisation ultérieure. 

Les versions non persistantes utilisent une collection de bureaux auxquels les utilisateurs peuvent accéder en fonction des besoins. L’état d’origine de ces bureaux non persistants est rétabli une fois que l’utilisateur se déconnecte.

Cet article présente les conseils de Microsoft aux administrateurs, en matière de prise en charge de l’identité d’appareil et de la solution VDI. Pour plus d’informations sur l’identité d’appareil, consultez l’article [Présentation de l’identité d’appareil](overview.md).

## <a name="supported-scenarios"></a>Scénarios pris en charge

Avant de configurer les identités d’appareil dans Azure AD pour votre environnement VDI, familiarisez-vous avec les scénarios pris en charge. Le tableau ci-dessous illustre les scénarios de provisionnement pris en charge. Dans ce contexte, le provisionnement implique qu’un administrateur peut configurer des identités d’appareil à grande échelle, sans nécessiter l’intervention d’utilisateurs finals.

| Type d’identité d’appareil | Infrastructure d’identité | Appareils Windows | Version de la plateforme VDI | Prise en charge |
| --- | --- | --- | --- | --- |
| Appareils joints Azure AD hybrides | Fédérée* | Windows actuel*** et Windows de bas niveau**** | Persistante | Oui |
|   |   | Windows actuel | Non persistante | Non |
|   |   | Appareils Windows de bas niveau | Non persistante | Oui |
|   | Managée** | Windows actuel et Windows de bas niveau | Persistante | Oui |
|   |   | Windows actuel | Non persistante | Non |
|   |   | Appareils Windows de bas niveau | Non persistante | Oui |
| Appareil joints Azure AD | Adresses IP fédérées | Windows actuel | Persistante | Non |
|   |   |   | Non persistante | Non |
|   | Adresses IP gérées | Windows actuel | Persistante | Non |
|   |   |   | Non persistante | Non |
| Appareils inscrits sur Azure AD | Adresses IP fédérées | Windows actuel | Persistante | Non applicable |
|   |   |   | Non persistante | Non applicable |
|   | Adresses IP gérées | Windows actuel | Persistante | Non applicable |
|   |   |   | Non persistante | Non applicable |

\* Un environnement d’infrastructure d’identité **fédérée** représente un environnement avec un fournisseur d’identité, tel que AD FS ou d’autres IdP tiers.

\*\* Un environnement d’infrastructure d’identité **managée** représente un environnement avec Azure AD en tant que fournisseur d’identité déployé au moyen de la [synchronisation de hachage de mot de passe (PHS)](../hybrid/whatis-phs.md) ou de l’[authentification directe (PTA)](../hybrid/how-to-connect-pta.md) avec l’[authentification unique fluide](../hybrid/how-to-connect-sso.md).

\*\*\* Les appareils **Windows actuels** sont Windows 10, Windows Server 2016 et Windows Server 2019.

\*\*\*\* Les appareils **Windows de bas niveau** sont Windows 7, Windows 8.1, Windows Server 2008 R2, Windows Server 2012 et Windows Server 2012 R2. Pour des informations relatives à la prise en charge de Windows 7, consultez [Fin de la prise en charge de Windows 7](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support). Pour obtenir des informations de support sur Windows Server 2008 R2, consultez [Préparez-vous à la fin du support de Windows Server 2008](https://www.microsoft.com/cloud-platform/windows-server-2008).

## <a name="microsofts-guidance"></a>Conseils de Microsoft

Les administrateurs doivent se référer aux articles suivants, en fonction de leur infrastructure d’identité, pour savoir comment configurer la jonction Azure AD Hybride.

- [Configurer la jonction Azure Active Directory Hybride pour un environnement fédéré](hybrid-azuread-join-federated-domains.md)
- [Configurer la jonction Azure Active Directory Hybride pour un environnement managé](hybrid-azuread-join-managed-domains.md)

Si vous comptez sur l’outil de préparation système (sysprep.exe) et utilisez une image pré-Windows 10 1809 pour l’installation, vérifiez que cette image ne provient pas d’un appareil déjà inscrit auprès d’Azure AD en tant qu’appareil Azure AD Hybride joint.

Si vous utilisez une capture instantanée de machine virtuelle pour créer des machines virtuelles supplémentaires, vérifiez qu’elle ne provient pas d'une machine virtuelle déjà inscrite auprès d'Azure AD en tant que jonction Azure AD Hybride.

La jointure Azure AD Hybride d’une infrastructure VDI non persistante n’est pas prise en charge dans les versions actuelles de Windows. Lors du déploiement d’une infrastructure VDI non persistante Windows de bas niveau, les administrateurs informatiques doivent prêter une attention toute particulière à la gestion des appareils obsolètes dans Azure AD. Microsoft recommande aux administrateurs informatiques d’implémenter les instructions ci-dessous. Dans le cas contraire, l’annuaire reçoit un grand nombre d’appareils Azure AD Hybride joints, qui ont été enregistrés depuis la plateforme VDI non persistante.

- Créez et utilisez un préfixe pour le nom d’affichage de l’ordinateur qui indique le bureau comme étant basé sur VDI.
- Implémentez la commande suivante dans le cadre du script de fermeture de session. Cette commande déclenche le meilleur appel possible à Azure AD pour supprimer l’appareil.
   - Pour les appareils Windows de bas niveau – autoworkplace.exe /leave
- Définissez et implémentez le processus de [gestion des appareils obsolètes](manage-stale-devices.md).
   - Une fois que vous disposez d’une stratégie d’identification de vos appareils Azure AD Hybride joints non persistants, vous pouvez être plus agressif lors du nettoyage de ces appareils, pour être certain que votre annuaire ne sera pas consommé par un grand nombre d’appareils obsolètes.
 
## <a name="next-steps"></a>Étapes suivantes

[Configuration de la jonction Azure AD Hybride pour un environnement fédéré](hybrid-azuread-join-federated-domains.md)
