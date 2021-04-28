---
title: Créer des kits SDK de langages personnalisés avec AutoRest
titleSuffix: Azure Digital Twins
description: Apprenez à utiliser AutoRest pour générer des Kits de développement logiciel (SDK) en langage personnalisé, pour l’écriture de code Azure Digital Twins dans d’autres langues qui n’ont pas de SDK publiés.
author: baanders
ms.author: baanders
ms.date: 3/9/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom:
- devx-track-js
- contperf-fy21q3
ms.openlocfilehash: 4e91bf5acc5290229afa8dc7a849e8953257bcfd
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751109"
---
# <a name="create-custom-language-sdks-for-azure-digital-twins-using-autorest"></a>Créer des Kits de développement logiciel (SDK) en langage personnalisé pour Azure Digital Twins avec AutoRest

Si vous devez travailler avec Azure Digital Twins dans un langage pour lequel aucun [Kit de développement logiciel (SDK) Azure Digital Twins n’a été publié](how-to-use-apis-sdks.md), cet article vous montrera comment utiliser AutoRest pour générer votre propre SDK dans le langage de votre choix. 

Les exemples de cet article montrent la création d’un [Kit de développement logiciel (SDK) de plan de données](how-to-use-apis-sdks.md#overview-data-plane-apis), mais ce processus permet également de générer un [Kit de développement logiciel (SDK) de plan de contrôle](how-to-use-apis-sdks.md#overview-control-plane-apis).

## <a name="prerequisites"></a>Prérequis

Pour générer un Kit de développement logiciel (SDK), vous devez d’abord effectuer la configuration suivante sur votre ordinateur local :
* Installez [**AutoRest**](https://github.com/Azure/autorest), version 2.0.4413 (la version 3 n’est pas prise en charge pour le moment).
* Installez [**Node.js**](https://nodejs.org), qui est une condition préalable à l’utilisation d’AutoRest.
* Installez [**Visual Studio**](https://visualstudio.microsoft.com/downloads/).
* Téléchargez la dernière version du fichier **Swagger du plan de données** (OpenAPI) d’Azure Digital Twins dans le [dossier du fichier Swagger du plan de données](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins), ainsi que le dossier d’exemples qui l’accompagne. Le fichier Swagger est celui appelé *digitaltwins.json*.

>[!TIP]
> Pour créer un **Kit de développement logiciel (SDK) de plan de contrôle**, suivez les étapes de cet article en utilisant la dernière version du **fichier Swagger du plan de contrôle** (OpenAPI) dans le [dossier du fichier Swagger du plan de contrôle](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/) au lieu de celui du plan de données.

Une fois que votre machine est équipée de tous les éléments de la liste ci-dessus, vous êtes prêt à utiliser AutoRest pour créer un SDK.

## <a name="create-the-sdk-using-autorest"></a>Créer le SDK avec AutoRest 

Une fois Node.js installé, vous pouvez exécuter cette commande pour vérifier que vous disposez de la version requise d’AutoRest :
```cmd/sh
npm install -g autorest@2.0.4413
```

Pour exécuter AutoRest sur le fichier Swagger Azure Digital Twins, procédez comme suit :
1. Copiez le fichier Swagger Azure Digital Twins et le dossier d’exemples qui l’accompagne dans un répertoire de travail.
2. Utilisez une fenêtre d’invite de commandes pour basculer vers ce répertoire de travail.
3. Exécutez AutoRest avec la commande suivante. Remplacez l’espace réservé `<language>` par le langage de votre choix : `python`, `java`, `go`, etc. (Vous trouverez la liste complète des options dans le [LISEZMOI AutoRest](https://github.com/Azure/autorest).)

```cmd/sh
autorest --input-file=digitaltwins.json --<language> --output-folder=DigitalTwinsApi --add-credentials --azure-arm --namespace=DigitalTwinsApi
```

Un nouveau dossier nommé *DigitalTwinsApi* s’affiche alors dans votre répertoire de travail. Les fichiers de SDK générés auront l’espace de noms *DigitalTwinsApi*. Vous continuerez à utiliser cet espace de noms dans les autres exemples d’utilisation de cet article.

AutoRest prend en charge un large éventail de générateurs de code de langage.

## <a name="make-the-sdk-into-a-class-library"></a>Transformer le SDK en bibliothèque de classes

Il est possible d’inclure directement les fichiers générés par AutoRest dans une solution .NET. Toutefois, il est probable que vous souhaitiez inclure le Kit de développement logiciel (SDK) Azure Digital Twins dans plusieurs projets distincts (vos applications clientes, applications Azure Functions, etc.). Pour cette raison, il peut être utile de créer un projet distinct (une bibliothèque de classes .NET) à partir des fichiers générés. Vous pouvez ensuite inclure ce projet de bibliothèque de classes dans plusieurs solutions en tant que référence de projet.

Cette section fournit les instructions à suivre pour générer le kit SDK sous la forme d’une bibliothèque de classes, qui constitue son propre projet et peut être incluse dans d’autres projets. Ces étapes reposent sur **Visual Studio**.

Voici la procédure à suivre :

1. Créez une solution Visual Studio pour une bibliothèque de classes.
2. Utilisez *DigitalTwinsApi* comme nom de projet
3. Dans l’Explorateur de solutions, cliquez avec le bouton droit pour sélectionner le projet *DigitalTwinsApi* de la solution générée, puis choisissez *Ajouter > Élément existant…*
4. Recherchez le dossier dans lequel vous avez généré le kit SDK et sélectionnez les fichiers au niveau racine.
5. Appuyez sur « OK ».
6. Ajoutez un dossier au projet (cliquez avec le bouton droit pour sélectionner le projet dans l’Explorateur de solutions, puis sélectionnez *Ajouter > Nouveau dossier*).
7. Nommez le dossier *Modèles*.
8. Cliquez avec le bouton droit pour sélectionner le dossier *Modèles* dans l’Explorateur de solutions, puis sélectionnez *Ajouter un élément existant…* .
9. Sélectionnez les fichiers dans le dossier *Modèles* du kit SDK généré et appuyez sur « OK ».

Les références suivantes sont nécessaires dans votre projet pour générer le kit SDK :
* `Microsoft.Rest.ClientRuntime`
* `Microsoft.Rest.ClientRuntime.Azure`

Pour les ajouter, ouvrez *Outils > Gestionnaire de package NuGet > Gérer les packages NuGet pour la solution…* .

1. Dans le volet, vérifiez que l’onglet *Parcourir* est sélectionné.
2. Recherchez *Microsoft.Rest*.
3. Sélectionnez les packages `ClientRuntime` et `ClientRuntime.Azure`, puis ajoutez-les à votre solution.

Vous pouvez maintenant générer le projet et l’inclure comme référence de projet dans toutes les applications Azure Digital Twins que vous écrivez.

## <a name="tips-for-using-the-sdk"></a>Conseils d’utilisation du SDK

Cette section contient des informations générales sur le kit SDK généré, ainsi que les instructions à suivre pour l’utiliser.

### <a name="synchronous-and-asynchronous-calls"></a>Appels synchrones et asynchrones

Toutes les fonctions du kit SDK sont disponibles en version synchrone et asynchrone.

### <a name="typed-and-untyped-data"></a>Données typées et non typées

Les appels d’API REST retournent généralement des objets fortement typés. Toutefois, étant donné qu’Azure Digital Twins permet aux utilisateurs de définir leurs propres types personnalisés pour les jumeaux, il n’existe pour de nombreux appels Azure Digital Twins aucun moyen de prédéfinir des données de retour statiques. Au lieu de cela, les API retournent des types de wrappers fortement typés le cas échéant, et les données de jumeaux de type personnalisé se trouvent dans des objets JSON.NET (utilisés partout où le type de données « object » apparaît dans les signatures d’API). Vous pouvez effectuer une conversion de type (transtypage) sur ces objets là où c’est nécessaire dans votre code.

### <a name="error-handling"></a>Gestion des erreurs

Chaque fois qu’une erreur se produit dans le kit SDK (y compris des erreurs HTTP comme 404), ce dernier lève une exception. C’est pourquoi il est important d’encapsuler tous les appels d’API dans des blocs try/catch.

Voici un extrait de code qui tente d’ajouter un jumeau et intercepte les erreurs dans ce processus :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_errorHandling":::

### <a name="paging"></a>Pagination

AutoRest génère deux types de modèles de pagination pour le kit SDK :
* Une pour toutes les API, à l’exception de l’API de requête
* Une pour l’API de requête

Dans le modèle de pagination sans requête, voici un exemple de méthode qui montre comment récupérer une liste paginée de relations sortantes à partir d’Azure Digital Twins :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindOutgoingRelationshipsMethod":::

Le deuxième modèle n’est généré que pour l’API de requête. Il utilise explicitement `continuationToken`.

>[!TIP]
> L’une des principales raisons pour lesquelles vous obtenez des pages est le calcul des [frais d’unités de requête](concepts-query-units.md) pour un appel d’API de requête.

Voici un exemple utilisant ce modèle :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="PagedQuery":::

## <a name="next-steps"></a>Étapes suivantes

Suivez la procédure de création d’une application cliente dans laquelle vous pouvez utiliser votre kit SDK :
* [*Tutoriel : Coder une application cliente*](tutorial-code.md)
