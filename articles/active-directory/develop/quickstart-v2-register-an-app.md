---
title: Inscrire une application auprès du point de terminaison Azure AD v2.0 | Microsoft Docs
description: Découvrez comment inscrire une application auprès de Microsoft pour l’activation de l’authentification et l’accès aux services Microsoft à l’aide du point de terminaison Azure AD v2.0.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: bb2f701f-3bc3-4759-94a5-8b9d53a8a0b6
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: lenalepa
ms.custom: aaddev
ms.openlocfilehash: b2dea11b6573be8f574bd18fa69ee76658d6d698
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46975808"
---
# <a name="quickstart-register-an-app-with-the-azure-active-directory-v20-endpoint"></a>Démarrage rapide : inscrire une application auprès du point de terminaison Azure Active Directory v2.0

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Pour générer une application prenant en charge à la fois les connexions aux comptes Microsoft personnels (MSA) et aux comptes professionnels ou scolaires (Azure AD), vous devez d’abord l’inscrire auprès du point de terminaison Azure Active Directory (Azure AD) v2.0. Pour le moment, vous ne pouvez pas utiliser les applications existantes avec Azure AD ou MSA. Vous devez en créer une.

> [!NOTE]
> Les scénarios et les fonctionnalités d’Azure AD ne sont pas tous pris en charge par le point de terminaison v2.0. Pour déterminer si vous devez utiliser le point de terminaison v2.0, consultez les [limites de v2.0](active-directory-v2-limitations.md).

## <a name="step-1-sign-in-to-the-microsoft-application-registration-portal"></a>Étape 1 : Se connecter au portail d’inscription des applications de Microsoft

1. Accédez au portail d’inscription des applications Microsoft à l’adresse [https://apps.dev.microsoft.com/](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).
1. Connectez-vous à l’aide d’un compte Microsoft personnel, professionnel ou scolaire. Si vous en êtes dépourvu, inscrivez-vous pour obtenir un nouveau compte personnel.
1. Vous avez terminé ? À présent, consultez votre liste d’applications Microsoft, qui est probablement vide. Nous allons y remédier.

## <a name="step-2-register-an-app"></a>Étape 2 : Inscrire une application

1. Sélectionnez **Ajouter une application**, et donnez-lui un nom.
    Le portail attribue à votre application un ID d’application global unique que vous utiliserez ultérieurement dans votre code. Si votre application inclut un composant côté serveur, qui nécessite des jetons d’accès pour appeler des API (à savoir Office, Azure ou votre propre API Web), créez également ici un **secret d’application** .
1. Ensuite, ajoutez les **plateformes** que votre application utilisera.
    * Pour les applications web, fournissez une **URI de redirection** où les messages de connexion peuvent être envoyés.
    * Pour les applications mobiles, copiez la valeur par défaut de l’URI de redirection créée automatiquement pour vous.
    * Pour les API web, une étendue par défaut pour accéder à l’API web est automatiquement créée pour vous.
        Vous pouvez ajouter d’autres étendues à l’aide du bouton **Ajouter une étendue**. Vous pouvez également ajouter des applications préalablement autorisées pour utiliser votre API web à l’aide du formulaire **Applications préalablement autorisées**.
1. Si vous le souhaitez, personnalisez l’apparence de votre page de connexion dans la section **Profil**. 
1. **Enregistrez** vos modifications avant de continuer.

> [!NOTE]
> Lorsque vous inscrivez une application à l’aide de [https://apps.dev.microsoft.com/](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), l’application est inscrite auprès du locataire de base du compte que vous utilisez pour vous connecter au portail. Cela signifie que vous ne pouvez pas inscrire une application dans votre client Azure AD à l’aide d’un compte Microsoft personnel. Si vous souhaitez réellement inscrire une application dans un client particulier, connectez-vous avec un compte créé à l’origine dans ce client.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous disposez d’une application Microsoft, vous pouvez suivre l’un des démarrages rapides v2.0. Voici quelques recommandations :

[!INCLUDE [active-directory-v2-quickstart-table](../../../includes/active-directory-v2-quickstart-table.md)]
