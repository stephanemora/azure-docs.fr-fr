---
title: Comment configurer la réécriture du mot de passe pour Azure AD SSPR
description: Utilisation d’Azure AD et d’Azure AD Connect pour l’écriture différée des mots de passe dans un annuaire local
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: e613ff742096077fe1765d4b855b6c7d409cc228
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/19/2018
ms.locfileid: "39158945"
---
# <a name="how-to-configure-password-writeback"></a>Comment : configurer la réécriture du mot de passe

Nous vous recommandons d’utiliser la fonctionnalité de mise à jour d’[Azure AD Connect](./../connect/active-directory-aadconnect-get-started-express.md) lors de l’utilisation de la réécriture du mot de passe.

Les étapes suivantes partent du principe que vous avez déjà configuré Azure AD Connect dans votre environnement à l’aide des paramètres [Express](./../connect/active-directory-aadconnect-get-started-express.md) ou [Personnalisé](./../connect/active-directory-aadconnect-get-started-custom.md).

1. Pour configurer et activer l’écriture différée des mots de passe, connectez-vous à votre serveur Azure AD Connect et démarrez l’Assistant de configuration **Azure AD Connect**.
2. Sur la page d’**accueil**, sélectionnez **Configurer**.
3. Sur la page **Tâches supplémentaires**, sélectionnez **Personnalisation des options de synchronisation**, puis cliquez sur **Suivant**.
4. Sur la page **Connexion à Azure AD**, entrez des informations d’identification d’administrateur général et sélectionnez **Suivant**.
5. Sur les pages **Connexion des annuaires** et **Filtrage par domaine/unité d’organisation**, sélectionnez **Suivant**.
6. Sur la page **Fonctionnalités facultatives**, cochez la case située à côté de l’option **Écriture différée de mot de passe**, puis sélectionnez **Suivant**.
   ![Activation de l’écriture différée du mot de passe dans Azure AD Connect][Writeback]
7. Sur la page **Prêt à configurer**, sélectionnez **Configurer** et attendez la fin du processus.
8. Lorsque la configuration prend fin, sélectionnez **Quitter**.

Pour apprendre à connaître les tâches de dépannage courantes associées à l’écriture différée des mots de passe, voir la section [Résoudre les problèmes de réécriture du mot de passe](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) dans l’article portant sur la résolution des problèmes.

## <a name="active-directory-permissions"></a>Autorisations Active Directory

Pour rester dans le cadre de la réinitialisation des mots de passe en libre-service, vous devez définir les éléments suivants dans le compte spécifié (utilitaire Azure AD Connect) :

* **Réinitialiser le mot de passe** 
* **Modifier le mot de passe** 
* **Autorisations en écriture** sur `lockoutTime`
* **Autorisations en écriture** sur `pwdLastSet`
* **Droits étendus** sur l’un ou l’autre des éléments suivants :
   * L’objet racine de *chaque domaine* dans cette forêt
   * Les unités d’organisation utilisateur (UO) que vous souhaitez utiliser pour rester dans le cadre de la réinitialisation des mots de passe en libre-service

Si vous ignorez de quel compte il s’agit exactement, ouvrez l’interface utilisateur de configuration d’Azure Active Directory Connect, puis sélectionnez l’option **View current configuration** (Afficher la configuration actuelle). Le compte auquel vous devez ajouter l’autorisation est répertorié sous **Synchronized Directories** (Annuaires synchronisés).

Si vous définissez ces autorisations, le compte de service de l’agent de gestion de chaque forêt peut gérer les mots de passe pour les comptes d’utilisateur de cette forêt. 

> [!IMPORTANT]
> Sans ces autorisations, même si l’écriture différée semble configurée correctement, les utilisateurs rencontreront des erreurs en essayant de gérer leurs mots de passe locaux à partir du cloud.
>

> [!NOTE]
> La réplication de ces autorisations pour tous les objets de l’annuaire peut durer une heure ou plus.
>

Afin de configurer les autorisations appropriées pour l’écriture différée de mot de passe, procédez comme suit :

1. Ouvrez Utilisateurs et ordinateurs Active Directory avec un compte disposant des autorisations d’administration de domaine appropriées.
2. Dans le menu **Affichage**, assurez-vous que l’option **Fonctionnalités avancées** est activée.
3. Dans le volet gauche, cliquez avec le bouton droit sur l’objet représentant la racine du domaine, puis sélectionnez **Propriétés** > **Sécurité** > **Avancée**.
4. Dans l’onglet **Autorisations**, sélectionnez **Ajouter**.
5. Sélectionnez le compte auquel les autorisations sont appliquées (à partir de la configuration Azure AD Connect).
6. Dans la liste déroulante **S’applique à**, sélectionnez les objets de type **utilisateur descendant**.
7. Sous **Autorisations**, cochez les cases correspondant aux éléments suivants :
    * **Réinitialiser le mot de passe**
    * **Modifier le mot de passe**
    * **Écrire lockoutTime**
    * **Écrire pwdLastSet**
8. Cliquez sur **Appliquer/OK** pour appliquer les changements et fermer les boîtes de dialogue ouvertes.

## <a name="next-steps"></a>Étapes suivantes

[Qu’est-ce que la réécriture du mot de passe ?](concept-sspr-writeback.md)

[Writeback]: ./media/howto-sspr-writeback/enablepasswordwriteback.png "Activation de la réécriture du mot de passe dans Azure AD Connect"
