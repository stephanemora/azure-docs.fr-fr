---
title: Combinée d’inscription pour Azure AD SSPR et MFA (version préliminaire) - Azure Active Directory
description: L’authentification multifacteur Azure AD et le mot de passe libre-service de réinitialisation d’inscription (version préliminaire)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 536d26abf563f18ed7cec6668fcd1d4223f5a135
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370157"
---
# <a name="combined-security-information-registration-preview"></a>Inscription d’informations de sécurité combinée (version préliminaire)

Avant l’inscription combinée, utilisateurs inscrits des méthodes d’authentification pour Azure multi-Factor Authentication (MFA) et de réinitialisation de mot de passe libre-service (SSPR) via deux différentes expériences. Personnes ont été confondus que des méthodes similaires ont été utilisés pour Azure MFA et SSPR, mais ils avaient à inscrire séparément pour chaque fonctionnalité. Désormais, avec l’enregistrement combiné, inscription des utilisateurs qu’une seule fois et bénéficiez des avantages d’Azure MFA et SSPR.

![Affichage de mon profil inscrit les informations de sécurité pour un utilisateur](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

Avant d’activer la nouvelle expérience, consultez cette documentation axée sur l’administrateur et la documentation orienté utilisateur afin de bien que comprendre les fonctionnalités et l’impact de cette fonctionnalité. Baser votre formation sur la documentation utilisateur pour préparer vos utilisateurs pour la nouvelle expérience et de contribuer à assurer un déploiement réussi.

|     |
| --- |
| L’enregistrement d’informations de sécurité combinée pour la réinitialisation du mot de passe libre-service Azure multi-Factor Authentication et Azure AD est une fonctionnalité en préversion publique d’Azure Active Directory. Pour plus d’informations sur les préversions, consultez [Conditions d’utilisation supplémentaires pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

> [!IMPORTANT]
> Si un utilisateur est activé pour la version préliminaire d’origine et l’expérience d’inscription combiné améliorée, ils verront la nouvelle expérience. Les utilisateurs qui sont activés pour les deux expériences ne voient que la nouvelle expérience de mon profil. Le nouveau profil de mon s’aligne sur l’apparence de l’enregistrement combiné et offre une expérience transparente pour les utilisateurs. Les utilisateurs peuvent voir mon profil en accédant à [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com).

Les pages MyProfile sont localisés selon les paramètres de langue en cours sur l’ordinateur d’accéder à la page. Microsoft stocke la langue la plus récente utilisée dans le cache du navigateur afin que les tentatives suivantes pour accéder à continue à afficher dans la dernière langue utilisée. Effacement du cache entraîne les pages à restituer. Si vous souhaitez forcer l’ajout d’un langage spécifique à un `?lng=de-DE` à la fin de l’URL où `de-DE` est définie pour la langue appropriée code force les pages à afficher dans cette langue.

![Le programme d’installation SSPR ou autres méthodes de vérification de sécurité supplémentaire](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-converged-registration"></a>Méthodes disponibles dans l’inscription convergence

À ce stade, l’inscription combinée prend en charge les méthodes suivantes et les actions pour ces méthodes :

|   | Register  | Modifier | Supprimer |
| --- | --- | --- | --- |
| Microsoft Authenticator | Oui (5 max) | Non  | Oui |
| Autres application authenticator | Oui (5 max) | Non  | Oui |
| Jeton matériel | Non  | Non  | Oui |
| Téléphone | Oui | OUI | Oui |
| Autre téléphone | Oui | OUI | Oui |
| Téléphone de bureau | Non  | Non  | Non  |
| Email | Oui | OUI | Oui |
| Questions de sécurité | Oui | Non  | Oui |
| Mots de passe d'application | Oui | Non  | Oui |

> [!NOTE]
> Mots de passe d’application sont disponibles uniquement pour les utilisateurs qui ont été appliqués pour l’authentification Multifacteur. Mots de passe ne sont pas disponibles pour les utilisateurs qui sont activés pour MFA via une stratégie d’accès conditionnel.

Les utilisateurs peuvent définir les options suivantes comme méthode par défaut pour l’authentification Multifacteur :

- Microsoft Authenticator – notification
- Application d’authentification ou de matériel de jeton : code
- appel téléphonique
- SMS

Comme nous continuons d’ajouter ces plusieurs méthodes d’authentification à Azure AD, ces méthodes seront disponibles dans une inscription combinée.

## <a name="combined-registration-modes"></a>Inscription combinée Modes

Il existe deux « modes » de l’enregistrement combiné : interrompre et à gérer.

Mode d’interruption, est une expérience de type Assistant affichée à un utilisateur lorsqu’ils s’inscrire ou actualiser les informations de sécurité lors de la connexion.

Gérer le mode fait partie du profil de l’utilisateur et leur permet de gérer leurs informations de sécurité.

Pour les deux modes, si un utilisateur a déjà été inscrit une méthode qui peut être utilisée pour l’authentification Multifacteur, ils devrez effectuer une MFA avant de pouvoir accéder à leurs informations de sécurité.

### <a name="interrupt-mode"></a>Mode d’interruption

Inscription combinée respecte les stratégies MFA et SSPR, si les deux sont activés pour votre client. Contrôle de ces stratégies, si un utilisateur est interrompu pour inscrire pendant la connexion et quelles méthodes sont disponibles à inscrire.

Ce qui suit répertorie plusieurs scénarios où un utilisateur peut être invité à enregistrer ou actualiser les informations de sécurité :

* Inscription à MFA appliquée via Identity Protection : Les utilisateurs doivent inscrire pendant la connexion. Ils s’inscrivent de méthodes d’authentification Multifacteur et les méthodes SSPR (si l’utilisateur est activé pour SSPR).
* Inscription à MFA appliquée via l’authentification Multifacteur par utilisateur : Les utilisateurs doivent inscrire pendant la connexion. Ils s’inscrivent de méthodes d’authentification Multifacteur et les méthodes SSPR (si l’utilisateur est activé pour SSPR).
* Inscription à MFA appliquée via l’accès conditionnel ou d’autres stratégies : Les utilisateurs sont invités à s’inscrire lorsqu’ils accèdent à une ressource qui exige l’authentification Multifacteur. Les utilisateurs seront inscrire des méthodes d’authentification Multifacteur et les méthodes SSPR (si l’utilisateur est activé pour SSPR).
* Inscription SSPR appliquée : Les utilisateurs sont invités à s’inscrire pendant la connexion. Ils s’inscrivent uniquement les méthodes SSPR
* Actualisation SSPR appliquée : Les utilisateurs doivent vérifier leurs informations de sécurité à la fréquence définie par l’administrateur. Les utilisateurs sont affichés à leurs informations et peuvent choisir « Looks good » ou apporter des modifications si nécessaire.

Si l’inscription a été appliquée, les utilisateurs sont affichés le nombre minimal de méthodes nécessaires pour être conformes aux stratégies MFA et SSPR à partir de la plupart à la moins sécurisée.

Exemple :

* Un utilisateur est activé pour SSPR. La stratégie SSPR requis de deux méthodes pour réinitialiser et a activé les téléphone, e-mail et le code de l’application mobile.
   * Cet utilisateur est requis pour enregistrer les deux méthodes.
      * Par défaut, elles sont affichées Authenticator et téléphone.
      * L’utilisateur peut choisir d’inscrire e-mail au lieu d’application d’authentification ou téléphone.

L’organigramme suivant décrit les méthodes sont affichés à un utilisateur lorsque interrompu pour inscrire pendant la connexion :

![Informations de sécurité combinée organigramme](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

Si vous avez MFA et SSPR est activé, nous vous recommandons d’appliquer l’inscription MFA.

Si la stratégie SSPR nécessite que les utilisateurs de vérifier leurs informations de sécurité à intervalles réguliers, les utilisateurs sont interrompues pendant la connexion et indiqués toutes leurs méthodes inscrites. Ils peuvent choisir « Looks good » si les informations sont à jour, ou ils peuvent choisir « Modifier info » pour apporter des modifications.

### <a name="manage-mode"></a>Gérer le mode

Les utilisateurs peuvent accéder à gérer le mode en accédant à [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo) ou en choisissant « Informations de sécurité » de mon profil. À partir de là, les utilisateurs peuvent ajouter des méthodes, supprimer ou modifier les méthodes existantes, modifier leur méthode par défaut et bien plus encore.

## <a name="key-usage-scenarios"></a>Principaux scénarios d’utilisation

### <a name="set-up-security-info-during-sign-in"></a>Configurer les informations de sécurité pendant la connexion

Un administrateur a mis en application d’inscription.

Un utilisateur n’a pas configuré toutes les informations de sécurité requises et accède au portail Azure. Après avoir entré leur nom d’utilisateur et le mot de passe, l’utilisateur est invité à configurer les informations de sécurité. Ensuite, l’utilisateur suit les étapes indiquées dans l’Assistant pour configurer les informations de sécurité requis. L’utilisateur peut choisir définir des méthodes autres que ce qui est affiché par défaut, si vos paramètres autorisent. À la fin de l’Assistant, l’utilisateur passe en revue les méthodes qu'ils configurent et leur méthode par défaut pour l’authentification Multifacteur. Pour terminer le processus d’installation, l’utilisateur confirme les informations et continue vers le portail Azure.

### <a name="set-up-security-info-from-my-profile"></a>Configurer des informations de sécurité à partir de mon profil

Un administrateur n’a pas a appliqué l’inscription.

Un utilisateur qui n’a pas encore configurée toutes les informations de sécurité requises pour atteindre [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com). L’utilisateur choisit ensuite **des informations de sécurité** dans le volet de navigation gauche. À partir de là, l’utilisateur choisit d’ajouter une méthode sélectionne une des méthodes à leur disposition et suit les étapes pour configurer cette méthode. Lorsque vous avez terminé, l’utilisateur voit la méthode, qu'il suffit de définir sur la page d’informations de sécurité.

### <a name="delete-security-info-from-my-profile"></a>Supprimer les informations de sécurité à partir de mon profil

Un utilisateur qui a créé précédemment au moins une méthode accède à [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo). L’utilisateur choisit de supprimer une des méthodes précédemment enregistrés. Lorsque vous avez terminé, l’utilisateur ne détecte plus cette méthode sur la page d’informations de sécurité.

### <a name="change-default-method-from-my-profile"></a>Modification de la méthode par défaut de mon profil

Un utilisateur qui a créé précédemment au moins une méthode qui peut être utilisée pour l’authentification Multifacteur accède à [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo). L’utilisateur modifie leur méthode par défaut actuelle à une autre méthode par défaut. Lorsque vous avez terminé, l’utilisateur voit sa nouvelle méthode par défaut sur la page d’informations de sécurité.

## <a name="next-steps"></a>Étapes suivantes

[Activer l’inscription de combiné dans votre client](howto-registration-mfa-sspr-combined.md)

[Méthodes disponibles pour l’authentification Multifacteur et SSPR](concept-authentication-methods.md)

[Configurer la réinitialisation du mot de passe libre-service](howto-sspr-deployment.md)

[Configurer l’authentification multifacteur Azure](howto-mfa-getstarted.md)
