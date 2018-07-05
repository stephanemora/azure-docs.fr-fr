---
title: Gérer les paramètres de votre compte dans LUIS | Microsoft Docs
description: Utilisez le site web LUIS pour gérer les paramètres de votre compte.
titleSuffix: Azure
services: cognitive-services
author: v-geberr
manager: Kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/04/2018
ms.author: v-geberr
ms.openlocfilehash: 1f22112a38bf32af03ffaf0493db16839b3fe794
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36749961"
---
# <a name="manage-your-luis-account"></a>Gérer votre compte LUIS
Les deux informations clés d’un compte LUIS sont le compte d’utilisateur et la clé de création. Vos informations de connexion sont gérées sur [account.microsoft.com](https://account.microsoft.com). Votre clé de création est gérée depuis le site web [LUIS][LUIS], dans la page **Paramètres**. 

## <a name="authoring-key"></a>Clé de création

Dans la page **Paramètres**, cette unique clé de création régionale vous permet d’éditer toutes vos applications depuis le site web [LUIS][LUIS], ainsi que vos [API de création](https://aka.ms/luis-authoring-api). Chaque mois, pour des raisons pratiques, la clé de création est autorisée à effectuer un nombre [limité](luis-boundaries.md) de requêtes de point de terminaison. 

![Page Paramètres LUIS](./media/luis-how-to-account-settings/account-settings.png)

La clé de création peut être utilisée pour chacune de vos applications ainsi que pour toutes les applications que vous avez listées comme collaborateur.

## <a name="authoring-key-regions"></a>Régions de clé de création
La clé de création est spécifique à la [région de création](luis-reference-regions.md#publishing-regions). La clé ne fonctionne pas dans une autre région. 

## <a name="reset-authoring-key"></a>Réinitialiser la clé de création
Si votre clé de création est compromise, réinitialisez la clé. La clé est réinitialisée sur toutes vos applications depuis le site web [LUIS]. Si vous éditez vos applications par le biais des API de création, vous devez changer la valeur de `Ocp-Apim-Subscription-Key` pour la nouvelle clé. 

## <a name="delete-account"></a>Supprimer le compte
Consultez la page [Stockage et suppression de données](luis-concept-data-storage.md#accounts) pour savoir quelles données sont supprimées lorsque vous supprimez votre compte. 

## <a name="azure-active-directory-tenant-user"></a>Utilisateur du locataire Azure Active Directory
LUIS utilise le flux de consentement standard Azure Active Directory (Azure AD). 

L’administrateur de locataire doit travailler directement avec l’utilisateur qui a besoin d’accéder à LUIS dans Azure AD. 

D’abord, l’utilisateur se connecte à LUIS, et une boîte de dialogue contextuelle nécessitant l’approbation de l’administrateur s’affiche. L’utilisateur doit contacter l’administrateur de locataire avant de poursuivre. 

Ensuite, l’administrateur de locataire se connecte à LUIS, et une boîte de dialogue contextuelle de flux de consentement s’affiche. C’est avec cette boîte de dialogue que l’administrateur accorde l’autorisation à l’utilisateur. Une fois que l’administrateur a donné son autorisation, l’utilisateur peut poursuivre avec LUIS.

Si l’administrateur de locataire ne se connecte pas à LUIS, l’administrateur peut [consentir](https://account.activedirectory.windowsazure.com/r#/applications) à l’accès à LUIS. 

![Autorisation Azure Active Directory par site web d’application](./media/luis-how-to-account-settings/tenant-permissions.png)

Si l’administrateur de locataire souhaite que seulement certains utilisateurs accèdent à LUIS, référez-vous à ce [blog d’identité](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/).

### <a name="user-accounts-with-multiple-emails-for-collaborators"></a>Comptes d’utilisateur avec plusieurs adresses e-mails pour les collaborateurs
Si vous ajoutez des collaborateurs à votre application LUIS, vous devez spécifier l’adresse e-mail exacte dont un collaborateur a besoin pour utiliser LUIS en tant que collaborateur. Si Azure Active Directory (Azure AD) permet à un utilisateur unique d’utiliser plusieurs adresses e-mails de manière interchangeable, pour LUIS l’utilisateur doit se connecter avec l’adresse e-mail spécifiée dans la liste du collaborateur. 


## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur votre [clé de création](luis-concept-keys.md#authoring-key). 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
