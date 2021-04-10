---
title: Sécuriser l’accès externe à Microsoft Teams, SharePoint et OneDrive avec Azure Active Directory
description: Sécuriser l’accès aux services Microsoft 365 dans le cadre de votre sécurité globale en lien avec l’accès externe.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f373c849cd542769fec7a10f20dad04e742c9591
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102565136"
---
# <a name="secure-external-access-to-microsoft-teams-sharepoint-and-onedrive-for-business"></a>Sécuriser l’accès externe à Microsoft Teams, SharePoint et OneDrive Entreprise 

Microsoft Teams, SharePoint et OneDrive Entreprise sont trois des méthodes les plus utilisées pour collaborer et partager du contenu avec des utilisateurs externes. Si les méthodes « approuvées » sont trop restrictives, les utilisateurs choisissent d’autres solutions en envoyant du contenu par e-mail ou en configurant des applications et des processus externes non sécurisés, tels qu’un espace DropBox ou OneDrive personnel. Votre objectif est de trouver un équilibre entre vos besoins en matière de sécurité et la facilité de collaboration.

Cet article vous guide dans la détermination et la configuration de la collaboration externe pour répondre à vos objectifs métiers en utilisant Microsoft Teams et SharePoint.

## <a name="governance-begins-in-azure-active-directory"></a>La gouvernance commence dans Azure Active Directory

