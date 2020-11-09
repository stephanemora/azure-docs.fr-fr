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
ms.openlocfilehash: 4c2900ed5ebe0df3ed827acc1a16caff3beaf4d4
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93241087"
---
# <a name="move-an-azure-digital-twins-instance-to-a-different-azure-region"></a>Déplacer une instance Azure Digital Twins vers une autre région Azure

Si vous devez déplacer votre instance Azure Digital Twins d’une région vers une autre région, la démarche actuelle consiste à **recréer vos ressources dans la nouvelle région** , puis à supprimer les ressources d’origine. À l’issue de ce processus, vous utiliserez une nouvelle instance Azure Digital Twins qui est identique à la première, à l’exception de l’emplacement mis à jour.

Cet article fournit des conseils sur la mise en œuvre d’un déplacement complet, par la copie de tout ce dont vous avez besoin pour que la nouvelle instance corresponde à celle d’origine.

Ce processus comprend les étapes suivantes :
1. Préparation : télécharger vos modèles, jumeaux et graphe d’origine.
2. Déplacement : créer une instance Azure Digital Twins, dans une nouvelle région.
3. Déplacement : remplir à nouveau l’instance Azure Digital Twins.
    - Charger les modèles, jumeaux et graphe d’origine.
    - Recréer les points de terminaison et les routes.
    - Lier à nouveau les ressources connectées.
4. Nettoyer les ressources sources : supprimer l’instance d’origine.

## <a name="prerequisites"></a>Prérequis

Avant d’essayer de recréer votre instance Azure Digital Twins, il est judicieux de passer en revue les composants de votre instance d’origine, afin de vous faire une idée claire de tous les éléments qui doivent être recréés.

Voici quelques questions à prendre en compte :
* Quels sont les **modèles** chargés dans mon instance ? Combien y en a-t-il ?
* Quels sont les **jumeaux** dans mon instance ? Combien y en a-t-il ?
* Quelle est la forme générale du **graphe** dans mon instance ? Combien de relations y a-t-il ?
* De quels **points de terminaison** est-ce que je dispose dans mon instance ?
* Quelles **routes** figurent dans mon instance ? Sont-elles équipées de filtres ?
* Où mon instance **se connecte-elle aux autres services Azure**  ? Certains points d’intégration courants comprennent...
    - Event Grid, Event Hub ou Service Bus
    - Azure Functions
    - Logic Apps
    - Time Series Insights
    - Azure Maps
    - Service de provisionnement d’appareil (DPS)
* Quelles autres **applications personnelles ou d’entreprise** se connectent à mon instance ?

