---
title: Accès conditionnel - Exiger des appareils conformes - Azure Active Directory
description: Créer une stratégie d’accès conditionnel personnalisée pour exiger des appareils conformes
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83a4323c2679bdf55709aeaed82134b7b4457fee
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74803697"
---
# <a name="conditional-access-require-compliant-devices"></a>Accès conditionnel : Exiger un appareil conforme

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

## <a name="next-steps"></a>Étapes suivantes

[Stratégies d’accès conditionnel courantes](concept-conditional-access-policy-common.md)

[Simuler le comportement de connexion à l’aide de l’outil What If pour l’accès conditionnel](troubleshoot-conditional-access-what-if.md)

[Les stratégies de conformité des appareils fonctionnent avec Azure AD](https://docs.microsoft.com/intune/device-compliance-get-started#device-compliance-policies-work-with-azure-ad)
