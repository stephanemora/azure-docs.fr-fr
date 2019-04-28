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
ms.openlocfilehash: 7a6fa45d0f4ea09fbc84d3185aa0e58db165ac19
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60300575"
---
## <a name="add-the-applications-registration-information-to-your-app"></a>Ajouter les informations d’inscription de l’application à votre application

Pour cette étape, vous devez configurer l’URL de redirection des informations d’inscription de votre application, puis ajouter l’ID d’application à votre application SPA JavaScript.

### <a name="configure-redirect-url"></a>Configurer l’URL de redirection

Configurez le champ `Redirect URL` avec l’URL de la page index.html de votre serveur web, puis cliquez sur *Mettre à jour*.

> #### <a name="visual-studio-instructions-for-obtaining-the-redirect-url"></a>Instructions Visual Studio pour obtenir l’URL de redirection
> Pour obtenir l’URL de redirection, procédez comme suit :
> 1. Dans **l’Explorateur de solutions**, sélectionnez le projet et examinez la fenêtre **Propriétés**. Si vous ne voyez pas de fenêtre **Propriétés**, appuyez sur **F4**.
> 2. Copiez la valeur du champ **URL** dans le Presse-papiers :<br/> ![Propriétés du projet](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3. Collez la valeur dans le champ **URL de redirection** situé en haut de la page, puis sélectionnez **Mettre à jour**.

<p>

> #### <a name="setting-redirect-url-for-node"></a>Définition d’une URL de redirection pour Node
> Pour Node.js, vous pouvez définir le port du serveur web dans le fichier *server.js*. Ce didacticiel utilise le port 30662 pour référence, mais vous pouvez utiliser tout autre port disponible. Suivez les instructions ci-après pour configurer une URL de redirection dans les informations d’inscription d’application :<br/>
> Définissez `http://localhost:30662/` comme **URL de redirection** en haut de cette page, ou utilisez `http://localhost:[port]/` si vous avez recours à un port TCP personnalisé (où *[port]* correspond au numéro de port TCP personnalisé), puis cliquez sur **Mettre à jour**.

### <a name="configure-your-javascript-spa-application"></a>Configurer votre application SPA JavaScript

1. Dans le fichier `index.html` créé au cours de la configuration du projet, ajoutez les informations d’inscription d’application. Ajoutez le code ci-après dans la partie supérieure entre les balises `<script></script>` dans le corps de votre fichier `index.html` :

```javascript
var applicationConfig = {
    clientID: "Enter_the_Application_Id_here",
    authority: "https://login.microsoftonline.com/common",
    graphScopes: ["user.read"],
    graphEndpoint: "https://graph.microsoft.com/v1.0/me"
};
```
