---
title: Tester le portail des développeurs auto-hébergé
titleSuffix: Azure API Management
description: Découvrez comment configurer des tests unitaires et des tests de bout en bout pour votre portail Gestion des API auto-hébergé.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: ab6a7aa8fc4f11c34126415379294ef1e48fa286
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741209"
---
# <a name="test-the-self-hosted-developer-portal"></a>Tester le portail des développeurs auto-hébergé

Cet article explique comment configurer des tests unitaires et des tests de bout en bout pour votre [portail auto-hébergé](developer-portal-self-host.md).

## <a name="unit-tests"></a>Tests unitaires

Un test unitaire est une approche permettant de valider de petites parties de fonctionnalités. Il est effectué de manière isolée des autres parties de l’application.

### <a name="example-scenario"></a>Exemple de scénario

Dans ce scénario, vous testez un contrôle d’entrée de mot de passe. Il accepte uniquement les mots de passe contenant au moins :

- Une lettre

- Un chiffre

- Un caractère spécial
 
Ainsi, le test pour répondre à ces exigences ressemble à ceci :

```typescript
const passwordInput = new PasswordInput();

passwordInput.value = "";
expect(passwordInput.isValid).to.equal(false);

passwordInput.value = "password";
expect(passwordInput.isValid).to.equal(false);

passwordInput.value = "p@ssw0rd";
expect(passwordInput.isValid.to.equal(true);
```
 
### <a name="project-structure"></a>Structure du projet

Il est courant de conserver un test unitaire à côté du composant qu’il est censé valider.

```console
component.ts
component.spec.ts
```

### <a name="mock-http-requests"></a>Requêtes HTTP factices

Dans certains cas, vous vous attendez à ce qu’un composant effectue des requêtes HTTP. Le composant doit réagir correctement à différents types de réponses. Pour simuler des réponses HTTP spécifiques, utilisez `MockHttpClient`. Il implémente l'interface `HttpClient` utilisée par de nombreux autres composants du projet.

```typescript
const httpClient = new MockHttpClient();

httpClient.mock()
    .get("/users/jane")
    .reply(200, {
        firstName: "Jane",
        lastName: "Doe"
    });
```

## <a name="end-to-end-tests"></a>Tests de bout en bout

Un test de bout en bout exécute un scénario utilisateur spécifique en suivant scrupuleusement les étapes que l’utilisateur doit effectuer. Dans une application web comme le portail des développeurs Gestion des API Azure, l’utilisateur fait défiler le contenu et sélectionne des options pour atteindre certains résultats. 

