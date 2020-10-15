---
title: Ajouter un menu volant à l’interface utilisateur de la solution de supervision à distance - Azure | Microsoft Docs
description: Cet article vous montre comment ajouter un nouveau menu volant dans une page de l’interface utilisateur web de l’accélérateur de solution de supervision à distance.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.custom: devx-track-js
ms.openlocfilehash: afd8c5b01b9f36606bf6bd43fbed747ca83ba320
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91318413"
---
# <a name="add-a-custom-flyout-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Ajouter un menu volant personnalisé à l’interface utilisateur web de l’accélérateur de solution de supervision à distance

Cet article vous montre comment ajouter un nouveau menu volant dans une page de l’interface utilisateur web de l’accélérateur de solution de supervision à distance. Cet article aborde les points suivants :

- Comment préparer un environnement de développement local.
- Comment ajouter un nouveau menu volant dans une page de l’interface utilisateur web.

L’exemple de menu volant de cet article s’affiche dans la page avec la grille que le guide pratique [Ajouter une grille personnalisée à l’interface utilisateur web de l’accélérateur de solution de supervision à distance](iot-accelerators-remote-monitoring-customize-grid.md) vous montre comment ajouter.

## <a name="prerequisites"></a>Conditions préalables requises

Pour effectuer les étapes de ce guide pratique, vous devez avoir installé les logiciels suivants sur votre machine de développement locale :

- [Git](https://git-scm.com/downloads)
- [Node.JS](https://nodejs.org/download/)

## <a name="before-you-start"></a>Avant de commencer

Vous devez effectuer les étapes des articles suivants avant de continuer :

- [Ajouter une page personnalisée à l’interface utilisateur web de l’accélérateur de solution de supervision à distance](iot-accelerators-remote-monitoring-customize-page.md)
- [Ajouter un service personnalisé à l’interface utilisateur web de l’accélérateur de solution de supervision à distance](iot-accelerators-remote-monitoring-customize-service.md)
- [Ajouter une grille personnalisée à l’interface utilisateur web de l’accélérateur de solution de supervision à distance](iot-accelerators-remote-monitoring-customize-grid.md)

## <a name="add-a-flyout"></a>Ajouter un menu volant

Pour ajouter un menu volant à l’interface utilisateur web, vous devez ajouter les fichiers sources qui définissent le menu volant et modifier certains fichiers existants pour que l’interface utilisateur web ait connaissance du nouveau composant.

### <a name="add-the-new-files-that-define-the-flyout"></a>Ajouter les nouveaux fichiers qui définissent le menu volant

Pour vous aider à démarrer, le dossier **src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout** contient les fichiers qui définissent un menu volant :

**exampleFlyout.container.js**

[!code-javascript[Example flyout container](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.container.js?name=flyoutcontainer "Example flyout container")]

**exampleFlyout.js**

[!code-javascript[Example flyout](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.js?name=flyout "Example flyout")]

Copiez le dossier **src/walkthrough/components/pages/pageWithFlyout/flyouts** dans le dossier **src/components/pages/example**.

### <a name="add-the-flyout-to-the-page"></a>Ajouter le menu volant dans la page

Modifiez le fichier **src/components/pages/example/basicPage.js** pour ajouter le menu volant.

Ajoutez **Btn** aux importations qui sont dans **components/shared** et ajoutez les importations pour **svgs** et **ExampleFlyoutContainer** :

```js
import {
  AjaxError,
  ContextMenu,
  PageContent,
  RefreshBar,
  Btn
} from 'components/shared';
import { ExampleGrid } from './exampleGrid';
import { svgs } from 'utilities';
import { ExampleFlyoutContainer } from './flyouts/exampleFlyout';
```

Ajoutez une définition **const** pour **closedFlyoutState** et ajoutez-le à l’état dans le constructeur :

```js
const closedFlyoutState = { openFlyoutName: undefined };

export class BasicPage extends Component {
  constructor(props) {
    super(props);
    this.state = { contextBtns: null, closedFlyoutState };
  }
```

Ajoutez les fonctions suivantes à la classe **BasicPage** :

```js
  closeFlyout = () => this.setState(closedFlyoutState);

  openFlyout = (name) => () => this.setState({ openFlyoutName: name });
```

Ajoutez les définitions **const** suivantes dans la fonction **render** :

```js
    const { openFlyoutName } = this.state;

    const isExampleFlyoutOpen = openFlyoutName === 'example';
```

Ajoutez un bouton pour ouvrir le menu volant au menu contextuel :

```js
      <ContextMenu key="context-menu">
        {this.state.contextBtns}
        <Btn svg={svgs.reconfigure} onClick={this.openFlyout('example')}>{t('walkthrough.pageWithFlyout.open')}</Btn>
      </ContextMenu>,
```

Ajoutez du texte et le conteneur du menu volant au contenu de la page :

```js
      <PageContent className="basic-page-container" key="page-content">
        {t('walkthrough.pageWithFlyout.pageBody')}
        { isExampleFlyoutOpen && <ExampleFlyoutContainer onClose={this.closeFlyout} /> }
        <RefreshBar refresh={fetchData} time={lastUpdated} isPending={isPending} t={t} />
        {!!error && <AjaxError t={t} error={error} />}
        {!error && <ExampleGrid {...gridProps} />}
      </PageContent>
```

## <a name="test-the-flyout"></a>Tester le menu volant

Si l’interface utilisateur web n’est pas déjà en cours d’exécution localement, exécutez la commande suivante à la racine de votre copie locale du référentiel :

```cmd/sh
npm start
```

La commande précédente exécute l’interface utilisateur localement à l’adresse `http://localhost:3000/dashboard`. Accédez à la page **Example** et cliquez sur **Open Flyout**.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert les ressources disponibles pour ajouter ou personnaliser des pages dans l’interface utilisateur web de l’accélérateur de solution de supervision à distance.

Maintenant que vous avez défini un menu volant dans une page, l’étape suivante consiste à [Ajouter un panneau au tableau de bord dans l’interface utilisateur web de l’accélérateur de solution de supervision à distance](iot-accelerators-remote-monitoring-customize-panel.md).

Pour plus d’informations conceptuelles sur l’accélérateur de solution de supervision à distance, consultez [Architecture de la supervision à distance](iot-accelerators-remote-monitoring-sample-walkthrough.md).
