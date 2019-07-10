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
ms.openlocfilehash: 5ce0f18c1ec7a0fcb6465ab20e774976552687f1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133061"
---
## <a name="set-up-your-web-server-or-project"></a>Configurer le serveur web ou projet

> Vous préférez télécharger le projet de cet exemple ? Effectuez l’une des actions suivantes :
> 
> - [Téléchargez les fichiers de projet](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) pour exécuter le projet avec un serveur web local, tel que Node.
>
> - (Facultatif) [Téléchargez le projet Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip) pour exécuter le projet avec le serveur IIS.
>
> Passez ensuite à l’étape [Configuration](#register-your-application) pour configurer l’exemple de code avant son exécution.

## <a name="prerequisites"></a>Prérequis

* L’exécution de ce didacticiel requiert un serveur web local tel que [Node.js](https://nodejs.org/en/download/), [.NET Core](https://www.microsoft.com/net/core) ou l’intégration d’IIS Express à [Visual Studio 2017](https://www.visualstudio.com/downloads/).

* Si vous utilisez Node.js pour exécuter le projet, installez un environnement de développement intégré (IDE), tel que [Visual Studio Code](https://code.visualstudio.com/download), pour modifier les fichiers de projet.

* Les instructions de ce guide reposent sur Node.js et Visual Studio 2017. Toutefois, vous pouvez utiliser n’importe quel environnement de développement ou serveur web.

## <a name="create-your-project"></a>Créer votre projet

> ### <a name="option-1-nodejs-or-other-web-servers"></a>Option 1 : Node.js ou autres serveurs web
> Assurez-vous que vous avez installé [Node.js](https://nodejs.org/en/download/), puis procédez comme suit :
> - Créez un dossier pour héberger votre application.
>
> ### <a name="option-2-visual-studio"></a>Option 2 : Visual Studio
> Si vous utilisez Visual Studio et créez un nouveau projet, procédez comme suit :
> 1. Dans Visual Studio, sélectionnez **Fichier** > **Nouveau** > **Projet**.
> 1. Sous **Visual C#\Web**, sélectionnez **Application web ASP.NET (.NET Framework)** .
> 1. Entrez un nom pour votre application, puis sélectionnez **OK**.
> 1. Sous **Nouvelle application web ASP.NET**, sélectionnez **Vide**.

## <a name="create-the-spa-ui"></a>Créer l’interface utilisateur de SPA
1. Créez un fichier *index.html* pour votre application SPA JavaScript. Si vous utilisez Visual Studio, sélectionnez le projet (dossier racine du projet), cliquez avec le bouton droit, sélectionnez **Ajouter** > **Nouvel élément** > **Page HTML**, puis nommez le fichier *index.html*.

1. Dans le fichier *index.html*, ajoutez le code suivant :

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
   > Vous pouvez remplacer la version de MSAL.js dans le script précédent par la dernière version publiée sous [Publications MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).
