---
title: Inscription convergée pour la SSPR et la MFA Azure AD (préversion publique)
description: Authentification multifacteur et inscription à la réinitialisation de mot de passe en libre-service Azure AD (préversion publique)
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry, michmcla
ms.openlocfilehash: d6915ce659d96021d4185be3818919fcfb9d4371
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2018
ms.locfileid: "39492890"
---
# <a name="converged-registration-for-self-service-password-reset-and-azure-multi-factor-authentication-public-preview"></a>Inscription convergée pour la réinitialisation de mot de passe en libre-service et l’authentification multifacteur Azure (préversion publique)

Jusqu’à présent, les utilisateurs devaient inscrire des méthodes d’authentification pour l’authentification multifacteur (MFA) et la réinitialisation de mot de passe en libre-service (SSPR) Azure dans deux portails différents. De nombreux utilisateurs ont été troublés par le fait que des méthodes similaires étaient utilisées pour l’authentification MFA et la réinitialisation SSPR Azure et n’ont pas pu s’inscrire dans les deux portails. Cette disparité a entraîné l’impossibilité d’utiliser l’authentification MFA ou la réinitialisation SSPR Azure lorsque nécessaire pour certains utilisateurs, conduisant à des appels au support technique, ainsi qu’à l’énervement des utilisateurs. Désormais, il suffit aux utilisateurs de s’inscrire une seule fois pour bénéficier des avantages de l’authentification MFA et la réinitialisation SSPR Azure, supprimant la nécessité d’inscrire leurs méthodes d’authentification à deux reprises pour ces fonctionnalités.  

