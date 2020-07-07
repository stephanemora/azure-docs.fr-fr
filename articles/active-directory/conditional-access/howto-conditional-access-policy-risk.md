---
title: Accès conditionnel – Accès conditionnel basé sur les risques – Azure Active Directory
description: Créer des stratégies d’accès conditionnel pour profiter des améliorations apportées à Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9cfba377aba30d4687bab4ba7c5a311c70c4905
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "83995154"
---
# <a name="conditional-access-risk-based-conditional-access"></a>Accès conditionnel : Accès conditionnel basé sur les risques

Les organisations disposant de licences Azure AD Premium P2 peuvent créer des stratégies d’accès conditionnel incorporant les détections de risques Azure AD Identity Protection. Trois stratégies par défaut peuvent être activées dès le départ. 

* Exiger que tous les utilisateurs s’inscrivent pour Azure Multi-Factor Authentication.
* Exiger un changement de mot de passe pour les utilisateurs exposés à un risque élevé.
* Exigez l’authentification multifacteur pour les personnes exposés à un risque de connexion moyen à élevé.

## <a name="require-all-users-to-register-for-azure-multi-factor-authentication"></a>Exiger que tous les utilisateurs s’inscrivent pour Azure Multi-Factor Authentication

L’activation de cette stratégie nécessite que tous les utilisateurs s’inscrivent pour Azure Multi-Factor Authentication dans un délai de 14 jours. 

1. Connectez-vous au **portail Azure**.
1. Cliquez sur **Tous les services**, puis accédez à **Azure AD Identity Protection**.
1. Cliquez sur **Inscription à MFA**.
1. Sous **Attributions**, sélectionnez **Utilisateurs**.
   1. Sous **Inclure**, sélectionnez **Tous les utilisateurs**.
   1. Sous **Exclure**, sélectionnez **Sélectionner les utilisateurs exclus**, choisissez les comptes d’accès d’urgence ou de secours de votre organisation, puis sélectionnez **Sélectionner**. 
   1. Sélectionnez **Terminé**.
1. Définissez **Appliquer la stratégie** sur **Activé**.
1. Cliquez sur **Enregistrer**.

## <a name="require-a-password-change-high-risk-users"></a>Exiger un changement de mot de passe pour les utilisateurs à haut risque

Microsoft travaille avec des chercheurs, les forces de l’ordre, différentes équipes de sécurité chez Microsoft et autres sources de confiance pour trouver des paires nom d’utilisateur/mot de passe. Lorsqu’une de ces paires correspond à un compte dans votre environnement, une modification de mot de passe en fonction des risques peut être déclenchée à l’aide de la stratégie suivante.

1. Connectez-vous au **portail Azure**.
1. Cliquez sur **Tous les services**, puis accédez à **Azure AD Identity Protection**.
1. Cliquez sur **Stratégie de risque utilisateur**.
1. Sous **Attributions**, sélectionnez **Utilisateurs**
   1. Sous **Inclure**, sélectionnez **Tous les utilisateurs**.
   1. Sous **Exclure**, sélectionnez **Sélectionner les utilisateurs exclus**, choisissez les comptes d’accès d’urgence ou de secours de votre organisation, puis sélectionnez **Sélectionner**.
   1. Sélectionnez **Terminé**.
1. Sous **Conditions**, sélectionnez **Risque de l’utilisateur**, puis choisissez **Élevé**.
   1. Cliquez sur **Sélectionner**, puis sur **Effectué**.
1. Sous **Contrôles** > **Accès**, choisissez **Autoriser l’accès**, puis sélectionnez **Nécessite une modification du mot de passe**.
   1. Cliquez sur **Sélectionner**.
1. Définissez **Appliquer la stratégie** sur **Activé**.
1. Cliquez sur **Enregistrer**.

## <a name="require-mfa-medium-or-high-sign-in-risk-users"></a>Exiger MFA pour les utilisateurs à risque de connexion moyen à élevé

La plupart des utilisateurs ont un comportement normal qui peut être suivi. Lorsqu’ils dévient de cette norme, il peut être risqué de les autoriser à se connecter uniquement. Vous pouvez bloquer ces utilisateurs ou simplement leur demander d’effectuer une authentification multifacteur pour prouver qu’ils sont vraiment ceux qu’ils prétendent être. Pour activer une stratégie nécessitant une authentification multifacteur lorsqu’une connexion à risque est détectée, activez la stratégie suivante.

1. Connectez-vous au **portail Azure**.
1. Cliquez sur **Tous les services**, puis accédez à **Azure AD Identity Protection**.
1. Cliquez sur **Stratégie de connexion à risque**.
1. Sous **Attributions**, sélectionnez **Utilisateurs**
   1. Sous **Inclure**, sélectionnez **Tous les utilisateurs**.
   1. Sous **Exclure**, sélectionnez **Sélectionner les utilisateurs exclus**, choisissez les comptes d’accès d’urgence ou de secours de votre organisation, puis sélectionnez **Sélectionner**.
   1. Sélectionnez **Terminé**.
1. Sous **Conditions**, sélectionnez **Risque de connexion**, puis choisissez **Moyen et supérieur**.
   1. Cliquez sur **Sélectionner**, puis sur **Effectué**.
1. Sous **Contrôles** > **Accès**, choisissez **Autoriser l’accès**, puis sélectionnez **Exiger une authentification multifacteur**.
   1. Cliquez sur **Sélectionner**.
1. Définissez **Appliquer la stratégie** sur **Activé**.
1. Cliquez sur **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

[Stratégies d’accès conditionnel courantes](concept-conditional-access-policy-common.md)

[Déterminer l'impact à l'aide du mode Rapport seul de l'Accès conditionnel](howto-conditional-access-report-only.md)

[Simuler le comportement de connexion à l’aide de l’outil What If pour l’accès conditionnel](troubleshoot-conditional-access-what-if.md)

[Fonctionnement : Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)

[Qu’est-ce que Azure Active Directory Identity Protection ?](../identity-protection/overview.md)
