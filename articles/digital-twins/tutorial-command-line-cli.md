---
title: 'Tutoriel : Créer un graphe dans Azure Digital Twins (CLI)'
titleSuffix: Azure Digital Twins
description: Tutoriel pour créer un scénario Azure Digital Twins à l’aide d’Azure CLI
author: baanders
ms.author: baanders
ms.date: 2/26/2021
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: 0b45c23bc7d8679ce1fa2135efc2a99fc443e57d
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109788869"
---
# <a name="tutorial-create-an-azure-digital-twins-graph-using-the-azure-cli"></a>Tutoriel : Créer un graphe Azure Digital Twins à l’aide d’Azure CLI

[!INCLUDE [digital-twins-tutorial-selector.md](../../includes/digital-twins-tutorial-selector.md)]

Dans ce tutoriel, vous allez créer un graphe dans Azure Digital Twins à l’aide de modèles, de jumeaux et de relations. L’outil pour ce tutoriel est le [jeu de commandes Azure Digital Twins pour Azure CLI](concepts-cli.md). 

Vous pouvez utiliser les commandes CLI pour effectuer des actions Azure Digital Twins essentielles, telles que le chargement de modèles, la création et la modification de jumeaux et la création de relations. Vous pouvez également consulter la [documentation de référence sur le jeu de commandes az dt](/cli/azure/dt) pour voir l’ensemble complet de commandes CLI.

Ce tutoriel présente les procédures suivantes :
> [!div class="checklist"]
> * Modéliser un environnement
> * Créer des jumeaux numériques
> * Ajouter des relations pour former un graphe
> * Interroger le graphe pour répondre aux questions

## <a name="prerequisites"></a>Prérequis

Pour effectuer les étapes de ce tutoriel, vous devez d’abord satisfaire les prérequis suivants.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

### <a name="download-the-sample-models"></a>Télécharger les exemples de modèle

Ce tutoriel utilise deux modèles préécrits qui font partie de l’[exemple de projet de bout en bout](/samples/azure-samples/digital-twins-samples/digital-twins-samples/) C# pour Azure Digital Twins. Les fichiers de modèle se trouvent ici : 
* [Room.json](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json)
* [Floor.json](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json)

Pour importer les fichiers sur votre machine, utilisez les liens de navigation ci-dessus et copiez les corps des fichiers dans des fichiers locaux sur votre machine avec le même nom (*Room.json* et *Floor.json*).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="set-up-cloud-shell-session"></a>Configurer une session Cloud Shell
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

### <a name="prepare-an-azure-digital-twins-instance"></a>Préparer une instance Azure Digital Twins

Pour utiliser Azure Digital Twins dans cet article, vous devez d’abord **configurer une instance Azure Digital Twins** et les autorisations requises pour l’utiliser. Si vous disposez déjà d’une instance Azure Digital Twins configurée lors d’une tâche précédente, vous pouvez utiliser cette instance.

Dans le cas contraire, suivez les instructions fournies dans [Procédure : Configurer une instance et l’authentification](how-to-set-up-instance-cli.md). Ces instructions contiennent également les étapes permettant de vérifier que vous avez correctement effectué chaque étape et que vous êtes prêt à passer à l’utilisation de votre nouvelle instance.

Une fois que vous avez configuré votre instance Azure Digital Twins, notez les valeurs suivantes dont vous aurez besoin pour vous connecter ultérieurement à l’instance :
* Le **_nom d’hôte_** de l’instance
* L’**abonnement Azure** que vous avez utilisé pour créer l’instance 

Vous pouvez obtenir ces deux valeurs pour votre instance dans la sortie de la commande Azure CLI suivante : 

```azurecli-interactive
az dt show --dt-name <ADT_instance_name>
```

:::image type="content" source="media/tutorial-command-line/cli/instance-details.png" alt-text="Capture d’écran d’une fenêtre de navigateur Cloud Shell montrant la sortie de la commande az dt show. Le champ hostName et l’ID d’abonnement (qui fait partie du champ id) sont mis en évidence.":::

## <a name="model-a-physical-environment-with-dtdl"></a>Modéliser un environnement physique avec DTDL

Maintenant que l’interface CLI et l’instance Azure Digital Twins sont configurées, vous pouvez commencer à créer un graphe d’un scénario. 

La première étape de la création d’une solution Azure Digital Twins consiste à définir des [modèles](concepts-models.md) de jumeaux pour votre environnement. 