Avant d’activer cette nouvelle expérience pour votre organisation, nous vous recommandons de consulter cet article ainsi que notre [documentation pour les utilisateurs finaux](https://aka.ms/securityinfoguide) pour comprendre l’impact que l’expérience aura sur vos utilisateurs. Vous pouvez utiliser la [documentation pour les utilisateurs finaux](https://aka.ms/securityinfoguide) pour former et préparer vos utilisateurs à la nouvelle expérience et garantir un déploiement réussi.

|     |
| --- |
| L’inscription convergée pour la réinitialisation de mot de passe en libre-service et l’authentification multifacteur Azure est une fonctionnalité en préversion publique d’Azure Active Directory. Pour plus d’informations sur les préversions, consultez [Conditions d’utilisation supplémentaires pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Pour autoriser les utilisateurs à inscrire des méthodes d’authentification pour l’authentification multifacteur Azure et la réinitialisation de mot de passe en libre-service dans une expérience unique, procédez comme suit :

1. Connectez-vous au portail Azure en tant qu’administrateur général ou administrateur d’utilisateurs.
2. Accédez à **Azure Active Directory**, **Paramètres utilisateur**, **Gérer les paramètres des fonctionnalités préliminaires du volet d’accès**.
3. Sous **Les utilisateurs peuvent utiliser les fonctionnalités en préversion pour inscrire et gérer les informations de sécurité**, vous pouvez choisir d’autoriser un groupe **sélectionné** d’utilisateurs ou **Tous** les utilisateurs.

Les utilisateurs peuvent désormais accéder à [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) pour inscrire leurs méthodes d’authentification pour l’authentification MFA et la réinitialisation SSPR. Pour en savoir plus sur ce que vos utilisateurs verront dans cette nouvelle expérience, consultez notre [documentation pour les utilisateurs finaux](https://aka.ms/securityinfoguide).  

> [!NOTE]
> Une fois que vous activez cette expérience, les utilisateurs qui inscrivent ou confirment leur numéro de téléphone ou une application mobile via la nouvelle expérience auront la possibilité de les utiliser pour l’authentification MFA et la réinitialisation SSPR, si ces méthodes sont activées dans les stratégies MFA et SSPR. Si vous désactivez ensuite cette expérience, les utilisateurs qui accèdent à la page d’inscription SSPR précédente à l’adresse aka.ms/ssprsetup doivent effectuer une authentification multifacteur pour accéder à la page.  

## <a name="how-it-works"></a>Fonctionnement

Si un utilisateur a précédemment inscrit des méthodes d’authentification via les expériences d’inscription MFA et SSPR distinctes, il n’a pas besoin d’inscrire à nouveau ces informations. Toutefois, si vos paramètres exigent que les utilisateurs s’inscrivent pour l’authentification MFA ou la réinitialisation SSPR, une invite peut s’afficher pour passer en revue les informations de sécurité lors de la connexion.

Si un utilisateur a inscrit une méthode qui peut être utilisée pour l’authentification MFA, il sera invité à effectuer une authentification multifacteur avant de pouvoir accéder à la nouvelle expérience.

Si vous avez mis en place l’inscription forcée pour l’authentification MFA ou la réinitialisation SSPR, et qu’un utilisateur n’est pas encore inscrit, il sera invité à s’inscrire lorsqu’il se connectera.

Les utilisateurs invités à s’inscrire lors de la connexion verront l’expérience suivante :

![Inscription convergée. Configurer des méthodes en tant que nouvel utilisateur](./media/concept-registration-mfa-sspr-converged/concept-registration-add-methods.png)

> [!NOTE]
> Cette expérience s’affiche uniquement lorsqu’un utilisateur est invité à s’inscrire lors de la connexion. Les utilisateurs qui accèdent directement à la nouvelle expérience à l’adresse aka.ms/setupsecurityinfo voient une version différente de l’expérience, qui est décrite plus loin dans cet article.

Les méthodes d’authentification affichées changent selon les méthodes activées dans vos stratégies MFA ou SSPR. L’utilisateur est invité à inscrire le nombre minimal de méthodes d’authentification nécessaires pour être conforme à la stratégie MFA, à la stratégie SSPR, ou aux deux. S’il existe une certaine flexibilité par rapport aux méthodes d’authentification que les utilisateurs peuvent inscrire, ils peuvent sélectionner **Choisir les informations de sécurité** pour choisir d’autres méthodes d’authentification.  

Contrairement à l’expérience d’inscription MFA précédente, les utilisateurs ne seront pas invités à inscrire un mot de passe d’application durant la nouvelle expérience d’inscription. Au lieu de cela, ils doivent suivre les étapes répertoriées dans notre tutoriel de mots de passe d’applications pour inscrire les mots de passe dans la nouvelle expérience.  

Une fois qu’un utilisateur a terminé l’inscription, sa méthode d’authentification MFA par défaut sera automatiquement configurée. Si l’utilisateur inscrit une application d’authentification, la méthode par défaut est définie sur « Application ». Si l’utilisateur n’a pas inscrit d’application d’authentification et n’a inscrit que son numéro de téléphone, la méthode par défaut est définie sur « Appel téléphonique ». Les utilisateurs peuvent modifier les valeurs par défaut en accédant à [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) et en sélectionnant **Modifier les valeurs par défaut**.  

Si l’inscription n’est pas appliquée, les utilisateurs peuvent gérer leurs propres méthodes d’authentification à l’adresse [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo). Si un utilisateur a précédemment inscrit une méthode qui peut être utilisée pour effectuer une authentification multifacteur, il sera invité à effectuer une authentification MFA avant de pouvoir accéder à la nouvelle expérience.  

![Inscription convergée. Modifier des méthodes en tant qu’un utilisateur inscrit](./media/concept-registration-mfa-sspr-converged/concept-registration-edit-methods.png)

Sur cette page, les utilisateurs peuvent voir les méthodes d’authentification précédemment inscrites et les méthodes d’authentification inscrites pour eux telles que « Téléphone de bureau ». Les utilisateurs peuvent également ajouter, modifier ou supprimer leurs méthodes d’authentification (sauf « Téléphone de bureau »).  

Les journaux d’audit de cette nouvelle expérience existent sous la catégorie Méthodes d’authentification du journal d’audit.  

## <a name="known-issues"></a>Problèmes connus

**La méthode MFA par défaut est définie sur « Appel téléphonique » lorsque l’utilisateur a inscrit le téléphone à l’aide d’un SMS**

   * Certains utilisateurs peuvent remarquer que leur méthode MFA par défaut est définie sur « Appel téléphonique » une fois qu’ils ont inscrit leur numéro de téléphone à l’aide d’un SMS. Les utilisateurs peuvent résoudre ce problème en modifiant leur méthode par défaut. Pour cela, ils peuvent suivre les instructions figurant dans l’article [Gérer vos informations de sécurité (préversion)](../user-help/security-info-manage-settings.md#change-your-info).

**Un utilisateur ne peut pas accéder à la nouvelle expérience d’inscription après la désactivation de sa méthode par défaut par l’administrateur**

   * Certains utilisateurs ne peuvent pas accéder à la nouvelle expérience d’inscription si leur méthode MFA par défaut précédemment inscrite a été désactivée par leur administrateur. Voici un exemple de scénario :
      1. L’utilisateur a précédemment inscrit son numéro de téléphone et configuré sa méthode par défaut sur « Appel téléphonique ».
      2. L’administrateur désactive la méthode MFA «Appel téléphonique » pour le locataire.
      3. L’utilisateur est invité à s’inscrire lors de la connexion, car il doit inscrire une méthode supplémentaire pour respecter la stratégie SSPR du locataire.
      4. L’utilisateur tente de s’inscrire, mais ne peut pas accéder à la page car il n’a pas de méthode par défaut définie et est bloqué dans une boucle.

## <a name="next-steps"></a>Étapes suivantes

[Découvrir comment déployer la réinitialisation de mot de passe en libre-service Azure AD](howto-sspr-deployment.md)

[Découvrir comment exiger l’authentification multifacteur lors de la connexion](howto-mfa-getstarted.md)

[Documentation relative à la configuration de la méthode d’authentification de l’utilisateur final](https://aka.ms/securityinfoguide)