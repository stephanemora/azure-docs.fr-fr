---
title: Fichier Include
description: Fichier Include
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 038ea48bedeb31416627f99b38ebb083846747e4
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48843394"
---
## <a name="register-your-application"></a>Inscrivez votre application

Il existe plusieurs manières de créer une application ; veuillez en sélectionnez une :

### <a name="option-1-register-your-application-express-mode"></a>Option 1 : Inscrire votre application (mode Express)
Maintenant, vous devez inscrire votre application dans le *portail d’inscription des applications de Microsoft* :

1.  Inscrivez votre application via le [portail d’inscription des applications de Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=singlePageApp&appTech=javascriptSpa&step=configure).
2.  Entrez un nom pour votre application, ainsi que votre adresse e-mail.
3.  Assurez-vous que la case de l’option **Assistant Installation** est cochée.
4.  Suivez les instructions à l’écran pour obtenir l’ID de l’application et collez-le dans votre code.

### <a name="option-2-register-your-application-advanced-mode"></a>Option 2 : Inscrire votre application (mode Avancé)

1. Accédez au [portail d’inscription des applications de Microsoft](https://apps.dev.microsoft.com/portal/register-app) pour inscrire une application.
2. Entrez un nom pour votre application, ainsi que votre adresse e-mail.
3. Assurez-vous que la case de l’option **Assistant Installation** est décochée.
4.  Cliquez sur **Ajouter une plateforme**, puis sélectionnez **Web**.
5. Ajoutez **l’URL de redirection** qui correspond à l’URL basée sur votre serveur web de l’application. Consultez les sections ci-dessous pour obtenir des instructions permettant de définir et d’obtenir l’URL de redirection dans Visual Studio et Node.
6. Sélectionnez **Enregistrer**.

> #### <a name="setting-redirect-url-for-node"></a>Définition d’une URL de redirection pour Node
> Pour Node.js, vous pouvez définir le port du serveur web dans le fichier *server.js*. Ce didacticiel utilise le port 30662 pour référence, mais vous pouvez utiliser tout autre port disponible. Dans tous les cas, suivez les instructions ci-dessous pour configurer une URL de redirection dans les informations d’inscription de l’application :<br/>
> - Revenez au *Portail d’inscription des applications* et entrez `http://localhost:30662/` dans `Redirect URL`, ou optez pour `http://localhost:[port]/` si vous utilisez un port TCP personnalisé (*[port]* étant le numéro de port TCP) et cliquez sur « Enregistrer ».

<p/>

> #### <a name="visual-studio-instructions-for-obtaining-the-redirect-url"></a>Instructions Visual Studio pour obtenir l’URL de redirection
> Pour obtenir l’URL de redirection, procédez comme suit :
> 1.    Dans **l’Explorateur de solutions**, sélectionnez le projet et examinez la fenêtre **Propriétés**. Si vous ne voyez pas de fenêtre **Propriétés**, appuyez sur **F4**.
> 2.    Copiez la valeur du champ **URL** dans le Presse-papiers :<br/> ![Propriétés du projet](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    Basculez vers le *portail d’inscription des applications*, collez la valeur en tant **qu’URL de redirection**, puis sélectionnez **Enregistrer**


#### <a name="configure-your-javascript-spa"></a>Configurer une application SPA JavaScript

1.  Dans le fichier `index.html` créé au cours de la configuration du projet, ajoutez les informations d’inscription d’application. Ajoutez le code suivant en haut, dans les balises `<script></script>` du corps de votre fichier `index.html` :

```javascript
var applicationConfig = {
    clientID: "[Enter the application Id here]",
    graphScopes: ["user.read"],
    graphEndpoint: "https://graph.microsoft.com/v1.0/me"
};
```
<ol start="3">
<li>
Remplacez <code>Enter the application Id here</code> par l’ID de l’application que vous venez d’inscrire.
</li>
</ol>
