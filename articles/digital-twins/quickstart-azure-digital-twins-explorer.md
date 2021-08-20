---
title: Démarrage rapide - Bien démarrer avec Azure Digital Twins Explorer
titleSuffix: Azure Digital Twins
description: Démarrage rapide - Utiliser l’exemple Azure Digital Twins Explorer pour visualiser et explorer un scénario prédéfini.
author: baanders
ms.author: baanders
ms.date: 4/27/2021
ms.topic: quickstart
ms.service: digital-twins
ms.openlocfilehash: 26063c9d0c02079b2c40eb85330be499b4bb624e
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114461979"
---
# <a name="quickstart---get-started-with-a-sample-scenario-in-azure-digital-twins-explorer"></a>Démarrage rapide - Bien démarrer avec un exemple de scénario dans Azure Digital Twins Explorer

Avec Azure Digital Twins, vous pouvez créer et interagir avec les modèles de vos environnements réels. Tout d’abord, vous modélisez des éléments sous la forme de **jumeaux numériques**. Ensuite, vous les connectez à un **graphe** de connaissances qui peut répondre aux événements en direct et être interrogé.

Dans ce guide de démarrage rapide, vous allez explorer un graphe Azure Digital Twins prédéfini à l’aide d’[Azure Digital Twins Explorer](concepts-azure-digital-twins-explorer.md). Il s’agit d’un outil qui vous permet de visualiser vos données Azure Digital Twins dans le portail Azure, et d’interagir avec elles.

Vous allez effectuez les étapes suivantes :

1. Créer une instance Azure Digital Twins et vous y connecter dans Azure Digital Twins Explorer.
1. Charger des modèles prédéfinis et des données de graphe pour construire l’exemple de scénario
1. Explorer le graphe de scénario créé
1. Apporter des modifications au graphe
1. Revoir ce que vous avez appris de l’expérience.

L’exemple de graphe avec lequel vous allez travailler représente un bâtiment comprenant deux étages et deux pièces. Floor0 contient Room0, et Floor1 contient Room1. Le graphe ressemblera à cette image :

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/graph-view-full.png" alt-text="Capture d’écran d’un graphe composé de quatre nœuds circulaires reliés par des flèches dans Azure Digital Twins Explorer.":::

## <a name="prerequisites"></a>Prérequis

