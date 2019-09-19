---
title: À propos de la personnalisation de l’interface utilisateur dans Azure Active Directory B2C | Microsoft Docs
description: Découvrez comment personnaliser l’interface utilisateur de vos applications qui utilisent Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 5ae12742c2ad50d5bf1caaf14ae2f6d34bd6d3a2
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70880786"
---
# <a name="about-user-interface-customization-in-azure-active-directory-b2c"></a>À propos de la personnalisation de l’interface utilisateur dans Azure Active Directory B2C

La possibilité de personnaliser l’interface utilisateur présentée par Azure Active Directory B2C (Azure AD B2C) à vos applications est importante pour offrir une expérience fluide à votre client. Ces expériences incluent la modification du profil, l’inscription, la connexion et la réinitialisation du mot de passe. Cet article fournit des informations pour vous aider à personnaliser l’interface utilisateur de vos applications.

Vous personnalisez l’interface utilisateur de votre application de différentes manières en fonction de vos besoins relatifs à ces expériences. Par exemple :

- Si vous utilisez des [flux d’utilisateur](active-directory-b2c-reference-policies.md) pour fournir des expériences de modification de profil, de réinitialisation du mot de passe, d’inscription ou de connexion dans votre application, vous utilisez le [portail Azure pour personnaliser l’interface utilisateur](tutorial-customize-ui.md).
- Si vous utilisez un flux utilisateur v2, vous pouvez utiliser un [modèle de disposition de page](#page-layout-templates) pour modifier l’apparence de vos pages de flux utilisateur sans davantage de personnalisation. Par exemple, vous pouvez appliquer un thème Bleu océan ou Gris ardoise à toutes les pages dans votre flux utilisateur.
- Si vous fournissez uniquement une expérience de connexion, la page de réinitialisation du mot de passe associée et des e-mails de vérification, vous utilisez les mêmes étapes de personnalisation que celles utilisées pour une [page de connexion Azure AD](../active-directory/fundamentals/customize-branding.md).
- Si les clients tentent de modifier leur profil avant de se connecter, ils sont redirigés vers une page que vous personnalisez à l’aide de la même procédure que celle utilisée pour la personnalisation de la page de connexion Azure AD.
- Si vous utilisez des [stratégies personnalisées](active-directory-b2c-overview-custom.md) pour fournir des expériences de modification de profil, de réinitialisation du mot de passe, d’inscription ou de connexion dans votre application, vous utilisez des [fichiers de stratégie pour personnaliser l’interface utilisateur](active-directory-b2c-ui-customization-custom.md).
- Si vous avez besoin de fournir du contenu dynamique basé sur la décision d’un client, vous utilisez des [stratégies personnalisées qui peuvent changer le contenu de la page](active-directory-b2c-ui-customization-custom-dynamic.md) en fonction d’un paramètre qui est envoyé dans une chaîne de requête. Par exemple, l’image d’arrière-plan dans la page de connexion ou d’inscription Azure AD B2C change en fonction d’un paramètre que vous transmettez à partir de votre application web ou mobile.
- Vous pouvez activer le code JavaScript côté client dans vos [flux utilisateur](user-flow-javascript-overview.md) ou [stratégies personnalisées](page-layout.md) Azure AD B2C.

Azure AD B2C exécute le code dans le navigateur de votre client et adopte une approche moderne appelée [Partage des ressources cross-origin (CORS)](https://www.w3.org/TR/cors/). Au moment de l’exécution, le contenu est chargé depuis une URL que vous spécifiez dans un flux d’utilisateur. Vous spécifiez différentes URL pour différentes pages. Une fois le contenu chargé à partir de votre URL, il est fusionné avec un fragment HTML inséré à partir d’Azure AD B2C, puis présenté à votre client.

Lorsque vous utilisez vos propres fichiers HTML et CSS pour personnaliser l’interface utilisateur, passez en revue les conseils suivants avant de commencer :

- Azure AD B2C fusionne le contenu HTML dans vos pages. Ne copiez pas et n’essayez pas de modifier le contenu par défaut fourni par Azure AD B2C. Il est préférable de créer votre contenu HTML à partir de zéro et d’utiliser le contenu par défaut comme référence.
- JavaScript peut désormais être inclus dans votre contenu personnalisé.
- Les versions de navigateur prises en charge sont les suivantes :
    - Internet Explorer 11, 10 et Microsoft Edge
    - Prise en charge limitée pour Internet Explorer 9 et 8
    - Google Chrome 42.0 et ultérieur
    - Mozilla Firefox 38.0 et ultérieur
- Veillez à ne pas inclure de balises de formulaire dans votre HTML, car cela interfère avec les opérations POST générées par le HTML injecté à partir d’Azure AD B2C.

## <a name="page-layout-templates"></a>Modèles de mise en page

Pour les flux utilisateur v2, vous pouvez choisir un modèle prédéfini qui donne un meilleur aspect à vos pages par défaut et constitue un bon point de départ pour votre propre personnalisation.

Dans le menu de gauche, sous **Personnaliser**, sélectionnez **Mises en page**. Puis sélectionnez **Modèle (préversion)** .

![Liste déroulante de sélection de modèle dans la page de flux d’utilisateur du portail Azure](media/customize-ui-overview/template.png)

Sélectionnez un modèle dans la liste. Par exemple, le modèle **Bleu océan** applique la mise en page suivante à vos pages de flux utilisateur :

![Exemple de modèle Bleu océan sur la page de connexion d’inscription](media/customize-ui-overview/ocean-blue.png)

Lorsque vous choisissez un modèle, la mise en page sélectionnée est appliquée à toutes les pages dans votre flux utilisateur et l’URI de chaque page est visible dans le champ **URI de la page personnalisée**.

## <a name="where-do-i-store-ui-content"></a>Où stocker le contenu de l’interface utilisateur ?

Lorsque vous utilisez vos propres fichiers HTML et CSS pour personnaliser l’IU, vous pouvez héberger le contenu de votre interface utilisateur n’importe où, par exemple dans le [Stockage Blob Azure](../storage/blobs/storage-blobs-introduction.md), sur des serveurs web, des CDN, AWS S3 ou des systèmes de partage de fichiers. Le point important est que vous hébergez le contenu sur un point de terminaison HTTPS disponible publiquement avec CORS activé. Vous devez utiliser une URL absolue quand vous le spécifiez dans votre contenu.

## <a name="how-do-i-get-started"></a>Comment faire pour démarrer ?

Pour personnaliser l’interface utilisateur, vous procédez comme suit :

- Créez du contenu HTML bien formé avec un élément `<div id="api"></div>` vide situé quelque part dans le `<body>`. Cet élément marque l’endroit où le contenu Azure AD B2C est inséré. L’exemple suivant illustre une page minimale :

    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>!Add your title here!</title>
        <link rel="stylesheet" href="https://mystore1.blob.core.windows.net/b2c/style.css">
      </head>
      <body>
        <h1>My B2C Application</h1>
        <div id="api"></div>   <!-- Leave this element empty because Azure AD B2C will insert content here. -->
      </body>
    </html>
    ```

- Héberger votre contenu sur un point de terminaison HTTPS (avec CORS activé). Vous devez activer à la fois les méthodes de requête GET et OPTIONS lors de la configuration de CORS.
- Utilisez les feuilles de style en cascade pour donner du style aux éléments d’interface utilisateur insérés par Azure AD B2C dans votre page. L’exemple suivant montre un fichier CSS simple qui inclut également des paramètres pour les éléments HTML d’inscription injectés :

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center;
    }
    .entry {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center;
    }
    .create {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

- Créez ou modifiez une stratégie pour utiliser le contenu que vous avez créé.

Le tableau suivant répertorie les fragments HTML qu’Azure AD B2C fusionne dans l’élément `<div id="api"></div>` situé dans votre contenu.

| Page insérée | Description du HTML |
| ------------- | ------------------- |
| Sélection du fournisseur d’identité | Contient une liste de boutons pour les fournisseurs d’identité que l’utilisateur peut choisir durant l’inscription ou la connexion. Ces boutons incluent des fournisseurs d’identité sociale tels que Facebook et Google, ou des comptes locaux (basés sur une adresse e-mail ou un nom d’utilisateur). |
| Inscription avec un compte local | Contient un formulaire d’inscription avec un compte local basé sur une adresse e-mail ou un nom d’utilisateur. Le formulaire peut contenir différentes commandes de saisie telles que la zone de saisie de texte, celle du mot de passe, le bouton radio, les cases du menu déroulant à sélection unique et la sélection de plusieurs cases à cocher. |
| Inscription avec un compte social | Peut s’afficher lors de l’inscription à l’aide d’un compte existant d’un fournisseur d’identité sociale tel que Facebook ou Google. Cette page est utilisée quand des informations supplémentaires doivent être recueillies à partir du client à l’aide d’un formulaire d’inscription. |
| Connexion ou inscription unifiée | Gère l’inscription et la connexion des clients qui peuvent utiliser des fournisseurs d’identité sociale tels que Facebook ou Google ou des comptes locaux. |
| Authentification multifacteur | Les clients peuvent vérifier leur numéro de téléphone (par voie textuelle ou vocale) au cours de l’inscription ou de la connexion. |
| Error | Fournit des informations d’erreur au client. |


## <a name="how-do-i-localize-content"></a>Comment localiser le contenu ?

Vous localisez votre contenu HTML en activant la [personnalisation de la langue](active-directory-b2c-reference-language-customization.md) dans votre locataire Azure AD B2C. L’activation de cette fonctionnalité permet à Azure AD B2C de transmettre le paramètre OpenID Connect `ui-locales` à votre point de terminaison. Votre serveur de contenu peut utiliser ce paramètre pour fournir des pages HTML propres à la langue.

Le contenu peut être extrait de différents emplacements en fonction des paramètres régionaux utilisés. Dans votre point de terminaison avec CORS activé, vous configurez une structure de dossiers pour héberger du contenu pour des langues spécifiques. Vous devez appeler celui qui convient si vous utilisez la valeur générique {Culture:RFC5646}. Par exemple, votre URI de page personnalisée peut ressembler à `https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html`. Vous pouvez charger la page en français en extrayant le contenu à partir de `https://contoso.blob.core.windows.net/fr/myHTML/unified.html`.

## <a name="examples"></a>Exemples

Pour obtenir des exemples de personnalisation, téléchargez et lisez ces [exemples de fichiers de modèle](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip).

## <a name="next-steps"></a>Étapes suivantes

- Si vous utilisez des **flux utilisateur**, vous pouvez commencer à personnaliser votre interface utilisateur avec le tutoriel :

    [Personnaliser l’interface utilisateur de vos applications dans Azure Active Directory B2C](tutorial-customize-ui.md).
- Si vous utilisez des **stratégies personnalisées**, vous pouvez commencer à personnaliser l’interface utilisateur avec l’article :

    [Personnaliser l’interface utilisateur de votre application à l’aide d’une stratégie personnalisée dans Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).