Vous pouvez collecter ces informations à l’aide du [portail Azure](https://portal.azure.com), des [API et kits SDK Azure Digital Twins](how-to-use-apis-sdks.md), des [commandes CLI d’Azure Digital Twins](how-to-use-cli.md) ou de l’exemple [Azure Digital Twins (ADT) Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/).

## <a name="prepare"></a>Préparation

Dans cette section, vous allez préparer la recréation de votre instance en **téléchargeant vos modèles, jumeaux et graphe d’origine** à partir de votre instance de départ. À cette fin, le présent article utilise l’exemple [Azure Digital Twins (ADT) Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/).

>[!NOTE]
>Vous disposez peut-être déjà des fichiers contenant les modèles et/ou le graphe dans votre instance. Si c’est le cas, vous n’avez pas besoin de tout retélécharger, récupérez seulement les éléments manquants, ou qui ont été modifiés depuis le chargement initial de ces fichiers (par exemple, les jumeaux qui ont peut-être été mis à jour avec de nouvelles données).

### <a name="limitations-of-adt-explorer"></a>Limites d’ADT Explorer

L’[exemple Azure Digital Twins (ADT) Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) est un exemple d’application cliente qui prend en charge une représentation visuelle de votre graphe et fournit une interaction visuelle avec votre instance. Cet article explique comment l’utiliser pour télécharger, puis recharger ultérieurement vos modèles, jumeaux et graphes.

Notez, cependant, qu’il s’agit d’un **exemple** et non d’un outil complet. Il n’a fait l’objet d’aucun test de contrainte et n’est pas conçu pour gérer des graphes de grande taille. Par conséquent, n’oubliez pas les limites suivantes de cet exemple prêt à l’emploi :
* Pour l’instant, l’exemple a été testé uniquement sur des tailles de graphes comprises entre 1 000 nœuds et 2 000 relations.
* L’exemple ne prend pas en charge les nouvelles tentatives en cas de défaillance intermittente.
* L’exemple n’avertit pas nécessairement l’utilisateur si les données chargées sont incomplètes.
* L’exemple ne gère pas les erreurs dues au dépassement des ressources disponibles comme la mémoire par les graphes très volumineux.

Si l’exemple n’est pas en mesure de gérer la taille de votre graphe, vous pouvez exporter et importer ce dernier à l’aide d’autres outils de développement Azure Digital Twins :
* [Commandes CLI Azure Digital Twins](how-to-use-cli.md)
* [API et SDK Azure Digital Twins](how-to-use-apis-sdks.md)

### <a name="set-up-adt-explorer-application"></a>Configurer l’application ADT Explorer

Pour continuer avec ADT Explorer, commencez par télécharger l’exemple de code d’application, puis configurez-le pour qu’il s’exécute sur votre machine. 

Accédez à l’exemple ici : [Azure Digital Twins (ADT) Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Cliquez sur le bouton *Télécharger le zip* pour télécharger sur votre ordinateur le fichier *.ZIP* de cet exemple de code : _**Azure_Digital_Twins__ADT__explorer.zip**_. Décompressez le fichier.

Ensuite, définissez et configurez les autorisations pour ADT Explorer. Pour ce faire, suivez les instructions de la section [*Configurer Azure Digital Twins et ADT Explorer*](quickstart-adt-explorer.md#set-up-azure-digital-twins-and-adt-explorer) du guide de démarrage rapide Azure Digital Twins. Cette section vous permet de vous familiariser avec les étapes suivantes :
1. Configurer une instance Azure Digital Twins (vous pouvez ignorer cette partie car vous disposez déjà d’une instance)
2. Configurer des informations d’identification Azure locales pour fournir l’accès à votre instance
3. Exécuter ADT Explorer et le configurer pour qu’il se connecte à votre instance. Vous utiliserez le **nom d’hôte** de l’instance Azure Digital Twins d’origine que vous déplacez

L’exemple de l’application ADT Explorer doit désormais s’exécuter dans un navigateur sur votre machine. L’exemple doit être connecté à votre instance Azure Digital Twins d’origine.

:::image type="content" source="media/how-to-move-regions/explorer-blank.png" alt-text="Fenêtre de navigateur présentant une application exécutée sur localhost:3000. L’application s’appelle ADT Explorer. Elle comprend les sections suivantes : Explorateur de requêtes, Vue du modèle, Vue du graphe et Explorateur de propriétés. Il n’y a pas encore de données à l’écran." lightbox="media/how-to-move-regions/explorer-blank.png":::

Afin de vérifier la connexion, vous pouvez cliquer sur le bouton *Exécuter la requête* pour exécuter la requête par défaut qui affiche tous les jumeaux et les relations du graphe dans la zone *EXPLORATEUR DU GRAPHE*.

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Bouton « Exécuter la requête » mis en évidence en haut de la fenêtre" lightbox="media/how-to-move-regions/run-query.png":::

Vous pouvez conserver ADT Explorer en cours d’exécution, car vous allez le réutiliser plus loin dans cet article pour recharger ces éléments sur votre nouvelle instance dans la région cible.

### <a name="download-models-twins-and-graph"></a>Télécharger les modèles, les jumeaux et le graphe

Ensuite, téléchargez les modèles, les jumeaux et le graphe dans votre solution sur votre machine.

Pour les télécharger tous en même temps, assurez-vous d’abord que le graphe complet s’affiche dans la zone *VUE DU GRAPHE* (pour cela, vous pouvez réexécuter la requête par défaut de `SELECT * FROM digitaltwins` dans la zone *EXPLORATEUR DE REQUÊTES* ).
 
Ensuite, cliquez sur l’icône *Exporter le graphe* dans la zone *VUE DU GRAPHE*.

:::image type="content" source="media/how-to-move-regions/export-graph.png" alt-text="Dans la section Vue du graphe, une icône est mise en évidence. On peut y voir une flèche pointant vers le bas, à partir d’un nuage." lightbox="media/how-to-move-regions/export-graph.png":::

Un lien de *Téléchargement* est alors activé dans la *VUE DU GRAPHE*. Sélectionnez-le pour télécharger une représentation JSON du résultat de la requête incluant vos modèles, jumeaux et relations. Un fichier *.json* doit être téléchargé dans votre machine.

>[!NOTE]
>Si le fichier téléchargé semble présenter une extension de fichier différente, essayez de modifier l’extension directement en la remplaçant par *.json*.

## <a name="move"></a>Déplacer

À présent, vous allez effectuer le « déplacement » de votre instance en créant une instance dans la région cible, et en la remplissant avec les données et les composants de votre instance d’origine.

### <a name="create-a-new-instance"></a>Créer une instance

Tout d’abord, **créez une instance d’Azure Digital Twins dans votre région cible**. Pour ce faire, suivez les étapes décrites dans le [*Guide pratique : Configurer une instance et l’authentification*](how-to-set-up-instance-portal.md), en gardant à l’esprit ces conseils :
* Vous pouvez conserver le même nom pour la nouvelle instance **si** elle se trouve dans un groupe de ressources différent. Si vous devez utiliser le groupe de ressources contenant votre instance d’origine, la nouvelle instance devra avoir son propre nom distinct.
* Lorsque vous êtes invité à indiquer un emplacement, entrez la nouvelle région cible.

Cette opération terminée, vous aurez besoin du **nom d’hôte** de votre nouvelle instance pour continuer sa configuration avec vos données. Si vous ne l’avez pas noté pendant la configuration, vous pouvez suivre [ces instructions](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values) et le récupérer maintenant à partir du portail Azure.

### <a name="repopulate-old-instance"></a>Remplir à nouveau l’ancienne instance

À présent, vous allez configurer la nouvelle instance pour en faire une copie de l’original.

#### <a name="upload-original-models-twins-and-graph-using-adt-explorer"></a>Charger les modèles, les jumeaux et le graphe d’origine à l’aide d’ADT Explorer

Dans cette section, vous pouvez charger à nouveau vos modèles, jumeaux et graphe dans la nouvelle instance. Si votre instance d’origine ne comporte aucun modèle, jumeau ou graphe, ou si vous ne souhaitez pas les déplacer vers la nouvelle instance, passez à la [section suivante](#recreate-endpoints-and-routes).

Autrement, pour continuer, revenez à la fenêtre du navigateur qui exécute **ADT Explorer** et suivez les étapes ci-dessous.

##### <a name="connect-to-the-new-instance"></a>Se connecter à la nouvelle instance

Pour le moment, ADT Explorer est connecté à votre instance Azure Digital Twins d’origine. Basculez la connexion pour qu’elle pointe vers votre nouvelle instance en cliquant sur le bouton *Connexion* en haut de la fenêtre. 

:::image type="content" source="media/how-to-move-regions/sign-in.png" alt-text="ADT Explorer : icône de connexion mise en évidence en haut de la fenêtre. L’icône représente la silhouette d’une personne à laquelle est superposée une clé." lightbox="media/how-to-move-regions/sign-in.png":::

Remplacez l’ *URL ADT* de façon à refléter votre nouvelle instance. Remplacez cette valeur par *https://{nom d’hôte de la nouvelle instance}* .

Cliquez sur *Connecter*. Vous serez peut-être amené à vous reconnecter à l’aide de vos informations d’identification Azure et/ou à accorder ce consentement d’application pour votre instance.

##### <a name="upload-models-twins-and-graph"></a>Charger les modèles, les jumeaux et le graphe

À présent, dans votre nouvelle instance, chargez les composants de la solution que vous avez téléchargés précédemment.

Pour charger vos **modèles, jumeaux et graphe** , cliquez sur l’icône *Importer le graphe* dans la zone *VUE DU GRAPHE*. Cette option permet de charger l’intégralité de ces trois composants à la fois (même les modèles qui ne sont pas actuellement utilisés dans le graphe).

:::image type="content" source="media/how-to-move-regions/import-graph.png" alt-text="Dans la section Vue du graphe, une icône est mise en évidence. On peut voir une flèche pointant vers un nuage." lightbox="media/how-to-move-regions/import-graph.png":::

Dans la zone de sélection des fichiers, accédez à votre graphe téléchargé. Sélectionnez le fichier *.json* du graphe et cliquez sur *Ouvrir*.

Après quelques secondes, ADT Explorer ouvre la vue *Importer* dans laquelle vous voyez un aperçu du graphe qui va être chargé.

Pour confirmer le chargement du graphe, cliquez sur l’icône *Enregistrer* située en haut à droite de *Vue du graphe*  :

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/graph-preview-save.png" alt-text="Icône Enregistrer mise en évidence dans le volet Aperçu du graphe" lightbox="media/how-to-move-regions/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

ADT Explorer va à présent charger vos modèles et votre graphe (y compris les jumeaux et les relations) dans votre nouvelle instance Azure Digital Twins. Vous devez voir un message de réussite indiquant le nombre de modèles, de jumeaux et de relations qui ont été chargés :

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/import-success.png" alt-text="Boîte de dialogue indiquant la réussite de l’importation du graphe. Le message dit « Importation réussie. 2 modèles ont été importés. 4 jumeaux ont été importés. 2 relations ont été importées. »" lightbox="media/how-to-move-regions/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Pour vérifier que tout a été correctement chargé, cliquez sur le bouton *Exécuter la requête* dans la zone *EXPLORATEUR DU GRAPHE* pour exécuter la requête par défaut qui affiche l’intégralité des jumeaux et des relations dans le graphe. Cette opération actualise également la liste des modèles dans la *VUE DU MODÈLE*.

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Mise en évidence du même bouton « Exécuter la requête » qu’indiqué précédemment, dans le haut de la fenêtre" lightbox="media/how-to-move-regions/run-query.png":::

Vous devez voir votre graphe, avec tous ses jumeaux et ses relations affichés dans la zone  *EXPLORATEUR DU GRAPHE*. Vous devez également voir vos modèles listés dans la zone *VUE DU MODÈLE*.

:::image type="content" source="media/how-to-move-regions/post-upload.png" alt-text="Vue d’ADT Explorer affichant 2 modèles mis en évidence dans la zone « Vue du modèle », et un graphe ressorti dans la zone « Explorateur du graphe »" lightbox="media/how-to-move-regions/post-upload.png":::

Cela confirme que vos modèles, jumeaux et graphe ont été rechargés dans la nouvelle instance de la région cible.

#### <a name="recreate-endpoints-and-routes"></a>Recréer les points de terminaison et les routes

Si vous avez des **points de terminaison et/ou des routes** dans votre instance d’origine, vous devez les recréer dans votre nouvelle instance. Si votre instance d’origine ne présente aucun point de terminaison et aucune route, ou si vous ne souhaitez pas les déplacer vers la nouvelle instance, vous pouvez passer à la [section suivante](#re-link-connected-resources).

Autrement, continuez en suivant les étapes du [*Guide pratique : Gérer les points de terminaison et les routes*](how-to-manage-routes-portal.md) à l’aide de la nouvelle instance, en tenant compte de ces conseils : 
* Vous n’avez **pas besoin** de recréer la ressource Event Grid, Event Hub ou Service Bus que vous utilisez pour le point de terminaison ( *section Prérequis* dans les instructions relatives aux points de terminaison). Il vous suffit de recréer le point de terminaison sur l’instance Azure Digital Twins.
* Vous pouvez réutiliser les **noms** des points de terminaison et des routes, car ils sont étendus à une autre instance.
* N’oubliez pas d’ajouter les **filtres** nécessaires pour les routes que vous créez.

#### <a name="re-link-connected-resources"></a>Lier à nouveau les ressources connectées

Si vous avez d’autres applications ou d’autres ressources Azure connectées à votre instance Azure Digital Twins d’origine, vous devez modifier la connexion afin qu’elles joignent plutôt votre nouvelle instance. Il peut s’agir d’autres services Azure, d’autres applications personnelles ou d’entreprise aussi et que vous avez configurés pour fonctionner avec Azure Digital Twins.

Si vous n’avez pas d’autres ressources connectées à votre instance d’origine, ou si vous ne souhaitez pas les déplacer vers la nouvelle instance, vous pouvez passer à la [section suivante](#verify).

Autrement, pour continuer, tenez compte des ressources connectées dans votre scénario. Vous n’avez pas besoin de supprimer et de recréer les ressources connectées ; il vous suffit de modifier les points via lesquels elles se connectent à une instance Azure Digital Twins par son **nom d’hôte** , et de procéder à la mise à jour pour utiliser le nom d’hôte de la nouvelle instance à la place de celui de l’original.

Les ressources exactes que vous devez modifier varient en fonction de votre scénario, mais il existe quelques points d’intégration courants que voici :
* Azure Functions. Si vous disposez d’une fonction Azure dont le code comprend le nom d’hôte de l’instance d’origine, vous devez mettre à jour cette valeur avec le nom d’hôte de la nouvelle instance, et publier à nouveau la fonction.
* Event Grid, Event Hubs ou Service Bus
* Logic Apps
* Time Series Insights
* Azure Maps
* Service de provisionnement d’appareil (DPS)
* Applications personnelles ou d’entreprise en dehors d’Azure, telles que l’ **application cliente** créée dans le [*Tutoriel : Coder une application cliente*](tutorial-code.md), qui se connecte à l’instance et appelle les API Azure Digital Twins
* Les inscriptions d’applications Azure AD **n’ont pas besoin** d’être recréées. Si vous utilisez une [inscription d’application](how-to-create-app-registration.md) pour vous connecter aux API Azure Digital Twins, vous pouvez réutiliser la même inscription d’application avec votre nouvelle instance.

À l’issue de cette étape, votre nouvelle instance située dans la région cible doit être une copie de l’instance d’origine.

## <a name="verify"></a>Vérification

Pour confirmer que votre nouvelle instance a été correctement configurée, vous pouvez utiliser les outils suivants :
* Le [**Portail Azure**](https://portal.azure.com) (idéal pour vérifier que votre nouvelle instance existe et qu’elle se trouve dans la région cible appropriée ; il est également pratique pour la vérification des points de terminaison et des routes ainsi que des connexions à d’autres services Azure)
* Les [**commandes CLI** d’Azure Digital Twins](how-to-use-cli.md) (idéales pour vérifier que votre nouvelle instance existe et qu’elle se trouve dans la région cible appropriée ; elles peuvent également être utilisées pour contrôler les données d’instance)
* [**ADT Explorer**](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) (pratique pour la vérification des données d’instance, telles que les modèles, les jumeaux et le graphe)
* Les [API et kits SDK Azure Digital Twins](how-to-use-apis-sdks.md) (pratiques pour la vérification des données d’instance, telles que les modèles, les jumeaux et le graphe ; également recommandés pour la vérification des points de terminaison et des routes)

Vous pouvez aussi essayer d’exécuter les applications personnalisées ou les flux de bout en bout que vous avez exécutés avec votre instance d’origine, pour vous permettre de vérifier leur fonctionnement correct avec la nouvelle instance.

## <a name="clean-up-source-resources"></a>Nettoyer les ressources sources

Maintenant que votre nouvelle instance est configurée dans la région cible, avec une copie des données et des connexions de l’instance originale, vous pouvez **supprimer l’instance d’origine**.

Vous pouvez le faire dans le [portail Azure](https://portal.azure.com), à l’aide de l’interface [CLI](how-to-use-cli.md) ou avec des [API de plan de contrôle](how-to-use-apis-sdks.md#overview-control-plane-apis).

Pour supprimer l’instance à l’aide du portail Azure, [ouvrez le portail](https://portal.azure.com) dans une fenêtre de navigateur et accédez à votre instance Azure Digital Twins d’origine en recherchant son nom dans la barre de recherche du portail.

Cliquez sur le bouton *Supprimer* et suivez les invites pour terminer la suppression.

:::image type="content" source="media/how-to-move-regions/delete-instance.png" alt-text="Affichage des détails de l’instance Azure Digital Twins dans l’onglet Vue d’ensemble du portail Azure. Le bouton Supprimer est mis en évidence.":::