---
title: Ajouter une page à l’interface utilisateur de la solution de supervision à distance - Azure | Microsoft Docs
description: Cet article vous montre comment ajouter une nouvelle page à l’interface utilisateur web de l’accélérateur de solution de supervision à distance.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.openlocfilehash: c90f4166bf88a8df18a93e84903c93461b904d2c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187260"
---
# <a name="add-a-custom-page-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Ajouter une page personnalisée à l’interface utilisateur web de l’accélérateur de solution de supervision à distance

Cet article vous montre comment ajouter une nouvelle page à l’interface utilisateur web de l’accélérateur de solution de supervision à distance. Cet article aborde les points suivants :

- Comment préparer un environnement de développement local.
- Comment ajouter une nouvelle page à l’interface utilisateur web.

D’autres procédures étendent ce scénario pour ajouter davantage de fonctionnalités à la page que vous ajoutez.

## <a name="prerequisites"></a>Conditions préalables requises

Pour effectuer les étapes de ce guide pratique, vous devez avoir installé les logiciels suivants sur votre machine de développement locale :

- [Git](https://git-scm.com/downloads)
- [Node.JS](https://nodejs.org/download/)

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Préparer un environnement de développement local pour l’interface utilisateur

Le code de l’interface utilisateur de l’accélérateur de solution de supervision à distance est implémenté à l’aide du framework [React](https://reactjs.org/) JavaScript. Vous pouvez trouver le code source dans le dépôt GitHub [Interface utilisateur web Supervision à distance](https://github.com/Azure/pcs-remote-monitoring-webui).

Pour effectuer et tester les modifications de l’interface utilisateur, vous pouvez l’exécuter sur votre ordinateur de développement local. Si vous le souhaitez, la copie locale peut se connecter à une instance déployée de l’accélérateur de solution pour lui permettre d’interagir avec vos appareils simulés ou réels.

Pour préparer votre environnement de développement local, utilisez Git pour cloner le dépôt [Interface utilisateur web Supervision à distance](https://github.com/Azure/pcs-remote-monitoring-webui) sur votre ordinateur local :

```cmd/sh
git clone https://github.com/Azure/pcs-remote-monitoring-webui.git
```

## <a name="add-a-page"></a>Ajouter une page

Pour ajouter une page à l’interface utilisateur web, vous devez ajouter les fichiers sources qui définissent la page et modifier certains fichiers existants pour que l’interface utilisateur web ait connaissance de la nouvelle page.

### <a name="add-the-new-files-that-define-the-page"></a>Ajouter les nouveaux fichiers qui définissent la page

Pour vous aider à démarrer, le dossier **src/walkthrough/components/pages/basicPage** contient quatre fichiers qui définissent une page simple :

**basicPage.container.js**

[!code-javascript[Page container source](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.container.js?name=container "Page container source")]

**basicPage.js**

[!code-javascript[Basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.js?name=page "Basic page")]

**basicPage.scss**

[!code-javascript[Page styling](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.scss?name=styles "Page styling")]

**basicPage.test.js**

[!code-javascript[Test code for basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.test.js?name=test "Test code for basic page")]

Créez un dossier **src/components/pages/example** et copiez-y ces quatre fichiers.

### <a name="add-the-new-page-to-the-web-ui"></a>Ajouter la nouvelle page à l’interface utilisateur web

Pour ajouter la nouvelle page à l’interface utilisateur web, apportez les modifications suivantes aux fichiers existants :

1. Ajoutez le conteneur de la nouvelle page au fichier **src/components/pages/index.js** :

    ```js
    export * from './example/basicPage.container';
    ```

1. (Facultatif) Ajoutez une icône SVG pour la nouvelle page. Pour plus d’informations, consultez [webui/src/utilities/README.md](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/utilities/README.md). Vous pouvez utiliser un fichier SVG existant.

1. Ajoutez le nom de la page au fichier de traductions, **public/locales/en/translations.json**. L’interface utilisateur web utilise [i18next](https://www.i18next.com/) pour l’internationalisation.

    ```json
    "tabs": {
      "basicPage": "Example",
    },
    ```

1. Ouvrez le fichier **src/components/app.js** qui définit la page d’application de niveau supérieur. Ajoutez la nouvelle page à la liste des importations :

    ```javascript
    // Page Components
    import  {
      //...
      BasicPageContainer
    } from './pages';
    ```

1. Dans le même fichier, ajoutez la nouvelle page au tableau `pagesConfig`. Définissez l’adresse `to` pour l’itinéraire, référencez l’icône SVG et les traductions ajoutées précédemment, et affectez au `component` le conteneur de la page :

    ```js
    const pagesConfig = [
      //...
      {
        to: '/basicpage',
        exact: true,
        svg: svgs.tabs.example,
        labelId: 'tabs.basicPage',
        component: BasicPageContainer
      },
      //...
    ];
    ```

1. Ajoutez toutes les nouvelles barres de navigation au tableau `crumbsConfig` :

    ```js
    const crumbsConfig = [
      //...
      {
        path: '/basicpage', crumbs: [
          { to: '/basicpage', labelId: 'tabs.basicPage' }
        ]
      },
      //...
    ];
    ```

    Cet exemple de page possède uniquement une barre de navigation, mais certaines pages peuvent en avoir plusieurs.

Enregistrez toutes vos modifications. Vous êtes prêt à exécuter l’interface utilisateur web avec votre nouvelle page ajoutée.

### <a name="test-the-new-page"></a>Tester la nouvelle page

À une invite de commandes, accédez à la racine de votre copie locale du dépôt, puis exécutez les commandes suivantes pour installer les bibliothèques requises et exécuter l’interface utilisateur web localement :

```cmd/sh
npm install
npm start
```

La commande précédente exécute l’interface utilisateur localement à l’adresse `http://localhost:3000/dashboard`.

Sans connecter votre instance locale de l’interface utilisateur web à une instance déployée de l’accélérateur de solution, vous voyez des erreurs sur le tableau de bord. Ces erreurs n’affectent pas votre capacité à tester la nouvelle page.

Vous pouvez maintenant modifier le code pendant que le site s’exécute localement et voir la mise à jour dynamique de l’interface utilisateur web.

## <a name="optional-connect-to-deployed-instance"></a>[Facultatif] Se connecter à une instance déployée

Si vous le souhaitez, vous pouvez connecter votre copie locale en cours d’exécution de l’interface utilisateur web à l’accélérateur de solution de supervision à distance dans le cloud :

1. Déployez une instance de **base** de l’accélérateur de solution à l’aide de l’interface CLI **pcs**. Notez le nom de votre déploiement et les informations d’identification que vous avez fournies pour la machine virtuelle. Pour plus d’informations, consultez [Déployer à l’aide de l’interface CLI](iot-accelerators-remote-monitoring-deploy-cli.md).

1. Utilisez le portail Azure ou [l’interface CLI az](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) pour activer l’accès SSH à la machine virtuelle qui héberge les microservices dans votre solution. Par exemple : 

    ```azurecli
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Vous devez uniquement activer l’accès SSH durant le test et le développement. Si vous activez SSH, [vous devez le désactiver à nouveau dès que possible](../security/fundamentals/network-best-practices.md).

1. Utilisez le portail Azure ou [l’interface CLI az](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) pour rechercher le nom et l’adresse IP publique de votre machine virtuelle. Par exemple : 

    ```azurecli
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Utilisez SSH pour vous connecter à la machine virtuelle à l’aide de l’adresse IP de l’étape précédente et des informations d’identification que vous avez fournies lors de l’exécution de **pcs** pour déployer la solution.

1. Pour permettre la connexion de l’expérience utilisateur locale, exécutez les commandes suivantes au niveau du shell bash dans la machine virtuelle :

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. Quand l’exécution de la commande est terminée et que le site web démarre, vous pouvez vous déconnecter de la machine virtuelle.

1. Dans votre copie locale du dépôt [Interface utilisateur web Supervision à distance](https://github.com/Azure/pcs-remote-monitoring-webui), modifiez le fichier **.env** pour ajouter l’URL de votre solution déployée :

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert les ressources disponibles pour personnaliser l’interface utilisateur web dans l’accélérateur de solution de supervision à distance.

Maintenant que vous avez défini une page, l’étape suivante consiste à [Ajouter un service personnalisé à l’interface utilisateur web de l’accélérateur de solution de supervision à distance](iot-accelerators-remote-monitoring-customize-service.md) qui récupère des données à afficher dans l’interface utilisateur.

Pour plus d’informations conceptuelles sur l’accélérateur de solution de supervision à distance, consultez [Architecture de la supervision à distance](iot-accelerators-remote-monitoring-sample-walkthrough.md).