Pour répliquer la navigation utilisateur, vous pouvez utiliser des bibliothèques d’assistance de manipulation de navigateur, telles que [Puppeteer](https://github.com/puppeteer/puppeteer). Cela vous permet de simuler les actions des utilisateurs et d’automatiser les scénarios pris par défaut. Puppeteer prend également automatiquement des captures d’écran de pages ou de composants à n’importe quel moment du test. Comparez-les ultérieurement avec les résultats précédents pour intercepter les écarts et les régressions potentielles.

### <a name="example-scenario"></a>Exemple de scénario

Dans ce scénario, vous devez valider un flux de connexion d’utilisateur. Pour ce scénario, la configuration suivante est requise :

1. Ouvrez le navigateur et accédez à la page de connexion.

1. Saisissez l'adresse e-mail.

1. Entrez le mot de passe.

1. Sélectionnez **Connexion**.

1. Vérifiez que l’utilisateur a été redirigé vers la page d’accueil.

1. Vérifiez que la page contient l’élément de menu **Profil**. C’est l’un des indices qui vous prouvent que vous êtes bien connecté.

Pour exécuter automatiquement le test, créez un script avec exactement les mêmes étapes :

```typescript
// 1. Open browser and navigate to the sign-in page.
const page = await browser.newPage();
await page.goto("https://contoso.com/signin");

// 2. Enter email.
await this.page.type("#email", "john.doe@contoso.com");

// 3. Enter password.
await this.page.type("#password", "p@s$w0rd");

// 4. Click Sign-in.
await this.page.click("#signin");

// 5. Verify that user got redirected to Home page.
expect(page.url()).to.equal("https://contoso.com");

// 6. Verify that the page includes the Profile menu item.
const profileMenuItem = await this.page.$("#profile");
expect(profileMenuItem).not.equals(null);
```

> [!NOTE]
> Les chaînes telles que « #email », « #password » et « #signin » sont des sélecteurs semblables à CSS qui identifient les éléments HTML de la page. Pour en savoir plus, consultez la spécification W3C sur les [sélecteurs de niveau 3](https://www.w3.org/TR/selectors-3/).

### <a name="ui-component-maps"></a>Mappages des composants de l’interface utilisateur

Les flux d’utilisateurs passent souvent par les mêmes pages ou composants. Un bon exemple est le menu principal du site Web qui est présent sur chaque page. 

Créez un mappage de composant d’interface utilisateur pour éviter de configurer et de mettre à jour les mêmes sélecteurs pour chaque test. Par exemple, vous pouvez remplacer les étapes 2 à 6 de l’exemple précédent par deux lignes :

```typescript
const signInWidget = new SigninBasicWidget(page);
await signInWidget.signInWithBasic({ email: "...", password: "..." });
```

### <a name="test-configuration"></a>Configuration de test

Certains scénarios peuvent nécessiter des données ou une configuration créées au préalable. Par exemple, vous devrez peut-être automatiser la connexion des utilisateurs avec des comptes de réseaux sociaux. Il est difficile de créer ces données rapidement ou facilement.

À cet effet, vous pouvez ajouter un fichier de configuration spécial à votre scénario de test. Les scripts de test peuvent récupérer les données requises à partir du fichier. En fonction du pipeline de build et de test, les tests peuvent extraire les secrets d’un magasin sécurisé nommé.

Voici un exemple de `validate.config.json` qui serait stocké dans le dossier `src` de votre projet.

```json
{
    "environment": "validation",
    "urls": {
        "home": "https://contoso.com",
        "signin": "https://contoso.com/signin",
        "signup": "https://contoso.com/signup/"
    },
    "signin": {
        "firstName": "John",
        "lastName": "Doe",
        "credentials": {
            "basic": {
                "email": "johndoe@contoso.com",
                "password": "< password >"
            },
            "aadB2C": {
                "email": "johndoe@contoso.com",
                "password": "< password >"
            }
        }
    },
    "signup": {
        "firstName": "John",
        "lastName": "Doe",
        "credentials": {
            "basic": {
                "email": "johndoe@contoso.com",
                "password": "< password >"
            }
        }
    }
}

```

### <a name="headless-vs-normal-tests"></a>Tests Headless et tests normaux

Les navigateurs modernes, tels que chrome ou Microsoft Edge, vous permettent d’exécuter l’automatisation en mode Headless et en mode normal. Le navigateur fonctionne sans interface utilisateur graphique en mode Headless. Il effectue toujours les mêmes manipulations de page et de DOM (Document Object Model). L’interface utilisateur du navigateur n’est généralement pas nécessaire dans les pipelines de remise. Le cas échéant, l’exécution de tests en mode Headless est une option intéressante.

Lorsque vous développez un script de test, il est utile de voir ce qui se passe exactement dans le navigateur. C’est le moment idéal pour utiliser le mode normal.

Pour basculer entre les modes, modifiez l'option `headless` dans le fichier `constants.ts`. Il se trouve dans le dossier `tests` de votre projet :

```typescript
export const LaunchOptions = {
    headless: false
};
```

Une autre option utile est `slowMo`. Il suspend l’exécution du test entre chaque action :

```typescript
export const LaunchOptions = {
    slowMo: 200 // milliseconds
};
```

## <a name="run-tests"></a>Exécuter les tests

Il existe deux façons intégrées d’exécuter des tests dans ce projet :

**commande npm**

```console
npm run test
```

**Explorateur de tests**

L’extension Explorateur de tests pour VS Code (par exemple, [l’Explorateur de tests Mocha](https://marketplace.visualstudio.com/items?itemName=hbenl.vscode-mocha-test-adapter)) a une interface utilisateur pratique et une option permettant d’exécuter automatiquement des tests à chaque modification du code source :

:::image type="content" source="media/developer-portal-testing/visual-studio-code-test-explorer.png" alt-text="Capture d’écran de l’Explorateur de tests Visual Studio Code":::

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le portail des développeurs :

- [Vue d’ensemble du portail des développeurs Gestion des API Azure](api-management-howto-developer-portal.md)

- [Auto-héberger le portail des développeurs](developer-portal-self-host.md)
