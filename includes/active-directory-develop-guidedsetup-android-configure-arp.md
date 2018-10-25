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
ms.openlocfilehash: fe2b02b2495b4f37cbc90e1ddbeaca43b41d008c
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48843477"
---
## <a name="add-the-applications-registration-to-your-code"></a>Ajouter l’inscription de l’application à votre code

À cette étape, vous devez ajouter l’ID de l’application/du client à votre projet.

1.  Ouvrez `MainActivity` (sous `app` > `java` > *`{host}.{namespace}`*).
2.  Remplacez la ligne commençant par `final static String CLIENT_ID` par :
```java
final static String CLIENT_ID = "[Enter the application Id here]";
```
3. Ouvrez `app` > `manifests` > `AndroidManifest.xml`.
4. Ajoutez l’activité suivante à `manifest\application`. L’activité `BrowserTabActivity` permet à Microsoft de rappeler votre application une fois l’authentification terminée :

```xml
<!--Intent filter to capture System Browser calling back to our app after Sign In-->
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

