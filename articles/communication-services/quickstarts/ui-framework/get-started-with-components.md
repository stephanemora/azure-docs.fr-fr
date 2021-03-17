---
title: Bien démarrer avec les composants de base de l’infrastructure d’interface utilisateur d’Azure Communication Services
titleSuffix: An Azure Communication Services quickstart
description: Dans ce guide de démarrage rapide, vous allez découvrir comment démarrer avec les composants de base de l’infrastructure d’interface utilisateur
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 6f4a8e8f26e88a73fc73c309ef336813282589f3
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488174"
---
# <a name="quickstart-get-started-with-ui-framework-base-components"></a>Démarrage rapide : Bien démarrer avec les composants de base de l’infrastructure d’interface utilisateur

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Commencez avec Azure Communication Services à l’aide de l’infrastructure d’interface utilisateur pour intégrer rapidement les expériences de communication à vos applications. Dans ce guide de démarrage rapide, vous allez découvrir comment intégrer des composants de base de l’infrastructure d’interface utilisateur à votre application pour générer des expériences de communication. 

Il existe deux types de composants d’infrastructure d’interface utilisateur : de base et composite.

- Les **composants de base** représentent les fonctionnalités de communication discrètes. Il s’agit des modules de base qui peuvent être utilisés pour générer des expériences de communication complexes. 
- Les **composants composites** sont des expériences clé en main pour les scénarios de communication courants qui ont été générés à l’aide de **composants de base** comme modules et empaquetés pour être facilement intégrés à des applications.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) versions Active LTS et Maintenance LTS (Nœud 12 recommandé).
- Une ressource Communication Services active. [Créez une ressource Communication Services](./../create-communication-resource.md).
- Un jeton d’accès utilisateur pour instancier le client d’appel. Découvrez comment [créer et gérer des jetons d’accès utilisateur](./../access-tokens.md)

## <a name="setting-up"></a>Configuration

L’infrastructure d’interface utilisateur nécessite qu’un environnement React soit configuré. C’est ce que nous allons faire maintenant. Si vous avez déjà eu une application React, vous pouvez ignorer cette section.

### <a name="set-up-react-app"></a>Configurer une application React

Dans le cadre de ce guide de démarrage rapide, nous allons utiliser le modèle create-react-app. Pour plus d'informations, consultez les pages suivantes : [Bien démarrer avec React](https://reactjs.org/docs/create-a-new-react-app.html)

```console

npx create-react-app my-app

cd my-app

```

À la fin de ce processus, vous devez disposer d’une application complète dans le dossier `my-app`. Pour ce guide de démarrage rapide, nous allons modifier des fichiers se trouvant dans le dossier `src`.

### <a name="install-the-package"></a>Installer le package

Utilisez la commande `npm install` pour installer la bibliothèque de client Azure Communication Services Calling pour JavaScript. Déplacez le tarball fourni (préversion privée) vers le répertoire my-app.

```console

//For Private Preview install tarball

npm install --save ./{path for tarball}

```

L’option `--save` liste la bibliothèque comme dépendance dans votre fichier **package.json**.

### <a name="run-create-react-app"></a>Exécuter le modèle de création d’application React

Testons l’installation de l’application React créée en exécutant :

```console

npm run start   

```

## <a name="object-model"></a>Modèle objet

Les classes et les interfaces suivantes gèrent certaines des principales fonctionnalités de la bibliothèque cliente d’interface utilisateur Azure Communication Services :

| Name                                  | Description                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| Fournisseur| Fournisseur d’interface utilisateur Fluent qui permet aux développeurs de modifier les composants d’interface utilisateur Fluent sous-jacents|
| CallingProvider| Fournisseur appelant utilisé pour instancier un appel. Nécessaire pour ajouter des composants supplémentaires|
| ChatProvider | Fournisseur de conversation utilisé pour instancier un thread de conversation. Nécessaire pour ajouter des composants supplémentaires|
| MediaGallery | Composant de base qui affiche les participants à l’appel et leurs flux vidéo distants |
| MediaControls | Composant de base pour contrôler l’appel, notamment la désactivation du son, la vidéo, le partage de l’écran |
| ChatThread | Composant de base qui restitue un thread de conversation avec des indicateurs de saisie, des confirmations de lecture, etc. |
| SendBox | Composant de base permettant à l’utilisateur d’entrer des messages qui seront envoyés au thread joint|

