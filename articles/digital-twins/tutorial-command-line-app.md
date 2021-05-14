---
title: 'Tutoriel : Créer un graphe dans Azure Digital Twins (application cliente)'
titleSuffix: Azure Digital Twins
description: Tutoriel permettant de mettre en œuvre un scénario Azure Digital Twins à l’aide d’un exemple d’application en ligne de commande.
author: baanders
ms.author: baanders
ms.date: 5/8/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: 419e609c4b78007f215d67ab4a69671bc9cbb198
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108205626"
---
# <a name="tutorial-create-an-azure-digital-twins-graph-using-a-sample-client-app"></a>Tutoriel : Créer un graphe Azure Digital Twins à l’aide d’un exemple d’application cliente

[!INCLUDE [digital-twins-tutorial-selector.md](../../includes/digital-twins-tutorial-selector.md)]

Dans ce tutoriel, vous allez créer un graphe dans Azure Digital Twins à l’aide de modèles, de jumeaux et de relations. L’outil utilisé pour ce tutoriel est un **exemple d’application cliente en ligne de commande** pour interagir avec une instance Azure Digital Twins. L’application cliente est semblable à celle écrite dans [Tutoriel : Coder une application cliente](tutorial-code.md).

Vous pouvez utiliser cet exemple pour effectuer des actions Azure Digital Twins essentielles, telles que le chargement de modèles, la création et la modification de jumeaux et la création de relations. Vous pouvez également examiner le [code de l’exemple](https://github.com/Azure-Samples/digital-twins-samples/tree/master/) pour en savoir plus sur les API Azure Digital Twins, et vous exercer à implémenter vos propres commandes en modifiant l’exemple de projet comme bon vous semble.

Ce tutoriel présente les procédures suivantes :
> [!div class="checklist"]
> * Modéliser un environnement
> * Créer des jumeaux numériques
> * Ajouter des relations pour former un graphe
> * Interroger le graphe pour répondre aux questions

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

### <a name="run-the-sample-project"></a>Exécuter l’exemple de projet

Maintenant que l’application et l’authentification sont configurées, exécutez le projet avec ce bouton dans la barre d’outils :

:::image type="content" source="media/tutorial-command-line/app/start-button-sample.png" alt-text="Capture d’écran du bouton de démarrage de Visual Studio (projet SampleClientApp)." lightbox="media/tutorial-command-line/app/start-button-sample.png":::

Une fenêtre de console s’ouvre, exécute l’authentification et attend une commande. 
* L’authentification est gérée par le biais du navigateur : votre navigateur web par défaut s’ouvre avec une invite d’authentification. Utilisez cette invite pour vous connecter avec vos informations d’identification Azure. Vous pouvez ensuite fermer l’onglet ou la fenêtre du navigateur.

Voici une capture d’écran montrant à quoi ressemble la console projet :

:::image type="content" source="media/tutorial-command-line/app/command-line-app.png" alt-text="Capture d’écran du message d’accueil de l’application en ligne de commande." lightbox="media/tutorial-command-line/app/command-line-app.png":::

> [!TIP]
> Pour obtenir la liste de toutes les commandes que vous pouvez utiliser avec ce projet, entrez `help` dans la console de projet et appuyez sur Entrée.

Laissez la console de projet en cours d’exécution pour le reste des étapes de ce tutoriel.

## <a name="model-a-physical-environment-with-dtdl"></a>Modéliser un environnement physique avec DTDL

Maintenant que l’instance Azure Digital Twins et l’exemple d’application sont configurés, vous pouvez commencer à créer un graphe d’un scénario. 

La première étape de la création d’une solution Azure Digital Twins consiste à définir des [modèles](concepts-models.md) de jumeaux pour votre environnement. 

Les modèles sont similaires aux classes dans les langages de programmation orientés objet. Ils fournissent des modèles de [jumeaux numériques](concepts-twins-graph.md) définis par l’utilisateur à suivre et à instancier ultérieurement. Ils sont écrits dans un langage de type JSON appelé **DTDL (Digital Twins Definition Language**) et peuvent définir les *propriétés*, la *télémétrie*, les *relations* et les *composants* d’un jumeau.

> [!NOTE]
> DTDL permet également de définir des *commandes* sur des jumeaux numériques. Toutefois, les commandes ne sont actuellement pas prises en charge dans le service Azure Digital Twins.

Dans la fenêtre Visual Studio où le projet _**AdtE2ESample**_ est ouvert, utilisez le volet *Explorateur de solutions* pour accéder au dossier *AdtSampleApp\SampleClientApp\Models*. Ce dossier contient des exemples de modèles.

Sélectionnez *Room.json* pour l’ouvrir dans la fenêtre d’édition, puis modifiez-le comme suit :

[!INCLUDE [digital-twins-tutorial-model-create.md](../../includes/digital-twins-tutorial-model-create.md)]

### <a name="upload-models-to-azure-digital-twins"></a>Charger des modèles sur Azure Digital Twins

Après avoir conçu les modèles, vous devez les charger sur votre instance Azure Digital Twins. Cela configure votre instance du service Azure Digital Twins avec votre propre vocabulaire de domaine personnalisé. Une fois que vous avez chargé les modèles, vous pouvez créer des instances de jumeau qui les utilisent.

1. Dans la fenêtre de console du projet, exécutez la commande suivante pour charger votre modèle *Room* mis à jour, ainsi qu’un modèle *Floor* que vous utiliserez aussi dans la section suivante pour créer différents types de jumeaux.

    ```cmd/sh
    CreateModels Room Floor
    ```
    
    La sortie doit indiquer que les modèles ont été créés avec succès.

1. Vérifiez que les modèles ont été créés en exécutant la commande `GetModels true`. Cette opération interroge l’instance Azure Digital Twins pour identifier tous les modèles qui ont été chargés, puis affiche leurs informations complètes. Recherchez le modèle *Room* modifié dans les résultats :

    :::image type="content" source="media/tutorial-command-line/app/output-get-models.png" alt-text="Capture d’écran du résultat de GetModels, montrant le modèle Room mis à jour." lightbox="media/tutorial-command-line/app/output-get-models.png":::

### <a name="errors"></a>Erreurs

L’exemple d’application gère également les erreurs du service. 

Réexécutez la commande `CreateModels` pour essayer de recharger l’un des modèles que vous venez de charger :

```cmd/sh
CreateModels Room
```

Comme les modèles ne peuvent pas être remplacés, une erreur de service est désormais retournée.
Pour plus d’informations sur la façon de supprimer des modèles existants, consultez [Guide pratique : Gérer les modèles DTDL](how-to-manage-model.md).
```cmd/sh
Response 409: Service request failed.
Status: 409 (Conflict)

Content:
{"error":{"code":"ModelAlreadyExists","message":"Could not add model dtmi:example:Room;2 as it already exists. Use Model_List API to view models that already exist. See the Swagger example.(http://aka.ms/ModelListSwSmpl)"}}

Headers:
Strict-Transport-Security: REDACTED
Date: Wed, 20 May 2020 00:53:49 GMT
Content-Length: 223
Content-Type: application/json; charset=utf-8
```

## <a name="create-digital-twins"></a>Créer des jumeaux numériques

Maintenant que certains modèles ont été chargés sur votre instance Azure Digital Twins, vous pouvez créer des [jumeaux numériques](concepts-twins-graph.md) basés sur les définitions de modèle. Les jumeaux numériques représentent les entités au sein de votre environnement d’entreprise (par exemple les capteurs dans une ferme, les salles d’un bâtiment ou les voyants d’une voiture). 

Pour créer un jumeau numérique, utilisez la commande `CreateDigitalTwin`. Vous devez référencer le modèle sur lequel le jumeau est basé, et vous pouvez éventuellement définir des valeurs initiales pour les propriétés du modèle. Vous n’avez pas besoin de transmettre d’informations de relation à ce stade.

1. Exécutez ce code dans la console de projet en cours d’exécution pour créer plusieurs jumeaux, basés sur le modèle *Room* que vous avez mis à jour et sur un autre modèle, *Floor*. Rappelez-vous que *Room* a trois propriétés ; vous pouvez donc fournir des arguments avec les valeurs initiales de ces propriétés. (L’initialisation des valeurs de propriétés est facultative en général, mais elle est nécessaire pour ce tutoriel.)

    ```cmd/sh
    CreateDigitalTwin dtmi:example:Room;2 room0 RoomName string Room0 Temperature double 70 HumidityLevel double 30
    CreateDigitalTwin dtmi:example:Room;2 room1 RoomName string Room1 Temperature double 80 HumidityLevel double 60
    CreateDigitalTwin dtmi:example:Floor;1 floor0
    CreateDigitalTwin dtmi:example:Floor;1 floor1
    ```

    La sortie de ces commandes doit indiquer que les jumeaux ont été créés avec succès. 
    
    :::image type="content" source="media/tutorial-command-line/app/output-create-digital-twin.png" alt-text="Capture d’écran montrant un extrait du résultat des commandes CreateDigitalTwin, qui comprend floor0, floor1, room0 et room1." lightbox="media/tutorial-command-line/app/output-create-digital-twin.png":::

1. Vous pouvez vérifier que les jumeaux ont été créés en exécutant la commande `Query`. Cette commande interroge votre instance Azure Digital Twins pour obtenir tous les jumeaux numériques qu’elle contient. Recherchez les jumeaux *room0*, *room1*, *floor0* et *floor1* dans les résultats.

### <a name="modify-a-digital-twin"></a>Modifier un jumeau numérique

Vous pouvez également modifier les propriétés d’un jumeau que vous avez créé. 

> [!NOTE]
> L’API REST sous-jacente utilise le format [JSON Patch](http://jsonpatch.com/) pour définir les mises à jour d’un jumeau. L’application en ligne de commande utilise également ce format pour offrir une expérience correspondant davantage à ce qui est attendu par les API sous-jacentes.

1. Exécutez cette commande pour changer le RoomName de *room0* de *Room0* en *PresidentialSuite* :
    
    ```cmd/sh
    UpdateDigitalTwin room0 add /RoomName string PresidentialSuite
    ```
    
    La sortie doit indiquer que le jumeau a été correctement mis à jour.

1. Vous pouvez vérifier que la mise à jour a réussi en exécutant cette commande pour voir les informations de *room0* :

    ```cmd/sh
    GetDigitalTwin room0
    ```
    
    La sortie doit refléter le nom mis à jour.


## <a name="create-a-graph-by-adding-relationships"></a>Créer un graphe en ajoutant des relations

Ensuite, vous pouvez créer des **relations** entre ces jumeaux, afin de les raccorder sur un [graphe de jumeaux](concepts-twins-graph.md). Les graphes de jumeaux servent à représenter un environnement entier. 

Les types de relations que vous pouvez créer d’un jumeau à un autre sont définis dans les [modèles](#model-a-physical-environment-with-dtdl) que vous avez chargés. La [définition de modèle pour Floor](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) spécifie que les étages peuvent avoir un type de relation nommé *contains*. Cela permet de créer une relation de type *contains* entre chaque jumeau *Floor* et la pièce correspondante qu’il contient.

Pour ajouter une relation, utilisez la commande `CreateRelationship`. Spécifiez le jumeau d’où provient la relation, le type de relation, et le jumeau avec lequel la relation établit une connexion. Pour finir, attribuez un ID unique à la relation.

1. Exécutez le code suivant pour ajouter une relation « contains » entre chaque jumeau *Floor* que vous avez créé et un jumeau *Room* correspondant. Les relations sont nommées *relationship0* et *relationship1*.

    ```cmd/sh
    CreateRelationship floor0 contains room0 relationship0
    CreateRelationship floor1 contains room1 relationship1
    ```

    >[!TIP]
    >La relation *contains* dans le modèle [Floor](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) a également été définie avec deux propriétés de chaîne, `ownershipUser` et `ownershipDepartment`. Vous pouvez donc aussi fournir des arguments avec les valeurs initiales de ces propriétés lorsque vous créez les relations.
    > Voici une autre version de la commande ci-dessus pour créer *relationship0* qui spécifie également des valeurs initiales pour ces propriétés :
    > ```cmd/sh
    > CreateRelationship floor0 contains room0 relationship0 ownershipUser string MyUser ownershipDepartment string myDepartment
    > ``` 
    
    La sortie de ces commandes confirme que les relations ont été créées correctement :
    
    :::image type="content" source="media/tutorial-command-line/app/output-create-relationship.png" alt-text="Capture d’écran d’un extrait du résultat des commandes CreateRelationship, qui comprend relationship0 et relationship1." lightbox="media/tutorial-command-line/app/output-create-relationship.png":::

1. Vous pouvez vérifier les relations avec l’une des commandes suivantes, qui interrogent les relations dans votre instance Azure Digital Twins.
    * Pour voir toutes les relations partant de chaque étage (affichage des relations d’un côté) :
        ```cmd/sh
        GetRelationships floor0
        GetRelationships floor1
        ```
    * Pour voir toutes les relations arrivant à chaque pièce (affichage de la relation de l’« autre » côté) :
        ```cmd/sh
        GetIncomingRelationships room0
        GetIncomingRelationships room1
        ```
    * Pour rechercher ces relations individuellement, par ID :
        ```cmd/sh
        GetRelationship floor0 relationship0
        GetRelationship floor1 relationship1
        ```

Les jumeaux et les relations que vous avez configurés dans ce tutoriel forment le graphe conceptuel suivant :

:::image type="content" source="media/tutorial-command-line/app/sample-graph.png" alt-text="Diagramme montrant un graphe conceptuel. floor0 est connecté par le biais de relationship0 à room0, et floor1 est connecté par le biais de relationship1 à room1." border="false" lightbox="media/tutorial-command-line/app/sample-graph.png":::

## <a name="query-the-twin-graph-to-answer-environment-questions"></a>Interroger le graphe de jumeaux pour répondre à des questions environnementales

L’une des principales fonctionnalités d’Azure Digital Twins est la capacité à [interroger facilement et efficacement](concepts-query-language.md) votre graphe de jumeaux pour répondre à des questions sur votre environnement. 

Exécutez les commandes suivantes dans la console de projet en cours d’exécution pour répondre à certaines questions à propos de l’exemple d’environnement.

1. **Quelles sont les entités de mon environnement représentées dans Azure Digital Twins ?** (interroger tout)

    ```cmd/sh
    Query
    ```

    Cela vous permet d’examiner votre environnement en un coup d’œil, et de vous assurer que tout est représenté comme vous le souhaitez dans Azure Digital Twins. Le résultat est une sortie contenant chaque jumeau numérique avec ses détails. Voici un extrait :

    :::image type="content" source="media/tutorial-command-line/app/output-query-all.png" alt-text="Capture d’écran montrant un résultat partiel de la requête de jumeau, qui comprend room0 et floor1.":::

    >[!NOTE]
    >Dans l’exemple de projet, la commande `Query` sans argument supplémentaire équivaut à `Query SELECT * FROM DIGITALTWINS`. Pour interroger tous les jumeaux dans votre instance à l’aide des [API de requête](/rest/api/digital-twins/dataplane/query) ou des [commandes CLI](how-to-use-cli.md), utilisez la requête la plus longue (complète).

1. **Quelles sont les pièces dans mon environnement ?** (requête par modèle)

    ```cmd/sh
    Query SELECT * FROM DIGITALTWINS T WHERE IS_OF_MODEL(T, 'dtmi:example:Room;2')
    ```

    Vous pouvez limiter votre requête aux jumeaux d’un certain type, afin d’obtenir des informations plus spécifiques sur ce qui est représenté. Le résultat montre *room0* et *room1*, mais ne montre **pas** *floor0* ni *floor1* (car il s’agit d’étages, et non de salles).
    
    :::image type="content" source="media/tutorial-command-line/app/output-query-model.png" alt-text="Capture d’écran du résultat de la requête de modèle, montrant uniquement room0 et room1.":::

1. **Quelles sont les pièces de *floor0* ?** (requête par relation)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0'
    ```

    Vous pouvez interroger en fonction des relations de votre graphe, afin d’obtenir des informations sur la façon dont les jumeaux sont raccordés ou de limiter votre requête à une certaine zone. Seule *room0* se trouve à l’étage *floor0* ; il s’agit donc de la seule pièce dans le résultat.

    :::image type="content" source="media/tutorial-command-line/app/output-query-relationship.png" alt-text="Capture d’écran du résultat de la requête de relation, montrant room0.":::

1. **Quels sont les jumeaux dans mon environnement dont la température est supérieure à 75 ?** (requête par propriété)

    ```cmd/sh
    Query SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
    ```

    Vous pouvez interroger le graphe en fonction de propriétés afin de répondre à diverses questions, notamment pour rechercher les anomalies dans votre environnement qui peuvent nécessiter votre attention. D’autres opérateurs de comparaison ( *<* , *>* , *=* ou *!=* ) sont également pris en charge. *room1* apparaît ici dans les résultats, car elle a une température de 80.

    :::image type="content" source="media/tutorial-command-line/app/output-query-property.png" alt-text="Capture d’écran du résultat de la requête de propriété, montrant uniquement room1.":::

1. **Quelles sont les pièces de l’étage *floor0* dont la température est supérieure à 75 ?** (requête composée)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0' AND IS_OF_MODEL(room, 'dtmi:example:Room;2') AND room.Temperature > 75
    ```

    Vous pouvez également combiner les requêtes précédentes comme vous le feriez dans SQL, à l’aide d’opérateurs de combinaison tels que `AND`, `OR` et `NOT`. Cette requête utilise `AND` pour rendre plus spécifique la requête précédente sur les températures des jumeaux. Le résultat contient désormais uniquement les pièces dont la température est supérieure à 75 et qui se trouvent à l’étage *floor0* (en l’occurrence, aucune). Le jeu de résultat est vide.

    :::image type="content" source="media/tutorial-command-line/app/output-query-compound.png" alt-text="Capture d’écran du résultat de la requête composée, ne montrant aucun résultat." lightbox="media/tutorial-command-line/app/output-query-compound.png":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

À l’issue de ce tutoriel, vous pourrez choisir les ressources à supprimer, en fonction de ce que vous souhaitez faire ensuite.

* **Si vous envisagez de continuer avec le tutoriel suivant**, vous pouvez conserver les ressources configurées ici pour continuer à utiliser cette instance Azure Digital Twins et l’exemple d’application configurée pour le prochain tutoriel.

* **Si vous souhaitez continuer à utiliser l’instance Azure Digital Twins, mais effacer tous ses modèles, jumeaux et relations**, vous pouvez utiliser les commandes `DeleteAllTwins` et `DeleteAllModels` de l’exemple d’application pour effacer les jumeaux et les modèles de votre instance, respectivement.

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Vous pouvez également supprimer le dossier de projet de votre ordinateur local.

## <a name="next-steps"></a>Étapes suivantes 

Dans ce tutoriel, vous avez commencé à utiliser Azure Digital Twins en générant un graphe dans votre instance à l’aide d’un exemple d’application cliente. Vous avez créé des modèles, des jumeaux numériques et des relations pour former un graphe. Vous avez également exécuté des requêtes sur le graphe, pour vous faire une idée des types de questions auxquelles Azure Digital Twins peut répondre à propos d’un environnement.

Passez au tutoriel suivant pour combiner Azure Digital Twins à d’autres services Azure afin de bénéficier d’un scénario de bout en bout piloté par les données :
> [!div class="nextstepaction"]
> [Tutoriel : Connecter une solution de bout en bout](tutorial-end-to-end.md)