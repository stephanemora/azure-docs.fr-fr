---
title: Gérer le graphe de jumeaux avec des relations
titleSuffix: Azure Digital Twins
description: Consultez la procédure de gestion d’un graphique de jumeaux numériques via la connexion de ceux-ci à des relations.
author: baanders
ms.author: baanders
ms.date: 11/03/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 037e7fd13f55a0f5de939197f71324221392bd55
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98601075"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>Gérer un graphique de jumeaux numériques à l’aide de relations

Azure Digital Twins consiste en un [graphique de jumeaux](concepts-twins-graph.md) représentant l’ensemble de votre environnement. Le graphe de jumeaux est constitué de jumeaux numériques individuels connectés via des **relations**. 

Une fois que vous disposez d’une [instance Azure Digital Twins](how-to-set-up-instance-portal.md) opérationnelle et que vous avez configuré un code d’[authentification](how-to-authenticate-client.md) dans votre application cliente, vous pouvez utiliser les [**API DigitalTwins**](/rest/api/digital-twins/dataplane/twins) pour créer, modifier et supprimer des jumeaux numériques et leurs relations dans une instance Azure Digital Twins. Vous pouvez également utiliser le [Kit de développement logiciel (SDK) .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) ou l’[interface CLI Azure Digital Twins](how-to-use-cli.md).

Cet article se concentre sur la gestion des relations et du graphique dans son ensemble. Pour utiliser des jumeaux numériques individuels, consultez [*Guide pratique : Gérer des jumeaux numériques*](how-to-manage-twin.md).

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="ways-to-manage-graph"></a>Modes de gestion d’un graphe

[!INCLUDE [digital-twins-ways-to-manage.md](../../includes/digital-twins-ways-to-manage.md)]

Vous pouvez également apporter des changements à votre graphe à l’aide de l’exemple d’ADT Explorer (Azure Digital Twins Explorer), qui vous permet de visualiser vos jumeaux et votre graphe, et qui utilise le kit SDK en arrière-plan. La section suivante décrit cet exemple en détail.

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-relationships"></a>Créer des relations

Les relations décrivent les interconnexions entre différents jumeaux numériques, ce qui constitue la base du graphique de jumeaux.

Les relations sont créées à l’aide de l’appel `CreateOrReplaceRelationshipAsync()`. 

Pour créer une relation, vous devez spécifier :
* L’ID de jumeau source (`srcId` dans l’exemple de code ci-dessous) : l’ID du jumeau à l’origine de la relation.
* L’ID de jumeau cible (`targetId` dans l’exemple de code ci-dessous) : l’ID du jumeau destinataire de la relation.
* Un nom de relation (`relName` dans l’exemple de code ci-dessous) : le type générique de la relation, tel que _contient_.
* Un ID de relation (`relId` dans l’exemple de code ci-dessous) : le nom spécifique de cette relation, tel que _Relation1_.

L’ID de relation doit être unique au sein du jumeau source donné. Il ne doit pas être globalement unique.
Par exemple, pour le jumeau *foo*, chaque ID de relation spécifique doit être unique. Toutefois, un autre jumeau *bar* peut avoir une relation sortante qui correspond au même ID d’une relation *foo*.

L’exemple de code suivant illustre la procédure de création d’une relation dans votre instance Azure Digital Twins.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="CreateRelationshipMethod":::

Dans votre méthode main, vous pouvez maintenant appeler la fonction `CreateRelationship()` pour créer une relation _contient_ de ce type : 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseCreateRelationship":::

Si vous souhaitez créer plusieurs relations, vous pouvez répéter des appels à la même méthode, en passant différents types de relations dans l’argument. 

