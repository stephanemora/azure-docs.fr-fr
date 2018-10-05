---
title: Fichier Include
description: Fichier Include
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 3274e5929985b2a78c5b463622be6cdbd7320d79
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47060525"
---
## <a name="setting-up-your-web-server-or-project"></a>Configuration du serveur web ou du projet

> Vous préférez télécharger le projet de cet exemple ?
> - [Télécharger les fichiers de projet](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) pour un serveur web local, tel que Node
>
> or
> - [Télécharger le projet Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)
>
> Passez ensuite à l’étape [Configuration](#register-your-application) pour configurer l’exemple de code avant son exécution.

## <a name="prerequisites"></a>Prérequis
L’exécution de ce didacticiel requiert un serveur web local tel que [Node.js](https://nodejs.org/en/download/), [.NET Core](https://www.microsoft.com/net/core) ou l’intégration d’IIS Express à [Visual Studio 2017](https://www.visualstudio.com/downloads/).

Les instructions de ce guide reposent sur Node.js et Visual Studio 2017. Toutefois, vous pouvez utiliser n’importe quel environnement de développement ou serveur web.

## <a name="create-your-project"></a>Créer votre projet

> ### <a name="option-1-node-other-web-servers"></a>Option 1 : Node/autres serveurs web
> Vérifiez que vous avez installé [Node.js](https://nodejs.org/en/download/), puis procédez comme suit :
> - Créez un dossier pour héberger votre application.

<p/><!-- -->

> ### <a name="option-2-visual-studio"></a>Option 2 : Visual Studio
> Si vous utilisez Visual Studio pour créer un projet, suivez les étapes ci-dessous pour créer une solution Visual Studio :
> 1.    Dans Visual Studio : **Fichier -> Nouveau > Projet**
> 2.    Sous **Visual C#\Web**, sélectionnez **Application web ASP.NET (.NET Framework)**.
> 3.    Entrez un nom pour votre application, puis sélectionnez **OK**.
> 4.    Sous **Nouvelle application web ASP.NET**, sélectionnez **Vide**.


## <a name="create-your-single-page-applications-ui"></a>Créer l’interface utilisateur de votre application à page unique (SPA)
1.  Créez un fichier `index.html` pour votre application SPA JavaScript. Si vous utilisez Visual Studio, sélectionnez le projet (dossier racine du projet), cliquez avec le bouton droit, sélectionnez **Ajouter > Nouvel élément > Page HTML**, puis nommez-le index.html.

2.  Ajoutez le code suivant à votre page :
```html
<!DOCTYPE html>
<html>
<head>
        <title>Quickstart for MSAL JS</title>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
        <script src="https://secure.aadcdn.microsoftonline-p.com/lib/0.2.3/js/msal.js"></script>
        <script src="https://code.jquery.com/jquery-3.2.1.min.js"></script>
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