## <a name="initialize-calling-and-chat-providers-using-azure-communication-services-credentials"></a>Initialiser des Fournisseurs d’appel et de conversation à l’aide d’informations d’identification Azure Communication Services

Accédez au dossier `src` se trouvant dans `my-app`, puis recherchez le fichier `app.js`. Ici, nous allons supprimer le code suivant pour initialiser nos fournisseurs d’appel et de conversation. Ces fournisseurs sont chargés de la maintenance du contexte des expériences d’appel et de conversation. Vous pouvez choisir celui à utiliser en fonction du type d’expérience de communication que vous générez. Si nécessaire, vous pouvez utiliser les deux en même temps. Pour initialiser les composants, vous avez besoin d’un jeton d’accès récupéré à partir d’Azure Communication Services. Pour plus d’informations sur l’obtention de jetons d’accès, consultez [Créer et gérer des jetons d’accès](./../access-tokens.md).

> [!NOTE]
> Les composants ne génèrent pas de jetons d’accès, d’ID de groupe ou d’ID de thread. Ces éléments proviennent de services qui suivent les étapes appropriées permettant de générer ces ID et de les transmettre à l’application cliente. Pour plus d'informations, consultez les pages suivantes : [Architecture du serveur client](./../../concepts/client-and-server-architecture.md).
> 
> Par exemple : Le Fournisseur de conversation s’attend à ce que le `userId` associé au jeton (`token`) utilisé pour l’initialiser ait déjà été joint au `threadId` fourni. Si le jeton n’a pas été joint à l’ID de thread, le Fournisseur de conversation échoue. Pour plus d’informations sur les conversations, consultez : [Bien démarrer avec les conversations](./../chat/get-started.md)

Nous allons utiliser un thème de l’interface utilisateur Fluent pour améliorer l’apparence de l’application :

`App.js`
```javascript

import {CallingProvider, ChatProvider} from "@azure/acs-ui-sdk"
import { mergeThemes, teamsTheme } from '@fluentui/react-northstar';
import { Provider } from '@fluentui/react-northstar/dist/commonjs/components/Provider/Provider';
import { svgIconStyles } from '@fluentui/react-northstar/dist/es/themes/teams/components/SvgIcon/svgIconStyles';
import { svgIconVariables } from '@fluentui/react-northstar/dist/es/themes/teams/components/SvgIcon/svgIconVariables';
import * as siteVariables from '@fluentui/react-northstar/dist/es/themes/teams/siteVariables';

const iconTheme = {
  componentStyles: {
    SvgIcon: svgIconStyles
  },
  componentVariables: {
    SvgIcon: svgIconVariables
  },
  siteVariables
};

function App(props) {

  return (
    <Provider theme={mergeThemes(iconTheme, teamsTheme)}>
        <CallingProvider
        displayName={/*Insert Display Name to be used for the user*/}
        groupId={/*Insert GUID for group call to be joined*/}
        token={/*Insert the Azure Communication Services access token*/}
        refreshTokenCallback={/*Optional, Insert refresh token call back function*/}
        >
            // Add Calling Components Here
        </CallingProvider>

        {/*Note: Make sure that the userId associated to the token has been added to the provided threadId*/}

        <ChatProvider
        token={/*Insert the Azure Communication Services access token*/}
        displayName={/*Insert Display Name to be used for the user*/}
        threadId={/*Insert id for group chat thread to be joined*/}
        endpointUrl={/*Insert the environment URL for the Azure Resource used*/}
        refreshTokenCallback={/*Optional, Insert refresh token call back function*/}
        >
            //  Add Chat Components Here
        </ChatProvider>
    </Provider>
  );
}

export default App;

```

Une fois initialisé, ce fournisseur vous permet de générer votre propre disposition à l’aide de composants de base de l’infrastructure d’interface utilisateur et de toute logique de disposition supplémentaire. Le fournisseur s’occupe de l’initialisation de toute la logique sous-jacente et de la connexion correcte des différents composants entre eux. Nous allons ensuite utiliser différents composants de base fournis par l’infrastructure d’interface utilisateur pour générer des expériences de communication. Vous pouvez personnaliser la disposition de ces composants et ajouter tous les autres composants personnalisés que vous voulez restituer avec eux. 

## <a name="build-ui-framework-calling-component-experiences"></a>Générer des expériences de composants d’appel d’infrastructure d’interface utilisateur

