---
title: Démarrage rapide - Explorer un exemple de scénario
titleSuffix: Azure Digital Twins
description: Démarrage rapide - Utiliser l’exemple Azure Digital Twins Explorer pour visualiser et explorer un scénario prédéfini.
author: baanders
ms.author: baanders
ms.date: 4/27/2021
ms.topic: quickstart
ms.service: digital-twins
ms.openlocfilehash: ca50c0221843b4e24a6287177f36608b86a0a1a9
ms.sourcegitcommit: 2e123f00b9bbfebe1a3f6e42196f328b50233fc5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/27/2021
ms.locfileid: "108075657"
---
# <a name="quickstart---explore-a-sample-azure-digital-twins-scenario-using-azure-digital-twins-explorer"></a>Démarrage rapide - Explorer un exemple de scénario Azure Digital Twins avec Azure Digital Twins Explorer

Avec Azure Digital Twins, vous pouvez créer et interagir avec les modèles de vos environnements réels. Tout d’abord, vous modélisez des éléments sous la forme de **jumeaux numériques**. Ensuite, vous les connectez à un **graphe** de connaissances qui peut répondre aux événements en direct et être interrogé.

Dans ce guide de démarrage rapide, vous allez explorer un graphe Azure Digital Twins prédéfini à l’aide d’un exemple d’application appelé [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Utilisez Azure Digital Twins Explorer pour :

- Charger une représentation numérique d’un environnement
- Afficher des images visuelles des jumeaux et du graphe qui sont créées pour représenter l’environnement dans Azure Digital Twins
- Effectuer d’autres activités de gestion par le biais d’une expérience visuelle basée sur un navigateur.

Le guide de démarrage rapide contient les étapes importantes qui suivent :

1. Configurer une instance Azure Digital Twins et Azure Digital Twins Explorer
1. Charger des modèles prédéfinis et des données de graphe pour construire l’exemple de scénario
1. Explorer le graphe de scénario créé
1. Apporter des modifications au graphe

L’exemple de graphe avec lequel vous allez travailler représente un bâtiment comprenant deux étages et deux pièces. Le graphe ressemblera à cette image :

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/graph-view-full.png" alt-text="Vue d’un graphe constitué de quatre nœuds circulaires reliés par des flèches. Un cercle nommé « Floor1 » est relié par une flèche nommée « contains » à un cercle intitulé « Room1 ». Un cercle nommé « Floor0 » est relié par une flèche nommée « contains » à un cercle intitulé « Room0 ». « Floor1 » et « Floor0 » ne sont pas reliés entre eux.":::

## <a name="prerequisites"></a>Prérequis

Pour suivre ce guide de démarrage rapide, vous devez avoir un abonnement Azure. Si vous n’en avez pas, [créez-en un gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

Vous avez également besoin de **Node.js** sur votre machine. Pour obtenir la version la plus récente, consultez [Node.js](https://nodejs.org/).

Enfin, vous devez également télécharger les exemples que vous utiliserez dans le cadre du guide de démarrage rapide. L’exemple d’application est **Azure Digital Twins Explorer**. Cet exemple contient l’application que vous utiliserez dans le guide de démarrage rapide pour charger et explorer un scénario Azure Digital Twins. Il contient également les fichiers d’exemples de scénario. Pour obtenir l’exemple, accédez à [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Sélectionnez le bouton **Browse code** sous le titre afin d’accéder au dépôt GitHub de l’exemple. Sélectionnez le bouton **Code** et **Download ZIP** pour télécharger l’exemple sous la forme d’un fichier *.ZIP*. 

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/download-repo-zip.png" alt-text="Capture d’écran du dépôt digital-twins-explorer sur GitHub. Le bouton Code est sélectionné et une petite boîte de dialogue s’ouvre, dans laquelle le bouton Download ZIP est mis en évidence." lightbox="media/quickstart-azure-digital-twins-explorer/download-repo-zip.png":::

Décompressez le dossier **digital-twins-explorer-main.zip** et extrayez les fichiers.

## <a name="set-up-azure-digital-twins-and-azure-digital-twins-explorer"></a>Configurer Azure Digital Twins et Azure Digital Twins Explorer

Si vous utilisez Azure Digital Twins, la première chose à faire est de configurer une instance Azure Digital Twins. Une fois que vous avez créé une instance de ce service et configuré vos informations d’identification pour vous authentifier auprès d’Azure Digital Twins Explorer, vous pouvez vous connecter à l’instance dans Azure Digital Twins Explorer et y ajouter les exemples de données que nous verrons plus loin dans ce guide de démarrage rapide.

Le reste de cette section vous guidera dans ces étapes.

### <a name="set-up-an-azure-digital-twins-instance"></a>Configurer une instance Azure Digital Twins

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="set-up-local-azure-credentials"></a>Configurer les informations d’identification Azure locales

L’application Azure Digital Twins Explorer utilise [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) (qui fait partie de la bibliothèque `Azure.Identity`) pour authentifier les utilisateurs auprès de l’instance Azure Digital Twins quand vous l’exécutez sur votre ordinateur local. Pour plus d’informations sur les différentes façons dont une application cliente peut s’authentifier auprès d’Azure Digital Twins, consultez [Écrire le code d’authentification de l’application](how-to-authenticate-client.md).

Avec ce type d’authentification, Azure Digital Twins Explorer recherche les informations d’identification dans votre environnement local, comme une connexion Azure dans une interface [Azure CLI](/cli/azure/install-azure-cli) locale ou dans Visual Studio ou Visual Studio Code. C’est la raison pour laquelle vous devez **vous connecter à Azure localement** avec l’un de ces mécanismes afin de configurer les informations d’identification pour l’application Azure Digital Twins Explorer.

Si vous êtes déjà connecté à Azure via l’une de ces manières, vous pouvez passer à la [section suivante](#run-and-configure-azure-digital-twins-explorer).

Dans le cas contraire, vous pouvez installer l’interface Azure CLI locale en procédant comme suit :

1. Suivez le processus via [ce lien d’installation](/cli/azure/install-azure-cli) pour effectuer l’installation qui correspond à votre système d’exploitation.
1. Ouvrez une fenêtre de console sur votre machine.
1. Exécutez `az login` et suivez les invites d’authentification pour vous connecter à votre compte Azure.
1. Dernière étape possible : Si vous utilisez plusieurs abonnements Azure sous ce compte, définissez le contexte d’authentification sur l’abonnement Azure qui contient votre instance Azure Digital Twins en exécutant `az account set --subscription "<your-subscription-name-or-ID>"` (la valeur du nom ou de l’ID de l’abonnement fonctionne).

Une fois que vous vous serez connecté, Azure Digital Twins Explorer récupérera automatiquement vos informations d’identification Azure quand vous l’exécuterez dans la section suivante.

Vous pouvez fermer la fenêtre de la console d’authentification si vous le souhaitez. Vous pouvez aussi la garder ouverte pour l’utiliser à l’étape suivante.

### <a name="run-and-configure-azure-digital-twins-explorer"></a>Exécuter et configurer Azure Digital Twins Explorer

Ensuite, vous allez exécuter l’application Azure Digital Twins Explorer et la configurer pour votre instance Azure Digital Twins.

1. Accédez au dossier **digital-twins-explorer-main** téléchargé et décompressé.
Ouvrez une fenêtre de console à l’emplacement du dossier **digital-twins-explorer-main/client/src**.

1. Exécutez `npm install` pour télécharger toutes les dépendances nécessaires.

1. Démarrez l’application en exécutant `npm run start`.

   Après quelques secondes, une fenêtre de navigateur s’ouvre et l’application s’affiche dans le navigateur.

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/explorer-blank.png" alt-text="Fenêtre de navigateur présentant une application exécutée sur localhost:3000. L’application s’appelle Azure Digital Twins Explorer. Elle contient les panneaux suivants : Explorateur de requêtes, Modèles, Vue du graphe et Propriétés. Il n’y a pas encore de données à l’écran." lightbox="media/quickstart-azure-digital-twins-explorer/explorer-blank.png":::

1. Sélectionnez le bouton **Se connecter** en haut à droite de la fenêtre, comme illustré dans l’image suivante, pour configurer Azure Digital Twins Explorer afin qu’il utilise l’instance que vous avez configurée.

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/sign-in.png" alt-text="Azure Digital Twins Explorer : icône de connexion mise en évidence en haut de la fenêtre. L’icône représente la silhouette d’une personne à laquelle est superposée une clé." lightbox="media/quickstart-azure-digital-twins-explorer/sign-in.png":::

1. Entrez l’URL de l’instance Azure Digital Twins que vous avez recueillie plus tôt dans la section [Configurer une instance Azure Digital Twins](#set-up-an-azure-digital-twins-instance), au format *https://<nom_hôte_instance>* .

> [!TIP]
> Si un message d’erreur `SignalRService.subscribe` apparaît quand vous vous connectez, vérifiez que votre URL Azure Digital Twins commence par *https://* .
>
> Si une erreur d’authentification s’affiche, vous souhaiterez peut-être vérifier vos **variables d’environnement** pour être sûr que les informations d’identification qui y figurent sont valides pour Azure Digital Twins. `DefaultAzureCredential` tente de procéder à l’authentification par rapport aux types d’informations d’identification dans un [ordre spécifique](/dotnet/api/overview/azure/identity-readme#defaultazurecredential), et les variables d’environnement sont évaluées en premier.

Si la fenêtre contextuelle **Autorisations demandées** Microsoft s’affiche, autorisez l’application, puis cliquez sur Accepter pour continuer.

>[!NOTE]
> Vous pouvez consulter ou modifier ces informations à tout moment en sélectionnant cette même icône afin de rouvrir la zone **Se connecter**. Les valeurs que vous avez passées auront été conservées.

## <a name="add-the-sample-data"></a>Ajouter les exemples de données

Ensuite, vous allez importer l’exemple de scénario et le graphe dans Azure Digital Twins Explorer. L’exemple de scénario se trouve également dans le dossier **digital-twins-explorer-main** que vous avez téléchargé.

### <a name="models"></a>Modèles

Dans une solution Azure Digital Twins, la première chose à faire est de définir le vocabulaire de votre environnement. Vous allez créer des [modèles](concepts-models.md) personnalisés qui décrivent les types d’entité qui se trouvent dans votre environnement.

Chaque modèle est écrit dans un langage tel que JSON-LD appelé Digital Twin Definition Language (DTDL). Chaque modèle décrit un type d’entité unique en termes de **propriétés**, de **télémétrie**, de **relations** et de **composants**. Plus tard, vous utiliserez ces modèles comme base pour vos jumeaux numériques, qui représentent des instances de ces types.

En général, quand vous créez un modèle, vous effectuez trois étapes :

1. Écrire la définition du modèle. Dans le guide de démarrage rapide, cette étape est effectuée dans le cadre de l’exemple de solution.
1. Valider le modèle pour être sûr que la syntaxe est exacte. Dans le guide de démarrage rapide, cette étape est effectuée dans le cadre de l’exemple de solution.
1. Charger le modèle dans l’instance Azure Digital Twins.
 
Pour ce guide de démarrage rapide, les fichiers de modèle sont déjà écrits et validés pour vous. Ils sont inclus avec la solution que vous avez téléchargée. Dans cette section, vous allez charger deux modèles préécrits dans votre instance pour définir les composants d’un environnement de génération :

* Floor
* Salle

#### <a name="upload-models"></a>Charger des modèles

Procédez comme suit pour charger les modèles.

1. Dans le panneau **MODÈLES**, sélectionnez l’icône **Charger un modèle**.

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/upload-model.png" alt-text="Dans le panneau Modèles, l’icône du milieu est mise en surbrillance. On peut voir une flèche pointant dans un nuage." lightbox="media/quickstart-azure-digital-twins-explorer/upload-model.png":::
 
1. Dans la fenêtre de sélection de fichier qui s’affiche, accédez au dossier **digital-twins-explorer-main/client/examples** dans le dépôt téléchargé.
1. Sélectionnez **Room.json** et **Floor.json**, puis sélectionnez **OK**. Vous pouvez charger d’autres modèles si vous le souhaitez. Cependant, nous ne les utiliserons pas dans ce guide de démarrage rapide.

Azure Digital Twins Explorer charge maintenant ces fichiers de modèle dans votre instance Azure Digital Twins. Ils doivent s’afficher dans le panneau **Modèles**, sous leur nom convivial et leur ID de modèle complet. Vous pouvez sélectionner les icônes d’informations **Vue du modèle** pour afficher le code DTDL sous-jacent.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/model-info.png" alt-text="Vue du panneau Modèles avec deux définitions de modèle : Floor (dtmi:example:Floor;1) et Room (dtmi:example:Room;1). L’icône d’informations Voir le modèle, qui représente la lettre « i » dans un cercle, est mise en surbrillance pour chaque modèle." lightbox="media/quickstart-azure-digital-twins-explorer/model-info.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="twins-and-the-twin-graph"></a>Jumeaux et graphe de jumeaux

Maintenant que certains modèles ont été chargés dans votre instance Azure Digital Twins, vous pouvez ajouter des [jumeaux numériques](concepts-twins-graph.md) basés sur les définitions de modèle.

Les jumeaux numériques représentent les véritables entités au sein de votre environnement métier. Il peut s’agir des capteurs dans une ferme, des voyants d’une voiture ou, dans ce guide de démarrage rapide, des pièces d’un étage. Vous pouvez créer de nombreux jumeaux d’un type de modèle donné, par exemple plusieurs pièces qui utilisent toutes le modèle *Room*. Vous les connectez à l’aide de relations dans un **graphe de jumeaux** qui représente l’environnement complet.

Dans cette section, vous allez charger des jumeaux précréés qui sont connectés dans un graphe lui aussi précréé. Le graphe contient deux étages et deux pièces, connectés de la façon suivante :

* Floor0
    - Contient Room0
* Floor1
    - Contient Room1

#### <a name="import-the-graph"></a>Importer le graphe

Procédez comme suit pour importer le graphe.

1. Dans le panneau **GRAPHE DES JUMEAUX**, sélectionnez l’icône **Importer le graphe**.

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/import-graph.png" alt-text="Dans le panneau Vue du graphe, une icône est mise en surbrillance. On peut voir une flèche pointant dans un nuage." lightbox="media/quickstart-azure-digital-twins-explorer/import-graph.png":::

2. Dans la fenêtre de sélection de fichier, accédez au dossier **digital-twins-explorer-main/client/examples** et sélectionnez la feuille de calcul **buildingScenario.xlsx**. Ce fichier contient une description de l’exemple de graphe. Sélectionnez **OK**.

   Après quelques secondes, Azure Digital Twins Explorer ouvre la vue **Importer** dans laquelle vous pouvez voir un aperçu du graphe à charger.

3. Pour confirmer le chargement du graphe, sélectionnez l’icône **Enregistrer** en haut à droite du panneau **GRAPHE DES JUMEAUX**.

   :::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/graph-preview-save.png" alt-text="Icône Enregistrer mise en évidence dans le volet Aperçu du graphe" lightbox="media/quickstart-azure-digital-twins-explorer/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
   :::row-end:::

4. Azure Digital Twins Explorer utilise maintenant le fichier chargé pour créer les jumeaux demandés et leurs relations. Une boîte de dialogue s’affiche une fois l’opération terminée. Sélectionnez **Fermer**.

   :::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/import-success.png" alt-text="Boîte de dialogue indiquant la réussite de l’importation du graphe. Le message dit « Importation réussie. 4 jumeaux ont été importés. 2 relations ont été importées. »" lightbox="media/quickstart-azure-digital-twins-explorer/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
   :::row-end:::

5. Le graphe a été chargé dans Azure Digital Twins Explorer. Revenez au panneau de **GRAPHE DES JUMEAUX**.
 
   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/twin-graph-tab.png" alt-text="L’onglet Graphe des jumeaux est mis en surbrillance." lightbox="media/quickstart-azure-digital-twins-explorer/twin-graph-tab.png":::

6. Pour voir le graphe, sélectionnez le bouton **Exécuter la requête** dans le panneau **EXPLORATEUR DE REQUÊTES**, dans la partie supérieure de la fenêtre Azure Digital Twins Explorer.

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/run-query.png" alt-text="Le bouton Exécuter la requête dans le coin supérieur droit de la fenêtre est mis en évidence." lightbox="media/quickstart-azure-digital-twins-explorer/run-query.png":::

Cette action exécute la requête par défaut pour sélectionner et afficher tous les jumeaux numériques. Azure Digital Twins Explorer récupère du service la totalité des jumeaux et des relations. Il dessine le graphe correspondant dans le panneau **GRAPHE DES JUMEAUX**.

## <a name="explore-the-graph"></a>Explorer le graphe

Vous pouvez maintenant voir le graphe chargé de l’exemple de scénario.

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/graph-view-full.png" alt-text="Vue du panneau Vue du graphe avec un graphe de jumeaux. Un cercle nommé « floor1 » est relié par une flèche nommée « contains » à un cercle intitulé « room1 ». Un cercle nommé « floor0 » est relié par une flèche nommée « contains » à un cercle intitulé « room0 ».":::

Les cercles (« nœuds » de graphe) représentent des jumeaux numériques. Les lignes représentent des relations. Le jumeau **Floor0** contient **Room0**, tandis que le jumeau **Floor1** contient **Room1**.

Si vous utilisez une souris, vous pouvez faire glisser des parties du graphe pour les déplacer.

### <a name="view-twin-properties"></a>Afficher les propriétés des jumeaux

Vous pouvez sélectionner un jumeau pour voir la liste de ses propriétés et de ses valeurs dans le panneau **PROPRIÉTÉS**.

Voici les propriétés de Room0 :

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/properties-room0.png" alt-text="Panneau Propriétés mis en surbrillance, avec les propriétés de Room0, qui comprennent, entre autres, un champ $dtId pour Room0, un champ Temperature avec la valeur 70 et un champ Humidity avec la valeur 30." lightbox="media/quickstart-azure-digital-twins-explorer/properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Room0 a une température de 70.

Voici les propriétés de Room1 :

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/properties-room1.png" alt-text="Panneau Propriétés mis en surbrillance, avec les propriétés de Room1, qui comprennent, entre autres, un champ $dtId pour Room1, un champ Temperature avec la valeur 80 et un champ Humidity avec la valeur 60." lightbox="media/quickstart-azure-digital-twins-explorer/properties-room1.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Room1 a une température de 80.

### <a name="query-the-graph"></a>Interroger le graphique

L’une des principales fonctionnalités d’Azure Digital Twins est la capacité à [interroger facilement et efficacement](concepts-query-language.md) votre graphe de jumeaux pour répondre à des questions sur votre environnement.

L’une des façons d’interroger les jumeaux de votre graphe est d’utiliser leurs **propriétés**. L’interrogation basée sur les propriétés peut vous aider à répondre à diverses questions. Par exemple, vous pouvez rechercher les valeurs hors norme dans votre environnement qui peuvent nécessiter votre attention.

Dans cette section, vous allez exécuter une requête pour déterminer le nombre de jumeaux numériques dans votre environnement ayant une température supérieure à 75.

Pour voir la réponse, exécutez la requête suivante dans le panneau **EXPLORATEUR DE REQUÊTES**.

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="TemperatureQuery":::

Comme nous l’avons vu, Room0 a une température de 70 et Room1 une température de 80. Ainsi, seul Room1 s’affiche dans les résultats.
    
:::image type="content" source="media/quickstart-azure-digital-twins-explorer/result-query-property-before.png" alt-text="Résultats de la requête de propriétés montrant uniquement Room1" lightbox="media/quickstart-azure-digital-twins-explorer/result-query-property-before.png":::

>[!TIP]
> D’autres opérateurs de comparaison (<,>, = ou !=) sont également pris en charge dans la requête précédente. Vous pouvez essayer de tester ces opérateurs, mais aussi d’autres valeurs ou propriétés de jumeaux, dans la requête pour tenter de répondre à vos propres questions.

## <a name="edit-data-in-the-graph"></a>Modifier les données du graphe

Vous pouvez utiliser Azure Digital Twins Explorer pour modifier les propriétés des jumeaux représentés dans votre graphe. Dans cette section, nous allons augmenter la température de Room0 sur 76.

Pour commencer, réexécutez la requête suivante pour sélectionner tous les jumeaux numériques. Le graphe complet s’affiche une fois de plus dans le panneau **GRAPHE DES JUMEAUX**.

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="GetAllTwins":::

Sélectionnez **Room0** pour afficher sa liste de propriétés dans le panneau **PROPRIÉTÉS**.

Les propriétés de cette liste sont modifiables. Sélectionnez la valeur de température de **70** pour entrer une nouvelle valeur. Entrez **76**, puis sélectionnez l’icône **Enregistrer** pour remplacer la température par **76**.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/new-properties-room0.png" alt-text="Panneau Propriétés montrant les propriétés de Room0. La valeur de température est une zone modifiable qui indique 76, et l’icône d’enregistrement est mise en surbrillance." lightbox="media/quickstart-azure-digital-twins-explorer/new-properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

À présent, vous voyez une fenêtre **Informations sur les correctifs** dans laquelle apparaît le code correctif utilisé en arrière-plan avec les [API](how-to-use-apis-sdks.md) Azure Digital Twins pour effectuer la mise à jour. Sélectionnez **Fermer**.

### <a name="query-to-see-the-result"></a>Exécuter une requête pour afficher le résultat

Pour vérifier que le graphe a bien enregistré votre modification de la température pour Room0, réexécutez la requête de tout à l’heure pour obtenir tous les jumeaux de l’environnement dont la température est supérieure à 75.

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="TemperatureQuery":::

Maintenant que la température de Room0 est passée de 70 à 76, les deux jumeaux doivent figurer dans les résultats.

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/result-query-property-after.png" alt-text="Résultats de la requête de propriétés montrant à la fois Room0 et Room1" lightbox="media/quickstart-azure-digital-twins-explorer/result-query-property-after.png":::

## <a name="review-and-contextualize-learnings"></a>Passer en revue et contextualiser les apprentissages

Dans ce guide de démarrage rapide, vous avez créé une instance Azure Digital Twins, vous l’avez connectée à Azure Digital Twins Explorer et vous l’avez remplie avec un exemple de scénario.

Vous avez ensuite exploré le graphe en :

* Utilisant une requête pour répondre à une question relative au scénario
* Modifiant une propriété d’un jumeau numérique
* Réexécutant la requête pour voir comment la réponse a changé après la modification

L’objectif de cet exercice est de montrer comment vous pouvez utiliser le graphe Azure Digital Twins pour répondre à des questions sur votre environnement, même si celui-ci change continuellement.

Dans ce guide de démarrage rapide, vous avez mis à jour la température manuellement. Il est courant dans Azure Digital Twins de connecter des jumeaux numériques à des appareils IoT réels afin qu’ils reçoivent automatiquement les mises à jour en fonction des données de télémétrie. De cette façon, vous pouvez créer un graphe dynamique qui reflète toujours l’état réel de votre environnement. Vous pouvez utiliser des requêtes pour obtenir des informations sur ce qui se passe dans votre environnement en temps réel.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Pour conclure ce guide de démarrage rapide, arrêtez l’exécution de l’application console. Cette action désactive la connexion à l’application Azure Digital Twins Explorer dans le navigateur. Vous ne serez plus en mesure de voir les données actives dans le navigateur. Vous pouvez fermer l’onglet du navigateur.

Vous pouvez alors choisir les ressources à supprimer, en fonction de ce que vous souhaitez faire ensuite.

* **Si vous envisagez de passer aux tutoriels Azure Digital Twins**, ne supprimez pas l’instance de ce guide de démarrage rapide, car vous pourrez la réutiliser.

[!INCLUDE [digital-twins-cleanup-clear-instance.md](../../includes/digital-twins-cleanup-clear-instance.md)]
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Vous pouvez également supprimer le dossier de projet de votre ordinateur local.

## <a name="next-steps"></a>Étapes suivantes

Ensuite, passez aux tutoriels Azure Digital Twins pour créer vos propres outils d’interaction et de scénario pour Azure Digital Twins.

> [!div class="nextstepaction"]
> [Tutoriel : Coder une application cliente](tutorial-code.md)
