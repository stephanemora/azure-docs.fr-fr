---
title: Comprendre les modèles objet et le graphe d’intelligence spatiale - Azure Digital Twins | Microsoft Docs
description: Azure Digital Twins permet de modéliser les relations entre des personnes, des lieux et des appareils
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/30/2019
ms.openlocfilehash: cafec321e7c40e27d8de731feda1103451271507
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75562593"
---
# <a name="understand-digital-twins-object-models-and-spatial-intelligence-graph"></a>Comprendre les modèles objet Digital Twins et le graphe d’intelligence spatiale

Azure Digital Twins est un service Azure IoT qui alimente des représentations virtuelles complètes des environnements physiques et des appareils, capteurs et personnes qui leur sont associés. Il améliore le développement en organisant les concepts spécifiques à un domaine en modèles utiles. Les modèles sont ensuite situés dans un graphe d’intelligence spatiale. Ces concepts modélisent fidèlement les relations et les interactions entre les personnes, les espaces et les appareils.

Les modèles objet Digital Twins décrivent les concepts, catégories et propriétés spécifiques à un domaine. Les modèles sont prédéfinis par les utilisateurs qui souhaitent adapter la solution à leurs besoins spécifiques. Ensemble, ces modèles objet Digital Twins prédéfinis constituent une _ontologie_. L’ontologie d’un bâtiment intelligent décrit les régions, les sites, les étages, les bureaux, les zones, les salles de conférence et les espaces privés. L’ontologie d’un réseau électrique décrit les centrales, les sous-centrales, les ressources d’énergie et les clients dans leur diversité. Les ontologies et modèles objet Digital Twins permettent de personnaliser divers scénarios et besoins.

Une fois les modèles objet Digital Twins et l’ontologie en place, vous pouvez remplir un _graphe spatial_. Les graphes spatiaux sont des représentations virtuelles des nombreuses relations entre les espaces, les appareils et les personnes d’une solution IoT. Ce diagramme montre un exemple de graphe spatial utilisant l’ontologie d’un bâtiment intelligent.

