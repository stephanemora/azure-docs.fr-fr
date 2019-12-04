---
title: Configurer la réécriture du mot de passe pour SSPR - Azure Active Directory
description: Utilisation d’Azure AD et d’Azure AD Connect pour l’écriture différée des mots de passe dans un annuaire local
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1acda877ecadc8ad0abd09b78d5453743e2470b1
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381151"
---
# <a name="how-to-configure-password-writeback"></a>Procédure : Configuration de l’écriture différée du mot de passe

Les étapes suivantes partent du principe que vous avez déjà configuré Azure AD Connect dans votre environnement à l’aide des paramètres [Express](../hybrid/how-to-connect-install-express.md) ou [Personnalisé](../hybrid/how-to-connect-install-custom.md).

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

> [!WARNING]
> La réécriture du mot de passe ne fonctionnera plus pour les clients qui utilisent les versions d’Azure AD Connect 1.0.8641.0 et antérieures quand [Azure Access Control Service (ACS) sera mis hors service le 7 novembre 2018](../develop/active-directory-acs-migration.md). Les versions d’Azure AD Connect 1.0.8641.0 et antérieures cesseront alors d’autoriser la réécriture du mot de passe, car elles dépendent d’ACS pour cette fonctionnalité.
>
> Pour éviter une interruption de service, effectuez une mise à niveau depuis une version antérieure d’Azure AD Connect vers une version plus récente ; consultez l’article [Azure AD Connect : Effectuer une mise à niveau vers la dernière version](../hybrid/how-to-upgrade-previous-version.md).
>

## <a name="licensing-requirements-for-password-writeback"></a>Conditions de licence pour la réécriture du mot de passe

**Réinitialisation/modification/déverrouillage de mot de passe libre-service avec écriture différée locale est une fonctionnalité Premium d’Azure AD**. Pour plus d’informations sur les licences, consultez la [page des tarifs Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

Pour que vous puissiez utiliser la réécriture du mot de passe, il faut que l’une des licences suivantes soit attribué sur votre locataire :

* Azure AD Premium P1
* Azure AD Premium P2
* Enterprise Mobility + Security E3 ou A3
* Enterprise Mobility + Security E5 ou A5
* Microsoft 365 E3 ou A3
* Microsoft 365 E5 ou A5
* Microsoft 365 F1
* Microsoft 365 Business

> [!WARNING]
> Les plans de licences Office 365 édition autonome *ne prennent pas en charge « les réinitialisation/modification/déverrouillage de mot de passe libre-service avec réécriture locale »* et nécessitent l’un des plans précédents pour que cette fonctionnalité soit opérationnelle.
>

## <a name="active-directory-permissions-and-on-premises-password-complexity-policies"></a>Autorisations Active Directory et stratégies de complexité de mot de passe local 

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
6. Dans la liste déroulante **S’applique à**, sélectionnez **Objets utilisateur descendants**.
7. Sous **Autorisations**, cochez les cases correspondant aux options suivantes :
    * **Modifier le mot de passe**
    * **Réinitialiser le mot de passe**
8. Sous **Propriétés**, cochez les cases correspondant aux options suivantes :
    * **Écrire lockoutTime**
    * **Écrire pwdLastSet**
9. Cliquez sur **Appliquer/OK** pour appliquer les changements et fermer les boîtes de dialogue ouvertes.

Étant donné que la source d’autorité est locale, les stratégies de complexité de mot de passe s’appliquent à partir de la même source de données connectée. Vérifiez que vous avez modifié les stratégies de groupe existantes pour « l’antériorité minimale du mot de passe ». La stratégie de groupe ne doit pas être définie sur 1, ce qui signifie que le mot de passe doit être âgé d’au moins un jour avant de pouvoir être mis à jour. Vous devez vérifier qu’elle est définie sur 0. Ces paramètres se trouvent dans `gpmc.msc` sous **Configuration ordinateur > Stratégies > Paramètres Windows > Paramètres de sécurité > Stratégies de compte**. Exécutez `gpupdate /force` pour vous assurer que la modification prend effet. 

## <a name="next-steps"></a>Étapes suivantes

[Qu’est-ce que la réécriture du mot de passe ?](concept-sspr-writeback.md)

[Writeback]: ./media/howto-sspr-writeback/enablepasswordwriteback.png "Activation de la réécriture du mot de passe dans Azure AD Connect"
