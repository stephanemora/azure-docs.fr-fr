---
title: Comment interdire les mots de passe faibles dans Azure AD - Azure Active Directory
description: Interdire les mots de passe faibles dans votre environnement via des mots de passe interdits dynamiquement dans Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: c58371edffa4400c2c2b3efd37c3ce6d7c3cad95
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74847913"
---
# <a name="configuring-the-custom-banned-password-list"></a>Configuration de la liste de mots de passe interdits personnalisée

Nombre d’organisations estiment que leurs utilisateurs créent des mots de passe à l’aide des mots locaux courants comme un établissement scolaire, une équipe de sport ou une personne célèbre, ce qui les rend faciles à deviner. La liste de mots de passe interdits personnalisée de Microsoft permet aux entreprises d’ajouter des chaînes à évaluer et bloquer, en plus de la liste de mots de passe interdits globale, lorsque des utilisateurs et administrateurs tentent de modifier ou de réinitialiser un mot de passe.

## <a name="add-to-the-custom-list"></a>Ajouter à la liste personnalisée

La configuration de la liste de mots de passe interdits personnalisée requiert une licence Azure Active Directory Premium P1 ou P2. Pour plus d’informations sur les licences Azure Active Directory, consultez la [page de tarification d’Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

1. Connectez-vous au [Portail Azure](https://portal.azure.com) et accédez à **Azure Active Directory**, **Méthodes d’authentification**, puis **Protection par mot de passe**.
1. Définissez l’option **Enforce custom list** (Appliquer la liste personnalisée) sur **Oui**.
1. Ajouter des chaînes à la **liste de mots de passe interdits personnalisée**, à raison d’une chaîne par ligne
   * La liste de mots de passe interdits personnalisée peut contenir jusqu’à 1 000 termes.
   * La liste de mots de passe interdits personnalisée respecte la casse.
   * La liste de mots de passe interdits personnalisée prend en compte la substitution de caractères courante.
      * Exemple : « o » et « 0 » ou « a » et « \@ »
   * La longueur de chaîne minimale est de quatre caractères et la valeur maximale est de 16 caractères.
1. Lorsque vous avez ajouté toutes les chaînes, cliquez sur **Enregistrer**.

> [!NOTE]
> L’application de la mise à jour de la liste de mots de passe interdits personnalisée peut prendre plusieurs heures.

> [!NOTE]
> La liste des mots de passe interdits personnalisée est limitée à un maximum de 1 000 termes. Elle n’est pas conçue pour bloquer des listes de mots de passe extrêmement longues. Afin de tirer pleinement parti des avantages de la liste des mots de passe interdits personnalisée, Microsoft vous recommande de commencer par passer en revue et comprendre la conception et l’utilisation prévues de la liste des mots de passe interdits personnalisée (voir [Liste des mots de passe interdits personnalisée](concept-password-ban-bad.md#custom-banned-password-list)), ainsi que l’algorithme d’évaluation des mots de passe (voir [Comment les mots de passe sont-ils évalués](concept-password-ban-bad.md#how-are-passwords-evaluated)).

![Modifier la liste de mots de passe interdits personnalisée sous Méthodes d’authentification dans le portail Azure](./media/howto-password-ban-bad/authentication-methods-password-protection.png)

## <a name="how-it-works"></a>Fonctionnement

Chaque fois qu’un utilisateur ou administrateur réinitialise ou modifie un mot de passe Azure AD, celui-ci transite par la liste de mots de passe interdits pour confirmer qu’il ne figure pas dans une liste. Cette vérification est incluse dans les mots de passe définis ou modifiés à l’aide d’Azure AD.

## <a name="what-do-users-see"></a>Ce que voient les utilisateurs

Lorsqu’un utilisateur tente de réinitialiser un mot de passe pour le remplacer par un mot de passe interdit, le message d’erreur suivant s’affiche :

* Malheureusement, votre mot de passe contient un mot, une expression ou un modèle qui rend votre mot de passe facile à deviner. Réessayez avec un autre mot de passe.
* Malheureusement, vous ne pouvez pas utiliser ce mot de passe, car il contient des mots ou des caractères qui ont été bloqués par votre administrateur. Réessayez avec un autre mot de passe.

## <a name="next-steps"></a>Étapes suivantes

[Vue d’ensemble conceptuelle des listes de mots de passe interdits](concept-password-ban-bad.md)

[Vue d’ensemble conceptuelle de la protection de mot de passe Azure AD](concept-password-ban-bad-on-premises.md)

[Activer l’intégration locale aux listes de mots de passe interdits](howto-password-ban-bad-on-premises.md)
