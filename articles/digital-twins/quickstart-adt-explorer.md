---
title: Démarrage rapide - Explorer un exemple de scénario
titleSuffix: Azure Digital Twins
description: Démarrage rapide - Utiliser l’exemple ADT Explorer pour visualiser et explorer un scénario prédéfini.
author: baanders
ms.author: baanders
ms.date: 9/24/2020
ms.topic: quickstart
ms.service: digital-twins
ms.openlocfilehash: dbe37e8a5cba18254cff1dc5d0fff4d5b9bc783d
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91372610"
---
# <a name="quickstart---explore-a-sample-azure-digital-twins-scenario-using-adt-explorer"></a>Démarrage rapide - Explorer un exemple de scénario Azure Digital Twins avec ADT Explorer

Avec Azure Digital Twins, vous pouvez créer et interagir avec les modèles de vos environnements réels. Pour cela, vous pouvez modéliser des éléments sous la forme de **jumeaux numériques**, puis les connecter à un**graphe** de connaissances qui peut répondre aux événements en direct et être interrogé.

Dans ce guide de démarrage rapide, vous allez explorer un graphe Azure Digital Twins prédéfini, à l’aide d’un exemple d’application appelé [**Azure Digital Twins (ADT) Explorer**](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). ADT Explorer vous permet de charger un scénario, d’afficher les représentations visuelles de vos jumeaux et de votre graphe, ainsi que d’effectuer d’autres activités de gestion par le biais d’une expérience visuelle basée sur un navigateur.

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

