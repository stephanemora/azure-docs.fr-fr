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
ms.openlocfilehash: da4944cc95750505e0099b7a987b78e132910460
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58125317"
---
## <a name="register-your-application"></a>Inscrivez votre application

Vous pouvez inscrire votre application de deux manières, comme décrit dans les deux sections suivantes.

### <a name="option-1-express"></a>Option 1 : Express

1. Accédez au [portail d’inscription des applications de Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=android&step=configure).
2. Dans **Nom de l’application**, attribuez un nom à votre application.
3. Vérifiez que la case **Guided Setup** (Installation guidée) est cochée et sélectionnez **Créer**.
4. Suivez les instructions à l’écran pour obtenir l’ID de l’application et collez-le dans votre code.

### <a name="option-2-advanced"></a>Option 2 : Avancé

1. Accédez au [portail d’inscription des applications de Microsoft](https://apps.dev.microsoft.com/portal/register-app).
2. Dans la zone **Nom de l’application**, attribuez un nom à votre application.
3. Vérifiez que la case **Guided Setup** (Installation guidée) est décochée et sélectionnez **Créer**.
4. Sélectionnez **Ajouter une plateforme**, puis **Application native**, et cliquez sur **Enregistrer**.
5. Sous **app** > **java** > **{host}.{namespace}**, ouvrez `MainActivity`. 
6. Remplacez *[Entrer l’ID d’application ici]* par votre ID application / client :

    ```java
    final static String CLIENT_ID = "[Enter the application Id here]";
    ```
   <!-- Workaround for Docs conversion bug -->
7. Sous **app** > **manifests**, ouvrez le fichier *AndroidManifest.xml*.
8. Dans `manifest\application`, ajoutez l’activité suivante. L’activité `BrowserTabActivity` permettant à Microsoft de rappeler votre application une fois l’authentification terminée :

    ```xml
    <!--Intent filter to capture System Browser calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            
            <!--Add in your scheme/host from registered redirect URI-->
            <!--By default, the scheme should be similar to 'msal[appId]' -->
            <data android:scheme="msal[Enter the application Id here]"
                android:host="auth" />
        </intent-filter>
    </activity>
    ```
   <!-- Workaround for Docs conversion bug -->
9. Dans `BrowserTabActivity`, remplacez `[Enter the application Id here]` par l’ID application / client.
