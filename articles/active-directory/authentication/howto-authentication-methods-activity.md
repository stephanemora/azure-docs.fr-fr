---
title: Activité des méthodes d'authentification - Azure Active Directory
description: Présentation des méthodes d'authentification inscrites par les utilisateurs pour se connecter et réinitialiser leur mot de passe.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/04/2021
ms.author: justinha
author: sopand
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0865fb2bda04f5a7e9ba2ef73a717946fa656a5
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175309"
---
# <a name="authentication-methods-activity"></a>Activité des méthodes d’authentification 

Le nouveau tableau de bord Activité des méthodes d'authentification permet aux administrateurs de surveiller l'inscription et l'utilisation des méthodes d'authentification au sein de leur organisation. Cette fonctionnalité de création de rapports permet à votre organisation d'identifier les méthodes inscrites et d'en savoir plus sur leur utilisation.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="permissions-and-licenses"></a>Autorisations et licences

Les rôles intégrés et personnalisés avec les autorisations suivantes peuvent accéder au panneau Activité des méthodes d’authentification et aux API :

- Microsoft.directory/auditLogs/allProperties/read
- Microsoft.directory/signInReports/allProperties/read

Les rôles suivants disposent des autorisations requises :

- Lecteur de rapports
- Lecteur de sécurité
- Lecteur général
- Opérateur de sécurité
- Security Administrator
- Administrateur général

 Une licence Azure AD Premium P1 ou P2 est nécessaire pour accéder à l’utilisation et aux informations. Les informations de licence relatives à Azure AD Multi-Factor Authentication et à la réinitialisation de mot de passe en libre-service sont disponibles sur le [Site de tarification d'Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="how-it-works"></a>Fonctionnement

Pour accéder aux informations et à l’utilisation de la méthode d’authentification :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Cliquez sur **Azure Active Directory** > **Sécurité** > **Méthodes d'authentification** > **Activité**.
1. Le rapport contient deux onglets : **Inscription** et **Utilisation**.

   ![Vue d'ensemble de l'activité des méthodes d'authentification](media/how-to-authentication-methods-usage-insights/registration-usage-tabs.png)

## <a name="registration-details"></a>détails de l’inscription

Vous pouvez accéder à l'[**onglet Inscription**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade) pour consulter le nombre d'utilisateurs prenant en charge l'authentification multifacteur, l'authentification sans mot de passe et la réinitialisation de mot de passe en libre-service. 

Cliquez sur l'une des options suivantes pour pré-filtrer la liste des détails d'inscription des utilisateurs :

- **Utilisateurs prenant en charge l'authentification multifacteur** présente la répartition des utilisateurs qui sont à la fois :
  - inscrits pour une méthode d'authentification forte ; 
  - activés par stratégie afin d'utiliser cette méthode pour l'authentification multifacteur. 
  
  Ce nombre ne comprend pas les utilisateurs inscrits pour l'authentification multifacteur en dehors d'Azure AD. 
- **Utilisateurs prenant en charge l'authentification sans mot de passe** présente la répartition des utilisateurs qui sont inscrits pour se connecter sans mot de passe via FIDO2, Windows Hello Entreprise ou la connexion par téléphone sans mot de passe avec l'application Microsoft Authenticator. 
- **Utilisateurs prenant en charge la réinitialisation de mot de passe en libre-service** présente la répartition des utilisateurs qui peuvent réinitialiser leur mot de passe. Les utilisateurs peuvent réinitialiser leur mot de passe s'ils sont à la fois :
  - inscrits à suffisamment de méthodes pour satisfaire la stratégie de leur organisation en matière de réinitialisation de mot de passe en libre-service ; 
  - activés pour réinitialiser leur mot de passe. 

  ![Capture d'écran des utilisateurs qui peuvent s'inscrire](media/how-to-authentication-methods-usage-insights/users-capable.png)

**Utilisateurs inscrits par méthode d'authentification** présente le nombre d'utilisateurs inscrits pour chaque méthode d'authentification. Cliquez sur une méthode d'authentification pour voir qui est inscrit à celle-ci.

![Capture d'écran des Utilisateurs inscrits](media/how-to-authentication-methods-usage-insights/users-registered.png)

**Inscription récente par méthode d'authentification** présente le nombre d'inscriptions qui ont réussi et échoué, classées par méthode d'authentification. Cliquez sur une méthode d'authentification pour afficher les événements d'inscription récents correspondant à celle-ci.

![Capture d'écran des Inscriptions récentes](media/how-to-authentication-methods-usage-insights/recently-registered.png)

## <a name="usage-details"></a>Usage details

Le rapport **Utilisation** présente les méthodes d'authentification utilisées pour se connecter et réinitialiser les mots de passe.

![Capture d'écran de la page Utilisation](media/how-to-authentication-methods-usage-insights/usage-page.png)

**Connexions par exigence d'authentification** présente le nombre de connexions utilisateur interactives réussies qui étaient requises pour l'authentification à facteur unique par rapport à l'authentification multifacteur dans Azure AD. Les connexions pour lesquelles l'authentification multifacteur a été appliquée par un fournisseur MFA tiers ne sont pas incluses.

![Capture d'écran des connexions par exigence d'authentification](media/how-to-authentication-methods-usage-insights/sign-ins-protected.png)

**Connexions par méthode d'authentification** présente le nombre de connexions utilisateur interactives (ayant réussi ou échoué) par méthode d'authentification utilisée. Cette option n'inclut pas les connexions pour lesquelles l'exigence d'authentification a été satisfaite par une revendication dans le jeton.

![Capture d'écran des connexions par méthode](media/how-to-authentication-methods-usage-insights/sign-ins-by-method.png)

**Nombre de réinitialisations de mot de passe et de déverrouillages de compte** présente le nombre de modifications et de réinitialisations (en libre-service et par l'administrateur) réussies du mot de passe au fil du temps.

![Capture d'écran des réinitialisations et des déverrouillages](media/how-to-authentication-methods-usage-insights/password-changes.png)

**Réinitialisations de mot de passe par méthode d'authentification** présente le nombre d'authentifications ayant réussi et échoué pendant le flux de réinitialisation de mot de passe par méthode d'authentification.

![Capture d’écran des Réinitialisations par méthode](media/how-to-authentication-methods-usage-insights/resets-by-method.png)

## <a name="user-registration-details"></a>Détails d'inscription des utilisateurs 

En utilisant les contrôles en haut de la liste, vous pouvez rechercher un utilisateur et filtrer la liste des utilisateurs en fonction des colonnes affichées.

Le rapport détaillé de l’inscription affiche les informations suivantes pour chaque utilisateur :

- Nom d’utilisateur principal
- Nom
- Prise en charge de l'authentification multifacteur (Oui, Non)
- Prise en charge de l'authentification sans mot de passe (Oui, Non)
- Inscription à la réinitialisation de mot de passe en libre-service (Oui, Non)
- Activation de la réinitialisation de mot de passe en libre-service (Oui, Non)
- Prise en charge de la réinitialisation de mot de passe en libre-service (Oui, Non) 
- Méthodes inscrites (E-mail, Téléphone mobile, Autre téléphone mobile, Téléphone de bureau, Microsoft Authenticator Push, Code secret à usage unique, FIDO2, Clé de sécurité, Questions de sécurité)

  ![Capture d'écran des détails d'inscription des utilisateurs](media/how-to-authentication-methods-usage-insights/registration-details.png)

## <a name="registration-and-reset-events"></a>Événements d'inscription et de réinitialisation 

**Événements d'inscription et de réinitialisation** présente les événements d'inscription et de réinitialisation des 24 dernières heures, des 7 derniers jours ou des 30 derniers jours, notamment :

- Date
- Nom d'utilisateur
- Utilisateur 
- Fonctionnalité (Inscription, Réinitialisation)
- Méthodes utilisées (Notification de l'application, Code de l'application, Appel téléphonique, Appel au bureau, Autre téléphone mobile, SMS, E-mail, Questions de sécurité)
- État (Réussite, Échec)
- Motif de l'échec (explication)

  ![Capture d’écran des événements d’inscription et de réinitialisation](media/how-to-authentication-methods-usage-insights/registration-and-reset-logs.png)

## <a name="limitations"></a>Limites

- Les données du rapport ne sont pas mises à jour en temps réel et peuvent refléter une latence allant jusqu’à quelques heures.
- Les inscriptions au Passe d’accès temporaire ne figurent pas dans l’onglet Inscription du rapport, car elles ne sont valables que pour une courte période.

## <a name="next-steps"></a>Étapes suivantes

- [Utilisation de l’API de rapport d’utilisation des méthodes d’authentification](/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [Choix des méthodes d’authentification pour votre organisation](concept-authentication-methods.md)
- [Expérience d’inscription combinée](concept-registration-mfa-sspr-combined.md)
