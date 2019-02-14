---
title: Inscription convergée pour la SSPR et la MFA Azure AD (préversion publique)
description: Authentification multifacteur et inscription à la réinitialisation de mot de passe en libre-service Azure AD (préversion publique)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66d4dc7e6fc1545e1de1e3b50887585c4156b4c0
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56208778"
---
# <a name="converged-registration-for-self-service-password-reset-and-azure-multi-factor-authentication-public-preview"></a>Inscription convergée pour la réinitialisation de mot de passe en libre-service et l’authentification multifacteur Azure (préversion publique)

Jusqu’à présent, les utilisateurs devaient inscrire des méthodes d’authentification pour l’authentification multifacteur (MFA) et la réinitialisation de mot de passe en libre-service (SSPR) Azure dans deux portails différents. De nombreux utilisateurs ont été troublés par le fait que des méthodes similaires servaient à la fois pour l’authentification MFA et la réinitialisation SSPR Azure, et ils n’ont pas pu s’inscrire sur ces deux portails. Certains utilisateurs ont ensuite été dans l’impossibilité d’utiliser Azure MFA ou SSPR lorsque nécessaire, ce qui les a amenés à appeler le support technique. 

Désormais, les utilisateurs peuvent inscrire une seule fois et bénéficier des avantages d’Azure MFA et SSPR. Ils n’ont pas besoin d’inscrire à deux reprises leurs méthodes d’authentification pour ces fonctionnalités.  

