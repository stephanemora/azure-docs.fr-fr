---
title: Collaborer avec d’autres personnes - LUIS
titleSuffix: Azure Cognitive Services
description: Un propriétaire d’application peut ajouter des contributeurs à la ressource de création. Ces contributeurs peuvent modifier le modèle, l’entraîner et publier l’application.
services: cognitive-services
author: aahill
ms.author: aahi
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2021
ms.openlocfilehash: 5a320d6368d4fdecaf4001fc9255c4c0ff3b276c
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114285592"
---
# <a name="add-contributors-to-your-app"></a>Ajouter des contributeurs à votre application

Un propriétaire d’application peut ajouter des contributeurs à des applications. Ces contributeurs peuvent modifier le modèle, l’entraîner et publier l’application. Une fois que vous avez [migré](luis-migration-authoring.md) votre compte, les _contributeurs_ sont gérés dans le portail Azure pour la ressource de création, à l’aide de la page **Contrôle d’accès (IAM)** . Ajoutez un utilisateur à l’aide de l’adresse e-mail du collaborateur et du rôle _contributeur_.

## <a name="add-contributor-to-azure-authoring-resource"></a>Ajouter un contributeur à une ressource de création Azure

Vous avez migré si votre expérience de création LUIS est liée à une ressource de création dans la page **Gérer -> Ressources Azure** du portail LUIS.

Dans le portail Azure, recherchez votre ressource de création LUIS (Language Understanding). Son type est `LUIS.Authoring`. Dans la page **Contrôle d’accès (IAM)** de la ressource, ajoutez le rôle **Contributeur** pour l’utilisateur que vous voulez ajouter. Pour connaître les étapes détaillées, consultez [Attribuer des rôles Azure à l’aide du portail Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="view-the-app-as-a-contributor"></a>Afficher l’application en tant que contributeur

Une fois que vous avez été ajouté en tant que contributeur, [connectez-vous au portail LUIS](sign-in-luis-portal.md).

[!INCLUDE [switch azure directories](includes/switch-azure-directories.md)]

### <a name="users-with-multiple-emails"></a>Utilisateurs avec plusieurs adresses e-mail

Si vous ajoutez des contributeurs à une application LUIS, vous spécifiez son adresse e-mail exacte. Si Azure Active Directory (Azure AD) permet à un utilisateur d’avoir plusieurs comptes de courrier interchangeables, LUIS exige que l’utilisateur se connecte avec l’adresse e-mail spécifiée lors de l’ajout du contributeur.

<a name="owner-and-collaborators"></a>

### <a name="azure-active-directory-resources"></a>Ressources Azure Active Directory

Si vous utilisez [Azure Active Directory](../../active-directory/index.yml) (Azure AD) dans votre organisation, Language Understanding (LUIS) a besoin d’une autorisation pour accéder aux informations sur l'accès de vos utilisateurs quand ils souhaitent utiliser LUIS. Les ressources dont LUIS a besoin sont minimes.

La description détaillée s’affiche quand vous tentez de vous inscrire avec un compte qui a un consentement de l’administrateur ou qui n’en a pas besoin, de sorte que le consentement de l’administrateur :

* Vous autorise à vous connecter à l’application avec votre compte professionnel et laisse l’application lire votre profil. Autorise également l’application à lire les informations de base sur l’entreprise. Cela accorde à LUIS l’autorisation de lire les données de profil de base, telles que l’ID d’utilisateur, l’adresse e-mail et le nom.
* Autorise l’application à afficher et à mettre à jour vos données, même quand vous n’utilisez pas l’application. L'autorisation est nécessaire pour actualiser le jeton d’accès de l’utilisateur.


### <a name="azure-active-directory-tenant-user"></a>Utilisateur du locataire Azure Active Directory

LUIS utilise le flux de consentement standard Azure Active Directory (Azure AD).

L’administrateur de locataire doit travailler directement avec l’utilisateur qui a besoin d’accéder à LUIS dans Azure AD.

* D’abord, l’utilisateur se connecte à LUIS, et une boîte de dialogue contextuelle nécessitant l’approbation de l’administrateur s’affiche. L’utilisateur doit contacter l’administrateur de locataire avant de poursuivre.
* Ensuite, l’administrateur de locataire se connecte à LUIS, et une boîte de dialogue contextuelle de flux de consentement s’affiche. C’est avec cette boîte de dialogue que l’administrateur accorde l’autorisation à l’utilisateur. Une fois que l’administrateur a donné son autorisation, l’utilisateur peut poursuivre avec LUIS. Si l’administrateur de locataire ne se connecte pas à LUIS, l’administrateur peut [consentir](https://account.activedirectory.windowsazure.com/r#/applications) à l’accès à LUIS. Dans cette page, vous pouvez filtrer la liste pour afficher uniquement les éléments comportant le nom `LUIS`.

Si l’administrateur de locataire veut uniquement que certaines personnes utilisent LUIS, plusieurs solutions sont possibles :
* Donnez le « consentement administrateur » (à tous les utilisateurs d’Azure AD), mais affectez ensuite la valeur « Oui » à « Affectation de l’utilisateur obligatoire » sous les propriétés de l’application d’entreprise, et enfin attribuer/ajouter uniquement les utilisateurs souhaités à l’application. Avec cette méthode, l’administrateur offre quand même le « consentement administrateur » à l’application, mais il peut contrôler les utilisateurs en mesure d’y accéder.
* Une deuxième solution consiste à utiliser l’[API de gestion des identités et des accès Azure AD dans Microsoft Graph](/graph/azuread-identity-access-management-concept-overview) pour fournir un consentement à chaque utilisateur spécifique.

Découvrez-en plus sur les utilisateurs Azure Active Directory et le consentement :
* [Limiter votre application](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) à un ensemble d’utilisateurs

## <a name="next-steps"></a>Étapes suivantes

* Découvrez [comment utiliser des versions](luis-how-to-manage-versions.md) pour contrôler le cycle de vie de votre application.
* Découvrez la [création de ressources](luis-how-to-azure-subscription.md) et l’[ajout de contributeurs](luis-how-to-collaborate.md) à ces ressources.
* Découvrez [comment créer](luis-how-to-azure-subscription.md) des ressources de création et de runtime.
* Migrez vers la nouvelle [ressource de création](luis-migration-authoring.md).