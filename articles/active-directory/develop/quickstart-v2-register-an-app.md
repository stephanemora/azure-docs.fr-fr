---
title: Inscription d’une application avec le point de terminaison Azure AD v2.0 à l’aide du portail | Microsoft Docs
description: Inscription d’une application avec Microsoft pour l’activation de l’authentification et l’accès aux services Microsoft à l’aide du point de terminaison v2.0
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
ms.topic: article
ms.date: 04/18/2018
ms.author: celested
ms.custom: aaddev
ms.openlocfilehash: 8ab4e6b5b2813a216b6dd6f0fc108a09239ca9a6
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2018
ms.locfileid: "39507610"
---
# <a name="how-to-register-an-app-with-the-v20-endpoint"></a>Inscription d’une application avec le point de terminaison v2.0
Pour générer une application prenant en charge à la fois les connexions aux comptes Microsoft personnels (MSA) et aux comptes éducatifs (Azure AD), vous devez d’abord l’inscrire auprès de Microsoft. Pour le moment, vous ne pouvez pas utiliser les applications existantes avec Azure AD ou MSA. Vous devez en créer une.

> [!NOTE]
> Les scénarios et les fonctionnalités Azure Active Directory ne sont pas tous pris en charge par le point de terminaison v2.0. Pour déterminer si vous devez utiliser le point de terminaison v2.0, consultez les [limites de v2.0](active-directory-v2-limitations.md).


## <a name="visit-the-microsoft-app-registration-portal"></a>Visiter le portail d’inscription des applications Microsoft
Tout d’abord, accédez au portail d’inscription d’application de Microsoft sur [https://apps.dev.microsoft.com/](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). 

Connectez-vous à l’aide d’un compte Microsoft personnel, professionnel ou scolaire. Si vous en êtes dépourvu, inscrivez-vous pour obtenir un nouveau compte personnel.

Vous avez terminé ? À présent, consultez votre liste d’applications Microsoft, qui est probablement vide. Nous allons y remédier.

Cliquez sur **Ajouter une application**, et attribuez-lui un nom. Le portail attribue à votre application un ID d’application global unique que vous utiliserez ultérieurement dans votre code. Si votre application inclut un composant côté serveur, qui nécessite des jetons d’accès pour appeler des API (à savoir Office, Azure ou votre propre API Web), créez également ici un **secret d’application** .

Ensuite, ajoutez les **plateformes** que votre application utilisera.

* Pour les applications web, fournissez une **URI de redirection** où les messages de connexion peuvent être envoyés.
* Pour les applications mobiles, copiez la valeur par défaut de l’URI de redirection créée automatiquement pour vous.
* Pour les API web, une étendue par défaut pour accéder à l’API web est automatiquement créée pour vous. Vous pouvez choisir d’ajouter d’autres étendues à l’aide du bouton **Ajouter une étendue**. Vous pouvez également ajouter des applications préalablement autorisées pour utiliser votre API web à l’aide du formulaire **Applications préalablement autorisées**. 

Si vous le souhaitez, vous pouvez personnaliser l’apparence de votre page de connexion dans la section **Profil**. Avant de continuer, veillez à cliquer sur **Enregistrer** .

> [!NOTE]
> Lorsque vous créez une application à l’aide de [https://apps.dev.microsoft.com/](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), l’application est inscrite auprès du locataire de base du compte que vous utilisez pour vous connecter au portail. Cela signifie que vous ne pouvez pas inscrire une application dans votre client Azure AD à l’aide d’un compte Microsoft personnel. Si vous souhaitez réellement inscrire une application dans un client particulier, connectez-vous avec un compte créé à l’origine dans ce client.


## <a name="build-a-quickstart-app"></a>Créer une application de démarrage rapide
Maintenant que vous disposez d’une application Microsoft, vous pouvez suivre l’un des didacticiels de démarrage rapide v2.0. Voici quelques recommandations :

[!INCLUDE [active-directory-v2-quickstart-table](../../../includes/active-directory-v2-quickstart-table.md)]

