---
title: Fichier Include
description: Fichier Include
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 991709ee635872e33dc89dcededc7f6ac3b28ea3
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48843006"
---
## <a name="test-your-app"></a>Test de l'application

1. Exécutez votre code sur votre appareil/émulateur.

2. Essayez de vous connecter avec un compte Azure Active Directory (compte professionnel ou scolaire) ou un compte Microsoft (live.com, outlook.com). 

    ![Tester votre application](media/active-directory-develop-guidedsetup-android-test/mainwindow.png)
    <br/><br/>
    ![Nom d'utilisateur et mot de passe](media/active-directory-develop-guidedsetup-android-test/usernameandpassword.png)

### <a name="consent-to-your-app"></a>Consentement à votre application
La première fois qu'un utilisateur se connecte à votre application, il est invité à consentir aux autorisations dont votre application a besoin, comme indiqué ici : 

![Donner votre accord pour l’accès par l’application](media/active-directory-develop-guidedsetup-android-test/androidconsent.png)


### <a name="success"></a>Vous avez réussi !
Une fois que vous êtes connecté et que vous avez consenti, l’application affiche la réponse à partir de l’API Microsoft Graph. Cet appel spécifique est adressé au point de terminaison **/me** et retourne le [profil utilisateur](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_get). Pour obtenir la liste d’autres points de terminaison Microsoft Graph, consultez la [documentation pour développeurs de l’API Microsoft Graph](https://developer.microsoft.com/graph/docs#common-microsoft-graph-queries).

<!--start-collapse-->
### <a name="scopes-and-delegated-permissions"></a>Étendues et autorisations déléguées

L’API Microsoft Graph requiert l’étendue *User.Read* pour lire le profil d’un utilisateur. Cette étendue se trouve automatiquement dans toutes les applications inscrites dans le portail d’inscription de l’application. D’autres API nécessiteront des étendues supplémentaires. Par exemple, l’API Microsoft Graph requiert l’étendue *Calendars.Read* pour répertorier les calendriers de l’utilisateur. 

Pour accéder aux calendriers de l’utilisateur, ajoutez l’autorisation déléguée *Calendars.Read* aux informations d’inscription de l’application. Ajoutez ensuite l’étendue *Calendars.Read* à l’appel `acquireTokenSilent`. 

>[!NOTE]
>Si vous modifiez votre inscription à l’application, vos utilisateurs peuvent être invités à redonner leur consentement.

<!--end-collapse-->

[!INCLUDE [Help and support](active-directory-develop-help-support-include.md)]
