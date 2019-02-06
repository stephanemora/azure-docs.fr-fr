---
title: Collaborer avec d’autres personnes
titleSuffix: Language Understanding - Azure Cognitive Services
description: Un propriétaire d’application peut ajouter des collaborateurs à l’application. Ces collaborateurs peuvent modifier le modèle, entraîner et publier l’application.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: bf714e5bd47e244a410d1062488af623253bbee6
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55217779"
---
# <a name="how-to-manage-authors-and-collaborators"></a>Comment gérer les créateurs et collaborateurs 

Un propriétaire d’application peut ajouter des collaborateurs à l’application. Ces collaborateurs peuvent modifier le modèle, entraîner et publier l’application. 

<a name="owner-and-collaborators"></a>

## <a name="add-collaborator"></a>Ajouter un collaborateur

Une application possède un seul créateur, son propriétaire, mais peut avoir de nombreux collaborateurs. Pour autoriser des collaborateurs à modifier votre application LUIS, vous devez ajouter l’e-mail qu’ils utilisent pour accéder au portail LUIS à la liste des collaborateurs. Une fois qu’ils sont ajoutés, l’application apparaît dans leur portail LUIS.

1. Sélectionnez **Gérer** dans le menu supérieur droit, puis **Collaborateurs** dans le menu gauche.

2. Sélectionnez **Ajouter un collaborateur** dans la barre d’outils.

    [![Ajouter un collaborateur](./media/luis-how-to-collaborate/add-collaborator.png "Ajouter un collaborateur")](./media/luis-how-to-collaborate/add-collaborator.png#lightbox)

3. Entrez l’adresse e-mail utilisée par le collaborateur pour se connecter au portail LUIS.

    ![Ajouter l’adresse e-mail du collaborateur](./media/luis-how-to-collaborate/add-collaborator-pop-up.png)

## <a name="transfer-of-ownership"></a>Transfert de propriété

Bien que LUIS ne prenne actuellement pas en charge le transfert de propriété, vous pouvez exporter votre application, et un autre utilisateur LUIS peut l’importer. Il peut y avoir des différences mineures de scores LUIS entre les deux applications. 

## <a name="azure-active-directory-resources"></a>Ressources Azure Active Directory

Si vous utilisez Azure Active Directory (Azure AD) dans votre organisation, LUIS a besoin d’une autorisation pour accéder aux informations sur vos utilisateurs quand ils souhaitent utiliser LUIS. Les ressources dont LUIS a besoin sont minimes. 

La description détaillée s’affiche quand vous tentez de vous inscrire avec un compte qui a un consentement de l’administrateur ou qui n’en a pas besoin, de sorte que le consentement de l’administrateur :

* Vous autorise à vous connecter à l’application avec votre compte professionnel et laisse l’application lire votre profil. Autorise également l’application à lire les informations de base sur l’entreprise.
* Autorise l’application à afficher et à mettre à jour vos données, même quand vous n’utilisez pas l’application.

La première autorisation accorde à LUIS l’autorisation de lire les données de profil de base, telles que l’ID d’utilisateur, l’adresse e-mail et le nom. La deuxième autorisation est nécessaire pour actualiser le jeton d’accès de l’utilisateur.

## <a name="azure-active-directory-tenant-user"></a>Utilisateur du locataire Azure Active Directory

LUIS utilise le flux de consentement standard Azure Active Directory (Azure AD). 

L’administrateur de locataire doit travailler directement avec l’utilisateur qui a besoin d’accéder à LUIS dans Azure AD. 

D’abord, l’utilisateur se connecte à LUIS, et une boîte de dialogue contextuelle nécessitant l’approbation de l’administrateur s’affiche. L’utilisateur doit contacter l’administrateur de locataire avant de poursuivre. 

Ensuite, l’administrateur de locataire se connecte à LUIS, et une boîte de dialogue contextuelle de flux de consentement s’affiche. C’est avec cette boîte de dialogue que l’administrateur accorde l’autorisation à l’utilisateur. Une fois que l’administrateur a donné son autorisation, l’utilisateur peut poursuivre avec LUIS.

Si l’administrateur de locataire ne se connecte pas à LUIS, l’administrateur peut [consentir](https://account.activedirectory.windowsazure.com/r#/applications) à l’accès à LUIS. 

![Autorisation Azure Active Directory par site web d’application](./media/luis-how-to-collaborate/tenant-permissions.png)

Si l’administrateur de locataire veut uniquement que certaines personnes utilisent LUIS, plusieurs solutions sont possibles :
* Donnez le « consentement administrateur » (à tous les utilisateurs d’Azure AD), mais affectez ensuite la valeur « Oui » à « Affectation de l’utilisateur obligatoire » sous les propriétés de l’application d’entreprise, et enfin attribuer/ajouter uniquement les utilisateurs souhaités à l’application. Avec cette méthode, l’administrateur offre quand même le « consentement administrateur » à l’application, mais il peut contrôler les utilisateurs en mesure d’y accéder.
* Une autre solution consiste à utiliser l’[API Graph Azure AD](https://docs.microsoft.com/graph/azuread-identity-access-management-concept-overview) pour donner le consentement à chaque utilisateur spécifique. 

Découvrez-en plus sur les utilisateurs Azure Active Directory et le consentement : 
* [Limiter votre application](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) à un ensemble d’utilisateurs

### <a name="user-accounts-with-multiple-emails-for-collaborators"></a>Comptes d’utilisateur avec plusieurs adresses e-mails pour les collaborateurs

Si vous ajoutez des collaborateurs à votre application LUIS, vous devez spécifier l’adresse e-mail exacte dont un collaborateur a besoin pour utiliser LUIS en tant que collaborateur. Alors que Azure Active Directory (Azure AD) permet à un utilisateur unique d’utiliser plusieurs adresses e-mails de manière interchangeable, LUIS nécessite que l’utilisateur se connecte avec l’adresse e-mail spécifiée dans la liste du collaborateur.

