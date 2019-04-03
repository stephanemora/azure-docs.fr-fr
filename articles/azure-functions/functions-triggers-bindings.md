---
title: Déclencheurs et liaisons dans Azure Functions
description: Apprenez à utiliser des déclencheurs et liaisons pour connecter votre fonction Azure pour les événements en ligne et de services basés sur le cloud.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 3865f748a9ca2fe09660d6454542d64f73a8e3c1
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2019
ms.locfileid: "56736955"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Concepts des déclencheurs et liaisons Azure Functions

Dans cet article, vous découvrez les concepts de haut niveau qui entourent les déclencheurs et liaisons functions.

Les déclencheurs sont la cause d’une fonction à exécuter. Un déclencheur définit la façon dont une fonction est appelée et une fonction doit avoir un seul déclencheur. Déclencheurs sont associés à des données, qui sont souvent fournies en tant que la charge utile de la fonction. 

Liaison à une fonction est un moyen de façon déclarative connecter une autre ressource à la fonction ; liaisons peuvent être connectés en tant que *liaisons d’entrée*, *liaisons de sortie*, ou les deux. Données à partir des liaisons sont fournies à la fonction en tant que paramètres.

Vous pouvez combiner des liaisons différentes pour répondre à vos besoins. Les liaisons sont facultatives et une fonction peut avoir une ou plusieurs entrées et/ou les liaisons de sortie.

Déclencheurs et liaisons vous permettent d’éviter de coder en dur l’accès à d’autres services. Votre fonction reçoit des données (par exemple, le contenu d’un message de la file d’attente) dans les paramètres de fonction. Vous envoyez des données (par exemple pour créer un message de la file d’attente) en utilisant la valeur de retour de la fonction. 

Prenons les exemples suivants de la façon dont vous pouvez implémenter des fonctions différentes.

| Exemple de scénario | Déclencheur | Liaison d’entrée | Liaison de sortie |
|-------------|---------|---------------|----------------|
| Un nouveau message de file d’attente arrive qui exécute une fonction pour écrire dans une autre file d’attente. | File d’attente<sup>*</sup> | *Aucun(e)* | File d’attente<sup>*</sup> |
|Une tâche planifiée lit le contenu du stockage d’objets Blob et crée un document Cosmos DB. | Minuteur | Stockage Blob | Cosmos DB |
|La grille d’événements est utilisée pour lire une image à partir de stockage d’objets Blob et un document à partir de Cosmos DB pour envoyer un e-mail. | Grille d'événement | Stockage d’objets BLOB et Cosmos DB | SendGrid |
| Un webhook qui utilise Microsoft Graph pour mettre à jour d’une feuille Excel. | HTTP | *Aucun(e)* | Microsoft Graph |

<sup>\*</sup> Représente différentes files d’attente

Ces exemples ne sont pas destinés à être exhaustive, mais sont fournis pour illustrer comment vous pouvez utiliser les déclencheurs et liaisons ensemble.

###  <a name="trigger-and-binding-definitions"></a>Définitions de déclencheur et de liaison

Déclencheurs et liaisons sont définis différemment selon l’approche de développement.

| Plateforme | Déclencheurs et liaisons sont configurées par... |
|-------------|--------------------------------------------|
| Bibliothèque de classes C# | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;décorer les méthodes et les paramètres avec C# attributs |
| Tous les autres utilisateurs (y compris portail Azure) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;la mise à jour [function.json](./functions-reference.md) ([schéma](http://json.schemastore.org/function)) |

Le portail fournit une interface utilisateur pour cette configuration, mais vous pouvez modifier le fichier directement en ouvrant le **éditeur avancé** disponible via le **intégrer** onglet de votre fonction.

Dans .NET, le type de paramètre définit le type de données pour les données d’entrée. Par exemple, utilisez `string` à lier au texte d’un déclencheur de file d’attente, un tableau d’octets à lire au format binaire et un type personnalisé pour désérialiser un objet.

Pour les langages dont le type est dynamique, tels que JavaScript, utilisez la propriété `dataType` dans le fichier *function.json*. Par exemple, pour lire le contenu d’une requête HTTP au format binaire, définissez `dataType` sur `binary` :

```json
{
    "dataType": "binary",
    "type": "httpTrigger",
    "name": "req",
    "direction": "in"
}
```

Les autres options pour `dataType` sont `stream` et `string`.

## <a name="binding-direction"></a>Sens de la liaison

Tous les déclencheurs et liaisons ont une propriété `direction` dans le fichier [function.json](./functions-reference.md) :

- Pour les déclencheurs, la direction est toujours `in`
- Utilisent des liaisons d’entrée et de sortie `in` et `out`
- Certaines liaisons prennent en charge un sens spécial `inout`. Si vous utilisez `inout`, seule la **éditeur avancé** est disponible via le **intégrer** onglet dans le portail.

Lorsque vous utilisez des [attributs dans une bibliothèque de classes](functions-dotnet-class-library.md) pour configurer les déclencheurs et les liaisons, la direction est fournie dans un constructeur d’attribut ou déduite du type du paramètre.

## <a name="supported-bindings"></a>Liaisons prises en charge

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Pour plus d’informations sur les liaisons en préversion ou approuvées pour la production, consultez [Langages pris en charge](supported-languages.md).

## <a name="resources"></a>Ressources
- [Modèles et expressions de liaison](./functions-bindings-expressions-patterns.md)
- [À l’aide de la valeur de retour de fonction Azure](./functions-bindings-return-value.md)
- [Comment inscrire une expression de liaison](./functions-bindings-register.md)
- Test :
  - [Stratégies permettant de tester votre code dans Azure Functions](functions-test-a-function.md)
  - [Exécuter manuellement une fonction non déclenchée via HTTP](functions-manually-run-non-http.md)
- [Gestion des erreurs de liaison](./functions-bindings-errors.md)

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Inscrire des extensions de liaison d’Azure Functions](./functions-bindings-register.md)
