---
title: Déplacer une instance vers une autre région Azure
titleSuffix: Azure Digital Twins
description: Découvrez comment déplacer une instance Azure Digital Twins d’une région Azure à une autre.
author: baanders
ms.author: baanders
ms.date: 08/26/2020
ms.topic: how-to
ms.custom: subject-moving-resources
ms.service: digital-twins
ms.openlocfilehash: 9b4d896edea86d85b650325ac5efb7f3cf439b17
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111953935"
---
# <a name="move-an-azure-digital-twins-instance-to-a-different-azure-region"></a>Déplacer une instance Azure Digital Twins vers une autre région Azure

Si vous devez déplacer votre instance Azure Digital Twins d’une région à l’autre, la démarche actuelle consiste à recréer vos ressources dans la nouvelle région, puis à supprimer les ressources d’origine. À l’issue de ce processus, vous utiliserez une nouvelle instance Azure Digital Twins identique à la première, à l’exception de l’emplacement mis à jour.

Cet article fournit des conseils sur la mise en œuvre d’un déplacement complet et d’une copie de tout ce dont vous avez besoin pour que la nouvelle instance corresponde à celle d’origine.

Ce processus comprend les étapes suivantes :

1. Préparation : télécharger vos modèles, jumeaux et graphe d’origine.
1. Déplacement : créer une instance Azure Digital Twins dans une nouvelle région.
1. Déplacement : remplir à nouveau l’instance Azure Digital Twins.
    - Charger les modèles, jumeaux et graphe d’origine.
    - Recréer les points de terminaison et les routes.
    - Relier les ressources connectées.
1. Nettoyer les ressources sources : Supprimez l'instance d'origine.

## <a name="prerequisites"></a>Prérequis

Avant d’essayer de recréer votre instance Azure Digital Twins, passez en revue les composants de votre instance d’origine afin de vous faire une idée claire de tous les éléments qui doivent être recréés.

Voici quelques questions à considérer :

* Quels sont les *modèles* chargés dans mon instance ? Combien y en a-t-il ?
* Quels sont les *jumeaux* dans mon instance ? Combien y en a-t-il ?
* Quelle est la forme générale du *graphe* dans mon instance ? Combien de relations y a-t-il ?
* De quels *points de terminaison* est-ce que je dispose dans mon instance ?
* Quelles *routes* figurent dans mon instance ? Sont-elles équipées de filtres ?
* Où mon instance *se connecte-elle aux autres services Azure* ? Certains points d’intégration courants comprennent :

    - Azure Event Grid, Azure Event Hubs ou Azure Service Bus
    - Azure Functions
    - Azure Logic Apps
    - Azure Time Series Insights
    - Azure Maps
    - Service Azure IoT Hub Device Provisioning
* Quelles autres *applications personnelles ou d’entreprise* se connectent à mon instance ?

