---
title: Accès conditionnel - Exiger des appareils conformes - Azure Active Directory
description: Créer une stratégie d’accès conditionnel personnalisée pour exiger des appareils conformes
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a18f0b1f83ae02b06344c332cfdd1cc093f37fc
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424892"
---
# <a name="conditional-access-require-compliant-devices"></a>Accès conditionnel : Exiger un appareil conforme

Les organisations qui ont déployé Microsoft Intune peuvent utiliser les informations retournées par leurs appareils pour identifier les appareils qui répondent aux exigences de conformité, comme :

* Exiger un code confidentiel pour le déverrouillage
* Exiger le chiffrement de l’appareil
* Exiger une version minimale ou maximale du système d’exploitation
* Exiger qu’un appareil ne soit ni jailbroken ni rooté

Ces informations de conformité de la stratégie sont transmises à Azure AD, où l’accès conditionnel peut prendre des décisions pour accorder ou bloquer l’accès aux ressources.

## <a name="create-a-conditional-access-policy"></a>Créer une stratégie d’accès conditionnel

Les étapes suivantes vous aideront à créer une stratégie d’accès conditionnel pour exiger que les appareils qui accèdent aux ressources soient marqués comme conformes aux stratégies de conformité Intune de votre organisation.

1. Connectez-vous au **portail Microsoft Azure** en tant qu’administrateur général, administrateur de sécurité ou administrateur de l’accès conditionnel.
1. Accédez à **Azure Active Directory** > **Sécurité** > **Accès conditionnel.**
1. Sélectionnez **Nouvelle stratégie**.
1. Donnez un nom à votre stratégie. Nous recommandons aux organisations de créer une norme explicite pour les noms de leurs stratégies.
1. Sous **Affectations**, sélectionnez **Utilisateurs et groupes**
   1. Sous **Inclure**, sélectionnez **Tous les utilisateurs**.
   1. Sous **Exclure**, sélectionnez **Utilisateurs et groupes**, puis choisissez les comptes d’accès d’urgence ou de secours de votre organisation. 
   1. Sélectionnez **Terminé**.
1. Sous **Applications ou actions cloud** > **Inclure**, sélectionnez **Toutes les applications cloud**.
   1. Si vous devez exclure des applications spécifiques de votre stratégie, vous pouvez les choisir dans l'onglet **Exclure** sous **Sélectionner les applications cloud exclues**, puis choisir **Sélectionner**.
   1. Sélectionnez **Terminé**.
1. Sous **Contrôles d’accès** > **Accorder**, sélectionnez **Exiger que l’appareil soit marqué comme conforme**.
   1. Sélectionnez **Sélectionner**.
1. Confirmez vos paramètres et réglez **Activer la stratégie** sur **Activé**.
1. Sélectionnez **Créer** pour créer votre stratégie.

### <a name="known-behavior"></a>Comportement connu

Sur des systèmes Windows 7, iOS, Android, macOS, et dans certains navigateurs web tiers, Azure AD identifie l’appareil à l’aide d’un certificat client, qui est provisionné lorsque l’appareil est inscrit auprès d’Azure AD. Lorsqu’un utilisateur se connecte pour la première fois via le navigateur, l’utilisateur est invité à sélectionner le certificat. L’utilisateur final doit sélectionner ce certificat pour pouvoir continuer à utiliser le navigateur.

## <a name="next-steps"></a>Étapes suivantes

[Stratégies d’accès conditionnel courantes](concept-conditional-access-policy-common.md)

[Déterminer l'impact à l'aide du mode Rapport seul de l'Accès conditionnel](howto-conditional-access-report-only.md)

[Simuler le comportement de connexion à l’aide de l’outil What If pour l’accès conditionnel](troubleshoot-conditional-access-what-if.md)

[Les stratégies de conformité des appareils fonctionnent avec Azure AD](https://docs.microsoft.com/intune/device-compliance-get-started#device-compliance-policies-work-with-azure-ad)