[![Création d’un graphe spatial Digital Twins](media/concepts/digital-twins-spatial-graph-building.png)](media/concepts/digital-twins-spatial-graph-building.png#lightbox)

Le graphe spatial regroupe des espaces, des appareils, des capteurs et des utilisateurs. Ils sont tous liés entre eux pour modéliser le monde réel. Dans cet exemple, le site 43 comporte quatre étages, chacun ayant de nombreuses zones différentes. Les utilisateurs sont associés à leurs postes de travail et ont accès à des parties du graphe. Un administrateur est autorisé à apporter des changements au graphe spatial, alors qu’un visiteur est autorisé uniquement à voir certaines données du bâtiment.

## <a name="digital-twins-object-models"></a>Modèles objet Digital Twins

Les modèles objet Digital Twins prennent en charge ces catégories d’objets principales :

- Les **espaces** sont des emplacements virtuels ou physiques, par exemple `Tenant`, `Customer`, `Region` et `Venue`.
- Les **appareils** sont des équipements virtuels ou physiques, par exemple `AwesomeCompany Device` et `Raspberry Pi 3`.
- Les **capteurs** sont des objets qui détectent des événements, par exemple `AwesomeCompany Temperature Sensor` et `AwesomeCompany Presence Sensor`.
- Les **utilisateurs** identifient les occupants et leurs caractéristiques.

Les autres catégories d’objets sont les suivantes :

- Les **ressources** sont attachées à un espace et représentent généralement les ressources Azure destinées à être utilisées par des objets dans le graphe spatial, par exemple `IoTHub`.
- Les objets **blob** sont liés à des objets (espaces, appareils, capteurs et utilisateurs, par exemple). Ils sont utilisés en tant que fichiers avec un type mime et des métadonnées, par exemple `maps`, `pictures` et `manuals`.
- Les **types étendus** sont des énumérations extensibles qui étendent les entités avec des caractéristiques spécifiques, par exemple `SpaceType`et`SpaceSubtype`.
- Les **ontologies** représentent un ensemble de types étendus, par exemple `Default`, `Building`, `BACnet` et `EnergyGrid`.
- Les **valeurs et clés de propriété** sont des caractéristiques personnalisées d’espaces, d’appareils, de capteurs et d’utilisateurs. Elles peuvent être utilisées en plus des caractéristiques intégrées, par exemple `DeltaProcessingRefreshTime` en tant que clé, et `10` en tant que valeur.
- Les **rôles** sont des ensembles d’autorisations affectées aux utilisateurs et appareils dans le graphe spatial, par exemple `Space Administrator`, `User Administrator` et `Device Administrator`.
- Les **attributions de rôles** représentent l’association entre un rôle et un objet dans le graphe spatial. Par exemple, un utilisateur ou un principal de service peut se voir accorder l’autorisation de gérer un espace dans le graphe spatial.
- Les **magasins de clés de sécurité** fournissent les clés de sécurité de tous les appareils de la hiérarchie sous un objet d’espace donné, ce qui permet à l’appareil de communiquer de manière sécurisée avec Digital Twins.
- Les **fonctions définies par l’utilisateur** (ou fonctions UDF) permettent de personnaliser le traitement des données de télémétrie des capteurs dans le graphe spatial. Par exemple, une fonction UDF peut :
  - Définir la valeur d’un capteur.
  - Exécuter une logique personnalisée basée sur les lectures de capteurs, et définir la sortie vers un espace.
  - Attacher des métadonnées à un espace.
  - Envoyer des notifications quand des conditions prédéfinies sont remplies. Les fonctions UDF peuvent être écrites en JavaScript.
- Les **matchers** sont des objets qui déterminent les fonctions UDF à exécuter pour un message de télémétrie donné.
- Les **points de terminaison** sont les emplacements où peuvent être routés les messages de télémétrie et les événements Digital Twins, par exemple `Event Hub`, `Service Bus` et `Event Grid`.

## <a name="spatial-intelligence-graph"></a>Graphique d’intelligence spatiale

Le graphe spatial est le graphe hiérarchique des espaces, des appareils et des personnes définis dans le modèle objet Digital Twins. Le graphe spatial prend en charge l’héritage, le filtrage, le parcours, la scalabilité et l’extensibilité. Vous pouvez gérer votre graphe spatial et interagir avec ce dernier à l’aide d’une collection d’API REST.

Si vous déployez un service Digital Twins dans votre abonnement, vous devenez l’administrateur général du nœud racine. Vous disposez alors automatiquement d’un accès total à l’intégralité de la structure. Provisionnez des espaces dans le graphe à l’aide de l’API d’espace. Provisionnez des services à l’aide de l’API d’appareil, et des capteurs à l’aide de l’API de capteur. Des [outils open source](https://github.com/Azure-Samples/digital-twins-samples-csharp) sont également disponibles pour provisionner le graphe en bloc.

**Héritage du graphe**. L’héritage s’applique aux autorisations et propriétés qui descendent d’un nœud parent vers tous les nœuds situés en dessous. Par exemple, quand un rôle est attribué à un utilisateur sur un nœud donné, l’utilisateur a les autorisations de ce rôle sur ce nœud et sur tous les nœuds situés en dessous. Chaque clé de propriété et type étendu défini pour un nœud donné est hérité par tous les nœuds situés sous ce nœud.

**Filtrage du graphe**. Le filtrage permet d’affiner les résultats des requêtes. Vous pouvez filtrer par ID, par noms, par types, par sous-types, par espaces parents et par espaces associés. Vous pouvez également filtrer en fonction des types de données de capteur, des clés et valeurs de propriété, de *traverse*, *minLevel*, *maxLevel* et d’autres paramètres de filtre OData.

**Parcours du graphe**. Vous pouvez parcourir le graphe spatial en profondeur et en largeur. Pour la profondeur, parcourez le graphe de haut en bas ou de bas en haut à l’aide des paramètres *traverse*, *minLevel* et *maxLevel*. Pour la largeur, parcourez le graphe afin d’obtenir des nœuds frères directement attachés à un espace parent ou à l’un de ses descendants. Quand vous interrogez un objet, vous pouvez obtenir tous les objets connexes ayant des relations avec cet objet à l’aide du paramètre *includes* des API GET.

**Scalabilité du graphe**. Dans la mesure où Digital Twins garantit la scalabilité du graphe, il peut traiter vos charges de travail réelles. Vous pouvez utiliser Digital Twins pour représenter de grands portefeuilles de biens immobiliers, une infrastructure, des appareils, des capteurs, des données de télémétrie et bien plus encore.

**Extensibilité du graphe**. Utilisez l’extensibilité pour personnaliser les modèles objet Digital Twins sous-jacents avec de nouveaux types et ontologies. Vos données Digital Twins peuvent également être enrichies avec des valeurs et propriétés extensibles.

### <a name="spatial-intelligence-graph-management-apis"></a>API de gestion du graphe d’intelligence spatiale

Une fois que vous avez déployé Digital Twins à partir du [portail Azure](https://portal.azure.com), l’URL [Swagger](https://swagger.io/tools/swagger-ui/) des API de gestion est automatiquement générée. Elle est affichée dans le portail Azure, dans la section **Vue d’ensemble**, au format suivant.

```plaintext
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| Name | Remplacer par |
| --- | --- |
| YOUR_INSTANCE_NAME | Nom de votre instance Digital Twins |
| YOUR_LOCATION | Région de serveur dans laquelle votre instance est hébergée |

 Le format complet de l’URL apparaît dans cette image.

[![API de gestion dans le portail Digital Twins](media/concepts/digital-twins-spatial-graph-management-api-url.png)](media/concepts/digital-twins-spatial-graph-management-api-url.png#lightbox)

Pour plus d’informations sur l’utilisation des graphes d’intelligence spatiale, consultez le premier aperçu des API de gestion Azure Digital Twins.

> [!TIP]
> Vous pouvez obtenir un premier aperçu de Swagger qui illustre le jeu de fonctionnalités de l’API.
> Pour ce faire, rendez-vous à l’adresse [docs.westcentralus.azuresmartspaces.net/management/swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

Découvrez-en plus sur l’[utilisation de Swagger](how-to-use-swagger.md).

Tous les appels d’API doivent être authentifiés via [OAuth](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code). Les API suivent les [conventions indiquées dans les directives relatives aux API REST Microsoft](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md). La plupart des API qui retournent des collections prennent en charge les options de requête système [OData](https://www.odata.org/getting-started/basic-tutorial/#queryData).

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la connectivité des appareils et sur l’envoi de messages de télémétrie à Digital Twins, consultez [Entrée de télémétrie et connectivité des appareils Azure Digital Twins](concepts-device-ingress.md).

- Pour en savoir plus sur les limitations de l’API de gestion, consultez [Gestion et limitations de l’API Azure Digital Twins](concepts-service-limits.md).