Vous pouvez collecter ces informations en utilisant le [portail Azure](https://portal.azure.com), les [API et kits SDK Azure Digital Twins](concepts-apis-sdks.md), les [commandes CLI Azure Digital Twins](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) ou [Azure Digital Twins Explorer](concepts-azure-digital-twins-explorer.md).

## <a name="prepare"></a>Préparation

Dans cette section, vous allez préparer la recréation de votre instance en téléchargeant vos modèles, jumeaux et graphe d’origine à partir de votre instance de départ. Cet article utilise [Azure Digital Twins Explorer](concepts-azure-digital-twins-explorer.md) pour cette tâche.

>[!NOTE]
>Vous avez peut-être déjà des fichiers qui contiennent les modèles ou le graphe dans votre instance. Si c’est le cas, vous n’avez pas besoin de tout retélécharger, mais uniquement ce qui vous manque ou ce qui a été modifié depuis le chargement initial de ces fichiers. Par exemple, vous pouvez avoir des jumeaux qui ont été mis à jour avec de nouvelles données.

### <a name="download-models-twins-and-graph-with-azure-digital-twins-explorer"></a>Télécharger les modèles, les jumeaux et le graphe à l’aide d’Azure Digital Twins Explorer

Tout d’abord, ouvrez **Azure Digital Twins Explorer** pour votre instance Azure Digital Twins dans le [portail Azure](https://portal.azure.com). Pour ce faire, accédez à l’instance Azure Digital Twins dans le portail en recherchant son nom dans la barre de recherche du portail. Ensuite, sélectionnez le bouton **Atteindre l’Explorateur (préversion)** . 

:::image type="content" source="media/includes/azure-digital-twins-explorer-portal-access.png" alt-text="Capture d’écran du portail Azure montrant la page de présentation d’une instance Azure Digital Twins. Le bouton Atteindre l’Explorateur (préversion) est en évidence." lightbox="media/includes/azure-digital-twins-explorer-portal-access.png":::

Cela ouvre une fenêtre Azure Digital Twins Explorer connectée à cette instance.

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/explorer-blank.png" alt-text="Capture d’écran du portail Azure dans un navigateur Internet. Le portail affiche Azure Digital Twins Explorer, qui ne contient pas de données." lightbox="media/quickstart-azure-digital-twins-explorer/explorer-blank.png":::

Suivez les instructions d’Azure Digital Twins Explorer pour [exporter le graphe et les modèles](how-to-use-azure-digital-twins-explorer.md#export-graph-and-models). Cela permet de télécharger un fichier JSON sur votre ordinateur, qui contient le code pour vos modèles, jumeaux et relations (y compris les modèles qui ne sont pas actuellement utilisés dans le graphe).

## <a name="move"></a>Déplacer

Ensuite, vous allez finaliser le déplacement de votre instance en créant une instance dans la région cible. Vous allez ensuite la renseigner avec les données et composants de votre instance d’origine.

### <a name="create-a-new-instance"></a>Créer une instance

Tout d’abord, créez une instance d’Azure Digital Twins dans votre région cible. Effectuez les étapes indiquées dans le [guide pratique Configurer une instance et l’authentification](how-to-set-up-instance-portal.md). Gardez à l’esprit les conseils suivants :

* Vous pouvez conserver le même nom pour la nouvelle instance *si* elle se trouve dans un groupe de ressources différent. Si vous devez utiliser le groupe de ressources contenant votre instance d’origine, la nouvelle instance doit porter son propre nom distinct.
* Lorsque vous êtes invité à indiquer une localisation, entrez la nouvelle région cible.

Cette étape terminée, vous allez avoir besoin du nom d’hôte de votre nouvelle instance pour continuer sa configuration avec vos données. Si vous n’avez pas noté le nom d’hôte pendant la configuration, suivez [ces instructions](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values) pour le récupérer maintenant à partir du portail Azure.

À présent, vous allez configurer les données de la nouvelle instance pour en faire une copie de l’instance d’origine.

#### <a name="upload-models-twins-and-graph-with-azure-digital-twins-explorer"></a>Charger les modèles, les jumeaux et le graphe à l’aide d’Azure Digital Twins Explorer

Dans cette section, vous pouvez recharger vos modèles, jumeaux et graphe dans la nouvelle instance. Si votre instance d’origine ne comporte aucun modèle, jumeau ou graphe, ou si vous ne voulez pas les déplacer vers la nouvelle instance, passez à la [section suivante](#re-create-endpoints-and-routes).

Tout d’abord, accédez à **Azure Digital Twins Explorer** pour la nouvelle instance dans le [portail Azure](https://portal.azure.com). 

Importez le [fichier JSON que vous avez téléchargé](#download-models-twins-and-graph-with-azure-digital-twins-explorer) plus tôt dans cet article dans votre nouvelle instance, en suivant les étapes décrites dans les instructions d’Azure Digital Twins Explorer pour [importer un fichier dans Azure Digital Twins Explorer](how-to-use-azure-digital-twins-explorer.md#import-file-to-azure-digital-twins-explorer). Cela permet de charger tous les modèles, jumeaux et relations à partir de votre instance d’origine dans la nouvelle instance.

Pour vérifier que tout a été correctement chargé, retournez sous l’onglet **Graphe de jumeaux** et sélectionnez le bouton **Exécuter la requête** dans le panneau **Explorateur de requêtes** pour exécuter la requête par défaut, qui affiche tous les jumeaux et relations dans le graphe. Cette action actualise aussi la liste des modèles dans le panneau **Modèles**.

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Capture d’écran d’Azure Digital Twins Explorer, mettant en évidence le bouton « Exécuter la requête » en haut à droite de la fenêtre" lightbox="media/how-to-move-regions/run-query.png":::

Vous devez voir votre graphe, avec tous ses jumeaux et toutes ses relations affichés dans le panneau **Graphe de jumeaux**. Vous devez également voir vos modèles listés dans le panneau **Modèles**.

:::image type="content" source="media/how-to-move-regions/post-upload.png" alt-text="Capture d’écran d’Azure Digital Twins Explorer montrant deux modèles en évidence dans la zone Modèles et un graphe en évidence dans la zone Graphe de jumeaux" lightbox="media/how-to-move-regions/post-upload.png":::

Ces vues confirment que vos modèles, jumeaux et graphe ont été rechargés dans la nouvelle instance de la région cible.

#### <a name="re-create-endpoints-and-routes"></a>Recréer les points de terminaison et les routes

Si vous avez des points de terminaison ou des routes dans votre instance d’origine, vous devez les recréer dans votre nouvelle instance. Si votre instance d’origine ne présente aucun point de terminaison et aucune route, ou si vous ne souhaitez pas les déplacer vers la nouvelle instance, vous pouvez passer à la [section suivante](#relink-connected-resources).

Sinon, suivez les étapes décrites dans le [guide pratique Gérer les points de terminaison et les routes](how-to-manage-routes-portal.md) en utilisant la nouvelle instance. Gardez à l’esprit les conseils suivants :

* Vous n’avez *pas* besoin de recréer la ressource Event Grid, Event Hubs ou Service Bus que vous utilisez pour le point de terminaison. Pour plus d’informations, consultez la section « Prérequis » dans les instructions relatives au point de terminaison. Il vous suffit de recréer le point de terminaison sur l’instance Azure Digital Twins.
* Vous pouvez réutiliser les noms des points de terminaison et des routes, car ils sont étendus à une autre instance.
* N’oubliez pas d’ajouter les filtres nécessaires pour les routes que vous créez.

#### <a name="relink-connected-resources"></a>Relier les ressources connectées

Si vous avez d’autres applications ou d’autres ressources Azure connectées à votre instance Azure Digital Twins d’origine, vous devez modifier la connexion afin qu’elles joignent plutôt votre nouvelle instance. Ces ressources peuvent inclure d’autres services Azure, d’autres applications personnelles ou d’entreprise que vous avez configurés pour fonctionner avec Azure Digital Twins.

Si vous n’avez pas d’autres ressources connectées à votre instance d’origine, ou si vous ne souhaitez pas les déplacer vers la nouvelle instance, vous pouvez passer à la [section suivante](#verify).

Sinon, tenez compte des ressources connectées dans votre scénario. Vous n’avez pas besoin de supprimer puis de recréer les ressources connectées. Il vous suffit plutôt de modifier les points où elles se connectent à une instance Azure Digital Twins par le biais de son nom d’hôte. Ensuite, vous mettez à jour ces points pour utiliser le nom d’hôte de la nouvelle instance au lieu de l’original.

Les ressources exactes que vous devez modifier varient en fonction de votre scénario, mais il existe quelques points d’intégration courants que voici :

* Azure Functions. Si vous disposez d’une fonction Azure dont le code comprend le nom d’hôte de l’instance d’origine, vous devez mettre à jour cette valeur avec le nom d’hôte de la nouvelle instance, puis republier la fonction.
* Event Grid, Event Hubs ou Service Bus.
* Logic Apps.
* Time Series Insights.
* Azure Maps.
* Service IoT Hub Device Provisioning.
* Applications personnelles ou d’entreprise en dehors d’Azure, comme l’application cliente créée dans le [Tutoriel : Coder une application cliente](tutorial-code.md), qui se connecte à l’instance et appelle les API Azure Digital Twins.
* Les inscriptions d’applications Azure AD n’ont *pas* besoin d’être recréées. Si vous utilisez une [inscription d’application](./how-to-create-app-registration-portal.md) pour vous connecter aux API Azure Digital Twins, vous pouvez réutiliser la même inscription d’application avec votre nouvelle instance.

À l’issue de cette étape, votre nouvelle instance située dans la région cible doit être une copie de l’instance d’origine.

## <a name="verify"></a>Vérification

Pour confirmer que votre nouvelle instance a été correctement configurée, utilisez les outils suivants :

* [Portail Azure](https://portal.azure.com). Le portail est une bonne solution pour vérifier que votre nouvelle instance existe et qu’elle se trouve dans la bonne région cible. Il permet aussi de vérifier les points de terminaison, les routes et les connexions à d’autres services Azure.
* [Commandes CLI Azure Digital Twins](/cli/azure/dt?view=azure-cli-latest&preserve-view=true). Ces commandes sont une bonne solution pour vérifier que votre nouvelle instance existe et qu’elle se trouve dans la bonne région cible. Elles peuvent aussi servir à vérifier les données d’instance.
* [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Azure Digital Twins Explorer s’avère pratique pour vérifier des données d’instance, comme les modèles, les jumeaux et les graphes.
* [API et SDK Azure Digital Twins](concepts-apis-sdks.md). Ces ressources s’avèrent pratiques pour vérifier des données d’instance, comme les modèles, les jumeaux et les graphes. Elles s’avèrent aussi utiles pour vérifier les points de terminaison et les routes.

Vous pouvez aussi essayer d’exécuter les applications personnalisées ou les flux de bout en bout que vous avez exécutés avec votre instance d’origine, pour mieux vérifier que leur fonctionnement est correct avec la nouvelle instance.

## <a name="clean-up-source-resources"></a>Nettoyer les ressources sources

Maintenant que votre nouvelle instance est configurée dans la région cible, avec une copie des données et des connexions de l’instance originale, vous pouvez supprimer l’instance d’origine.

Vous pouvez utiliser le [portail Azure](https://portal.azure.com), [Azure CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) ou les [API du plan de contrôle](concepts-apis-sdks.md#overview-control-plane-apis).

Pour supprimer l’instance à l’aide du portail Azure, [ouvrez le portail](https://portal.azure.com) dans une fenêtre de navigateur et accédez à votre instance Azure Digital Twins d’origine en recherchant son nom dans la barre de recherche du portail.

Sélectionnez le bouton **Supprimer** et suivez les invites pour finaliser la suppression.

:::image type="content" source="media/how-to-move-regions/delete-instance.png" alt-text="Capture d’écran des détails de l’instance Azure Digital Twins sous l’onglet Vue d’ensemble du portail Azure. Le bouton Supprimer est en évidence.":::