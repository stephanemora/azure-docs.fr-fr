---
title: Combinée d’inscription pour Azure AD SSPR et une authentification multifacteur (version préliminaire) - Azure Active Directory
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
ms.openlocfilehash: 7cf8d5cb13b39d58920555ff9d99a4949e1bfc20
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/12/2019
ms.locfileid: "59521431"
---
# <a name="combined-security-information-registration-preview"></a>Inscription d’informations de sécurité combinée (version préliminaire)

Avant l’inscription combinée, utilisateurs inscrits des méthodes d’authentification pour Azure multi-Factor Authentication et de réinitialisation de mot de passe libre-service (SSPR) séparément. Personnes ont été confondus que des méthodes similaires ont été utilisés pour l’authentification multifacteur et SSPR, mais ils devaient s’inscrire pour ces deux fonctionnalités. Désormais, avec l’enregistrement combiné, inscription des utilisateurs qu’une seule fois et bénéficiez des avantages de l’authentification multifacteur et SSPR.

![Affichage de mon profil inscrit les informations de sécurité pour un utilisateur](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

Avant d’activer la nouvelle expérience, consultez cette documentation axée sur l’administrateur et la documentation orienté utilisateur afin de bien que comprendre les fonctionnalités et les effets de cette fonctionnalité. Baser votre formation sur la documentation utilisateur pour préparer vos utilisateurs pour la nouvelle expérience et de contribuer à assurer un déploiement réussi.

Azure AD associé à l’inscription n’est pas disponible actuellement sur des clouds nationaux tels que Azure US Government, Azure Allemagne ou Azure China 21Vianet des informations de sécurité.

|     |
| --- |
| L’inscription d’informations de sécurité combinée pour l’authentification multifacteur et de réinitialisation de mot de passe libre-service d’Azure Active Directory (Azure AD) est une fonctionnalité en préversion publique d’Azure AD. Pour plus d’informations sur les préversions, consultez [Conditions d’utilisation supplémentaires pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

> [!IMPORTANT]
> Les utilisateurs qui sont activées pour la version préliminaire d’origine et l’expérience d’inscription combiné améliorée voient le nouveau comportement. Les utilisateurs qui sont activés pour les deux expériences verront la nouvelle expérience de mon profil. Le nouveau profil de mon s’aligne sur l’apparence de l’enregistrement combiné et offre une expérience transparente pour les utilisateurs. Les utilisateurs peuvent voir mon profil en accédant à [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com).

Mes pages de profil sont localisés en fonction des paramètres de langue de l’ordinateur d’accéder à la page. Microsoft stocke la langue la plus récente utilisée dans le cache du navigateur, afin que les tentatives suivantes pour accéder aux pages continue à afficher dans la dernière langue utilisée. Si vous effacez le cache, les pages seront réaffiché. Si vous souhaitez forcer une langue spécifique, vous pouvez ajouter `?lng=<language>` à la fin de l’URL, où `<language>` est le code de la langue que vous souhaitez restituer.

![Configurer SSPR ou d’autres méthodes de vérification de sécurité](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>Méthodes disponibles dans l’enregistrement combiné

Combiné prend en charge l’inscription méthodes d’authentification suivantes et les actions :

|   | Register  | Modifier | Supprimer |
| --- | --- | --- | --- |
| Microsoft Authenticator | Oui (jusqu'à 5) | Non  | Oui |
| Autres application authenticator | Oui (jusqu'à 5) | Non  | Oui |
| Jeton matériel | Non  | Non  | Oui |
| Téléphone | Oui | OUI | Oui |
| Autre téléphone | Oui | OUI | Oui |
| Téléphone de bureau | Non  | Non  | Non  |
| Email | Oui | OUI | Oui |
| Questions de sécurité | Oui | Non  | Oui |
| Mots de passe d'application | Oui | Non  | Oui |

> [!NOTE]
> Mots de passe d’application sont disponibles uniquement pour les utilisateurs qui ont été appliquées pour l’authentification multifacteur. Mots de passe ne sont pas disponibles pour les utilisateurs qui sont activés pour l’authentification multifacteur via une stratégie d’accès conditionnel.

Les utilisateurs peuvent définir une des options suivantes en tant que la méthode d’authentification multifacteur par défaut :

- Microsoft Authenticator – notification.
- Application d’authentification ou de matériel de jeton : le code.
- Appel téléphonique.
- message texte.

Comme nous continuons d’ajouter plusieurs méthodes d’authentification à Azure AD, ces méthodes seront disponibles dans une inscription combinée.

## <a name="combined-registration-modes"></a>Modes d’enregistrement combiné

Il existe deux modes d’enregistrement combiné : interrompre et à gérer.

- **Mode d’interruption** est une expérience de type Assistant, présentée aux utilisateurs lorsqu’ils s’inscrire ou actualiser les informations de sécurité à la connexion.

- **Gérer le mode** fait partie du profil utilisateur et permet aux utilisateurs de gérer leurs informations de sécurité.

Pour les deux modes, les utilisateurs qui se sont inscrits précédemment une méthode qui peut être utilisée pour l’authentification multifacteur devrez effectuer une authentification multifacteur avant de pouvoir accéder à leurs informations de sécurité.

### <a name="interrupt-mode"></a>Mode d’interruption

Inscription combinée respecte les stratégies multi-Factor Authentication et SSPR, si les deux sont activés pour votre client. Ces stratégies contrôlent si un utilisateur est interrompu pour l’inscription pendant la connexion et quelles méthodes sont disponibles pour l’inscription.

Voici plusieurs scénarios dans lesquels les utilisateurs peuvent être invités à enregistrer ou actualiser les informations de sécurité :

* Inscription à l’authentification multifacteur appliquée via Identity Protection : Les utilisateurs sont invités à s’inscrire pendant la connexion. Ils s’inscrivent de méthodes d’authentification multifacteur et SSPR (si l’utilisateur est activé pour SSPR).
* Inscription à l’authentification multifacteur appliquée via l’authentification multifacteur par utilisateur : Les utilisateurs sont invités à s’inscrire pendant la connexion. Ils s’inscrivent de méthodes d’authentification multifacteur et SSPR (si l’utilisateur est activé pour SSPR).
* Inscription à l’authentification multifacteur appliquée via l’accès conditionnel ou d’autres stratégies : Les utilisateurs sont invités à s’inscrire lorsqu’ils utilisent une ressource qui nécessite une authentification multifacteur. Ils s’inscrivent de méthodes d’authentification multifacteur et SSPR (si l’utilisateur est activé pour SSPR).
* Inscription SSPR appliquée : Les utilisateurs sont invités à s’inscrire pendant la connexion. Ils s’inscrivent uniquement les méthodes SSPR.
* Actualisation SSPR appliquée : Les utilisateurs doivent vérifier leurs informations de sécurité à la fréquence définie par l’administrateur. Les utilisateurs sont affichés à leurs informations et peuvent confirmer les informations actuelles ou apporter des modifications si nécessaire.

Si l’inscription a été appliquée, les utilisateurs sont affichés le nombre minimal de méthodes nécessaires pour être conformes aux stratégies de l’authentification multifacteur et SSPR, à partir de la plupart à la moins sécurisée.

Par exemple : 

* Un utilisateur est activé pour SSPR. La stratégie SSPR requis de deux méthodes pour réinitialiser et a activé les téléphone, e-mail et le code de l’application mobile.
   * Cet utilisateur est requis pour enregistrer les deux méthodes.
      * L’utilisateur voit Authenticator et téléphone par défaut.
      * L’utilisateur peut choisir d’inscrire e-mail au lieu d’application d’authentification ou téléphone.

Cet organigramme décrit quelles méthodes sont affichés à un utilisateur lorsque interrompu pour inscrire pendant la connexion :

![Organigramme des informations de sécurité combinée](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

Si vous avez une authentification multifacteur et SSPR est activé, nous vous recommandons d’appliquer l’inscription de l’authentification multifacteur.

Si la stratégie SSPR nécessite que les utilisateurs de vérifier leurs informations de sécurité à intervalles réguliers, les utilisateurs sont interrompues pendant la connexion et indiqués toutes leurs méthodes inscrites. Ils peuvent confirmer les informations actuelles s’il est à jour, ou ils peuvent apporter des modifications si nécessaire.

### <a name="manage-mode"></a>Gérer le mode

Les utilisateurs peuvent accéder à gérer le mode en accédant à [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo) ou en sélectionnant **des informations de sécurité** à partir de mon profil. À partir de là, les utilisateurs peuvent ajouter des méthodes, supprimer ou modifier les méthodes existantes, modifier la méthode par défaut et bien plus encore.

## <a name="key-usage-scenarios"></a>Principaux scénarios d’utilisation

### <a name="set-up-security-info-during-sign-in"></a>Configurer les informations de sécurité pendant la connexion

Un administrateur a mis en application d’inscription.

Un utilisateur n’a pas configuré toutes les informations de sécurité requises et accède au portail Azure. Après avoir entré le nom d’utilisateur et le mot de passe, l’utilisateur est invité à configurer les informations de sécurité. Ensuite, l’utilisateur suit les étapes indiquées dans l’Assistant pour configurer les informations de sécurité requis. Si vos paramètres permettent, l’utilisateur peut choisir définir des méthodes autres que celles affichées par défaut. À l’issue de l’Assistant, les utilisateurs passez en revue les méthodes qu'ils configurent et leur méthode par défaut pour l’authentification multifacteur. Pour terminer le processus d’installation, l’utilisateur confirme les informations et continue vers le portail Azure.

### <a name="set-up-security-info-from-my-profile"></a>Configurer des informations de sécurité à partir de mon profil

Un administrateur n’a pas a appliqué l’inscription.

Un utilisateur qui n’a pas encore configuré de toutes les informations de sécurité requis se [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com). L’utilisateur sélectionne **des informations de sécurité** dans le volet gauche. À partir de là, l’utilisateur choisit d’ajouter une méthode, sélectionne une des méthodes disponibles et suit les étapes pour configurer cette méthode. Lorsque vous avez terminé, l’utilisateur voit la méthode que vous venez de paramétrer sur la page d’informations de sécurité.

### <a name="delete-security-info-from-my-profile"></a>Supprimer les informations de sécurité à partir de mon profil

Un utilisateur qui a créé précédemment au moins une méthode accède à [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo). L’utilisateur choisit de supprimer une des méthodes précédemment enregistrés. Lorsque vous avez terminé, l’utilisateur ne détecte plus cette méthode sur la page d’informations de sécurité.

### <a name="change-the-default-method-from-my-profile"></a>Modifier la méthode par défaut de mon profil

Un utilisateur qui a créé précédemment au moins une méthode qui peut être utilisée pour l’authentification multifacteur accède à [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo). L’utilisateur modifie la méthode par défaut actuelle à une autre méthode par défaut. Lorsque vous avez terminé, l’utilisateur voit la nouvelle méthode par défaut sur la page d’informations de sécurité.

## <a name="next-steps"></a>Étapes suivantes

[Activer l’inscription de combiné dans votre client](howto-registration-mfa-sspr-combined.md)

[Méthodes disponibles pour l’authentification multifacteur et SSPR](concept-authentication-methods.md)

[Configurer la réinitialisation du mot de passe libre-service](howto-sspr-deployment.md)

[Configurer l’authentification multifacteur Azure](howto-mfa-getstarted.md)