Avant d’activer cette nouvelle expérience pour votre organisation, nous vous recommandons de consulter cet article ainsi que notre [documentation pour les utilisateurs](https://aka.ms/securityinfoguide) afin de comprendre son éventuel impact. Vous pouvez utiliser la documentation pour les utilisateurs dans le but de les former, de les préparer à cette nouvelle expérience et de garantir un déploiement réussi.

|     |
| --- |
| L’inscription convergée pour la réinitialisation de mot de passe en libre-service et l’authentification multifacteur Azure est une fonctionnalité d’évaluation publique d’Azure Active Directory (Azure AD). Pour plus d’informations sur les préversions, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Pour autoriser les utilisateurs à inscrire des méthodes d’authentification pour l’authentification multifacteur Azure et la réinitialisation de mot de passe en libre-service dans une expérience unique, procédez comme suit :

1. Connectez-vous au portail Azure en tant qu’administrateur général ou administrateur d’utilisateurs.
2. Accédez à **Azure Active Directory** > **Paramètres utilisateur** > **Gérer les paramètres des fonctionnalités préliminaires du volet d’accès**.
3. Sous **Les utilisateurs peuvent utiliser les fonctionnalités en préversion pour inscrire et gérer les informations de sécurité**, vous pouvez choisir d’autoriser un groupe **sélectionné** d’utilisateurs ou **Tous** les utilisateurs.

Les utilisateurs peuvent désormais accéder à [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) pour inscrire leurs méthodes d’authentification pour l’authentification MFA et la réinitialisation SSPR. Pour en savoir plus sur ce que vos utilisateurs verront dans cette nouvelle expérience, consultez notre [documentation pour les utilisateurs](https://aka.ms/securityinfoguide).  

> [!NOTE]
> Une fois que vous activez cette expérience, les utilisateurs qui inscrivent ou confirment leur numéro de téléphone ou une application mobile via celle-ci ont la possibilité d’utiliser aussi bien le numéro que l’application pour l’authentification MFA et la réinitialisation SSPR, si ces méthodes sont activées dans les stratégies MFA et SSPR. Si vous désactivez cette expérience par la suite, les utilisateurs qui accèdent à la précédente page d’inscription SSPR à l’adresse https:/aka.ms/ssprsetup doivent effectuer une authentification multifacteur.  

## <a name="how-it-works"></a>Fonctionnement

Si un utilisateur a précédemment inscrit des méthodes d’authentification via les expériences d’inscription MFA et SSPR, il n’a pas besoin d’inscrire à nouveau ces informations. Néanmoins, si vos paramètres exigent que les utilisateurs s’inscrivent pour l’authentification MFA ou la réinitialisation SSPR, une invite peut s’afficher au moment de la connexion leur demandant de passer en revue les informations de sécurité.

Si un utilisateur a inscrit une méthode qui peut être utilisée pour l’authentification MFA, il est invité à effectuer une authentification multifacteur avant de pouvoir accéder à la nouvelle expérience.

Si vous avez mis en place l’inscription forcée pour l’authentification MFA ou la réinitialisation SSPR, et qu’un utilisateur n’est pas encore inscrit, il est invité à s’inscrire au moment où il se connecte.

Les utilisateurs invités à s’inscrire lors de la connexion accèdent à l’expérience suivante :

![Inscription convergée. Configurer des méthodes en tant que nouvel utilisateur.](./media/concept-registration-mfa-sspr-converged/concept-registration-add-methods.png)

> [!NOTE]
> Cette expérience s’affiche uniquement lorsqu’un utilisateur est invité à s’inscrire au moment de la connexion. Les utilisateurs qui accèdent directement à la nouvelle expérience à l’adresse https://aka.ms/setupsecurityinfo voient une version différente de l’expérience, décrite plus loin dans cet article.

Les méthodes d’authentification affichées changent selon les méthodes activées dans vos stratégies MFA ou SSPR. L’utilisateur est invité à inscrire le nombre minimal de méthodes d’authentification nécessaires pour se conformer à la stratégie MFA et/ou à la stratégie SSPR. S’il existe une certaine flexibilité par rapport aux méthodes d’authentification que les utilisateurs peuvent inscrire, ils peuvent sélectionner **Choisir les informations de sécurité** pour choisir d’autres méthodes d’authentification.  

> [!NOTE]
> Si vous activez l’utilisation de la notification d’application mobile et du code d’application mobile, les utilisateurs qui inscrivent l’application Microsoft Authenticator via une notification peuvent utiliser la notification et le code pour valider leur identité.

Contrairement à l’expérience d’inscription MFA précédente, les utilisateurs ne sont pas invités à inscrire un mot de passe d’application durant la nouvelle expérience d’inscription. Au lieu de cela, ils doivent suivre les étapes répertoriées dans le tutoriel des mots de passe d’application pour inscrire les mots de passe dans la nouvelle expérience.  

Une fois qu’un utilisateur a terminé l’inscription, sa méthode d’authentification MFA par défaut est automatiquement configurée. Si l’utilisateur inscrit une application d’authentification, la méthode par défaut est définie sur « Application ». Si l’utilisateur n’a pas inscrit d’application d’authentification et n’a inscrit que son numéro de téléphone, la méthode par défaut est définie sur « Appel téléphonique ». Les utilisateurs peuvent modifier les valeurs par défaut en accédant à https://aka.ms/setupsecurityinfo et en sélectionnant **Modifier les valeurs par défaut**.  

Si l’inscription n’est pas appliquée, les utilisateurs peuvent gérer leurs propres méthodes d’authentification à l’adresse https://aka.ms/setupsecurityinfo. Si un utilisateur a inscrit au préalable une méthode qui peut être utilisée pour l’authentification MFA, il est invité à effectuer une authentification multifacteur avant de pouvoir accéder à cette page.  

![Inscription convergée. Modifier des méthodes en tant qu’utilisateur inscrit.](./media/concept-registration-mfa-sspr-converged/concept-registration-edit-methods.png)

Sur cette page, les utilisateurs peuvent voir les méthodes d’authentification précédemment inscrites et les méthodes d’authentification inscrites pour eux telles que « Téléphone de bureau ». Les utilisateurs peuvent également ajouter, modifier ou supprimer leurs méthodes d’authentification (sauf « Téléphone de bureau »).  

Les journaux d’audit de cette nouvelle expérience existent sous la catégorie Méthodes d’authentification du journal d’audit.  

## <a name="known-issues"></a>Problèmes connus

**La méthode MFA par défaut est définie sur « Appel téléphonique » lorsqu’un utilisateur a inscrit un numéro de téléphone à l’aide d’un SMS**

   * Certains utilisateurs constatent parfois que leur méthode MFA par défaut est définie sur « Appel téléphonique » une fois qu’ils ont inscrit leur numéro de téléphone à l’aide d’un SMS. Ils peuvent résoudre ce problème en modifiant la méthode par défaut. Pour cela, nous leur conseillons de suivre les instructions figurant dans l’article [Gérer vos informations de sécurité (préversion)](../user-help/security-info-manage-settings.md#change-your-info).

**Un utilisateur ne peut pas accéder à la nouvelle expérience d’inscription après la désactivation de sa méthode par défaut par un administrateur**

   * Certains utilisateurs ne peuvent parfois pas accéder à la nouvelle expérience d’inscription si la méthode MFA par défaut précédemment inscrite a été désactivée par leur administrateur. Voici un exemple de scénario :
      1. L’utilisateur a précédemment inscrit son numéro de téléphone et configuré sa méthode par défaut sur « Appel téléphonique ».
      2. L’administrateur désactive la méthode MFA «Appel téléphonique » pour le locataire.
      3. L’utilisateur est invité à s’inscrire lors de la connexion, car il doit inscrire une méthode supplémentaire pour respecter la stratégie SSPR du locataire.
      4. L’utilisateur tente de s’inscrire mais ne peut pas accéder à la page, car il n’a pas défini de méthode par défaut et il est bloqué dans une boucle.

## <a name="next-steps"></a>Étapes suivantes

[Découvrir comment déployer la réinitialisation de mot de passe en libre-service Azure AD](howto-sspr-deployment.md)

[Comment exiger l’authentification multifacteur pour la connexion](howto-mfa-getstarted.md)

[Comment configurer les méthodes d’authentification utilisateur](https://aka.ms/securityinfoguide)
