---
title: Fichier Include
description: Fichier Include
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 09/19/2018
ms.author: dadobali
ms.custom: include file
ms.openlocfilehash: 9782c6c2024c5cf490f207bb12a214c93a53b813
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203492"
---
## <a name="register-your-application"></a>Inscrivez votre application

Vous pouvez inscrire votre application de deux manières, comme décrit dans les deux sections suivantes.

### <a name="option-1-express-mode"></a>Option 1 : Mode Express

Maintenant, vous devez inscrire votre application dans le *portail d’inscription des applications de Microsoft* :

1. Inscrivez votre application via le [portail d’inscription des applications de Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=ios&step=configure).
2. Entrez un nom pour votre application, ainsi que votre adresse e-mail.
3. Assurez-vous que la case de l’option Assistant Installation est cochée.
4. Suivez les instructions à l’écran pour obtenir l’ID de l’application et collez-le dans votre code.

### <a name="option-2-advanced-mode"></a>Option 2 : Mode Avancé

1. Accédez au [portail d’inscription des applications de Microsoft](https://apps.dev.microsoft.com/portal/register-app).
2. Entrez un nom pour votre application.
3. Assurez-vous que la case de l’option Assistant Installation est décochée.
4. Sélectionnez `Add Platform`, puis `Native Application`.
5. Sélectionnez `Save`.
6. Revenez à Xcode. Dans `ViewController.swift`, remplacez la ligne commençant par « `let kClientID` » par l’ID d’application que vous venez d’inscrire :

```swift
let kClientID = "Your_Application_Id_Here"
```

<!-- Workaround for Docs conversion bug -->
<ol start="6">
<li>
Maintenez la touche Ctrl enfoncée et cliquez sur <code>Info.plist</code> pour faire apparaître le menu contextuel, puis cliquez sur : <code>Open As</code> > <code>Source Code</code>
</li>
<li>
Sous le nœud racine <code>dict</code>, ajoutez le code suivant :
</li>
</ol>

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>$(PRODUCT_BUNDLE_IDENTIFIER)</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msal[Your_Application_Id_Here]</string>
        </array>
    </dict>
</array>
```
<ol start="8">
<li>
Remplacez <i><code>[Your_Application_Id_Here]</code></i> par l’ID d’application que vous venez d’inscrire.
</li>
</ol>
