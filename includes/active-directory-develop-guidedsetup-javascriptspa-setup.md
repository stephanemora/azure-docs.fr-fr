---
title: Fichier Include
description: Fichier Include
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: a00bc7a05af9e329494a11f9bee444827cbebf38
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121796"
---
## <a name="setting-up-your-web-server-or-project"></a>Configuration du serveur web ou du projet

> Vous préférez télécharger le projet de cet exemple ?
> - [Télécharger les fichiers de projet](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) à exécuter avec un serveur web local, tel que Node
>
> or
> - (Facultatif) [Télécharger le projet Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip) à exécuter avec le serveur IIS
>
> Passez ensuite à l’étape [Configuration](#register-your-application) pour configurer l’exemple de code avant son exécution.

## <a name="prerequisites"></a>Prérequis
L’exécution de ce didacticiel requiert un serveur web local tel que [Node.js](https://nodejs.org/en/download/), [.NET Core](https://www.microsoft.com/net/core) ou l’intégration d’IIS Express à [Visual Studio 2017](https://www.visualstudio.com/downloads/).

Si vous utilisez Node.js pour exécuter le projet, installez un IDE tel que [Visual Studio Code](https://code.visualstudio.com/download) pour modifier les fichiers projet.

Les instructions de ce guide reposent sur Node.js et Visual Studio 2017. Toutefois, vous pouvez utiliser n’importe quel environnement de développement ou serveur web.

## <a name="create-your-project"></a>Créer votre projet

> ### <a name="option-1-node-other-web-servers"></a>Option 1 : Node/autres serveurs web
> Vérifiez que vous avez installé [Node.js](https://nodejs.org/en/download/), puis procédez comme suit :
> - Créez un dossier pour héberger votre application.

<p><!-- -->

> ### <a name="option-2-visual-studio"></a>Option 2 : Visual Studio
> Si vous utilisez Visual Studio pour créer un projet, suivez les étapes ci-dessous pour créer une solution Visual Studio :
> 1.    Dans Visual Studio :  **Fichier > Nouveau > Projet**
> 2.    Sous **Visual C#\Web**, sélectionnez **Application web ASP.NET (.NET Framework)**.
> 3.    Entrez un nom pour votre application, puis sélectionnez **OK**.
> 4.    Sous **Nouvelle application web ASP.NET**, sélectionnez **Vide**.

## <a name="create-your-single-page-applications-ui"></a>Créer l’interface utilisateur de votre application à page unique (SPA)
1. Créez un fichier `index.html` pour votre application SPA JavaScript. Si vous utilisez Visual Studio, sélectionnez le projet (dossier racine du projet), cliquez avec le bouton droit et sélectionnez : **Ajouter > Nouvel élément > Page HTML** et nommez-le index.html.

2. Ajoutez le code suivant à votre page :
   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>Quickstart for MSAL JS</title>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
       <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
   </head>
   <body>
       <h2>Welcome to MSAL.js Quickstart</h2><br/>
       <h4 id="WelcomeMessage"></h4>
       <button id="SignIn" onclick="signIn()">Sign In</button><br/><br/>
       <pre id="json"></pre>
       <script>
           //JS code
       </script>
   </body>
   </html>
   ```

   > [!TIP]
   > Vous pouvez remplacer la version de MSAL.js dans le script ci-dessus par la dernière version publiée sous [Publications MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).
