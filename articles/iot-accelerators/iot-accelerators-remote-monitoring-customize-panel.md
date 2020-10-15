---
title: Ajouter un panneau à l’interface utilisateur de la solution de supervision à distance - Azure | Microsoft Docs
description: Cet article vous montre comment ajouter un nouveau panneau au tableau de bord dans l’interface utilisateur web de l’accélérateur de solution de supervision à distance.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.custom: devx-track-js
ms.openlocfilehash: 1dcca8409022ba4cf1f988b7c777e3a1fa511060
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91318362"
---
# <a name="add-a-custom-panel-to-the-dashboard-in-the-remote-monitoring-solution-accelerator-web-ui"></a>Ajouter un panneau personnalisé au tableau de bord dans l’interface utilisateur web de l’accélérateur de solution de supervision à distance

Cet article vous montre comment ajouter un nouveau panneau à une page du tableau de bord dans l’interface utilisateur web de l’accélérateur de solution de supervision à distance. Cet article aborde les points suivants :

- Comment préparer un environnement de développement local.
- Comment ajouter un nouveau panneau à une page du tableau de bord dans l’interface utilisateur web.

L’exemple de panneau dans cet article s’affiche dans la page du tableau de bord existante.

## <a name="prerequisites"></a>Conditions préalables requises

Pour effectuer les étapes de ce guide pratique, vous devez avoir installé les logiciels suivants sur votre machine de développement locale :

- [Git](https://git-scm.com/downloads)
- [Node.JS](https://nodejs.org/download/)

## <a name="before-you-start"></a>Avant de commencer

Vous devez effectuer les étapes décrites dans l’article [Ajouter une page personnalisée à l’interface utilisateur web de l’accélérateur de solution de supervision à distance](iot-accelerators-remote-monitoring-customize-page.md) avant de continuer.

## <a name="add-a-panel"></a>Ajouter un panneau

Pour ajouter un panneau à l’interface utilisateur web, vous devez ajouter les fichiers sources qui définissent le panneau, puis modifier le tableau de bord pour afficher le panneau.

### <a name="add-the-new-files-that-define-the-panel"></a>Ajouter les nouveaux fichiers qui définissent le panneau

Pour vous aider à démarrer, le dossier **src/walkthrough/components/pages/dashboard/panels/examplePanel** contient les fichiers qui définissent un panneau, notamment :

**examplePanel.js**

[!code-javascript[Example panel](~/remote-monitoring-webui/src/walkthrough/components/pages/dashboard/panels/examplePanel/examplePanel.js?name=panel "Example panel")]

Copiez le dossier **src/walkthrough/components/pages/dashboard/panels/examplePanel** dans le dossier **src/components/pages/dashboard/panels**.

Ajoutez l’exportation suivante au fichier **src/walkthrough/components/pages/dashboard/panels/index.js** :

```js
export * from './examplePanel';
```

### <a name="add-the-panel-to-the-dashboard"></a>Ajouter le panneau au tableau de bord

Modifiez le fichier **src/components/pages/dashboard/dashboard.js** pour ajouter le panneau.

Ajoutez l’exemple de panneau à la liste des importations à partir de panneaux :

```js
import {
  OverviewPanel,
  AlertsPanel,
  TelemetryPanel,
  AnalyticsPanel,
  MapPanel,
  transformTelemetryResponse,
  chartColorObjects,
  ExamplePanel
} from './panels';
```

Ajoutez la définition de cellule suivante à la grille dans le contenu de la page :

```js
          <Cell className="col-2">
            <ExamplePanel t={t} />
          </Cell>
```

## <a name="test-the-flyout"></a>Tester le menu volant

Si l’interface utilisateur web n’est pas déjà en cours d’exécution localement, exécutez la commande suivante à la racine de votre copie locale du référentiel :

```cmd/sh
npm start
```

La commande précédente exécute l’interface utilisateur localement à l’adresse `http://localhost:3000/dashboard`. Accédez à la page **Tableau de bord** pour afficher le nouveau panneau.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert les ressources disponibles pour ajouter ou personnaliser des tableaux de bord dans l’interface utilisateur web de l’accélérateur de solution de supervision à distance.

Pour plus d’informations conceptuelles sur l’accélérateur de solution de supervision à distance, consultez [Architecture de la supervision à distance](iot-accelerators-remote-monitoring-sample-walkthrough.md).