Enfin, vous devez également télécharger deux exemples que vous utiliserez dans le cadre du guide de démarrage rapide :
* L’exemple d’application **ADT Explorer**. Cet exemple contient l’application principale que vous utiliserez dans le guide de démarrage rapide pour charger et explorer un scénario Azure Digital Twins. Pour accéder à l’application, cliquez sur le lien suivant : [Azure Digital Twins (ADT) Explorer](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Cliquez sur le bouton *Télécharger le zip* pour télécharger sur votre ordinateur le fichier *.ZIP* de cet exemple de code : _**Azure_Digital_Twins__ADT__explorer.zip**_. Décompressez le dossier et extrayez les fichiers.
* L’**exemple de scénario Azure Digital Twins**. Celui-ci comprend un graphe Azure Digital Twins prédéfini que vous allez charger dans ADT Explorer en vue de l’utiliser. Pour accéder au scénario, cliquez sur le lien suivant : [Exemples Azure Digital Twins](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples). Cliquez sur le bouton *Télécharger le zip* pour télécharger sur votre ordinateur sous la forme d’un dossier .ZIP le fichier *.ZIP* de cet exemple de code : **Azure_Digital_Twins_samples.zip**. Décompressez le dossier et extrayez les fichiers.

## <a name="set-up-azure-digital-twins-and-adt-explorer"></a>Configurer Azure Digital Twins et ADT Explorer

Si vous utilisez Azure Digital Twins, la première chose à faire est de configurer une **instance Azure Digital Twins**. Une fois que vous avez créé une instance de ce service, vous pouvez y ajouter les exemples de données que nous verrons plus loin dans ce guide.

Vous allez également définir des autorisations pour qu’ADT Explorer s’exécute sur votre ordinateur et accède à votre instance Azure Digital Twins. Cela vous permettra d’utiliser l’exemple d’application pour explorer votre instance et les données qu’elle contient.

### <a name="set-up-azure-digital-twins-instance"></a>Configurer l’instance Azure Digital Twins

Tout d’abord, configurez une instance Azure Digital Twins et l’authentification nécessaire à son utilisation. Pour ce faire, suivez les instructions indiquées dans [*Procédure : Configurer une instance et l’authentification*](how-to-set-up-instance-portal.md). L’article propose trois modes de configuration : avec le [portail Azure](how-to-set-up-instance-portal.md), [CLI](how-to-set-up-instance-cli.md) ou un [exemple de script de déploiement Cloud Shell automatisé](how-to-set-up-instance-scripted.md). Toutes les versions des instructions contiennent également les étapes permettant de vérifier que vous avez correctement effectué chaque étape et que vous êtes prêt à passer à l’utilisation de votre nouvelle instance.

Dans ce guide de démarrage rapide, vous aurez besoin des valeurs suivantes utilisées pour configurer votre instance. S’il vous faut récupérer ces valeurs, utilisez les liens ci-dessous pour accéder aux sections correspondantes de l’article d’installation et les rechercher dans le [portail Azure](https://portal.azure.com).
* **_Nom d’hôte_** de l’instance Azure Digital Twins ([à rechercher dans le portail](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))
* ID d’application (client) **_d’inscription de l’application Azure AD_** ([à rechercher dans le portail](how-to-set-up-instance-portal.md#collect-important-values))
* ID de répertoire (locataire) **_d’inscription de l’application Azure AD_** ([à rechercher dans le portail](how-to-set-up-instance-portal.md#collect-important-values))

### <a name="set-adt-explorer-permissions"></a>Définir les autorisations d’ADT Explorer

Ensuite, vous allez préparer l’instance Azure Digital Twins que vous avez créée pour qu’elle utilise ADT Explorer, qui est une application web hébergée localement. Accédez à la page [Inscriptions d’applications](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) du portail Azure, puis sélectionnez le nom de votre inscription d’application dans la liste.

Sélectionnez *Authentification* dans le menu de l’inscription, puis appuyez sur *+ Ajouter une plateforme*.

:::image type="content" source="media/quickstart-adt-explorer/authentication-pre.png" alt-text="Vue d’un graphe constitué de quatre nœuds circulaires reliés par des flèches. Un cercle nommé « Floor1 » est relié par une flèche nommée « contains » à un cercle intitulé « Room1 » ; un cercle nommé « Floor0 » est relié par une flèche nommée « contains » à un cercle intitulé « Room0 ». « Floor1 » et « Floor0 » ne sont pas reliés entre eux." lightbox="media/quickstart-adt-explorer/authentication-pre.png":::

Dans la page *Configurer les plateformes* qui suit, sélectionnez *Web*.
Fournissez les informations de configuration suivantes :
* **URI de redirection** : ajoutez l’URI de redirection de *http://localhost:3000* .
* **Octroi implicite** : cochez la case *Jetons d’accès*.

Appuyez sur *Configurer* pour terminer.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/authentication-configure-web.png" alt-text="Vue d’un graphe constitué de quatre nœuds circulaires reliés par des flèches. Un cercle nommé « Floor1 » est relié par une flèche nommée « contains » à un cercle intitulé « Room1 » ; un cercle nommé « Floor0 » est relié par une flèche nommée « contains » à un cercle intitulé « Room0 ». « Floor1 » et « Floor0 » ne sont pas reliés entre eux.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Vous disposez maintenant d’une configuration web qu’ADT Explorer pourra utiliser. L’onglet Authentification du portail Azure doit refléter cette configuration. Après avoir vérifié les sections ci-dessous, cliquez sur *Enregistrer*.

:::image type="content" source="media/quickstart-adt-explorer/authentication-post.png" alt-text="Vue d’un graphe constitué de quatre nœuds circulaires reliés par des flèches. Un cercle nommé « Floor1 » est relié par une flèche nommée « contains » à un cercle intitulé « Room1 » ; un cercle nommé « Floor0 » est relié par une flèche nommée « contains » à un cercle intitulé « Room0 ». « Floor1 » et « Floor0 » ne sont pas reliés entre eux.":::

### <a name="run-and-configure-adt-explorer"></a>Exécuter et configurer ADT Explorer

Ensuite, vous allez exécuter l’application ADT Explorer et la configurer pour votre instance Azure Digital Twins.

Accédez au dossier _**Azure_Digital_Twins__ADT__explorer**_ téléchargé et décompressé. Ouvrez une invite de commandes à l’emplacement du dossier *Azure_Digital_Twins__ADT__explorer/client/src*.

Exécutez `npm install` pour télécharger toutes les dépendances nécessaires.

Ensuite, démarrez l’application en exécutant `npm run start`.

Après quelques secondes, une fenêtre de navigateur s’ouvre et l’application s’affiche dans le navigateur.

:::image type="content" source="media/quickstart-adt-explorer/explorer-blank.png" alt-text="Vue d’un graphe constitué de quatre nœuds circulaires reliés par des flèches. Un cercle nommé « Floor1 » est relié par une flèche nommée « contains » à un cercle intitulé « Room1 » ; un cercle nommé « Floor0 » est relié par une flèche nommée « contains » à un cercle intitulé « Room0 ». « Floor1 » et « Floor0 » ne sont pas reliés entre eux." lightbox="media/quickstart-adt-explorer/explorer-blank.png":::

Cliquez sur le bouton *Se connecter* situé en haut de la fenêtre (illustré dans l’image ci-dessous) pour configurer ADT Explorer de manière à ce qu’il utilise l’instance que vous avez configurée. 

:::image type="content" source="media/quickstart-adt-explorer/sign-in.png" alt-text="Vue d’un graphe constitué de quatre nœuds circulaires reliés par des flèches. Un cercle nommé « Floor1 » est relié par une flèche nommée « contains » à un cercle intitulé « Room1 » ; un cercle nommé « Floor0 » est relié par une flèche nommée « contains » à un cercle intitulé « Room0 ». « Floor1 » et « Floor0 » ne sont pas reliés entre eux." lightbox="media/quickstart-adt-explorer/sign-in.png":::

Entrez les informations importantes que vous avez collectées précédemment dans la section [Prérequis](#prerequisites) :
* ID d’application (client)
* ID de l’annuaire (locataire)
* URL de l’instance Azure Digital Twins, au format *https://{nom d’hôte de l’instance}*

>[!NOTE]
> Vous pouvez modifier ces informations à tout moment en sélectionnant cette même icône afin de vous connecter. Les valeurs que vous avez passées auront été conservées.

> [!TIP]
> Si un message d’erreur `SignalRService.subscribe` s’affiche quand vous vous connectez, vérifiez que votre URL Azure Digital Twins commence par *https://* .

Si la fenêtre contextuelle *Autorisations demandées* Microsoft s’affiche, autorisez l’application, puis cliquez sur Accepter pour continuer.

## <a name="add-the-sample-data"></a>Ajouter les exemples de données

Ensuite, vous allez importer l’exemple de scénario et le graphe dans ADT Explorer.

L’exemple de scénario se trouve dans le dossier _**Azure_Digital_Twins_samples**_ que vous avez téléchargé et décompressé ; accédez à présent au dossier.

### <a name="models"></a>Modèles

Dans une solution Azure Digital Twins, la première chose à faire est de définir le vocabulaire de votre environnement. Pour ce faire, vous allez créer des [**modèles**](concepts-models.md) personnalisés, qui décrivent les types d’entité qui se trouvent dans votre environnement. 

Chaque modèle est écrit dans un langage de type JSON-LD appelé **Digital Twin Definition Language (DTDL)** . En outre, chaque modèle décrit un seul type d’entité selon ses *propriétés*, ses *données de télémétrie*, ses *relations* et ses *composants*. Plus tard, vous utiliserez ces modèles comme base pour vos jumeaux numériques, qui représentent des instances de ces types.

En général, lorsque vous créez un modèle, vous effectuez trois étapes :
1. Écrire la définition du modèle (nous l’avons déjà fait dans le cadre de l’exemple de solution)
2. Valider le modèle pour être sûr que la syntaxe est exacte (nous l’avons déjà fait dans le cadre de l’exemple de solution)
3. Charger le modèle dans l’instance Azure Digital Twins
 
Dans ce guide de démarrage rapide, les fichiers de modèle ont déjà été écrits et validés, et ils sont inclus dans la solution que vous avez téléchargée. Dans cette section, vous allez charger deux modèles préécrits dans votre instance pour définir les composants d’un environnement de génération :
* Floor
* Salle

#### <a name="upload-models"></a>Charger des modèles

Dans la section *Vue du modèle*, cliquez sur l’icône *Charger un modèle*.

:::image type="content" source="media/quickstart-adt-explorer/upload-model.png" alt-text="Vue d’un graphe constitué de quatre nœuds circulaires reliés par des flèches. Un cercle nommé « Floor1 » est relié par une flèche nommée « contains » à un cercle intitulé « Room1 » ; un cercle nommé « Floor0 » est relié par une flèche nommée « contains » à un cercle intitulé « Room0 ». « Floor1 » et « Floor0 » ne sont pas reliés entre eux." lightbox="media/quickstart-adt-explorer/upload-model.png":::
 
1. Dans la zone de sélection des fichiers qui s’affiche, accédez au dossier *Azure_Digital_Twins_samples/AdtSampleApp/SampleClientApp/models* dans le référentiel téléchargé.
2. Sélectionnez *Room.json* et *Floor.json*, puis cliquez sur OK (vous pouvez charger les autres modèles si vous le souhaitez. Cependant, nous ne les utiliserons pas dans ce guide de démarrage rapide).
3. Connectez-vous à votre compte Azure, comme vous le demande la fenêtre contextuelle.

>[!NOTE]
>Si le message d’erreur suivant s’affiche : :::image type="content" source="media/quickstart-adt-explorer/error-models-popup.png" alt-text="Vue d’un graphe constitué de quatre nœuds circulaires reliés par des flèches. Un cercle nommé « Floor1 » est relié par une flèche nommée « contains » à un cercle intitulé « Room1 » ; un cercle nommé « Floor0 » est relié par une flèche nommée « contains » à un cercle intitulé « Room0 ». « Floor1 » et « Floor0 » ne sont pas reliés entre eux." border="false"::: 
> Essayez de désactiver le bloqueur de fenêtres pop-up ou d’utiliser un autre navigateur.

ADT Explorer va maintenant charger ces fichiers de modèle dans votre instance Azure Digital Twins. Ils doivent s’afficher dans la section *Vue du modèle*, sous leur nom convivial et leur ID de modèle complet. Vous pouvez cliquer sur les bulles d’informations *Vue du modèle* pour afficher le code DTDL sous-jacent.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/model-info.png" alt-text="Vue d’un graphe constitué de quatre nœuds circulaires reliés par des flèches. Un cercle nommé « Floor1 » est relié par une flèche nommée « contains » à un cercle intitulé « Room1 » ; un cercle nommé « Floor0 » est relié par une flèche nommée « contains » à un cercle intitulé « Room0 ». « Floor1 » et « Floor0 » ne sont pas reliés entre eux." lightbox="media/quickstart-adt-explorer/model-info.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="twins-and-the-twin-graph"></a>Jumeaux et graphe de jumeaux

Maintenant que certains modèles ont été chargés dans votre instance Azure Digital Twins, vous pouvez ajouter des [**jumeaux numériques**](concepts-twins-graph.md) basés sur les définitions de modèle. 

Les jumeaux numériques représentent les entités de votre environnement d’entreprise (par exemple les capteurs dans une ferme, les voyants d’une voiture, ou dans notre exemple, les pièces d’un étage). Vous pouvez créer de nombreux jumeaux d’un type de modèle donné (par exemple, plusieurs pièces qui utilisent le modèle *Room*), et les connecter à l’aide de relations dans un **graphe de jumeaux** représentant l’environnement complet.

Dans cette section, vous allez charger des jumeaux précréés qui sont connectés dans un graphe lui aussi précréé. Le graphe contient deux étages et deux pièces, connectés de la façon suivante :
* *Floor0*
    - contient *Room0*
* *Floor1*
    - contient *Room1*

#### <a name="import-the-graph"></a>Importer le graphe

Dans la section *Vue du graphe*, cliquez sur l’icône *Importer le graphe*.

:::image type="content" source="media/quickstart-adt-explorer/import-graph.png" alt-text="Vue d’un graphe constitué de quatre nœuds circulaires reliés par des flèches. Un cercle nommé « Floor1 » est relié par une flèche nommée « contains » à un cercle intitulé « Room1 » ; un cercle nommé « Floor0 » est relié par une flèche nommée « contains » à un cercle intitulé « Room0 ». « Floor1 » et « Floor0 » ne sont pas reliés entre eux." lightbox="media/quickstart-adt-explorer/import-graph.png":::

Dans le sélecteur de fichiers, accédez au dossier *Azure_Digital_Twins_samples/AdtSampleApp/SampleClientApp* et choisissez la feuille de calcul _**buildingScenario.xlsx**_. Ce fichier contient une description de l’exemple de graphe. Appuyez sur OK.

Après quelques secondes, ADT Explorer ouvre la vue *Importer* dans laquelle vous voyez un aperçu du graphe qui va être chargé.

Pour confirmer le chargement du graphe, cliquez sur l’icône *Enregistrer* située en haut à droite de *Vue du graphe* :

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/graph-preview-save.png" alt-text="Vue d’un graphe constitué de quatre nœuds circulaires reliés par des flèches. Un cercle nommé « Floor1 » est relié par une flèche nommée « contains » à un cercle intitulé « Room1 » ; un cercle nommé « Floor0 » est relié par une flèche nommée « contains » à un cercle intitulé « Room0 ». « Floor1 » et « Floor0 » ne sont pas reliés entre eux." lightbox="media/quickstart-adt-explorer/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

ADT Explorer va maintenant utiliser le fichier chargé pour créer les jumeaux demandés et leurs relations. Une boîte de dialogue s’affiche pour indiquer que l’opération est terminée. Appuyez sur *Fermer*.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/import-success.png" alt-text="Vue d’un graphe constitué de quatre nœuds circulaires reliés par des flèches. Un cercle nommé « Floor1 » est relié par une flèche nommée « contains » à un cercle intitulé « Room1 » ; un cercle nommé « Floor0 » est relié par une flèche nommée « contains » à un cercle intitulé « Room0 ». « Floor1 » et « Floor0 » ne sont pas reliés entre eux." lightbox="media/quickstart-adt-explorer/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Le graphe a été chargé dans ADT Explorer. Pour voir le graphe, cliquez sur le bouton *Exécuter la requête* dans la section  *Explorateur du graphe*, qui est situé en haut de la fenêtre ADT Explorer. 

:::image type="content" source="media/quickstart-adt-explorer/run-query.png" alt-text="Vue d’un graphe constitué de quatre nœuds circulaires reliés par des flèches. Un cercle nommé « Floor1 » est relié par une flèche nommée « contains » à un cercle intitulé « Room1 » ; un cercle nommé « Floor0 » est relié par une flèche nommée « contains » à un cercle intitulé « Room0 ». « Floor1 » et « Floor0 » ne sont pas reliés entre eux." lightbox="media/quickstart-adt-explorer/run-query.png":::

La requête par défaut est exécutée pour sélectionner et afficher tous les jumeaux numériques. ADT Explorer récupère tous les jumeaux et toutes les relations à partir du service, puis dessine le graphe correspondant dans la section *Vue du graphe*.

## <a name="explore-the-graph"></a>Explorer le graphe

Vous pouvez maintenant voir le graphe chargé de l’exemple de scénario :

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="Vue d’un graphe constitué de quatre nœuds circulaires reliés par des flèches. Un cercle nommé « Floor1 » est relié par une flèche nommée « contains » à un cercle intitulé « Room1 » ; un cercle nommé « Floor0 » est relié par une flèche nommée « contains » à un cercle intitulé « Room0 ». « Floor1 » et « Floor0 » ne sont pas reliés entre eux.":::

Les cercles (« nœuds » de graphe) représentent des jumeaux numériques, et les lignes représentent des relations. Vous verrez que le jumeau *Floor0* contient *Room0*et que le jumeau *Floor1* contient *Room1*.

Si vous utilisez une souris, vous pouvez cliquer et faire glisser des parties du graphe pour les déplacer.

### <a name="view-twin-properties"></a>Afficher les propriétés des jumeaux 

Vous pouvez sélectionner un jumeau pour afficher la liste de ses propriétés et de ses valeurs dans la section *Explorateur de propriétés*. 

Voici les propriétés de *Room0* :

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room0.png" alt-text="Vue d’un graphe constitué de quatre nœuds circulaires reliés par des flèches. Un cercle nommé « Floor1 » est relié par une flèche nommée « contains » à un cercle intitulé « Room1 » ; un cercle nommé « Floor0 » est relié par une flèche nommée « contains » à un cercle intitulé « Room0 ». « Floor1 » et « Floor0 » ne sont pas reliés entre eux." lightbox="media/quickstart-adt-explorer/properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Notez que *Room0* a une température de **70**.

Voici les propriétés de *Room1* :

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room1.png" alt-text="Vue d’un graphe constitué de quatre nœuds circulaires reliés par des flèches. Un cercle nommé « Floor1 » est relié par une flèche nommée « contains » à un cercle intitulé « Room1 » ; un cercle nommé « Floor0 » est relié par une flèche nommée « contains » à un cercle intitulé « Room0 ». « Floor1 » et « Floor0 » ne sont pas reliés entre eux." lightbox="media/quickstart-adt-explorer/properties-room1.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Notez que *Room1* a une température de **80**.

### <a name="query-the-graph"></a>Interroger le graphique

L’une des principales fonctionnalités d’Azure Digital Twins est la capacité à [interroger facilement et efficacement](concepts-query-language.md) votre graphe de jumeaux pour répondre à des questions sur votre environnement. 

L’une des façons d’interroger les jumeaux de votre graphe est d’utiliser leurs *propriétés*. L’interrogation basée sur les propriétés permet de répondre à diverses questions, et notamment de trouver les anomalies de votre environnement.

Dans cette section, vous allez exécuter une requête pour répondre à la question suivante : _**Quels sont les jumeaux de mon environnement dont la température est supérieure à 75 ?**_

Pour voir la réponse, exécutez la requête suivante dans la section *Explorateur de requêtes* :

```SQL
SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
```

Comme nous l’avons vu, *Room0* a une température de **70** et *Room1* une température de **80**. Par conséquent, seul _**Room1**_ s’affiche dans les résultats.
    
:::image type="content" source="media/quickstart-adt-explorer/result-query-property-before.png" alt-text="Vue d’un graphe constitué de quatre nœuds circulaires reliés par des flèches. Un cercle nommé « Floor1 » est relié par une flèche nommée « contains » à un cercle intitulé « Room1 » ; un cercle nommé « Floor0 » est relié par une flèche nommée « contains » à un cercle intitulé « Room0 ». « Floor1 » et « Floor0 » ne sont pas reliés entre eux." lightbox="media/quickstart-adt-explorer/result-query-property-before.png":::

>[!TIP]
> D’autres opérateurs de comparaison ( *<* , *>* , *=* ou *!=* ) sont également pris en charge dans la requête ci-dessus. Vous pouvez essayer de brancher ces propriétés, mais aussi des valeurs ou des propriétés de jumeaux, dans la requête pour tenter de répondre à vos propres questions.

## <a name="edit-data-in-the-graph"></a>Modifier les données du graphe

Vous pouvez utiliser ADT Explorer pour modifier les propriétés des jumeaux représentés dans votre graphe. Dans cette section, nous allons **_augmenter la température de_ Room0 sur 76**.

Pour ce faire, sélectionnez *Room0*, en affichant la liste de ses propriétés dans la section *Explorateur de propriétés*.

Les propriétés de cette liste sont modifiables. Sélectionnez la valeur de température de **70** pour entrer une nouvelle valeur. Entrez **76**, puis cliquez sur l’icône *Enregistrer* pour remplacer la température par **76**.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/new-properties-room0.png" alt-text="Vue d’un graphe constitué de quatre nœuds circulaires reliés par des flèches. Un cercle nommé « Floor1 » est relié par une flèche nommée « contains » à un cercle intitulé « Room1 » ; un cercle nommé « Floor0 » est relié par une flèche nommée « contains » à un cercle intitulé « Room0 ». « Floor1 » et « Floor0 » ne sont pas reliés entre eux." lightbox="media/quickstart-adt-explorer/new-properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Une fois l’enregistrement réussi, vous voyez la fenêtre *Informations sur les correctifs* qui affiche le code correctif utilisé en arrière-plan avec les [API](how-to-use-apis-sdks.md) Azure Digital Twins pour effectuer la mise à jour. Appuyez sur *Fermer*.

### <a name="query-to-see-the-result"></a>Exécuter une requête pour afficher le résultat

Pour vérifier que le graphe a bien enregistré votre modification de la température pour *Room0*, réexécutez la requête de tout à l’heure pour **obtenir tous les jumeaux de l’environnement dont la température est supérieure à 75 :**

```SQL
SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
```

Maintenant que la température de *Room0* est passée de **70** à **76**, les deux jumeaux doivent figurer dans les résultats.

:::image type="content" source="media/quickstart-adt-explorer/result-query-property-after.png" alt-text="Vue d’un graphe constitué de quatre nœuds circulaires reliés par des flèches. Un cercle nommé « Floor1 » est relié par une flèche nommée « contains » à un cercle intitulé « Room1 » ; un cercle nommé « Floor0 » est relié par une flèche nommée « contains » à un cercle intitulé « Room0 ». « Floor1 » et « Floor0 » ne sont pas reliés entre eux." lightbox="media/quickstart-adt-explorer/result-query-property-after.png":::

## <a name="review-and-contextualize-learnings"></a>Passer en revue et contextualiser les apprentissages

Dans ce guide de démarrage rapide, vous avez créé une instance Azure Digital Twins, vous l’avez connectée à ADT Explorer et vous l’avez remplie avec un exemple de scénario. 

Vous avez ensuite exploré le graphe en...
1. Utilisant une requête pour répondre à une question relative au scénario
2. Modifiant une propriété d’un jumeau numérique
    * Réexécutant la requête pour voir comment la réponse a changé après la modification

L’objectif de cet exercice est de montrer comment vous pouvez utiliser le graphe Azure Digital Twins pour répondre à des questions sur votre environnement, même si celui-ci change continuellement. 

Dans ce guide de démarrage rapide, vous avez mis à jour la température manuellement. Cependant, il est courant dans Azure Digital Twins de connecter des jumeaux numériques à des appareils IoT réels afin qu’ils reçoivent automatiquement les mises à jour en fonction des données de télémétrie. Cela vous permet de créer un graphe dynamique qui reflète toujours l’état réel de votre environnement, ainsi que d’utiliser des requêtes pour obtenir des informations sur ce qui se passe dans votre environnement en temps réel.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Pour conclure ce guide de démarrage rapide, arrêtez l’exécution de l’application console. Cela désactivera la connexion à l’application ADT Explorer dans le navigateur, et vous ne pourrez plus voir les données actives dans le navigateur. Vous pouvez fermer l’onglet du navigateur.

Si vous envisagez de passer aux tutoriels Azure Digital Twins, ne supprimez pas l’instance de ce guide de démarrage rapide, car vous pourrez la réutiliser.
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Enfin, supprimez les dossiers d’exemples de projets que vous avez téléchargés sur votre ordinateur local (_**Azure_Digital_Twins__ADT__explorer**_ et _**Azure_Digital_Twins_samples**_). Vous devrez peut-être supprimer à la fois les versions compressées et décompressées.

## <a name="next-steps"></a>Étapes suivantes 

Ensuite, passez aux tutoriels Azure Digital Twins pour créer vos propres outils d’interaction et de scénario pour Azure Digital Twins.

> [!div class="nextstepaction"]
> [*Tutoriel : Coder une application cliente*](tutorial-code.md)
