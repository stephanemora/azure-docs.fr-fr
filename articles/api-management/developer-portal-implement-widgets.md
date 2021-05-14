---
title: Implémenter des widgets dans le portail des développeurs
titleSuffix: Azure API Management
description: Découvrez comment implémenter des widgets qui consomment des données provenant d’API externes et les affichent sur le portail des développeurs Gestion des API.
author: dlepow
ms.author: apimpm
ms.date: 04/15/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: 4bda136a1abe8f9ffb443973731ebbe13b56ac3b
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741193"
---
# <a name="implement-widgets-in-the-developer-portal"></a>Implémenter des widgets dans le portail des développeurs

Dans ce tutoriel, implémentez des widgets qui consomment des données provenant d’API externes et les affichent sur le portail des développeurs Gestion des API.

Le widget récupère les descriptions de session à partir de l’exemple [API de conférence](https://conferenceapi.azurewebsites.net/?format=json). L’identificateur de session est défini à l’aide d’un éditeur de widget désigné.

Pour vous aider dans le processus de développement, reportez-vous au widget terminé situé dans le dossier `examples` du [référentiel GitHub](https://github.com/Azure/api-management-developer-portal/) du portail des développeurs Gestion des API : `/examples/widgets/conference-session`.

:::image type="content" source="media/developer-portal-implement-widgets/widget-published.png" alt-text="Capture d’écran du widget publié":::

## <a name="prerequisites"></a>Prérequis

* Configurez un [environnement local](developer-portal-self-host.md#step-1-set-up-local-environment) pour la dernière version du portail des développeurs.

* Vous devez comprendre l’[anatomie du widget Paperbits](https://paperbits.io/wiki/widget-anatomy).


## <a name="copy-the-scaffold"></a>Copier la structure

Utilisez une structure `widget` à partir du dossier `/scaffolds` comme point de départ pour générer le nouveau widget.

1. Copiez le dossier `/scaffolds/widget` vers `/community/widgets`.
1. Renommez le dossier en `conference-session`.

## <a name="rename-exported-module-classes"></a>Renommer les classes de module exportées

Renommez les classes de module exportées en remplaçant le préfixe `Widget` par `ConferenceSession` dans ces fichiers :

- `widget.design.module.ts`

- `widget.publish.module.ts`

- `widget.runtime.module.ts`
    
Par exemple, dans le fichier `widget.design.module.ts`, remplacez `WidgetDesignModule` par `ConferenceSessionDesignModule` :
    
```typescript
export class WidgetDesignModule implements IInjectorModule {
```
to 
    
```typescript
export class ConferenceSessionDesignModule implements IInjectorModule {
```
    
   
## <a name="register-the-widget"></a>Inscrire le widget

Inscrivez les modules du widget dans les modules racine du portail en ajoutant les lignes suivantes dans les fichiers respectifs :

1. `src/apim.design.module.ts` : un module qui inscrit les dépendances au moment de la conception.

   ```typescript
   import { ConferenceSessionDesignModule } from "../community/widgets/conference-session/widget.design.module";
   
   ...
   injector.bindModule(new ConferenceSessionDesignModule());
   ```
1. `src/apim.publish.module.ts` : un module qui inscrit les dépendances au moment de la publication.

   ```typescript
   import { ConferenceSessionPublishModule } from "../community/widgets/conference-session/widget.publish.module";

    ...

    injector.bindModule(new ConferenceSessionPublishModule());
    ```

1. `src/apim.runtime.module.ts` : dépendances d’exécution.

    ```typescript
    import { ConferenceSessionRuntimeModule } from "../community/widgets/conference-session/widget.runtime.module";

    ...

    injector.bindModule(new ConferenceSessionRuntimeModule());
    ```

## <a name="place-the-widget-in-the-portal"></a>Placer le widget dans le portail

Vous êtes maintenant prêt à intégrer la structure dupliquée et à l’utiliser dans le portail des développeurs.

1. Exécutez la commande `npm start`.

1. Lorsque l’application se charge, placez le nouveau widget sur une page. Vous pouvez le trouver sous le nom `Your widget` dans la catégorie `Community` du sélecteur de widgets.

    :::image type="content" source="media/developer-portal-implement-widgets/widget-selector.png" alt-text="Capture d’écran du sélecteur de widgets":::

1. Enregistrez la page en appuyant sur **Ctrl**+**S** (ou **⌘**+**S** sur macOS).

    > [!NOTE]
    > Au moment de la conception, vous pouvez toujours interagir avec le site web en maintenant la touche **CTRL** (ou **⌘**) enfoncée.

## <a name="add-custom-properties"></a>Ajouter des propriétés personnalisées

Pour que le widget récupère les descriptions de session, il doit être conscient de l’identificateur de session. Ajoutez la propriété `Session ID` aux classes et interfaces respectives :

Pour que le widget récupère la description de session, il doit être conscient de l’identificateur de session. Ajoutez la propriété d’ID de session aux classes et interfaces respectives :

1. `widgetContract.ts` : contrat de données (couche de données) définissant la manière dont la configuration du widget est conservée.

    ```typescript
    export interface WidgetContract extends Contract {
        sessionNumber: string;
    }
    ```

1. `widgetModel.ts` : modèle (couche métier) : représentation principale du widget dans le système. Mis à jour par les éditeurs et rendu par la couche de présentation.

    ```typescript
    export class WidgetModel {
        public sessionNumber: string;
    }
    ```

1. `ko/widgetViewModel.ts` : ViewModel (couche de présentation) : objet spécifique à l’infrastructure d’interface utilisateur que le portail des développeurs affiche avec le modèle HTML.

    > [!NOTE]
    > Vous n’avez pas besoin de modifier quoi que ce soit dans ce fichier.

## <a name="configure-binders"></a>Configurer des classeurs

Activez le flux de `sessionNumber` de la source de données à la présentation du widget. Modifiez les entités `ModelBinder` et `ViewModelBinder` :

1. `widgetModelBinder.ts` vous aide à préparer le modèle avec les données décrites dans le contrat.

    ```typescript
    export class WidgetModelBinder implements IModelBinder<WidgetModel> {
        public async contractToModel(contract: WidgetContract): Promise<WidgetModel> {
            model.sessionNumber = contract.sessionNumber || "107"; // 107 is the default session id
            ...
        }
    
        public modelToContract(model: WidgetModel): Contract {
            const contract: WidgetContract = {
                sessionNumber: model.sessionNumber
                ...
            };
            ...
        }
    }
    ```

1. `ko/widgetViewModelBinder.ts` sait comment le portail des développeurs doit présenter le modèle (en tant que ViewModel) dans une infrastructure d’interface utilisateur spécifique.

    ```typescript
    ...
    public async updateViewModel(model: WidgetModel, viewModel: WidgetViewModel): Promise<void> {
            viewModel.runtimeConfig(JSON.stringify({
                sessionNumber: model.sessionNumber
            }));
        }
    }
    ...
    ```

## <a name="adjust-design-time-widget-template"></a>Ajuster le modèle de widget au moment de la conception

Les composants de chaque étendue s’exécutent indépendamment. Ils ont des conteneurs d’injection de dépendances distincts, leur propre configuration, leur propre cycle de vie, etc. Ils peuvent même être alimentés par différentes infrastructures d’interface utilisateur (dans cet exemple, il s’agit de Knockout JS).

Du point de vue de la conception, tout composant d’exécution est simplement une balise HTML avec certains attributs et/ou un certain contenu. Si nécessaire, la configuration est passée avec un balisage brut. Dans les cas simples, comme dans cet exemple, le paramètre est passé dans l’attribut. Si la configuration est plus complexe, vous pouvez utiliser un identificateur du ou des paramètres requis en le récupérant par un fournisseur de configuration désigné (par exemple, `ISettingsProvider`).

1. Mettez à jour le fichier `ko/widgetView.html` :

    ```html
    <widget-runtime data-bind="attr: { params: runtimeConfig }"></widget-runtime>
    ```

    Lorsque le portail des développeurs exécute la liaison `attr` au moment de la *conception* ou de la *publication*, le code HTML obtenu est le suivant :

    ```html
    <widget-runtime params="{ sessionNumber: 107 }"></widget-runtime>
    ```

    Ensuite, dans le runtime, le composant `widget-runtime` lit `sessionNumber` et l’utilise dans le code d’initialisation (voir ci-dessous).

1. Mettez à jour le fichier `widgetHandlers.ts` pour attribuer l’ID de session à la création :

    ```typescript
    ...
    createModel: async () => {
        var model = new ConferenceSessionModel();
        model.sessionNumber = "107";
            return model;
        }
    ...
    ```

## <a name="revise-runtime-view-model"></a>Modifier le modèle de vue d’exécution

Les composants d’exécution sont le code qui s’exécute sur le site web lui-même. Par exemple, dans le portail des développeurs Gestion des API, il s’agit de tous les scripts sous-jacents à des composants dynamiques (par exemple, les *détails de l’API*, la *console API*), ainsi que des opérations telles que la génération d’exemples de code, l’envoi de requêtes, etc.

Le modèle de vue de votre composant d’exécution doit avoir les méthodes et propriétés suivantes :

- La propriété `sessionNumber` (marquée avec l’élément décoratif `Param`) utilisée comme paramètre d’entrée de composant qui est passé de l’extérieur (le balisage généré au moment de la conception, voir l’étape précédente).
- La propriété `sessionDescription` liée au modèle de widget (voir `widget-runtime.html` plus loin dans cet article).
- La méthode `initialize` (avec l’élément décoratif `OnMounted`) est appelée après la création du widget et l’affectation de tous ses paramètres. Il s’agit d’un bon emplacement pour lire `sessionNumber` et appeler l’API à l’aide de `HttpClient`. `HttpClient` est une dépendance injectée par le conteneur IoC (inversion de contrôle).

- Tout d’abord, le portail des développeurs crée le widget et attribue tous ses paramètres. Il appelle ensuite la méthode `initialize`.

    ```typescript
    ...
    import * as ko from "knockout";
    import { Component, RuntimeComponent, OnMounted, OnDestroyed, Param } from "@paperbits/common/ko/decorators";
    import { HttpClient, HttpRequest } from "@paperbits/common/http";
    ...

    export class WidgetRuntime {
        public readonly sessionDescription: ko.Observable<string>;

        constructor(private readonly httpClient: HttpClient) {
            ...
            this.sessionNumber = ko.observable();
            this.sessionDescription = ko.observable();
            ...
        }

        @Param()
        public readonly sessionNumber: ko.Observable<string>;

        @OnMounted()
        public async initialize(): Promise<void> {
            ...
            const sessionNumber = this.sessionNumber();

            const request: HttpRequest = {
                url: `https://conferenceapi.azurewebsites.net/session/${sessionNumber}`,
                method: "GET"
            };

            const response = await this.httpClient.send<string>(request);
            const sessionDescription = response.toText();
    
            this.sessionDescription(sessionDescription);
            ...
        }
        ...
    }
    ```

## <a name="tweak-the-widget-template"></a>Ajuster le modèle de widget

Mettez à jour votre widget pour afficher la description de la session.

Utilisez une balise de paragraphe et une liaison `markdown` (ou `text` ) dans le fichier `ko/runtime/widget-runtime.html` pour afficher la description :

```html
<p data-bind="markdown: sessionDescription"></p>
```

## <a name="add-the-widget-editor"></a>Ajouter l’éditeur de widget

Le widget est maintenant configuré pour extraire la description de la session `107`. Vous avez spécifié `107` dans le code comme session par défaut. Pour vérifier que vous avez bien effectué ces étapes, exécutez `npm start` et confirmez que le portail des développeurs affiche la description sur la page.

À présent, procédez comme suit pour permettre à l’utilisateur de configurer l’ID de session à l’aide d’un éditeur de widget :

1. Mettez à jour le fichier `ko/widgetEditorViewModel.ts` :

    ```typescript
    export class WidgetEditor implements WidgetEditor<WidgetModel> {
        public readonly sessionNumber: ko.Observable<string>;

        constructor() {
            this.sessionNumber = ko.observable();
        }

        @Param()
        public model: WidgetModel;

        @Event()
        public onChange: (model: WidgetModel) => void;

        @OnMounted()
        public async initialize(): Promise<void> {
            this.sessionNumber(this.model.sessionNumber);
            this.sessionNumber.subscribe(this.applyChanges);
        }

        private applyChanges(): void {
            this.model.sessionNumber = this.sessionNumber();
            this.onChange(this.model);
        }
    }
    ```

    Le modèle de vue de l’éditeur utilise la même approche que celle que vous avez déjà vue, mais il existe une nouvelle propriété `onChange`, décorée avec `@Event()`. Elle relie le rappel pour notifier les écouteurs (dans le cas présent, un éditeur de contenu) des modifications apportées au modèle.

1. Mettez à jour le fichier `ko/widgetEditorView.html` :

    ```html
    <input type="text" class="form-control" data-bind="textInput: sessionNumber" />
    ```

1. Exécutez de nouveau `npm start`. Vous devriez être en mesure de modifier `sessionNumber` dans l’éditeur de widget. Modifiez l’ID en `108`, enregistrez les modifications, puis actualisez l’onglet du navigateur. Si vous rencontrez des problèmes, vous devrez peut-être ajouter à nouveau le widget sur la page.

    :::image type="content" source="media/developer-portal-implement-widgets/widget-editor.png" alt-text="Capture d’écran de l’éditeur de widget":::

## <a name="rename-the-widget"></a>Renommer le widget

Modifiez le nom du widget dans le fichier `constants.ts` :

```typescript
...
export const widgetName = "conference-session";
export const widgetDisplayName = "Conference session";
...
```

> [!NOTE]
> Si vous contribuez au widget dans le référentiel, `widgetName` doit être identique à son nom de dossier et doit être dérivé du nom d’affichage (les minuscules et les espaces sont remplacés par des tirets). La catégorie doit rester `Community`.

## <a name="next-steps"></a>Étapes suivantes


Pour plus d’informations sur le portail des développeurs :

- [Vue d’ensemble du portail des développeurs Gestion des API Azure](api-management-howto-developer-portal.md)

- [Contribuer aux widgets](developer-portal-widget-contribution-guidelines.md) : nous accueillons et encourageons les contributions de la communauté.

- Consultez [Utiliser les widgets de la communauté](developer-portal-use-community-widgets.md) pour apprendre à utiliser les widgets fournis par la communauté.