Pour plus d’information sur la classe d’assistance `BasicRelationship`, consultez [*Guide pratique : Utiliser les kits SDK et les API Azure Digital Twins*](how-to-use-apis-sdks.md#serialization-helpers).

### <a name="create-multiple-relationships-between-twins"></a>Créer plusieurs relations entre jumeaux

Les relations peuvent être classées comme suit : 

* Relations sortantes : relations appartenant à ce jumeau qui pointent vers l’extérieur pour le connecter à d’autres jumeaux. La méthode `GetRelationshipsAsync()` est utilisée pour obtenir les relations sortantes d’un jumeau.
* Relations entrantes : relations appartenant à d’autres jumeaux qui pointent vers ce jumeau pour créer un lien « entrant ». La méthode `GetIncomingRelationshipsAsync()` est utilisée pour obtenir les relations entrantes d’un jumeau.

Il n’existe aucune restriction du nombre de relations que vous pouvez avoir entre deux jumeaux : vous pouvez avoir autant de relations entre jumeaux que vous le souhaitez. 

Cela signifie que vous pouvez exprimer plusieurs types de relations entre deux jumeaux à la fois. Par exemple, le *Jumeau A* peut avoir une relation *stockée* et une relation *fabriquée* avec le *Jumeau B*.

Vous pouvez même créer plusieurs instances du même type de relation entre les deux mêmes jumeaux si vous le souhaitez. Dans cet exemple, le *Jumeau A* peut avoir deux relations *stockées* différentes avec le *Jumeau B*, à condition que les relations aient différents ID de relation.

## <a name="list-relationships"></a>Lister les relations

Pour accéder à la liste des relations **sortantes** pour un jumeau donné dans le graphe, vous pouvez utiliser la méthode `GetRelationships()` comme suit :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="GetRelationshipsCall":::

Cette méthode retourne `Azure.Pageable<T>` ou `Azure.AsyncPageable<T>`, selon que vous utilisez la version synchrone ou asynchrone de l’appel.

Voici un exemple qui récupère une liste de relations :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindOutgoingRelationshipsMethod":::

Vous pouvez maintenant appeler cette méthode pour voir les relations sortantes des jumeaux comme suit :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFindOutgoingRelationships":::

Vous pouvez utiliser les relations récupérées pour accéder à d’autres jumeaux de votre graphique. Pour ce faire, lisez le champ `target` à partir de la relation retournée et utilisez-le comme ID de votre prochain appel à `GetDigitalTwin()`.

### <a name="find-incoming-relationships-to-a-digital-twin"></a>Rechercher des relations entrantes avec un jumeau numérique

Azure Digital Twins dispose également d’une API permettant de rechercher toutes les relations **entrantes** avec un jumeau donné. Cela s’avère souvent utile pour la navigation inverse ou lors de la suppression d’un jumeau.

L’exemple de code précédent se concentrait sur la recherche des relations sortantes d’un jumeau. L’exemple suivant est structuré de la même façon, mais il recherche des relations *entrantes*.

Notez que les appels `IncomingRelationship` ne retournent pas le corps complet de la relation.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindIncomingRelationshipsMethod":::

Vous pouvez maintenant appeler cette méthode pour voir les relations entrantes des jumeaux comme suit :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFindIncomingRelationships":::

### <a name="list-all-twin-properties-and-relationships"></a>Répertorier toutes les propriétés de jumeau et les relations

En utilisant les méthodes ci-dessus pour répertorier les relations sortantes et entrantes en lien avec un jumeau, vous pouvez créer une méthode qui imprime des informations complètes sur le jumeau, notamment les propriétés et les deux types de ses relations. Voici un exemple de méthode, appelé `FetchAndPrintTwinAsync()`, qui montre comment procéder.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FetchAndPrintMethod":::

Vous pouvez maintenant appeler cette fonction dans votre méthode main comme suit : 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFetchAndPrint":::

## <a name="delete-relationships"></a>Supprimer des relations

Le premier paramètre spécifie le jumeau source (le jumeau à l’origine de la relation). L’autre paramètre est l’ID de relation. Vous avez besoin de l’ID de jumeau et de l’ID de relation, car les ID de relation ne sont uniques que dans l’étendue d’un jumeau.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="DeleteRelationshipMethod":::

Vous pouvez maintenant appeler cette méthode pour supprimer une relation comme suit :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseDeleteRelationship":::

## <a name="runnable-twin-graph-sample"></a>Exemple de graphe de jumeaux exécutable

L’extrait de code exécutable suivant utilise les opérations de relation de cet article pour créer un graphe de jumeaux en dehors des relations et des jumeaux.

### <a name="set-up-the-runnable-sample"></a>Configurer l’exemple exécutable

L’extrait de code utilise les définitions de modèle [*Room.json*](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) et [*Floor.json*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) du [*Tutoriel : Explorer Azure Digital Twins avec un exemple d’application cliente*](tutorial-command-line-app.md). Vous pouvez utiliser ces liens pour accéder directement aux fichiers ou les télécharger dans le cadre de l’exemple de projet de bout en bout complet [ici](/samples/azure-samples/digital-twins-samples/digital-twins-samples/). 

Avant d’exécuter l’exemple, effectuez les étapes suivantes :
1. Téléchargez les fichiers de modèle, placez-les dans votre projet et remplacez l’espace réservé `<path-to>` dans le code ci-dessous pour indiquer à votre programme où les trouver.
2. Remplacez l’espace réservé `<your-instance-hostname>` par le nom d’hôte de votre instance Azure Digital Twins.
3. Ajoutez deux dépendances à votre projet. Elles seront nécessaires pour travailler avec Azure Digital Twins. La première est le package pour le [SDK Azure Digital Twins pour .NET](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true), tandis que la seconde fournit des outils facilitant l’authentification auprès d’Azure.

      ```cmd/sh
      dotnet add package Azure.DigitalTwins.Core
      dotnet add package Azure.Identity
      ```

Vous devez également configurer des informations d’identification locales si vous souhaitez exécuter l’exemple directement. La section suivante décrit cette procédure.
[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

### <a name="run-the-sample"></a>Exécution de l'exemple

Une fois les étapes ci-dessus terminées, vous pouvez exécuter directement l’exemple de code suivant.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs":::

Voici la sortie de la console du programme ci-dessus : 

:::image type="content" source="./media/how-to-manage-graph/console-output-twin-graph.png" alt-text="Sortie de la console présentant les détails sur les jumeaux, les relations entrantes et sortantes des jumeaux." lightbox="./media/how-to-manage-graph/console-output-twin-graph.png":::

> [!TIP]
> Le graphe de jumeaux est un concept en lien avec la création de relations entre les jumeaux. Si vous souhaitez afficher la représentation visuelle du graphe de jumeaux, consultez la section [*Visualisation*](how-to-manage-graph.md#visualization) de cet article. 

## <a name="create-graph-from-a-csv-file"></a>Créer un graphe à partir d’un fichier CSV

Dans des cas d’usage pratiques, les hiérarchies de jumeaux sont souvent créées à partir des données stockées dans une base de données différente ou peut-être dans une feuille de calcul ou un fichier CSV. Cette section explique comment lire des données à partir d’un fichier CSV et créer un graphe de jumeaux en dehors de celui-ci.

Examinez le tableau de données suivant, qui décrit un ensemble de jumeaux numériques et de relations.

|  ID de modèle    | ID de jumeau (doit être unique) | Nom de la relation  | ID de jumeau cible  | Données d’initialisation du jumeau |
| --- | --- | --- | --- | --- |
| dtmi:example:Floor;1    | Floor1 | contains | Room1 | |
| dtmi:example:Floor;1    | Floor0 | contains | Room0 | |
| dtmi:example:Room;1    | Room1 | | | {"Temperature": 80} |
| dtmi:example:Room;1    | Room0 | | | {"Temperature": 70} |

L’une des méthodes permettant d’obtenir ces données dans Azure Digital Twins consiste à convertir la table en fichier CSV et à écrire du code pour interpréter le fichier en commandes permettant de créer des jumeaux et des relations. L’exemple de code suivant illustre la lecture des données du fichier CSV et la création d’un graphe de jumeaux dans Azure Digital Twins.

Dans le code ci-dessous, le fichier CSV est appelé *data.csv* et il existe un espace réservé représentant le **nom d’hôte** de votre instance Azure Digital Twins. L’exemple utilise aussi plusieurs packages que vous pouvez ajouter à votre projet pour faciliter ce processus.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graphFromCSV.cs":::

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur l’interrogation d’un graphique de jumeaux Azure Digital Twins :
* [*Concepts : langage de requête*](concepts-query-language.md)
* [*Guide pratique : Interroger le graphe de jumeaux*](how-to-query-graph.md)