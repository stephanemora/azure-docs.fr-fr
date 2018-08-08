---
title: Collaborer avec d’autres contributeurs sur les applications LUIS dans Azure | Microsoft Docs
description: Découvrez comment collaborer avec d’autres contributeurs sur les applications Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 07/31/2018
ms.author: diberry
ms.openlocfilehash: 99f37cb6dc5e05fc5eb4bde09685435ee57fecc6
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397783"
---
# <a name="how-to-manage-authors-and-collaborators"></a>Comment gérer les créateurs et collaborateurs 

Vous pouvez collaborer avec d’autres utilisateurs sur votre application LUIS. 

## <a name="owner-and-collaborators"></a>Propriétaire et collaborateurs

Une application possède un seul créateur, son propriétaire, mais peut avoir de nombreux collaborateurs. 

## <a name="add-collaborator"></a>Ajouter un collaborateur

Pour autoriser des collaborateurs à modifier votre application LUIS, sur la page **Paramètres** de votre application LUIS, entrez l’adresse électronique du collaborateur et cliquez sur **Add collaborator** (Ajouter un collaborateur). Les collaborateurs peuvent se connecter et modifier votre application LUIS en même temps que vous travaillez sur l’application.

![Ajouter un collaborateur](./media/luis-how-to-collaborate/add-collaborator.png)

## <a name="transfer-of-ownership"></a>Transfert de propriété

Bien que LUIS ne prenne actuellement pas en charge le transfert de propriété, vous pouvez exporter votre application, et un autre utilisateur LUIS peut l’importer. Il peut y avoir des différences mineures de scores LUIS entre les deux applications. 

## <a name="azure-active-directory-tenant-user"></a>Utilisateur du locataire Azure Active Directory

LUIS utilise le flux de consentement standard Azure Active Directory (Azure AD). 

L’administrateur de locataire doit travailler directement avec l’utilisateur qui a besoin d’accéder à LUIS dans Azure AD. 

D’abord, l’utilisateur se connecte à LUIS, et une boîte de dialogue contextuelle nécessitant l’approbation de l’administrateur s’affiche. L’utilisateur doit contacter l’administrateur de locataire avant de poursuivre. 

Ensuite, l’administrateur de locataire se connecte à LUIS, et une boîte de dialogue contextuelle de flux de consentement s’affiche. C’est avec cette boîte de dialogue que l’administrateur accorde l’autorisation à l’utilisateur. Une fois que l’administrateur a donné son autorisation, l’utilisateur peut poursuivre avec LUIS.

Si l’administrateur de locataire ne se connecte pas à LUIS, l’administrateur peut [consentir](https://account.activedirectory.windowsazure.com/r#/applications) à l’accès à LUIS. 

![Autorisation Azure Active Directory par site web d’application](./media/luis-how-to-account-settings/tenant-permissions.png)

Si l’administrateur de locataire souhaite que seulement certains utilisateurs accèdent à LUIS, référez-vous à ce [blog d’identité](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/).

### <a name="user-accounts-with-multiple-emails-for-collaborators"></a>Comptes d’utilisateur avec plusieurs adresses e-mails pour les collaborateurs

Si vous ajoutez des collaborateurs à votre application LUIS, vous devez spécifier l’adresse e-mail exacte dont un collaborateur a besoin pour utiliser LUIS en tant que collaborateur. Alors que Azure Active Directory (Azure AD) permet à un utilisateur unique d’utiliser plusieurs adresses e-mails de manière interchangeable, LUIS nécessite que l’utilisateur se connecte avec l’adresse e-mail spécifiée dans la liste du collaborateur.