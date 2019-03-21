---
title: Interagir avec des rapports Power BI à l’aide de l’API JavaScript | Microsoft Docs
description: L’API JavaScript de Power BI vous permet d’incorporer facilement des rapports Power BI dans vos applications.
services: power-bi-embedded
author: markingmyname
ROBOTS: NOINDEX
ms.assetid: bdd885d3-1b00-4dcf-bdff-531eb1f97bfb
ms.service: power-bi-embedded
ms.topic: conceptual
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: d5a411e227704eb80b0020f68fad072491576f18
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57883795"
---
# <a name="interact-with-power-bi-reports-using-the-javascript-api"></a>Interagir avec des rapports Power BI à l’aide de l’API JavaScript

L’API JavaScript de Power BI vous permet d’incorporer facilement des rapports Power BI dans vos applications. Avec l’API, vos applications peuvent interagir par programmation avec différents éléments d’un rapport, comme des pages et des filtres. Cette interactivité intègre les rapports Power BI plus étroitement dans votre application.

> [!IMPORTANT]
> Les collections d’espaces de travail Power BI sont déconseillées et disponibles jusqu’en juin 2018 ou jusqu’à la date indiquée sur votre contrat. Nous vous conseillons de planifier votre migration vers Power BI Embedded pour éviter toute interruption dans votre application. Pour plus d’informations sur la migration de vos données vers Power BI Embedded, consultez l’article [How to migrate Power BI Workspace Collections content to Power BI Embedded (Migration du contenu de collections d’espaces de travail Power BI vers Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Vous incorporez un rapport Power BI dans votre application en utilisant un iframe qui est hébergé dans le cadre de l’application. Comme vous pouvez le voir dans l’image suivante, l’iframe agit comme une barrière entre votre application et le rapport :

![Iframe de collection d’espace de travail BI Power sans API JavaScript](media/interact-with-reports/iframe-without-javacript.png)

L’iframe facilite considérablement le processus d’incorporation, mais sans l’API JavaScript, le rapport et votre application ne peuvent pas interagir entre eux. Ce manque d’interaction peut faire penser que le rapport ne fait pas vraiment partie de l’application. Le rapport et l’application doivent véritablement communiquer entre eux, comme dans l’image suivante :

![Iframe de collection d’espace de travail BI Power avec l’API JavaScript](media/interact-with-reports/iframe-with-javascript.png)

L’API JavaScript de Power BI vous permet d’écrire du code capable de traverser en toute sécurité la limite de l’iframe. Cela permet à votre application d’exécuter par programmation une action dans un rapport et d’écouter des événements sur les actions effectuées par les utilisateurs dans le rapport.

## <a name="what-can-you-do-with-the-power-bi-javascript-api"></a>Que pouvez-vous faire avec l’API JavaScript de Power BI ?

Avec l’API JavaScript, vous pouvez gérer des rapports, accéder aux pages d’un rapport, filtrer un rapport et gérer l’incorporation des événements. Le schéma suivant présente la structure de l’API.

![Diagramme de l’API JavaScript de Power BI](media/interact-with-reports/javascript-api-diagram.png)

### <a name="manage-reports"></a>Gérer les rapports
L’API Javascript vous permet de gérer le comportement au niveau du rapport et de la page :

* Incorporer un rapport Power BI spécifique en toute sécurité dans votre application : essayer l’ [application de démonstration Embed](https://azure-samples.github.io/powerbi-angular-client/#/scenario1)
  * Définir le jeton d’accès
* Configurer le rapport
  * Activer et désactiver le volet de filtre et le volet de navigation entre les pages : essayez l’ [application de démonstration Update Settings](https://azure-samples.github.io/powerbi-angular-client/#/scenario6)
  * Définir les valeurs par défaut des pages et des filtres : essayez l’ [application de démonstration Set Defaults](https://azure-samples.github.io/powerbi-angular-client/#/scenario5)
* Afficher et quitter le mode plein écran

[En savoir plus sur l’incorporation d’un rapport](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embedding-Basics)

### <a name="navigate-to-pages-in-a-report"></a>Accéder aux pages d’un rapport
L’API JavaScript vous permet de découvrir toutes les pages d’un rapport et de définir la page actuelle. Essayez l’ [application de démonstration Navigation](https://azure-samples.github.io/powerbi-angular-client/#/scenario3).

[En savoir plus sur la navigation entre les pages](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Page-Navigation)

### <a name="filter-a-report"></a>Filtrer un rapport
L’API JavaScript fournit les fonctionnalités de filtrage de base et avancées pour les rapports incorporés et les pages de rapport. Essayez l’ [application de démonstration Filter](https://azure-samples.github.io/powerbi-angular-client/#/scenario4)et examinez le code de présentation ici.

#### <a name="basic-filters"></a>Filtres de base
Un filtre de base est placé sur une colonne ou un niveau hiérarchique, et contient une liste de valeurs à inclure ou exclure.

```typescript
const basicFilter: pbi.models.IBasicFilter = {
  $schema: "https://powerbi.com/product/schema#basic",
  target: {
    table: "Store",
    column: "Count"
  },
  operator: "In",
  values: [1,2,3,4]
}
```

#### <a name="advanced-filters"></a>Filtres avancés
Les filtres avancés utilisent l’opérateur logique AND ou OR, et acceptent une ou deux conditions, chacune avec son propre opérateur et sa propre valeur. Les conditions prises en charge sont les suivantes :

* Aucun
* LessThan
* LessThanOrEqual
* GreaterThan
* GreaterThanOrEqual
* Contains
* DoesNotContain
* StartsWith
* DoesNotStartWith
* Is
* IsNot
* IsBlank
* IsNotBlank

```typescript
const advancedFilter: pbi.models.IAdvancedFilter = {
  $schema: "https://powerbi.com/product/schema#advanced",
  target: {
    table: "Store",
    column: "Name"
  },
  logicalOperator: "Or",
  conditions: [
    {
      operator: "Contains",
      value: "Wash"
    },
    {
      operator: "Contains",
      value: "Park"
    }
  ]
}
```

[En savoir plus sur le filtrage](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Filters)

### <a name="handling-events"></a>Gestion des événements

Outre l’envoi d’informations dans l’iframe, votre application peut également recevoir des informations sur les événements suivants provenant de l’iframe :

* Embed
  * chargé
  * error
* Rapports
  * pageChanged
  * dataSelected (disponible prochainement)

[En savoir plus sur la gestion des événements](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Handling-Events)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’API JavaScript de Power BI, cliquez sur les liens suivants :

* [Wiki d’API JavaScript](https://github.com/Microsoft/PowerBI-JavaScript/wiki)
* [Référence du modèle d’objet](https://microsoft.github.io/powerbi-models/modules/_models_.html)
* [Démonstration en direct](https://microsoft.github.io/PowerBI-JavaScript/demo/)