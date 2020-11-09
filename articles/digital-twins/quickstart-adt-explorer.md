---
title: Démarrage rapide - Explorer un exemple de scénario
titleSuffix: Azure Digital Twins
description: Démarrage rapide - Utiliser l’exemple ADT Explorer pour visualiser et explorer un scénario prédéfini.
author: baanders
ms.author: baanders
ms.date: 9/24/2020
ms.topic: quickstart
ms.service: digital-twins
ms.openlocfilehash: 466129e8435ef694821b078592a100a111a43f3a
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93242277"
---
# <a name="quickstart---explore-a-sample-azure-digital-twins-scenario-using-adt-explorer"></a>Démarrage rapide - Explorer un exemple de scénario Azure Digital Twins avec ADT Explorer

Avec Azure Digital Twins, vous pouvez créer et interagir avec les modèles de vos environnements réels. Pour cela, vous pouvez modéliser des éléments sous la forme de **jumeaux numériques** , puis les connecter à un **graphe** de connaissances qui peut répondre aux événements en direct et être interrogé.

Dans ce guide de démarrage rapide, vous allez explorer un graphe Azure Digital Twins prédéfini, à l’aide d’un exemple d’application appelé [**Azure Digital Twins (ADT) Explorer**](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). ADT Explorer vous permet de charger une représentation numérique d’un environnement, d’afficher des images visuelles des jumeaux et du graphe qui sont créées pour représenter l’environnement dans Azure Digital Twins et d’effectuer d’autres activités de gestion par le biais d’une expérience visuelle basée sur un navigateur.

Le guide de démarrage rapide contient les étapes importantes qui suivent :

1. Configurer une instance Azure Digital Twins et ADT Explorer
1. Charger des modèles prédéfinis et des données de graphe pour construire l’exemple de scénario
1. Explorer le graphe de scénario créé
1. Apporter des modifications au graphe

L’exemple de graphe avec lequel vous allez travailler représente un bâtiment comprenant deux étages et deux pièces. Le graphe ressemblera à ceci :

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="Vue d’un graphe constitué de quatre nœuds circulaires reliés par des flèches. Un cercle nommé « Floor1 » est relié par une flèche nommée « contains » à un cercle intitulé « Room1 » ; un cercle nommé « Floor0 » est relié par une flèche nommée « contains » à un cercle intitulé « Room0 ». « Floor1 » et « Floor0 » ne sont pas reliés entre eux.":::

## <a name="prerequisites"></a>Prérequis