Pour suivre ce guide de démarrage rapide, vous devez avoir un abonnement Azure. Si vous n’en avez pas, [créez-en un gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

Vous devez également télécharger les ressources utiles à l’exemple de graphe qui sert dans le guide de démarrage rapide. Utilisez les liens et les instructions ci-dessous pour télécharger les trois fichiers nécessaires à partir du [dépôt GitHub digital-twins-explorer](https://github.com/Azure-Samples/digital-twins-explorer).
* [Room.js](https://raw.githubusercontent.com/Azure-Samples/digital-twins-explorer/main/client/examples/Room.json) : accédez au lien, cliquez avec le bouton droit n’importe où dans l’écran, puis sélectionnez **Enregistrer sous** dans le menu contextuel de votre navigateur. Utilisez la fenêtre Enregistrer sous suivante pour enregistrer le fichier sur votre ordinateur avec le nom **Room.json**.
* [Floor.js](https://raw.githubusercontent.com/Azure-Samples/digital-twins-explorer/main/client/examples/Floor.json) : accédez au lien, cliquez avec le bouton droit n’importe où dans l’écran, puis sélectionnez **Enregistrer sous** dans le menu contextuel de votre navigateur. Utilisez la fenêtre Enregistrer sous suivante pour enregistrer le fichier au même emplacement que **Room.json**, avec le nom **Floor.json**.
* [buildingScenario.xlsx](https://github.com/Azure-Samples/digital-twins-explorer/blob/main/client/examples/buildingScenario.xlsx) : accédez au lien et sélectionnez le bouton **Télécharger**. Le fichier est téléchargé à l’emplacement de téléchargement par défaut.

    :::image type="content" source="media/quickstart-azure-digital-twins-explorer/download-building-scenario.png" alt-text="Capture d’écran du fichier digital-twins-explorer/client/examples/buildingScenario.xlsx dans GitHub. Le bouton Télécharger est mis en évidence." lightbox="media/quickstart-azure-digital-twins-explorer/download-building-scenario.png":::

## <a name="set-up-azure-digital-twins"></a>Configurer Azure Digital Twins

Pour utiliser Azure Digital Twins, la première étape consiste à créer une instance Azure Digital Twins. Après avoir créé une instance de ce service, vous pouvez vous y connecter dans Azure Digital Twins Explorer. Vous allez vous en servir pour utiliser cette instance dans ce guide de démarrage rapide.

Le reste de cette section vous guidera dans ces étapes.

### <a name="create-an-azure-digital-twins-instance"></a>Créer une instance Azure Digital Twins

[!INCLUDE [digital-twins-setup-portal.md](../../includes/digital-twins-setup-portal.md)]

3. Renseignez les champs de l’onglet **Généralités** de la configuration, notamment votre abonnement, le groupe de ressources, l’emplacement et un nom de ressource pour votre nouvelle instance. Cochez la case **Attribuer le rôle Propriétaire des données Azure Digital Twins** pour vous accorder les autorisations nécessaires afin de gérer les données dans l’instance.

    >[!NOTE]
    > Si la zone Attribuer le rôle Propriétaire des données Azure Digital Twins est grisée, cela signifie que vous n’avez pas d’autorisations dans votre abonnement Azure pour gérer l’accès utilisateur aux ressources. Vous pouvez poursuivre la création de l’instance dans cette section. Une personne disposant des autorisations nécessaires doit ensuite [vous attribuer ce rôle sur l’instance](how-to-set-up-instance-portal.md#assign-the-role-using-azure-identity-management-iam) pour que vous puissiez terminer ce démarrage rapide.
    >
    > Les rôles communs qui répondent à cette exigence sont **Propriétaire**, **Administrateur de compte** ou la combinaison des rôles **Administrateur de l’accès utilisateur** et **Contributeur**.  

4. Sélectionnez **Vérifier + créer** pour terminer la création de votre instance.

    :::image type="content" source= "media/quickstart-azure-digital-twins-explorer/create-azure-digital-twins-basics.png" alt-text="Capture d’écran du processus de création de ressources pour Azure Digital Twins dans le portail Azure. Les valeurs décrites sont renseignées.":::
    
5. Une page de résumé s’affiche avec les détails que vous avez entrés. Confirmez et créez l’instance en sélectionnant **Créer**.

Vous serez dirigé vers une page de présentation du suivi de l’état du déploiement de l’instance.

### <a name="open-instance-in-azure-digital-twins-explorer"></a>Ouvrir une instance dans Azure Digital Twins Explorer

Une fois le déploiement de l’instance terminé, utilisez le bouton **Accéder à la ressource** pour passer à la page de présentation de l’instance dans le portail.

:::image type="content" source= "media/quickstart-azure-digital-twins-explorer/deployment-complete.png" alt-text="Capture d’écran de la page de déploiement pour Azure Digital Twins dans le portail Azure. La page indique que le déploiement est terminé.":::

Ensuite, sélectionnez le bouton **Ouvrir Azure Digital Twins Explorer (préversion)** .

:::image type="content" source="media/includes/azure-digital-twins-explorer-portal-access.png" alt-text="Capture d’écran de la page Vue d’ensemble d’une instance Azure Digital Twins dans le portail Azure. Le bouton Ouvrir Azure Digital Twins Explorer (préversion) est mis en évidence." lightbox="media/includes/azure-digital-twins-explorer-portal-access.png":::

Une fenêtre Azure Digital Twins Explorer liée à votre instance s’ouvre.

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/explorer-blank.png" alt-text="Capture d’écran d’Azure Digital Twins Explorer dans un navigateur Internet." lightbox="media/quickstart-azure-digital-twins-explorer/explorer-blank.png":::

## <a name="add-the-sample-data"></a>Ajouter les exemples de données

Ensuite, vous allez importer l’exemple de scénario et le graphe dans Azure Digital Twins Explorer. L’exemple de scénario se trouve dans le dossier **digital-twins-explorer-main** que vous avez téléchargé à la section [Prérequis](#prerequisites).

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

1. Dans le panneau **Modèles**, sélectionnez l’icône **Charger un modèle** symbolisée par une flèche pointant vers un nuage.

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/upload-model.png" alt-text="Capture d’écran d’Azure Digital Twins Explorer mettant en évidence le panneau Modèles et l’icône « Charger un modèle » présente." lightbox="media/quickstart-azure-digital-twins-explorer/upload-model.png":::
 
1. Dans la fenêtre ouverte qui apparaît, accédez au dossier contenant les fichiers **Room.json** et **Floor.json**, les fichiers que vous avez téléchargés précédemment.
1. Sélectionnez **Room.json** et **Floor.json**, puis sélectionnez **Ouvrir** pour les charger. 

Azure Digital Twins Explorer charge ces fichiers de modèle dans votre instance Azure Digital Twins. Ils doivent apparaître dans le panneau **Modèles**, sous leur nom convivial et leur ID de modèle complet. Vous pouvez sélectionner les icônes d’informations **Vue du modèle** pour afficher le code DTDL sous-jacent.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/model-info.png" alt-text="Capture d’écran dans Azure Digital Twins Explorer du panneau Modèles avec deux définitions de modèle listées, Floor et Room." lightbox="media/quickstart-azure-digital-twins-explorer/model-info.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="twins-and-the-twin-graph"></a>Jumeaux et graphe de jumeaux

Maintenant que certains modèles ont été chargés dans votre instance Azure Digital Twins, vous pouvez ajouter des [jumeaux numériques](concepts-twins-graph.md) basés sur les définitions de modèle.

Les jumeaux numériques représentent les véritables entités au sein de votre environnement métier. Il peut s’agir des capteurs dans une ferme, des voyants d’une voiture ou, dans ce guide de démarrage rapide, des pièces d’un étage. Vous pouvez créer de nombreux jumeaux d’un type de modèle donné, par exemple plusieurs pièces qui utilisent toutes le modèle Room. Vous les connectez à l’aide de relations dans un **graphe de jumeaux** qui représente l’environnement complet.

Dans cette section, vous allez charger des jumeaux précréés qui sont connectés dans un graphe lui aussi créé au préalable. Le graphe contient deux étages et deux pièces, connectés de la façon suivante :

* Floor0
    - Contient Room0
* Floor1
    - Contient Room1

#### <a name="import-the-graph"></a>Importer le graphe

Procédez comme suit pour importer le graphe.

1. Dans le volet **Graphe de jumeaux**, sélectionnez l’icône **Importer le graphe** repérable par une flèche pointant vers un nuage.

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/import-graph.png" alt-text="Capture d’écran dans Azure Digital Twins Explorer du panneau Vue du graphe, avec l’icône « Importer le graphe » mise en évidence." lightbox="media/quickstart-azure-digital-twins-explorer/import-graph.png":::

2. Dans la fenêtre ouverte, accédez au fichier **buildingScenario.xlsx** que vous avez téléchargé précédemment. Ce fichier contient une description de l’exemple de graphe. Sélectionnez **Ouvrir**.

   Après quelques secondes, Azure Digital Twins Explorer ouvre la vue **Importer** dans laquelle vous pouvez voir un aperçu du graphe à charger.

3. Pour confirmer le chargement du graphe, sélectionnez l’icône **Enregistrer** située en haut à droite du panneau d’aperçu du graphe.

   :::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/graph-preview-save.png" alt-text="Capture d’écran d’Azure Digital Twins Explorer mettant en évidence l’icône Enregistrer dans le volet d’aperçu du graphe." lightbox="media/quickstart-azure-digital-twins-explorer/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
   :::row-end:::

4. Azure Digital Twins Explorer utilisera le fichier chargé pour créer les jumeaux demandés et les relations existant entre eux. Une boîte de dialogue s’affiche une fois l’opération terminée. Sélectionnez **Fermer**.

   :::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/import-success.png" alt-text="Capture d’écran dans Azure Digital Twins Explorer d’une boîte de dialogue informant de la réussite de l’importation d’un graphe." lightbox="media/quickstart-azure-digital-twins-explorer/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
   :::row-end:::

5. Le graphe a été chargé dans Azure Digital Twins Explorer. Revenez au panneau **Graphe de jumeaux**.
 
   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/twin-graph-tab.png" alt-text="Capture d’écran dans Azure Digital Twins Explorer de l’onglet Graphe de jumeaux mis en évidence." lightbox="media/quickstart-azure-digital-twins-explorer/twin-graph-tab.png"::: 
 
6. Pour voir le graphe, sélectionnez le bouton **Exécuter la requête** du panneau **Explorateur de requêtes**, dans la partie supérieure de la fenêtre Azure Digital Twins Explorer.

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/run-query.png" alt-text="Capture d’écran d’Azure Digital Twins Explorer mettant en évidence le bouton « Exécuter la requête » dans le coin supérieur droit de la fenêtre." lightbox="media/quickstart-azure-digital-twins-explorer/run-query.png":::

Cette action exécute la requête par défaut pour sélectionner et afficher tous les jumeaux numériques. Azure Digital Twins Explorer récupère du service la totalité des jumeaux et des relations. Il dessine le graphe correspondant dans le panneau **Graphe de jumeaux**.

## <a name="explore-the-graph"></a>Explorer le graphe

Vous pouvez maintenant voir le graphe chargé de l’exemple de scénario.

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/graph-view-full.png" alt-text="Capture d’écran dans Azure Digital Twins Explorer du panneau Vue du graphe contenant un graphe de jumeaux.":::

Les cercles (« nœuds » de graphe) représentent des jumeaux numériques. Les lignes représentent des relations. Le jumeau Floor0 contient Room0, tandis que le jumeau Floor1 contient Room1.

Si vous utilisez une souris, vous pouvez faire glisser des parties du graphe pour les déplacer.

### <a name="view-twin-properties"></a>Afficher les propriétés des jumeaux

Vous pouvez sélectionner un jumeau pour voir la liste de ses propriétés et de ses valeurs dans le panneau **Propriétés**.

Voici les propriétés de Room0 :

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/properties-room0.png" alt-text="Capture d’écran d’Azure Digital Twins Explorer mettant en évidence le panneau Propriétés qui affiche les propriétés $dtId, Temperature et Humidity pour Room0." lightbox="media/quickstart-azure-digital-twins-explorer/properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Room0 a une température de 70.

Voici les propriétés de Room1 :

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/properties-room1.png" alt-text="Capture d’écran d’Azure Digital Twins Explorer mettant en évidence le panneau Propriétés qui affiche les propriétés $dtId, Temperature et Humidity pour Room1." lightbox="media/quickstart-azure-digital-twins-explorer/properties-room1.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Room1 a une température de 80.

### <a name="query-the-graph"></a>Interroger le graphique

L’une des principales fonctionnalités d’Azure Digital Twins est la capacité à [interroger facilement et efficacement](concepts-query-language.md) votre graphe de jumeaux pour répondre à des questions sur votre environnement.

L’une des façons d’interroger les jumeaux de votre graphe est d’utiliser leurs **propriétés**. L’interrogation basée sur les propriétés peut vous aider à répondre à diverses questions. Par exemple, vous pouvez rechercher les valeurs hors norme dans votre environnement qui peuvent nécessiter votre attention.

Dans cette section, vous allez exécuter une requête pour déterminer le nombre de jumeaux numériques dans votre environnement ayant une température supérieure à 75.

Pour voir la réponse, exécutez la requête suivante dans le panneau **Explorateur de requêtes**.

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="TemperatureQuery":::

Comme nous l’avons vu, Room0 a une température de 70 et Room1 une température de 80. Ainsi, seul Room1 s’affiche dans les résultats.
    
:::image type="content" source="media/quickstart-azure-digital-twins-explorer/result-query-property-before.png" alt-text="Capture d’écran d’Azure Digital Twins Explorer présentant les résultats de la requête de propriété, qui affichent uniquement Room1." lightbox="media/quickstart-azure-digital-twins-explorer/result-query-property-before.png":::

>[!TIP]
> D’autres opérateurs de comparaison (<,>, = ou !=) sont également pris en charge dans la requête précédente. Vous pouvez essayer de tester ces opérateurs, mais aussi d’autres valeurs ou propriétés de jumeaux, dans la requête pour tenter de répondre à vos propres questions.

## <a name="edit-data-in-the-graph"></a>Modifier les données du graphe

Vous pouvez utiliser Azure Digital Twins Explorer pour modifier les propriétés des jumeaux représentés dans votre graphe. Dans cette section, nous allons augmenter la température de Room0 sur 76.

Pour commencer, réexécutez la requête suivante pour sélectionner tous les jumeaux numériques. Le graphe complet s’affiche une fois de plus dans le panneau **Graphe de jumeaux**.

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="GetAllTwins":::

Sélectionnez **Room0** pour afficher sa liste de propriétés dans le panneau **Propriétés**.

Les propriétés de cette liste sont modifiables. Sélectionnez la valeur de température de **70** pour entrer une nouvelle valeur. Entrez **76**, puis sélectionnez l’icône **Enregistrer** pour remplacer la température par **76**.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/new-properties-room0.png" alt-text="Capture d’écran d’Azure Digital Twins Explorer mettant en évidence le fait que le panneau Propriétés affiche des propriétés pouvant être modifiées pour Room0." lightbox="media/quickstart-azure-digital-twins-explorer/new-properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

À présent, vous voyez une fenêtre **Informations sur les correctifs** dans laquelle apparaît le code correctif utilisé en arrière-plan avec les [API](concepts-apis-sdks.md) Azure Digital Twins pour effectuer la mise à jour. Sélectionnez **Fermer**.

### <a name="query-to-see-the-result"></a>Exécuter une requête pour afficher le résultat

Pour vérifier que le graphe a bien enregistré votre modification de la température pour Room0, réexécutez la requête de tout à l’heure pour obtenir tous les jumeaux de l’environnement dont la température est supérieure à 75.

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="TemperatureQuery":::

Maintenant que la température de Room0 est passée de 70 à 76, les deux jumeaux doivent figurer dans les résultats.

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/result-query-property-after.png" alt-text="Capture d’écran d’Azure Digital Twins Explorer présentant les résultats de la requête de propriété, qui affichent à la fois Room0 et Room1." lightbox="media/quickstart-azure-digital-twins-explorer/result-query-property-after.png":::

## <a name="review-and-contextualize-learnings"></a>Passer en revue et contextualiser les apprentissages

Dans ce guide de démarrage rapide, vous avez créé une instance Azure Digital Twins, et vous avez utilisé Azure Digital Twins Explorer pour la remplir à l’aide d’un exemple de scénario.

Vous avez ensuite exploré le graphe en :

* Utilisant une requête pour répondre à une question relative au scénario
* Modifiant une propriété d’un jumeau numérique
* Réexécutant la requête pour voir comment la réponse a changé après la modification

L’objectif de cet exercice est de montrer comment vous pouvez utiliser le graphe Azure Digital Twins pour répondre à des questions sur votre environnement, même si celui-ci change continuellement.

Dans ce guide de démarrage rapide, vous avez mis à jour la température manuellement. Il est courant dans Azure Digital Twins de connecter des jumeaux numériques à des appareils IoT réels afin qu’ils reçoivent automatiquement les mises à jour en fonction des données de télémétrie. De cette façon, vous pouvez créer un graphe dynamique qui reflète toujours l’état réel de votre environnement. Vous pouvez utiliser des requêtes pour obtenir des informations sur ce qui se passe dans votre environnement en temps réel.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Pour procéder au nettoyage à la fin de ce guide de démarrage rapide, choisissez les ressources que vous souhaitez supprimer en fonction de ce que vous voulez faire par la suite.

* **Si vous envisagez de passer aux tutoriels Azure Digital Twins**, ne supprimez pas l’instance de ce guide de démarrage rapide, car vous pourrez la réutiliser.

[!INCLUDE [digital-twins-cleanup-clear-instance.md](../../includes/digital-twins-cleanup-clear-instance.md)]
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Vous pouvez également supprimer le dossier de l’exemple de projet de votre machine locale.

## <a name="next-steps"></a>Étapes suivantes

Ensuite, passez aux tutoriels Azure Digital Twins pour créer vos propres outils d’interaction et de scénario pour Azure Digital Twins.

> [!div class="nextstepaction"]
> [Coder une application cliente](tutorial-code.md)
