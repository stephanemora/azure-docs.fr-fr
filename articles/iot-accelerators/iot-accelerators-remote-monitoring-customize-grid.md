---
title: Ajouter une grille à l’interface utilisateur de la solution de supervision à distance - Azure | Microsoft Docs
description: Cet article vous montre comment ajouter une nouvelle grille à une page dans l’interface utilisateur web de l’accélérateur de solution de supervision à distance.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/04/2018
ms.topic: conceptual
ms.openlocfilehash: e27c1c4303129467c0bd05152570e26f129585a1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82186286"
---
# <a name="add-a-custom-grid-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Ajouter une grille personnalisée à l’interface utilisateur web de l’accélérateur de solution de supervision à distance

Cet article vous montre comment ajouter une nouvelle grille à une page dans l’interface utilisateur web de l’accélérateur de solution de supervision à distance. Cet article aborde les points suivants :

- Comment préparer un environnement de développement local.
- Comment ajouter une nouvelle grille à une page dans l’interface utilisateur web.

L’exemple de grille de cet article affiche les données du service que l’article sur les procédures [Ajouter un service personnalisé à l’interface utilisateur web de l’accélérateur de solution de supervision à distance](iot-accelerators-remote-monitoring-customize-service.md) vous montre comment ajouter.

## <a name="prerequisites"></a>Conditions préalables requises

Pour effectuer les étapes de ce guide pratique, vous devez avoir installé les logiciels suivants sur votre machine de développement locale :