Pour suivre ce guide de démarrage rapide, vous devez avoir un abonnement Azure. Si vous n’en avez pas, **[créez-en un gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** .

Vous avez également besoin de **Node.js** sur votre machine. Vous pouvez vous procurer la dernière version à partir de ce lien : [Node.js](https://nodejs.org/).

Enfin, vous devez également télécharger l’exemple à utiliser dans le guide de démarrage rapide, à savoir l’exemple d’application **ADT Explorer**. Cet exemple contient l’application que vous utiliserez dans le guide de démarrage rapide pour charger et explorer un scénario Azure Digital Twins ainsi que les fichiers d’exemples de scénario. Pour accéder à l’exemple, cliquez sur le lien suivant : [Azure Digital Twins (ADT) Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Cliquez sur le bouton *Télécharger le zip* pour télécharger sur votre ordinateur le fichier *.ZIP* de cet exemple de code. Cette opération téléchargera un dossier .ZIP sur votre machine : _**Azure_Digital_Twins__ADT__explorer.zip**_. Décompressez le dossier et extrayez les fichiers.

## <a name="set-up-azure-digital-twins-and-adt-explorer"></a>Configurer Azure Digital Twins et ADT Explorer

Si vous utilisez Azure Digital Twins, la première chose à faire est de **configurer une instance Azure Digital Twins**. Une fois que vous avez créé une instance de ce service et **configuré vos informations d’identification** pour vous authentifier auprès d’ADT Explorer, vous pouvez **vous connecter à l’instance dans ADT Explorer** et y ajouter les exemples de données que nous verrons plus loin dans ce guide de démarrage rapide.

Le reste de cette section vous guidera dans ces étapes.

### <a name="set-up-azure-digital-twins-instance"></a>Configurer l’instance Azure Digital Twins

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="set-up-local-azure-credentials"></a>Configurer les informations d’identification Azure locales

L’application ADT Explorer utilise [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (qui fait partie de la bibliothèque `Azure.Identity`) pour authentifier les utilisateurs auprès de l’instance Azure Digital Twins quand vous l’exécutez sur votre ordinateur local. Pour plus d’informations sur les différentes façons dont une application cliente peut s’authentifier auprès d’Azure Digital Twins, consultez [*Guide pratique : Écrire le code d’authentification d’une application*](how-to-authenticate-client.md).

Avec ce type d’authentification, ADT Explorer recherche les informations d’identification dans votre environnement local, comme une connexion Azure dans une interface [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) locale ou dans Visual Studio/Visual Studio Code. Cela signifie que vous devez **vous connecter à Azure localement** via l’un de ces mécanismes, afin de configurer les informations d’identification pour l’application ADT Explorer.

Si vous êtes déjà connecté à Azure via l’une de ces manières, vous pouvez passer à la [section suivante](#run-and-configure-adt-explorer).

Dans le cas contraire, vous pouvez installer l’interface **Azure CLI** locale en procédant comme suit :
1. Suivez le processus via [**ce lien d’installation**](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) pour effectuer l’installation qui correspond à votre système d’exploitation.
2. Ouvrez une fenêtre de console sur votre machine.
3. Exécutez `az login` et suivez les invites d’authentification pour vous connecter à votre compte Azure.

Après cela, ADT Explorer récupérera automatiquement vos informations d’identification Azure quand vous l’exécuterez dans la section suivante.

Vous pouvez fermer la fenêtre de la console d’authentification si vous le souhaitez ou la garder ouverte pour l’utiliser à l’étape suivante.

### <a name="run-and-configure-adt-explorer"></a>Exécuter et configurer ADT Explorer

Ensuite, vous allez exécuter l’application ADT Explorer et la configurer pour votre instance Azure Digital Twins.

Accédez au dossier _**Azure_Digital_Twins__ADT__explorer**_ téléchargé et décompressé. Ouvrez une fenêtre de console dans l’emplacement de dossier *Azure_Digital_Twins__ADT__explorer/client/src*.

Exécutez `npm install` pour télécharger toutes les dépendances nécessaires.

Ensuite, démarrez l’application en exécutant `npm run start`.

Après quelques secondes, une fenêtre de navigateur s’ouvre et l’application s’affiche dans le navigateur.

:::image type="content" source="media/quickstart-adt-explorer/explorer-blank.png" alt-text="Fenêtre de navigateur présentant une application exécutée sur localhost:3000. L’application s’appelle ADT Explorer. Elle comprend les sections suivantes : Explorateur de requêtes, Vue du modèle, Vue du graphe et Explorateur de propriétés. Il n’y a pas encore de données à l’écran." lightbox="media/quickstart-adt-explorer/explorer-blank.png":::

Cliquez sur le bouton *Se connecter* situé en haut de la fenêtre (illustré dans l’image ci-dessous) pour configurer ADT Explorer de manière à ce qu’il utilise l’instance que vous avez configurée. 

:::image type="content" source="media/quickstart-adt-explorer/sign-in.png" alt-text="ADT Explorer : icône de connexion mise en évidence en haut de la fenêtre. L’icône représente la silhouette d’une personne à laquelle est superposée une clé." lightbox="media/quickstart-adt-explorer/sign-in.png":::

Entrez l’ *URL de l’instance Azure Digital Twins* que vous avez recueillie plus tôt dans la section [Conditions préalables](#prerequisites), au format *https://{nom d’hôte de l’instance}* .

>[!NOTE]
> Vous pouvez modifier ces informations à tout moment en sélectionnant cette même icône afin de vous connecter. Les valeurs que vous avez passées auront été conservées.

> [!TIP]
> Si un message d’erreur `SignalRService.subscribe` s’affiche quand vous vous connectez, vérifiez que votre URL Azure Digital Twins commence par *https://* .

Si la fenêtre contextuelle *Autorisations demandées* Microsoft s’affiche, autorisez l’application, puis cliquez sur Accepter pour continuer.

## <a name="add-the-sample-data"></a>Ajouter les exemples de données

Ensuite, vous allez importer l’exemple de scénario et le graphe dans ADT Explorer. L’exemple de scénario se trouve également dans le dossier **Azure_Digital_Twins__ADT__explorer** que vous avez téléchargé.

### <a name="models"></a>Modèles

Dans une solution Azure Digital Twins, la première chose à faire est de définir le vocabulaire de votre environnement. Pour ce faire, vous allez créer des [**modèles**](concepts-models.md) personnalisés, qui décrivent les types d’entité qui se trouvent dans votre environnement. 

Chaque modèle est écrit dans un langage de type JSON-LD appelé **Digital Twin Definition Language (DTDL)** . En outre, chaque modèle décrit un seul type d’entité selon ses *propriétés* , ses *données de télémétrie* , ses *relations* et ses *composants*. Plus tard, vous utiliserez ces modèles comme base pour vos jumeaux numériques, qui représentent des instances de ces types.

En général, lorsque vous créez un modèle, vous effectuez trois étapes :
1. Écrire la définition du modèle (nous l’avons déjà fait dans le cadre de l’exemple de solution)
2. Valider le modèle pour être sûr que la syntaxe est exacte (nous l’avons déjà fait dans le cadre de l’exemple de solution)
3. Charger le modèle dans l’instance Azure Digital Twins
 
Dans ce guide de démarrage rapide, les fichiers de modèle ont déjà été écrits et validés, et ils sont inclus dans la solution que vous avez téléchargée. Dans cette section, vous allez charger deux modèles préécrits dans votre instance pour définir les composants d’un environnement de génération :
* Floor
* Salle

#### <a name="upload-models"></a>Charger des modèles

Dans la section *Vue du modèle* , cliquez sur l’icône *Charger un modèle*.

:::image type="content" source="media/quickstart-adt-explorer/upload-model.png" alt-text="Dans la section Vue du modèle, l’icône du milieu est mise en évidence. On peut voir une flèche pointant vers un nuage." lightbox="media/quickstart-adt-explorer/upload-model.png":::
 
1. Dans la zone de sélection des fichiers qui s’affiche, accédez au dossier *Azure_Digital_Twins__ADT__explorer/client/examples* dans le dépôt téléchargé.
2. Sélectionnez *Room.json* et *Floor.json* , puis cliquez sur OK (vous pouvez charger d’autres modèles si vous le souhaitez. Cependant, nous ne les utiliserons pas dans ce guide de démarrage rapide).
3. Connectez-vous à votre compte Azure, comme vous le demande la fenêtre contextuelle.

>[!NOTE]
>Si le message d’erreur suivant s’affiche : :::image type="content" source="media/quickstart-adt-explorer/error-models-popup.png" alt-text="Fenêtre contextuelle affichant « Erreur : Erreur lors de la récupération des modèles : ClientAuthError : Erreur d’ouverture de la fenêtre contextuelle. Cela peut se produire si vous utilisez Internet Explorer ou si des fenêtres contextuelles sont bloquées dans le navigateur », avec un bouton Fermer en bas" border="false"::: 
> Essayez de désactiver le bloqueur de fenêtres pop-up ou d’utiliser un autre navigateur.

ADT Explorer va maintenant charger ces fichiers de modèle dans votre instance Azure Digital Twins. Ils doivent s’afficher dans la section *Vue du modèle* , sous leur nom convivial et leur ID de modèle complet. Vous pouvez cliquer sur les bulles d’informations *Vue du modèle* pour afficher le code DTDL sous-jacent.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/model-info.png" alt-text="Vue de la section « Vue du modèle » avec deux définitions de modèle : Floor (dtmi:example:Floor;1) et Room (dtmi:example:Room;1). L’icône « Afficher le modèle », qui représente la lettre « i » entourée d’un cercle, est mise en évidence pour chaque modèle." lightbox="media/quickstart-adt-explorer/model-info.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="twins-and-the-twin-graph"></a>Jumeaux et graphe de jumeaux

Maintenant que certains modèles ont été chargés dans votre instance Azure Digital Twins, vous pouvez ajouter des [**jumeaux numériques**](concepts-twins-graph.md) basés sur les définitions de modèle. 

Les jumeaux numériques représentent les entités de votre environnement d’entreprise (par exemple les capteurs dans une ferme, les voyants d’une voiture, ou dans notre exemple, les pièces d’un étage). Vous pouvez créer de nombreux jumeaux d’un type de modèle donné (par exemple, plusieurs pièces qui utilisent le modèle *Room* ), et les connecter à l’aide de relations dans un **graphe de jumeaux** représentant l’environnement complet.

Dans cette section, vous allez charger des jumeaux précréés qui sont connectés dans un graphe lui aussi précréé. Le graphe contient deux étages et deux pièces, connectés de la façon suivante :
* *Floor0*
    - contient *Room0*
* *Floor1*
    - contient *Room1*

#### <a name="import-the-graph"></a>Importer le graphe

Dans la section *Vue du graphe* , cliquez sur l’icône *Importer le graphe*.

:::image type="content" source="media/quickstart-adt-explorer/import-graph.png" alt-text="Dans la section Vue du graphe, une icône est mise en évidence. On peut voir une flèche pointant vers un nuage." lightbox="media/quickstart-adt-explorer/import-graph.png":::

Dans le sélecteur de fichiers, accédez au dossier *Azure_Digital_Twins__ADT__explorer/client/examples* , puis choisissez la feuille de calcul _**buildingScenario.xlsx**_. Ce fichier contient une description de l’exemple de graphe. Appuyez sur OK.

Après quelques secondes, ADT Explorer ouvre la vue *Importer* dans laquelle vous voyez un aperçu du graphe qui va être chargé.

Pour confirmer le chargement du graphe, cliquez sur l’icône *Enregistrer* située en haut à droite de *Vue du graphe*  :

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/graph-preview-save.png" alt-text="Icône Enregistrer mise en évidence dans le volet Aperçu du graphe" lightbox="media/quickstart-adt-explorer/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

ADT Explorer va maintenant utiliser le fichier chargé pour créer les jumeaux demandés et leurs relations. Une boîte de dialogue s’affiche pour indiquer que l’opération est terminée. Appuyez sur *Fermer*.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/import-success.png" alt-text="Boîte de dialogue indiquant la réussite de l’importation du graphe. Le message dit « Importation réussie. 4 jumeaux ont été importés. 2 relations ont été importées. »" lightbox="media/quickstart-adt-explorer/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Le graphe a été chargé dans ADT Explorer. Pour voir le graphe, cliquez sur le bouton *Exécuter la requête* dans la section  *Explorateur du graphe* , qui est situé en haut de la fenêtre ADT Explorer. 

:::image type="content" source="media/quickstart-adt-explorer/run-query.png" alt-text="Bouton « Exécuter la requête » mis en évidence en haut de la fenêtre" lightbox="media/quickstart-adt-explorer/run-query.png":::

La requête par défaut est exécutée pour sélectionner et afficher tous les jumeaux numériques. ADT Explorer récupère tous les jumeaux et toutes les relations à partir du service, puis dessine le graphe correspondant dans la section *Vue du graphe*.

## <a name="explore-the-graph"></a>Explorer le graphe

Vous pouvez maintenant voir le graphe chargé de l’exemple de scénario :

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="Vue de la section Vue du graphe avec un graphe de jumeaux. Un cercle nommé « floor1 » est connecté par une flèche nommée « contains » à un cercle intitulé « room1 » ; un cercle nommé « floor0 » est connecté par une flèche nommée « contains » à un cercle intitulé « room0 ».":::

Les cercles (« nœuds » de graphe) représentent des jumeaux numériques, et les lignes représentent des relations. Vous verrez que le jumeau *Floor0* contient *Room0* et que le jumeau *Floor1* contient *Room1*.

Si vous utilisez une souris, vous pouvez cliquer et faire glisser des parties du graphe pour les déplacer.

### <a name="view-twin-properties"></a>Afficher les propriétés des jumeaux 

Vous pouvez sélectionner un jumeau pour afficher la liste de ses propriétés et de ses valeurs dans la section *Explorateur de propriétés*. 

Voici les propriétés de *Room0*  :

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room0.png" alt-text="Section « Explorateur de propriétés » mise en surbrillance, avec les propriétés de Room0, notamment un champ $dtId pour « Room0 », un champ Temperature avec la valeur 70 et un champ Humidity avec la valeur 30." lightbox="media/quickstart-adt-explorer/properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Notez que *Room0* a une température de **70**.

Voici les propriétés de *Room1*  :

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room1.png" alt-text="Section « Explorateur de propriétés » mise en surbrillance, avec les propriétés de Room1, notamment un champ $dtId pour « Room1 », un champ Temperature avec la valeur 80 et un champ Humidity avec la valeur 60." lightbox="media/quickstart-adt-explorer/properties-room1.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Notez que *Room1* a une température de **80**.

### <a name="query-the-graph"></a>Interroger le graphique

L’une des principales fonctionnalités d’Azure Digital Twins est la capacité à [interroger facilement et efficacement](concepts-query-language.md) votre graphe de jumeaux pour répondre à des questions sur votre environnement. 

L’une des façons d’interroger les jumeaux de votre graphe est d’utiliser leurs *propriétés*. L’interrogation basée sur les propriétés permet de répondre à diverses questions, et notamment de trouver les anomalies de votre environnement.

Dans cette section, vous allez exécuter une requête pour répondre à la question suivante : _**Quels sont les jumeaux de mon environnement dont la température est supérieure à 75 ?**_

Pour voir la réponse, exécutez la requête suivante dans la section *Explorateur de requêtes*  :

```SQL
SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
```

Comme nous l’avons vu, *Room0* a une température de **70** et *Room1* une température de **80**. Par conséquent, seul _**Room1**_ s’affiche dans les résultats.
    
:::image type="content" source="media/quickstart-adt-explorer/result-query-property-before.png" alt-text="Résultats de la requête de propriétés, montrant uniquement Room1" lightbox="media/quickstart-adt-explorer/result-query-property-before.png":::

>[!TIP]
> D’autres opérateurs de comparaison ( *<* , *>* , *=* ou *!=* ) sont également pris en charge dans la requête ci-dessus. Vous pouvez essayer de brancher ces propriétés, mais aussi des valeurs ou des propriétés de jumeaux, dans la requête pour tenter de répondre à vos propres questions.

## <a name="edit-data-in-the-graph"></a>Modifier les données du graphe

Vous pouvez utiliser ADT Explorer pour modifier les propriétés des jumeaux représentés dans votre graphe. Dans cette section, nous allons **_augmenter la température de_ Room0 sur 76**.

Pour ce faire, sélectionnez *Room0* , en affichant la liste de ses propriétés dans la section *Explorateur de propriétés*.

Les propriétés de cette liste sont modifiables. Sélectionnez la valeur de température de **70** pour entrer une nouvelle valeur. Entrez **76** , puis cliquez sur l’icône *Enregistrer* pour remplacer la température par **76**.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/new-properties-room0.png" alt-text="La section Explorateur de propriétés montrant les propriétés de Room0. La valeur de température est une section modifiable qui indique 76, et l’icône d’enregistrement est mise en évidence." lightbox="media/quickstart-adt-explorer/new-properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Une fois l’enregistrement réussi, vous voyez la fenêtre *Informations sur les correctifs* qui affiche le code correctif utilisé en arrière-plan avec les [API](how-to-use-apis-sdks.md) Azure Digital Twins pour effectuer la mise à jour. Appuyez sur *Fermer*.

### <a name="query-to-see-the-result"></a>Exécuter une requête pour afficher le résultat

Pour vérifier que le graphe a bien enregistré votre modification de la température pour *Room0* , réexécutez la requête de tout à l’heure pour **obtenir tous les jumeaux de l’environnement dont la température est supérieure à 75 :**

```SQL
SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
```

Maintenant que la température de *Room0* est passée de **70** à **76** , les deux jumeaux doivent figurer dans les résultats.

:::image type="content" source="media/quickstart-adt-explorer/result-query-property-after.png" alt-text="Résultats de la requête de propriétés, montrant à la fois Room0 et Room1" lightbox="media/quickstart-adt-explorer/result-query-property-after.png":::

## <a name="review-and-contextualize-learnings"></a>Passer en revue et contextualiser les apprentissages

Dans ce guide de démarrage rapide, vous avez créé une instance Azure Digital Twins, vous l’avez connectée à ADT Explorer et vous l’avez remplie avec un exemple de scénario. 

Vous avez ensuite exploré le graphe en...
1. Utilisant une requête pour répondre à une question relative au scénario
2. Modifiant une propriété d’un jumeau numérique
3. Réexécutant la requête pour voir comment la réponse a changé après la modification

L’objectif de cet exercice est de montrer comment vous pouvez utiliser le graphe Azure Digital Twins pour répondre à des questions sur votre environnement, même si celui-ci change continuellement. 

Dans ce guide de démarrage rapide, vous avez mis à jour la température manuellement. Cependant, il est courant dans Azure Digital Twins de connecter des jumeaux numériques à des appareils IoT réels afin qu’ils reçoivent automatiquement les mises à jour en fonction des données de télémétrie. Cela vous permet de créer un graphe dynamique qui reflète toujours l’état réel de votre environnement, ainsi que d’utiliser des requêtes pour obtenir des informations sur ce qui se passe dans votre environnement en temps réel.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Pour conclure ce guide de démarrage rapide, arrêtez l’exécution de l’application console. Cela désactivera la connexion à l’application ADT Explorer dans le navigateur, et vous ne pourrez plus voir les données actives dans le navigateur. Vous pouvez fermer l’onglet du navigateur.

Si vous envisagez de passer aux tutoriels Azure Digital Twins, ne supprimez pas l’instance de ce guide de démarrage rapide, car vous pourrez la réutiliser.
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Enfin, supprimez le dossier d’exemple de projet que vous avez téléchargé sur votre ordinateur local ( _**Azure_Digital_Twins__ADT__explorer**_ ). Vous devrez peut-être supprimer à la fois les versions compressées et décompressées.

## <a name="next-steps"></a>Étapes suivantes 

Ensuite, passez aux tutoriels Azure Digital Twins pour créer vos propres outils d’interaction et de scénario pour Azure Digital Twins.

> [!div class="nextstepaction"]
> [*Tutoriel : Coder une application cliente*](tutorial-code.md)