Les modèles sont similaires aux classes dans les langages de programmation orientés objet. Ils fournissent des modèles de [jumeaux numériques](concepts-twins-graph.md) définis par l’utilisateur à suivre et à instancier ultérieurement. Ils sont écrits dans un langage de type JSON appelé **DTDL (Digital Twins Definition Language**) et peuvent définir les *propriétés*, la *télémétrie*, les *relations* et les *composants* d’un jumeau.

> [!NOTE]
> DTDL permet également de définir des *commandes* sur des jumeaux numériques. Toutefois, les commandes ne sont actuellement pas prises en charge dans le service Azure Digital Twins.

Sur votre machine, accédez au fichier *Room.json* que vous avez créé dans la section [Prérequis](#prerequisites). Ouvrez-le dans un éditeur de code et modifiez-le de la manière suivante :

[!INCLUDE [digital-twins-tutorial-model-create.md](../../includes/digital-twins-tutorial-model-create.md)]

### <a name="upload-models-to-azure-digital-twins"></a>Charger des modèles sur Azure Digital Twins

Après avoir conçu les modèles, vous devez les charger sur votre instance Azure Digital Twins. Cela configure votre instance du service Azure Digital Twins avec votre propre vocabulaire de domaine personnalisé. Une fois que vous avez chargé les modèles, vous pouvez créer des instances de jumeau qui les utilisent.

1. Pour ajouter des modèles à l’aide de Cloud Shell, vous devez charger vos fichiers de modèle dans le stockage de Cloud Shell afin qu’ils soient disponibles lorsque vous exécutez la commande Cloud Shell qui les utilise. Sélectionnez l’icône « Charger/Télécharger des fichiers » et choisissez « Charger ».

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="Capture d’écran de fenêtre de navigateur Cloud Shell montrant la sélection de l’icône Charger.":::
    
    Accédez au fichier *Room.json* sur votre machine et sélectionnez « Ouvrir ». Ensuite, répétez cette étape pour *Floor.json*.

1. Ensuite, utilisez la commande [az dt model create](/cli/azure/dt/model#az_dt_model_create) comme indiqué ci-dessous pour charger votre modèle Room mis à jour sur votre instance Azure Digital Twins. La deuxième commande charge un autre modèle, Floor, que vous utiliserez également dans la section suivante pour créer différents types de jumeaux.

    ```azurecli-interactive
    az dt model create --dt-name <ADT_instance_name> --models Room.json
    az dt model create --dt-name <ADT_instance_name> --models Floor.json
    ```
    
    La sortie de chaque commande affiche des informations sur le modèle chargé avec succès.

    >[!TIP]
    >Vous pouvez également charger tous les modèles dans un répertoire en même temps, à l’aide de l’option `--from-directory` de la commande model create. Pour plus d’informations, consultez [Paramètres facultatifs pour az dt model create](/cli/azure/dt/model#az_dt_model_create-optional-parameters).

1. Vérifiez que les modèles ont été créés avec la commande [az dt model list](/cli/azure/dt/model#az_dt_model_list) comme indiqué ci-dessous. Cette commande imprime une liste de tous les modèles qui ont été chargés sur l’instance Azure Digital Twins, avec leurs informations complètes. 

    ```azurecli-interactive
    az dt model list --dt-name <ADT_instance_name> --definition
    ```
    
    Recherchez le modèle Room modifié dans les résultats :
    
    :::image type="content" source="media/tutorial-command-line/cli/output-get-models.png" alt-text="Capture d’écran de Cloud Shell montrant le résultat de la commande model list, qui comprend le modèle Room mis à jour." lightbox="media/tutorial-command-line/cli/output-get-models.png":::

### <a name="errors"></a>Erreurs

L’interface CLI gère également les erreurs du service. 

Réexécutez la commande `az dt model create` pour essayer de recharger l’un des modèles que vous venez de charger :

```azurecli-interactive
az dt model create --dt-name <ADT_instance_name> --models Room.json
```

Comme les modèles ne peuvent pas être remplacés, un code d’erreur `ModelIdAlreadyExists` est désormais retourné.

## <a name="create-digital-twins"></a>Créer des jumeaux numériques

Maintenant que certains modèles ont été chargés sur votre instance Azure Digital Twins, vous pouvez créer des [jumeaux numériques](concepts-twins-graph.md) basés sur les définitions de modèle. Les jumeaux numériques représentent les entités au sein de votre environnement d’entreprise (par exemple les capteurs dans une ferme, les salles d’un bâtiment ou les voyants d’une voiture). 

Pour créer un jumeau numérique, utilisez la commande [az dt twin create](/cli/azure/dt/twin#az_dt_twin_create). Vous devez référencer le modèle sur lequel le jumeau est basé, et vous pouvez éventuellement définir des valeurs initiales pour les propriétés du modèle. Vous n’avez pas besoin de transmettre d’informations de relation à ce stade.

1. Exécutez ce code dans Cloud Shell pour créer plusieurs jumeaux, basés sur le modèle Room que vous avez mis à jour et sur un autre modèle, Floor. Rappelez-vous que Room a trois propriétés ; vous pouvez donc fournir des arguments avec les valeurs initiales de ces propriétés. (L’initialisation des valeurs de propriétés est facultative en général, mais elle est nécessaire pour ce tutoriel.)

    ```azurecli-interactive
    az dt twin create --dt-name <ADT_instance_name> --dtmi "dtmi:example:Room;2" --twin-id room0 --properties '{"RoomName":"Room0", "Temperature":70, "HumidityLevel":30}'
    az dt twin create --dt-name <ADT_instance_name> --dtmi "dtmi:example:Room;2" --twin-id room1 --properties '{"RoomName":"Room1", "Temperature":"80", "HumidityLevel":"60"}'
    az dt twin create --dt-name <ADT_instance_name> --dtmi "dtmi:example:Floor;1" --twin-id floor0
    az dt twin create --dt-name <ADT_instance_name> --dtmi "dtmi:example:Floor;1" --twin-id floor1
    ```

    >[!NOTE]
    > Si vous utilisez Cloud Shell dans l’environnement PowerShell, vous devrez peut-être placer les guillemets dans une séquence d’échappement afin que la valeur JSON `--properties` soit correctement analysée. Avec cette modification, les commandes permettant de créer les jumeaux de pièce se présentent comme suit :
    >
    > ```azurecli-interactive
    > az dt twin create --dt-name <ADT_instance_name> --dtmi "dtmi:example:Room;2" --twin-id room0 --properties '{\"RoomName\":\"Room0\", \"Temperature\":70, \"HumidityLevel\":30}'
    > az dt twin create --dt-name <ADT_instance_name> --dtmi "dtmi:example:Room;2" --twin-id room1 --properties '{\"RoomName\":\"Room1\", \"Temperature\":80, \"HumidityLevel\":60}'
    > ```
    > Ceci est reflété dans la capture d’écran ci-dessous.
    
    La sortie de chaque commande affiche des informations sur le jumeau créé (notamment les propriétés des jumeaux de pièce qui ont été initialisés avec eux).

1. Vous pouvez vérifier que les jumeaux ont été créés avec la commande [az dt twin query](/cli/azure/dt/twin#az_dt_twin_query), comme indiqué ci-dessous. La requête affichée recherche tous les jumeaux numériques dans votre instance Azure Digital Twins.
    
    ```azurecli-interactive
    az dt twin query --dt-name <ADT_instance_name> --query-command "SELECT * FROM DIGITALTWINS"
    ```
    
    Recherchez les jumeaux room0, room1, floor0 et floor1 dans les résultats. Voici un extrait qui présente une partie du résultat de cette requête.
    
    :::image type="content" source="media/tutorial-command-line/cli/output-query-all.png" alt-text="Capture d’écran de Cloud Shell montrant le résultat partiel de la requête de jumeau, notamment room0 et Room1." lightbox="media/tutorial-command-line/cli/output-query-all.png":::

### <a name="modify-a-digital-twin"></a>Modifier un jumeau numérique

Vous pouvez également modifier les propriétés d’un jumeau que vous avez créé. 

1. Exécutez cette commande [az dt twin update](/cli/azure/dt/twin#az_dt_twin_update) pour changer le RoomName de *room0* de *Room0* en *PresidentialSuite* :

    ```azurecli-interactive
    az dt twin update --dt-name <ADT_instance_name> --twin-id room0 --json-patch '{"op":"add", "path":"/RoomName", "value": "PresidentialSuite"}'
    ```
    
    >[!NOTE]
    > Si vous utilisez Cloud Shell dans l’environnement PowerShell, vous devrez peut-être placer les guillemets dans une séquence d’échappement afin que la valeur JSON `--json-patch` soit correctement analysée. Avec cette modification, la commande permettant de mettre à jour le jumeau ressemble à ceci :
    >
    > ```azurecli-interactive
    > az dt twin update --dt-name <ADT_instance_name> --twin-id room0 --json-patch '{\"op\":\"add\", \"path\":\"/RoomName\", \"value\": \"PresidentialSuite\"}'
    > ```
    > Ceci est reflété dans la capture d’écran ci-dessous.
    
    La sortie de cette commande affiche les informations actuelles du jumeau, et la nouvelle valeur de `RoomName` doit figurer dans le résultat.

    :::image type="content" source="media/tutorial-command-line/cli/output-update-twin.png" alt-text="Capture d’écran de Cloud Shell montrant le résultat de la commande update, qui mentionne PresidentialSuite comme RoomName." lightbox="media/tutorial-command-line/cli/output-update-twin.png":::

1. Vous pouvez vérifier que la mise à jour a réussi en exécutant la commande [az dt twin show](/cli/azure/dt/twin#az_dt_twin_show) pour voir les informations de room0 :

    ```azurecli-interactive
    az dt twin show --dt-name <ADT_instance_name> --twin-id room0
    ```
    
    La sortie doit refléter le nom mis à jour.

## <a name="create-a-graph-by-adding-relationships"></a>Créer un graphe en ajoutant des relations

Ensuite, vous pouvez créer des **relations** entre ces jumeaux, afin de les raccorder sur un [graphe de jumeaux](concepts-twins-graph.md). Les graphes de jumeaux servent à représenter un environnement entier. 

Les types de relations que vous pouvez créer d’un jumeau à un autre sont définis dans les [modèles](#model-a-physical-environment-with-dtdl) que vous avez chargés. La [définition de modèle pour Floor](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) spécifie que les étages peuvent avoir un type de relation nommé *contains*. Cela permet de créer une relation de type *contains* entre chaque jumeau Floor et la pièce correspondante qu’il contient.

Pour ajouter une relation, utilisez la commande [az dt twin relationship create](/cli/azure/dt/twin/relationship#az_dt_twin_relationship_create). Spécifiez le jumeau d’où provient la relation, le type de relation, et le jumeau avec lequel la relation établit une connexion. Pour finir, attribuez un ID unique à la relation. Si une relation a été définie avec des propriétés, vous pouvez également initialiser les propriétés de relation dans cette commande.

1. Exécutez le code suivant pour ajouter une relation de type *contains* entre chaque jumeau Floor que vous avez créé et le jumeau Room correspondant. Les relations sont nommées relationship0 et relationship1.

    ```azurecli-interactive
    az dt twin relationship create --dt-name <ADT_instance_name> --relationship-id relationship0 --relationship contains --twin-id floor0 --target room0
    az dt twin relationship create --dt-name <ADT_instance_name> --relationship-id relationship1 --relationship contains --twin-id floor1 --target room1
    ```
    
    >[!TIP]
    >La relation *contains* dans le modèle [Floor](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) a également été définie avec deux propriétés, `ownershipUser` et `ownershipDepartment`. Vous pouvez donc aussi fournir des arguments avec les valeurs initiales de ces propriétés lorsque vous créez les relations.
    > Pour créer une relation avec ces propriétés initialisées, ajoutez l’option `--properties` à l’une des commandes ci-dessus, comme suit :
    > ```azurecli-interactive
    > ... --properties '{"ownershipUser":"MyUser", "ownershipDepartment":"MyDepartment"}'
    > ``` 
    > 
    > Si vous utilisez Cloud Shell dans l’environnement PowerShell, vous devrez peut-être placer les guillemets dans une séquence d’échappement afin que la valeur JSON `--properties` soit correctement analysée.
    
    La sortie de chaque commande affiche des informations sur la relation créée.

1. Vous pouvez vérifier les relations avec l’une des commandes suivantes, qui interrogent les relations dans votre instance Azure Digital Twins.
    * Pour voir toutes les relations partant de chaque étage (affichage des relations d’un côté) :
        ```azurecli-interactive
        az dt twin relationship list --dt-name <ADT_instance_name> --twin-id floor0
        az dt twin relationship list --dt-name <ADT_instance_name> --twin-id floor1
        ```
    * Pour voir toutes les relations arrivant à chaque pièce (affichage de la relation de l’« autre » côté) :
        ```azurecli-interactive
        az dt twin relationship list --dt-name <ADT_instance_name> --twin-id room0 --incoming
        az dt twin relationship list --dt-name <ADT_instance_name> --twin-id room1 --incoming
        ```
    * Pour rechercher ces relations individuellement, par ID :
        ```azurecli-interactive
        az dt twin relationship show --dt-name <ADT_instance_name> --twin-id floor0 --relationship-id relationship0
        az dt twin relationship show --dt-name <ADT_instance_name> --twin-id floor1 --relationship-id relationship1
        ```

Les jumeaux et les relations que vous avez configurés dans ce tutoriel forment le graphe conceptuel suivant :

:::image type="content" source="media/tutorial-command-line/app/sample-graph.png" alt-text="Diagramme montrant un graphe conceptuel. floor0 est connecté par le biais de relationship0 à room0, et floor1 est connecté par le biais de relationship1 à room1." border="false" lightbox="media/tutorial-command-line/app/sample-graph.png":::

## <a name="query-the-twin-graph-to-answer-environment-questions"></a>Interroger le graphe de jumeaux pour répondre à des questions environnementales

L’une des principales fonctionnalités d’Azure Digital Twins est la capacité à [interroger facilement et efficacement](concepts-query-language.md) votre graphe de jumeaux pour répondre à des questions sur votre environnement. Dans Azure CLI, cette opération s’effectue à l’aide de la commande [az dt twin query](/cli/azure/dt/twin#az_dt_twin_query).

Exécutez les requêtes suivantes dans Cloud Shell pour répondre à certaines questions sur l’exemple d’environnement.

1. **Quelles sont les entités de mon environnement représentées dans Azure Digital Twins ?** (interroger tout)

    ```azurecli-interactive
    az dt twin query --dt-name <ADT_instance_name> --query-command "SELECT * FROM DIGITALTWINS"
    ```

    Cela vous permet d’évaluer votre environnement en un coup d’œil et de vérifier que tout est représenté comme vous le souhaitez dans Azure Digital Twins. Le résultat est une sortie contenant chaque jumeau numérique avec ses détails. Voici un extrait :

    :::image type="content" source="media/tutorial-command-line/cli/output-query-all.png" alt-text="Capture d’écran de Cloud Shell montrant le résultat partiel de la requête de jumeau, notamment room0 et Room1." lightbox="media/tutorial-command-line/cli/output-query-all.png":::

    >[!TIP]
    >Vous reconnaîtrez peut-être qu’il s’agit de la même commande que celle que vous avez utilisée dans la section [Créer des jumeaux numériques](#create-digital-twins) pour rechercher tous les jumeaux numériques Azure dans l’instance.

1. **Quelles sont les pièces dans mon environnement ?** (requête par modèle)

    ```azurecli-interactive
    az dt twin query --dt-name <ADT_instance_name> --query-command "SELECT * FROM DIGITALTWINS T WHERE IS_OF_MODEL(T, 'dtmi:example:Room;2')"
    ```

    Vous pouvez limiter votre requête aux jumeaux d’un certain type, afin d’obtenir des informations plus spécifiques sur ce qui est représenté. Le résultat montre room0 et room1, mais ne montre **pas** floor0 ni floor1 (car il s’agit d’étages, et non de pièces).
    
    :::image type="content" source="media/tutorial-command-line/cli/output-query-model.png" alt-text="Capture d’écran de Cloud Shell montrant le résultat d’une requête de modèle, qui comprend uniquement room0 et room1." lightbox="media/tutorial-command-line/cli/output-query-model.png":::

1. **Quelles sont les pièces de floor0 ?** (requête par relation)

    ```azurecli-interactive
    az dt twin query --dt-name <ADT_instance_name> --query-command "SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.`$dtId = 'floor0'"
    ```

    Vous pouvez interroger en fonction des relations de votre graphe, afin d’obtenir des informations sur la façon dont les jumeaux sont raccordés ou de limiter votre requête à une certaine zone. Seule room0 se trouve à l’étage floor0 ; il s’agit donc de la seule pièce dans le résultat.

    :::image type="content" source="media/tutorial-command-line/cli/output-query-relationship.png" alt-text="Capture d’écran de Cloud Shell montrant le résultat de la requête de relation, qui comprend room0." lightbox="media/tutorial-command-line/cli/output-query-relationship.png":::

    > [!NOTE]
    > Notez que l’ID d’un jumeau (comme floor0 dans la requête ci-dessus) est interrogé à l’aide du champ de métadonnées `$dtId`. 
    >
    >Lors de l’utilisation de Cloud Shell pour exécuter une requête avec des champs de métadonnées comme celui-ci qui commencent par `$`, vous devez échapper le `$` avec un backtick (apostrophe inversée) pour signaler à Cloud Shell qu’il ne s’agit pas d’une variable et qu’il doit être consommé comme littéral dans le texte de la requête. Ceci est reflété dans la capture d’écran ci-dessus.

1. **Quels sont les jumeaux dans mon environnement dont la température est supérieure à 75 ?** (requête par propriété)

    ```azurecli-interactive
    az dt twin query --dt-name <ADT_instance_name> --query-command "SELECT * FROM DigitalTwins T WHERE T.Temperature > 75"
    ```

    Vous pouvez interroger le graphe en fonction de propriétés afin de répondre à diverses questions, notamment pour rechercher les anomalies dans votre environnement qui peuvent nécessiter votre attention. D’autres opérateurs de comparaison ( *<* , *>* , *=* ou *!=* ) sont également pris en charge. room1 apparaît ici dans les résultats, car elle a une température de 80.

    :::image type="content" source="media/tutorial-command-line/cli/output-query-property.png" alt-text="Capture d’écran de Cloud Shell montrant le résultat d’une requête de propriété, qui comprend uniquement room1." lightbox="media/tutorial-command-line/cli/output-query-property.png":::

1. **Quelles sont les pièces de l’étage *floor0* dont la température est supérieure à 75 ?** (requête composée)

    ```azurecli-interactive
    az dt twin query --dt-name <ADT_instance_name> --query-command "SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.`$dtId = 'floor0' AND IS_OF_MODEL(room, 'dtmi:example:Room;2') AND room.Temperature > 75"
    ```

    Vous pouvez également combiner les requêtes précédentes comme vous le feriez dans SQL, à l’aide d’opérateurs de combinaison tels que `AND`, `OR` et `NOT`. Cette requête utilise `AND` pour rendre plus spécifique la requête précédente sur les températures des jumeaux. Le résultat contient désormais uniquement les pièces dont la température est supérieure à 75 et qui se trouvent à l’étage floor0 (en l’occurrence, aucune). Le jeu de résultat est vide.

    :::image type="content" source="media/tutorial-command-line/cli/output-query-compound.png" alt-text="Capture d’écran de Cloud Shell montrant le résultat d’une requête composée, qui ne comprend aucun élément." lightbox="media/tutorial-command-line/cli/output-query-compound.png":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

À l’issue de ce tutoriel, vous pourrez choisir les ressources à supprimer, en fonction de ce que vous souhaitez faire ensuite.

* **Si vous envisagez de passer au tutoriel suivant**, vous pouvez conserver les ressources que vous avez configurées ici et réutiliser l’instance Azure Digital Twins sans effacer quoi que ce soit.

* **Si vous souhaitez continuer à utiliser l’instance d’Azure Digital Twins tout en effaçant complètement ses modèles, jumeaux et relations**, vous pouvez utiliser les commandes [az dt twin relationship delete](/cli/azure/dt/twin/relationship#az_dt_twin_relationship_delete), [az dt twin delete](/cli/azure/dt/twin#az_dt_twin_delete) et [az dt model delete](/cli/azure/dt/model#az_dt_model_delete) pour effacer respectivement les relations, jumeaux et modèles de votre instance.

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Vous pouvez également supprimer les fichiers de modèle que vous avez créés sur votre machine locale.

## <a name="next-steps"></a>Étapes suivantes 

Dans ce tutoriel, vous avez commencé à utiliser Azure Digital Twins en générant un graphe dans votre instance à l’aide d’Azure CLI. Vous avez créé des modèles, des jumeaux numériques et des relations pour former un graphe. Vous avez également exécuté des requêtes sur le graphe, pour vous faire une idée des types de questions auxquelles Azure Digital Twins peut répondre à propos d’un environnement.

Passez au tutoriel suivant pour combiner Azure Digital Twins à d’autres services Azure afin de bénéficier d’un scénario de bout en bout piloté par les données :
> [!div class="nextstepaction"]
> [Tutoriel : Connecter une solution de bout en bout](tutorial-end-to-end.md)