- [Git](https://git-scm.com/downloads)
- [Node.JS](https://nodejs.org/download/)

## <a name="before-you-start"></a>Avant de commencer

Vous devez effectuer les étapes des articles suivants avant de continuer :

- [Ajouter une page personnalisée à l’interface utilisateur web de l’accélérateur de solution de supervision à distance](iot-accelerators-remote-monitoring-customize-page.md)
- [Ajouter un service personnalisé à l’interface utilisateur web de l’accélérateur de solution de supervision à distance](iot-accelerators-remote-monitoring-customize-service.md)

## <a name="add-a-grid"></a>Ajouter une grille

Pour ajouter une grille à l’interface utilisateur web, vous devez ajouter les fichiers sources qui définissent la grille et modifier certains fichiers existants pour que l’interface utilisateur web ait connaissance du nouveau composant.

### <a name="add-the-new-files-that-define-the-grid"></a>Ajouter les nouveaux fichiers qui définissent la grille

Pour vous aider à démarrer, le dossier **src/walkthrough/components/pages/pageWithGrid/exampleGrid** contient les fichiers qui définissent une grille :

**exampleGrid.js**

[!code-javascript[Example grid](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithGrid/exampleGrid/exampleGrid.js?name=grid "Example grid")]

**exampleGridConfig.js**

[!code-javascript[Example grid configuration](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithGrid/exampleGrid/exampleGridConfig.js?name=gridconfig "Example grid configuration")]

Copiez le dossier **src/walkthrough/components/pages/pageWithGrid/exampleGrid** dans le dossier **src/components/pages/example**.

### <a name="add-the-grid-to-the-page"></a>Ajouter la grille à la page

Modifiez le fichier **src/components/pages/example/basicPage.container.js** comme suit pour importer les définitions de service :

```js
import { connect } from 'react-redux';
import { translate } from 'react-i18next';

import {
  epics as exampleEpics,
  getExamples,
  getExamplesError,
  getExamplesLastUpdated,
  getExamplesPendingStatus
} from 'store/reducers/exampleReducer';
import { BasicPage } from './basicPage';

// Pass the data
const mapStateToProps = state => ({
  data: getExamples(state),
  error: getExamplesError(state),
  isPending: getExamplesPendingStatus(state),
  lastUpdated: getExamplesLastUpdated(state)
});

// Wrap the dispatch method
const mapDispatchToProps = dispatch => ({
  fetchData: () => dispatch(exampleEpics.actions.fetchExamples())
});

export const BasicPageContainer = translate()(connect(mapStateToProps, mapDispatchToProps)(BasicPage));
```

Modifiez le fichier **src/components/pages/example/basicPage.js** comme suit pour ajouter la grille :

```js
// Copyright (c) Microsoft. All rights reserved.

import React, { Component } from 'react';

import {
  AjaxError,
  ContextMenu,
  PageContent,
  RefreshBar
} from 'components/shared';
import { ExampleGrid } from './exampleGrid';

import './basicPage.css';

export class BasicPage extends Component {
  constructor(props) {
    super(props);
    this.state = { contextBtns: null };
  }

  componentDidMount() {
    const { isPending, lastUpdated, fetchData } = this.props;
    if (!lastUpdated && !isPending) fetchData();
  }

  onGridReady = gridReadyEvent => this.gridApi = gridReadyEvent.api;

  onContextMenuChange = contextBtns => this.setState({ contextBtns });

  render() {
    const { t, data, error, isPending, lastUpdated, fetchData } = this.props;
    const gridProps = {
      onGridReady: this.onGridReady,
      rowData: isPending ? undefined : data || [],
      onContextMenuChange: this.onContextMenuChange,
      t: this.props.t
    };

    return [
      <ContextMenu key="context-menu">
        {this.state.contextBtns}
      </ContextMenu>,
      <PageContent className="basic-page-container" key="page-content">
        <RefreshBar refresh={fetchData} time={lastUpdated} isPending={isPending} t={t} />
        {!!error && <AjaxError t={t} error={error} />}
        {!error && <ExampleGrid {...gridProps} />}
      </PageContent>
    ];
  }
}
```

Modifiez le fichier **src/components/pages/example/basicPage.test.js** comme suit pour mettre à jour les tests :

```js
// Copyright (c) Microsoft. All rights reserved.

import React from 'react';
import { shallow } from 'enzyme';
import 'polyfills';

import { BasicPage } from './basicPage';

describe('BasicPage Component', () => {
  it('Renders without crashing', () => {

    const fakeProps = {
      data: undefined,
      error: undefined,
      isPending: false,
      lastUpdated: undefined,
      fetchData: () => { },
      t: () => { },
    };

    const wrapper = shallow(
      <BasicPage {...fakeProps} />
    );
  });
});
```

## <a name="test-the-grid"></a>Tester la grille

Si l’interface utilisateur web n’est pas déjà en cours d’exécution localement, exécutez la commande suivante à la racine de votre copie locale du référentiel :

```cmd/sh
npm start
```

La commande précédente exécute l’interface utilisateur localement à l’adresse `http://localhost:3000/dashboard`. Accédez à la page **Exemple** pour voir la grille afficher des données à partir du service.

## <a name="select-rows"></a>Sélectionner des lignes

Il existe deux options pour permettre à un utilisateur de sélectionner des lignes dans la grille :

### <a name="hard-select-rows"></a>Sélectionner définitivement (hard-select) des lignes

Si un utilisateur doit agir sur plusieurs lignes en même temps, utilisez des cases à cocher sur les lignes :

1. Activez la sélection définitive des lignes en ajoutant un **checkboxColumn** aux **columnDefs** fournis dans la grille. **checkboxColumn** est défini dans **/src/components/shared/pcsGrid/pcsGrid.js** :

    ```js
    this.columnDefs = [
      checkboxColumn,
      exampleColumnDefs.id,
      exampleColumnDefs.description
    ];
    ```

1. Pour accéder aux éléments sélectionnés, vous obtenez une référence à l’API de grille interne :

    ```js
    onGridReady = gridReadyEvent => {
      this.gridApi = gridReadyEvent.api;
      // Call the onReady props if it exists
      if (isFunc(this.props.onGridReady)) {
        this.props.onGridReady(gridReadyEvent);
      }
    };
    ```

1. Ajoutez des boutons contextuels à la page quand une ligne de la grille est sélectionnée définitivement :

    ```js
    this.contextBtns = [
      <Btn key="context-btn-1" svg={svgs.reconfigure} onClick={this.doSomething()}>Button 1</Btn>,
      <Btn key="context-btn-2" svg={svgs.trash} onClick={this.doSomethingElse()}>Button 2</Btn>
    ];
    ```

    ```js
    onHardSelectChange = (selectedObjs) => {
      const { onContextMenuChange, onHardSelectChange } = this.props;
      // Show the context buttons when there are rows checked.
      if (isFunc(onContextMenuChange)) {
        onContextMenuChange(selectedObjs.length > 0 ? this.contextBtns : null);
      }
      //...
    }
    ```

1. Quand vous cliquez sur un bouton contextuel, les éléments sélectionnés définitivement font votre travail sur :

    ```js
    doSomething = () => {
      //Just for demo purposes. Don't console log in a real grid.
      console.log('Hard selected rows', this.gridApi.getSelectedRows());
    };
    ```

### <a name="soft-select-rows"></a>Sélectionner provisoirement (soft-select) des lignes

Si l’utilisateur doit agir sur une seule ligne, configurez un lien de sélection provisoire pour une ou plusieurs colonnes dans **columnDefs**.

1. Dans **exampleGridConfig.js**, ajoutez **SoftSelectLinkRenderer** comme **cellRendererFramework** pour **columnDef**.

    ```js
    export const exampleColumnDefs = {
      id: {
        headerName: 'examples.grid.name',
        field: 'id',
        sort: 'asc',
        cellRendererFramework: SoftSelectLinkRenderer
      }
    };
    ```

1. Quand vous cliquez sur un lien de sélection provisoire, il déclenche l’événement **onSoftSelectChange**. Effectuez l’action souhaitée pour cette ligne, telle que l’ouverture d’un menu volant de détails. Cet exemple écrit simplement dans la console :

    ```js
    onSoftSelectChange = (rowId, rowData) => {
      //Note: only the Id is reliable, rowData may be out of date
      const { onSoftSelectChange } = this.props;
      if (rowId) {
        //Just for demo purposes. Don't console log a real grid.
        console.log('Soft selected', rowId);
        this.setState({ softSelectedId: rowId });
      }
      if (isFunc(onSoftSelectChange)) {
        onSoftSelectChange(rowId, rowData);
      }
    }
    ```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert les ressources disponibles pour ajouter ou personnaliser des pages dans l’interface utilisateur web de l’accélérateur de solution de supervision à distance.

Maintenant que vous avez défini une grille, l’étape suivante consiste à [Ajouter un menu volant personnalisé à l’interface utilisateur web de l’accélérateur de solution de supervision à distance](iot-accelerators-remote-monitoring-customize-flyout.md) qui s’affiche dans l’exemple de page.

Pour plus d’informations conceptuelles sur l’accélérateur de solution de supervision à distance, consultez [Architecture de la supervision à distance](iot-accelerators-remote-monitoring-sample-walkthrough.md).