Pour les appels, nous allons utiliser les composants `MediaGallery` et `MediaControls`. Pour plus d’informations à leur sujet, consultez [Fonctionnalités de l’infrastructure d’interface utilisateur](./../../concepts/ui-framework/ui-sdk-features.md). Pour commencer, dans le dossier `src`, créez un fichier appelé `CallingComponents.js`. Ici, nous allons initialiser un composant de fonction qui contiendra nos composants de base à importer ensuite dans `app.js`. Vous pouvez ajouter une disposition et des styles supplémentaires autour des composants. 

`CallingComponents.js`
```javascript

import {MediaGallery, MediaControls, MapToCallConfigurationProps, connectFuncsToContext} from "@azure/acs-ui-sdk"

function CallingComponents(props) {

  if (props.isCallInitialized) {props.joinCall()}

  return (
    <div style = {{height: '35rem', width: '30rem', float: 'left'}}>
        <MediaGallery/>
        <MediaControls/>
    </div>
  );
}

export default connectFuncsToContext(CallingComponents, MapToCallConfigurationProps);

```

En bas de ce fichier, nous avons exporté les composants d’appel à l’aide de la méthode `connectFuncsToContext` à partir de l’infrastructure d’interface utilisateur pour connecter les composants d’interface utilisateur d’appel à l’état sous-jacent à l’aide des propriétés `MapToCallingSetupProps`. Cette méthode entraîne le remplissage des propriétés du composant, que nous utiliserons ensuite pour vérifier l’état et rejoindre l’appel. La propriété `isCallInitialized` permet de vérifier si l’agent `CallAgent` est prêt. Nous utilisons ensuite la méthode `joinCall` pour rejoindre l’appel. L’infrastructure d’interface utilisateur prend en charge les fonctions de mappage personnalisées à utiliser dans les scénarios où les développeurs veulent contrôler la façon dont les données sont envoyées (par push) aux composants.

## <a name="build-ui-framework-chat-component-experiences"></a>Générer des expériences de composants de conversation d’infrastructure d’interface utilisateur

Pour la conversation, nous allons utiliser les composants `ChatThread` et `SendBox`. Pour plus d’informations sur ces composants, consultez [Fonctionnalités de l’infrastructure d’interface utilisateur](./../../concepts/ui-framework/ui-sdk-features.md). Pour commencer, dans le dossier `src`, créez un fichier appelé `ChatComponents.js`. Ici, nous allons initialiser un composant de fonction qui contiendra nos composants de base à importer ensuite dans `app.js`.

`ChatComponents.js`
```javascript

import {ChatThread, SendBox} from '@azure/acs-ui-sdk'

function ChatComponents() {

  return (
    <div style = {{height: '35rem', width: '30rem', float: 'left'}}>
        <ChatThread />
        <SendBox />
    </div >
  );
}

export default ChatComponents;

```

## <a name="add-calling-and-chat-components-to-the-main-application"></a>Ajouter des composants d’appel et de conversation à l’application principale

De retour dans le fichier `app.js`, nous allons maintenant ajouter les composants aux fournisseurs `CallingProvider` et `ChatProvider` comme indiqué ci-dessous.

`App.js`
```javascript

import ChatComponents from './ChatComponents';
import CallingComponents from './CallingComponents';

<Provider ... >
    <CallingProvider .... >
        <CallingComponents/>
    </CallingProvider>

    <ChatProvider .... >
        <ChatComponents />
    </ChatProvider>
</Provider>

```

## <a name="run-quickstart"></a>Exécuter le démarrage rapide

Pour exécuter le code ci-dessus, utilisez la commande :

```console

npm run start   

```

Pour tester entièrement les fonctionnalités, vous devez disposer d’un deuxième client bénéficiant de la fonctionnalité d’appel et de conversation pour joindre l’appel et le thread de conversation. Consultez notre [exemple de bannière d’appel](./../../samples/calling-hero-sample.md) et notre [exemple de bannière de conversation](./../../samples/chat-hero-sample.md) comme options potentielles.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous voulez nettoyer et supprimer un abonnement Communication Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées. Apprenez-en davantage sur le [nettoyage des ressources](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Essayer les composants composites de l’infrastructure d’interface utilisateur](./get-started-with-composites.md)

Pour plus d’informations, consultez les ressources suivantes :
- [Vue d’ensemble de l’infrastructure d’interface utilisateur](../../concepts/ui-framework/ui-sdk-overview.md)
- [Fonctionnalités de l’infrastructure d’interface utilisateur](./../../concepts/ui-framework/ui-sdk-features.md)
