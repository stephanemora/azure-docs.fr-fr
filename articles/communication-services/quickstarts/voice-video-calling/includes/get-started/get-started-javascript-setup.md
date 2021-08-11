---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 5e09a3cbeac53393c686ceb298a217e1626442da
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2021
ms.locfileid: "112536280"
---
## <a name="setting-up"></a>Configuration

### <a name="create-a-new-nodejs-application"></a>Création d’une application Node.js

Ouvrez votre fenêtre de terminal ou de commande, créez un répertoire pour votre application, puis accédez-y.

```console
mkdir calling-quickstart && cd calling-quickstart
```

Exécutez `npm init -y` pour créer un fichier **package.json** avec les paramètres par défaut.

```console
npm init -y
```

### <a name="install-the-package"></a>Installer le package

Utilisez la commande `npm install` pour installer le kit de développement logiciel (SDK) Azure Communication Services Calling pour JavaScript.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling@1.1.0 --save
```

Les versions suivantes de webpack sont recommandées pour ce guide de démarrage rapide :

```console
"webpack": "^4.42.0",
"webpack-cli": "^3.3.11",
"webpack-dev-server": "^3.10.3"
```

L’option `--save` liste la bibliothèque comme dépendance dans votre fichier **package.json**.

### <a name="set-up-the-app-framework"></a>Configurer le framework d’application

Ce guide de démarrage rapide utilise webpack pour regrouper les ressources de l’application. Exécutez la commande suivante pour installer les packages npm webpack, webpack-cli et webpack-dev-server, puis listez-les comme dépendances de développement dans votre fichier **package.json** :

```console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```

Créez un fichier **index.html** dans le répertoire racine de votre projet. Nous utiliserons ce fichier pour configurer une disposition de base qui permettra à l’utilisateur d’effectuer un appel.
