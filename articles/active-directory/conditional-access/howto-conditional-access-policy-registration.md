---
title: Accès conditionnel - Informations de sécurité combinées - Azure Active Directory
description: Créer une stratégie d’accès conditionnel personnalisée pour l’inscription des informations de sécurité
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 03/29/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 38ddd825321a3cb7947eb76c4f5f6cdbc6cb3414
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055703"
---
# <a name="conditional-access-securing-security-info-registration"></a>Accès conditionnel : Sécurisation de l’inscription des informations de sécurité

La sécurisation de l’inscription des utilisateurs à Azure AD Multi-Factor Authentication et à la réinitialisation de mot de passe en libre-service est possible avec les actions de l’utilisateur dans une stratégie d’accès conditionnel. Cette fonctionnalité est accessible aux organisations qui ont activé l’[inscription combinée](../authentication/concept-registration-mfa-sspr-combined.md). Cette fonctionnalité permet aux organisations de traiter le processus d’inscription comme n’importe quelle application dans une stratégie d’accès conditionnel et d’utiliser toute la puissance de l’accès conditionnel pour sécuriser l’expérience. 

Certaines organisations du passé peuvent avoir utilisé l’emplacement réseau approuvé ou la conformité des appareils comme un moyen de sécuriser l’inscription. Avec l’ajout du [Passe d’accès temporaire](../authentication/howto-authentication-temporary-access-pass.md) dans Azure AD, les administrateurs peuvent configurer des informations d’identification limitées dans le temps à leurs utilisateurs, ce qui leur permet de s’inscrire à partir de n’importe quel appareil ou emplacement. Les informations d’identification du passe d’accès temporaire répondent aux exigences d’accès conditionnel pour l’authentification multifacteur.

## <a name="create-a-policy-to-secure-registration"></a>Créer une stratégie pour sécuriser l’inscription

La stratégie suivante s’applique aux utilisateurs sélectionnés, qui tente de s’inscrire au moyen de l’inscription combinée. La stratégie oblige les utilisateurs à effectuer une authentification multifacteur ou à utiliser les informations d’identification de passe d’accès temporaire.

1. Dans le portail **Azure**, accédez à **Azure Active Directory** > **Sécurité** > **Accès conditionnel**.
1. Sélectionnez **Nouvelle stratégie**.
1. Sous Nom, entrez un nom pour cette stratégie. Par exemple, **Inscription d’informations de sécurité combinée avec TAP**.
1. Sous **Affectations**, sélectionnez **Utilisateurs et groupes**, puis sélectionnez les utilisateurs et les groupes auxquels vous souhaitez appliquer cette stratégie.
   1. Sous **Inclure**, sélectionnez **Tous les utilisateurs**.

      > [!WARNING]
      > Les utilisateurs doivent être activés pour l’[inscription combinée](../authentication/howto-registration-mfa-sspr-combined.md).

   1. Sous **Exclure**.
      1. Sélectionnez **Tous les utilisateurs invités et externes**.
      
         > [!NOTE]
         > Le Passe d’accès temporaire ne fonctionne pas pour les utilisateurs invités.

      1. Sélectionnez **Utilisateurs et groupes** et choisissez les comptes d’accès d’urgence ou de secours de votre organisation. 
1. Sous **Applications cloud ou actions**, sélectionnez **Actions utilisateur**, cochez **Inscrire des informations de sécurité**.
1. Sous **Contrôles d’accès** > **Octroyer**.
   1. Sélectionner **Accorder l’accès**.
   1. Sélectionnez **Exiger une authentification multifacteur**.
   1. Cliquez sur **Sélectionner**.
1. Définissez l’option **Appliquer la stratégie** sur **Activé**.
1. Sélectionnez ensuite **Créer**.

Les administrateurs doivent désormais émettre des informations d’identification de passe d’accès temporaire aux nouveaux utilisateurs pour qu’ils puissent répondre aux conditions requises pour l’inscription de l’authentification multifacteur. Pour effectuer cette tâche, consultez la section [Créer un passe d’accès temporaire sur le Portail Azure AD](../authentication/howto-authentication-temporary-access-pass.md#create-a-temporary-access-pass).

Les organisations peuvent choisir d’exiger d’autres contrôles d’octroi en plus ou à la place de **Demander une authentification multifacteur** à l’étape 6B. Lorsque vous sélectionnez plusieurs contrôles, veillez à activer la case d’option appropriée pour exiger l'**ensemble** ou l'**un** des contrôles sélectionnés lors de l’exécution de cette modification.

### <a name="guest-user-registration"></a>Inscription des utilisateurs invités

Pour les [utilisateurs invités](../external-identities/what-is-b2b.md) qui doivent s’inscrire à l’authentification multifacteur dans votre répertoire, vous pouvez choisir de bloquer l’inscription en dehors des [emplacements réseau approuvés](concept-conditional-access-conditions.md#locations) à l’aide du guide suivant.

1. Dans le portail **Azure**, accédez à **Azure Active Directory** > **Sécurité** > **Accès conditionnel**.
1. Sélectionnez **Nouvelle stratégie**.
1. Sous Nom, entrez un nom pour cette stratégie. Par exemple, **Inscription d’informations de sécurité combinée sur les réseaux approuvés**.
1. Sous **Affectations**, sélectionnez **Utilisateurs et groupes**, puis sélectionnez les utilisateurs et les groupes auxquels vous souhaitez appliquer cette stratégie.
   1. Sous **Inclure**, sélectionnez **Tous les utilisateurs invités et externes**.
1. Sous **Applications cloud ou actions**, sélectionnez **Actions utilisateur**, cochez **Inscrire des informations de sécurité**.
1. Sous **Conditions** > **Emplacements**.
   1. Configurez **Oui**.
   1. Incluez **N’importe quel emplacement**.
   1. Excluez **Tous les emplacements approuvés**.
   1. Sélectionnez **Terminé** dans le panneau Emplacements.
   1. Sélectionnez **Terminé** dans le panneau des conditions.
1. Sous **Contrôles d’accès** > **Octroyer**.
   1. Sélectionnez **Bloquer l’accès**.
   1. Puis cliquez sur **Sélectionner**.
1. Définissez l’option **Appliquer la stratégie** sur **Activé**.
1. Ensuite, sélectionnez **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

[Stratégies d’accès conditionnel courantes](concept-conditional-access-policy-common.md)

[Déterminer l'impact à l'aide du mode Rapport seul de l'Accès conditionnel](howto-conditional-access-insights-reporting.md)

[Simuler le comportement de connexion à l’aide de l’outil What If pour l’accès conditionnel](troubleshoot-conditional-access-what-if.md)