Le partage dans Microsoft 365 est en partie régi par les [paramètres de collaboration externe de la page Identités externes](https://aad.portal.azure.com/) dans Azure Active Directory (Azure AD). Si le partage externe est désactivé ou restreint dans Azure AD, il remplace tous les paramètres de partage configurés dans Microsoft 365. À titre d’exception, si l’intégration d’Azure AD B2B n’est pas activée, SharePoint et OneDrive peuvent être configurés pour prendre en charge le partage ad hoc via des codes secrets à usage unique (OTP).

![Capture d’écran des paramètres de collaboration externe](media/secure-external-access/9-external-collaboration-settings.png)

### <a name="guest-user-access"></a>Accès utilisateur invité

Trois options s’offrent à vous pour l’accès des utilisateurs invités, qui contrôle ce que les utilisateurs invités peuvent voir une fois qu’ils ont été invités. 

Pour empêcher les utilisateurs invités d’afficher les détails d’autres utilisateurs invités et de pouvoir énumérer l’appartenance à un groupe, choisissez Les utilisateurs invités ont un accès limité aux propriétés et aux appartenances des objets d’annuaire.

### <a name="guest-invite-settings"></a>Paramètres d’invitation d’invités

Ces paramètres déterminent qui peut inviter des invités et comment ces invités peuvent être invités. Ces paramètres sont activés uniquement si l’intégration avec B2B est activée.

Nous vous recommandons d’activer l’option Les administrateurs et utilisateurs ayant le rôle d’Inviteur d’invités peuvent inviter. Ce paramètre permet de configurer des processus de collaboration contrôlés, comme dans l’exemple suivant.

* Le propriétaire de l’équipe soumet un ticket pour se voir attribuer le rôle Inviteur d’invités et

   * Devient responsable de toutes les invitations d’invités.

   * Accepte de ne pas ajouter directement d’utilisateurs à l’espace SharePoint sous-jacent

   * Est responsable de l’exécution de révisions d’accès régulières et de la révocation appropriée des accès.

* L’équipe informatique centrale effectue ce qui suit :

   * Active le partage externe en accordant le rôle demandé après l’achèvement de la formation.

   * Attribue une licence Azure AD P2 au propriétaire du groupe Microsoft 365 pour activer les révisions d’accès.
   * Crée une révision d’accès pour le groupe Microsoft 365.

   * Confirme que des révisions d’accès se produisent.

   * Supprime les utilisateurs directement ajoutés à l’espace SharePoint sous-jacent.

 Définissez **Activer automatiquement le code secret à usage unique par e-mail (préversion) et Activer l’inscription libre-service d’invité via des flux utilisateur** sur **Oui**. Ce paramètre tire parti de l’intégration avec les paramètres de collaboration externe d’Azure AD.

### <a name="collaboration-restrictions"></a>Restrictions de collaboration

Il existe trois options sous Restrictions de collaboration. Les besoins de votre entreprise déterminent ce que vous allez choisir.

* **Autoriser l’envoi des invitations à tous les domaines** signifie que tout utilisateur peut être invité à collaborer.

* **Refuser l’envoi des invitations aux domaines spécifiés** signifie que tout utilisateur en dehors de ces domaines peut être invité à collaborer.

* **Autoriser l’envoi des invitations uniquement aux domaines spécifiés** signifie qu’aucun utilisateur en dehors de ces domaines spécifiés ne peut être invité. 

## <a name="govern-access-in-teams"></a>Régir l’accès dans Teams

[Teams différencie les utilisateurs externes (toute personne extérieure à votre organisation) et les utilisateurs invités (ceux disposant de comptes invités)](/microsoftteams/communicate-with-users-from-other-organizations?WT.mc_id=TeamsAdminCenterCSH%e2%80%8b). Vous gérez les paramètres de collaboration dans le [portail d’administration Teams](https://admin.teams.microsoft.com/company-wide-settings/external-communications) sous Paramètres à l’échelle de l’organisation. 

> [!NOTE]
> Les paramètres de collaboration des identités externes dans Azure Active Directory contrôlent les autorisations effectives. Vous pouvez augmenter les restrictions dans Teams, mais vous ne pouvez pas les réduire en-deçà de ce qui est défini dans Azure AD.

* **Paramètres d’accès externe**. Par défaut, Teams autorise l’accès externe, ce qui signifie que l’organisation peut communiquer avec tous les domaines externes. Si vous souhaitez restreindre ou autoriser des domaines spécifiques uniquement pour Teams, vous pouvez le faire ici.

* **Accès invité**. L’accès invité contrôle ce que les utilisateurs invités peuvent faire dans Teams.

Pour en savoir plus sur la gestion de l’accès externe dans Teams, consultez les ressources suivantes.

* [Gérer l’accès externe dans Microsoft Teams](/microsoftteams/manage-external-access)

* [Modèles d’identité Microsoft 365 et Azure Active Directory](/microsoft-365/enterprise/about-microsoft-365-identity)

* [Modèles d’identité et authentification pour Microsoft Teams](/MicrosoftTeams/identify-models-authentication)

* [Étiquettes de confidentialité pour Microsoft Teams](/MicrosoftTeams/sensitivity-labels)

## <a name="govern-access-in-sharepoint-and-onedrive"></a>Régir l’accès dans SharePoint et OneDrive

Les administrateurs SharePoint ont de nombreux paramètres disponibles pour la collaboration. Les paramètres à l’échelle de l’organisation sont gérés à partir du centre d’administration SharePoint. Les paramètres peuvent être ajustés pour chaque site SharePoint. Nous vous recommandons d’utiliser les paramètres de sécurité minimaux de votre organisation et de renforcer la sécurité sur des sites spécifiques en fonction des besoins. Par exemple, pour un projet à haut risque, vous pouvez limiter les utilisateurs à certains domaines et désactiver la capacité des membres à inviter des invités.

### <a name="integrating-sharepoint-and-one-drive-with-azure-ad-b2b"></a>Intégration de SharePoint et OneDrive avec Azure AD B2B

Dans le cadre de votre stratégie globale de gestion de la collaboration externe, nous vous recommandons d’[activer la préversion de l’intégration de SharePoint et de OneDrive avec Azure AD B2B](/sharepoint/sharepoint-azureb2b-integration-preview).

Azure AD B2B assure l’authentification et la gestion des utilisateurs invités. Avec l’intégration de SharePoint et OneDrive, les [codes secrets à usage unique Azure AD B2B](../external-identities/one-time-passcode.md) sont utilisés pour le partage externe de fichiers, dossiers, éléments de liste, bibliothèques de documents et sites. Cette fonctionnalité offre une expérience mise à niveau à partir de l’[expérience de destinataires de partage externe sécurisée](/sharepoint/what-s-new-in-sharing-in-targeted-release).

> [!NOTE]
> Si vous activez la préversion de l’intégration d’Azure AD B2B, le partage SharePoint et OneDrive est soumis aux paramètres de relations organisationnelles Azure AD, tels que **Les membres peuvent inviter** et **Les invités peuvent inviter**.

### <a name="sharing-policies"></a>Stratégies de partage

Le *partage externe* peut être défini pour SharePoint et OneDrive. Les restrictions OneDrive ne peuvent pas être plus permissives que les paramètres SharePoint.

 ![Capture d’écran des paramètres de partage externe dans SharePoint et OneDrive](media/secure-external-access/9-sharepoint-settings.png)

L’intégration de SharePoint à Azure AD B2B modifie la manière dont les contrôles interagissent avec les comptes.

* **Tout le monde**. Non recommandé

   * Quel que soit l’état de l’intégration, l’activation des liens Tout le monde signifie qu’aucune stratégie Azure n’est appliquée lorsque ce type de lien est utilisé. 

   * Dans un scénario de collaboration régie, n’activez pas cette fonctionnalité. 
   > [!NOTE]
   > Si, dans un scénario, vous devez activer ce paramètre pour un site spécifique, vous devez l’activer ici et définir la restriction supérieure sur les sites individuels.

* **Invités nouveaux et existants**. Recommandé si l’intégration est activée.

   * Lorsque **l’intégration d’Azure AD B2B** est activée, les invités nouveaux et existants disposent d’un compte invité Azure AD B2B qui peut être géré avec des stratégies Azure AD.

   * Lorsque l’**intégration d’Azure AD B2B** n’est pas activée, les nouveaux invités n’ont pas de compte Azure AD B2B créé et ne peuvent pas être gérés à partir d’Azure AD. Selon la façon dont les invités existants ont été créés, ils disposent ou non d’un compte Azure AD B2B.

* **Invités existants**. Recommandé si vous n’avez pas activé l’intégration.

   * Lorsque cette option est activée, les utilisateurs peuvent partager uniquement avec d’autres utilisateurs figurant déjà dans votre annuaire.

* **Uniquement les membres de votre organisation**. Non recommandé lorsque vous avez besoin de collaborer avec des utilisateurs externes.

   * Quel que soit l’état de l’intégration, les utilisateurs peuvent uniquement partager du contenu avec les utilisateurs de votre organisation. 

* **Limiter le partage externe par domaine**. Par défaut, SharePoint autorise l’accès externe, ce qui signifie que le partage est autorisé avec tous les domaines externes. Si vous souhaitez restreindre ou autoriser des domaines spécifiques uniquement pour SharePoint, vous pouvez le faire ici.

* **Autoriser uniquement les utilisateurs de groupes de sécurité spécifiques à partager en externe**.  Ce paramètre limite les personnes qui peuvent partager du contenu dans SharePoint et OneDrive, tandis que le paramètre Azure AD s’applique à toutes les applications. La restriction des utilisateurs autorisés à partager peut être utile si vous souhaitez obliger les utilisateurs à suivre une formation sur le partage sécurisé avant de les ajouter à un groupe de sécurité de partage approuvé. Si ce paramètre est sélectionné et que les utilisateurs n’ont pas la possibilité d’accéder à un « partage approuvé », ils peuvent trouver des moyens non approuvés d’effectuer un partage. 

* **Autoriser les invités à partager des éléments qui ne leur appartiennent pas**. Nous vous recommandons de laisser cette option désactivée.

* **Les personnes qui utilisent un code de vérification doivent s’authentifier après ce nombre de jours (30 par défaut)** . Nous vous recommandons d’activer ce paramètre.

### <a name="access-controls"></a>Contrôles d’accès

Le paramètre Contrôles d’accès affecte tous les utilisateurs de votre organisation. Étant donné que vous ne pourrez peut-être pas contrôler si les utilisateurs externes ont des appareils conformes, nous ne parlerons pas de ces contrôles ici. 

* **Déconnexion de session pour cause d’inactivité**. Nous vous recommandons d’activer ce contrôle, ce qui vous permet d’avertir et de déconnecter les utilisateurs sur les appareils non gérés après une certaine période d’inactivité. Vous pouvez configurer la période d’inactivité et l’avertissement. 

* **Emplacement réseau**. La définition de ce contrôle signifie que vous pouvez autoriser uniquement l’accès aux adresses IP de votre organisation. Dans les scénarios de collaboration externe, définissez cette valeur uniquement si tous vos partenaires externes accèdent aux ressources uniquement au sein de votre réseau ou via votre VPN.

### <a name="file-and-folder-links"></a>Liens de fichiers et de dossiers

Dans le centre d’administration SharePoint, vous pouvez également définir le mode de partage des liens de fichiers et de dossiers. Vous pouvez également configurer ces paramètres pour chaque site. 

 ![Capture d’écran des paramètres de lien de fichier et de dossier](media/secure-external-access/9-file-folder-links.png)

Si vous avez activé l’intégration à Azure AD B2B, le partage de fichiers et de dossiers avec les utilisateurs qui se trouvent en dehors de l’organisation entraîne la création d’un utilisateur B2B lorsque des fichiers et dossiers sont partagés.

Nous vous recommandons de définir le type de lien par défaut sur **Uniquement les membres de votre organisation** et les autorisations par défaut sur **Modifier**. Cela permet de s’assurer que les éléments sont partagés intelligemment. Vous pouvez ensuite personnaliser ce paramètre pour la valeur par défaut par site qui répond aux besoins de collaboration spécifiques.

### <a name="anyone-links"></a>Liens Tout le monde

Nous vous déconseillons d’activer des liens Tout le monde. Si vous choisissez de le faire, nous vous recommandons de définir un délai d’expiration et de les restreindre avec des autorisations d’affichage. Si vous choisissez Autorisations d’affichage uniquement pour les fichiers ou les dossiers, les utilisateurs ne sont pas en mesure de modifier les liens pour inclure des privilèges de modification.

Pour en savoir plus sur l’administration de l’accès externe à SharePoint, consultez les rubriques suivantes :

* [Vue d’ensemble du partage externe SharePoint](/sharepoint/external-sharing-overview)

* [Intégration de SharePoint et OneDrive à Azure AD B2B](/sharepoint/sharepoint-azureb2b-integration-preview)

#### <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants sur la sécurisation de l’accès externe aux ressources. Nous vous recommandons d’effectuer les actions dans l’ordre indiqué.

1. [Déterminer votre posture de sécurité pour l’accès externe](1-secure-access-posture.md)

2. [Découvrir votre état actuel](2-secure-access-current-state.md)

3. [Créer un plan de gouvernance](3-secure-access-plan.md)

4. [Utiliser des groupes pour la sécurité](4-secure-access-groups.md)

5. [Transition vers Azure AD B2B](5-secure-access-b2b.md)

6. [Sécuriser l’accès avec la gestion des droits d’utilisation](6-secure-access-entitlement-managment.md)

7. [Sécuriser l’accès avec des stratégies d’Accès conditionnel](7-secure-access-conditional-access.md)

8. [Sécuriser l’accès avec des étiquettes de confidentialité](8-secure-access-sensitivity-labels.md)

9. [Sécuriser l’accès à Microsoft Teams, OneDrive et SharePoint](9-secure-access-teams-sharepoint.md) (il s’agit de l’article en cours)