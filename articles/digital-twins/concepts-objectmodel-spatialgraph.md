---
title: Présentation du graphe d’intelligence spatiale et des modèles objet Digital Twins | Microsoft Docs
description: Utilisation d’Azure Digital Twins pour modéliser les relations entre personnes, lieux et appareils
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: alinast
ms.openlocfilehash: 1c2068af510cb3733ce99a6ae7b40487a8c1a015
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323733"
---
# <a name="understanding-digital-twins-object-models-and-spatial-intelligence-graph"></a>Présentation du graphe d’intelligence spatiale et des modèles objet Digital Twins

Azure Digital Twins est un service Azure IoT qui alimente des représentations virtuelles complètes des environnements physiques et des appareils, capteurs et personnes qui leur sont associés. Il améliore le développement en organisant les concepts propres à un domaine en modèles utiles, eux-mêmes situés dans un graphe d’intelligence spatiale. Ces concepts modélisent fidèlement les relations et les interactions entre les personnes, les espaces et les appareils.

Les _modèles objet Digital Twins_ décrivent les concepts, catégories et propriétés propres à un domaine. Les modèles sont prédéfinis par les utilisateurs qui cherchent à adapter la solution à leurs besoins spécifiques. Ensemble, ces modèles objet Digital Twins prédéfinis constituent une _ontologie_. L’ontologie d’un bâtiment intelligent décrirait des régions, lieux, étages, bureaux, zones, salles de conférence et espaces privés. L’ontologie d’un réseau électrique décrirait diverses centrales, sous-centrales, ressources d’énergie et clients. Ces modèles objet Digital Twins et ontologies permettent de personnaliser Azure Digital Twins en fonction des besoins et des scénarios.

Une fois les _modèles objet Digital Twins_ et l’_ontologie_ en place, il est possible de remplir un _graphe spatial_. Les graphes spatiaux sont des représentations virtuelles des nombreuses relations entre les espaces, les appareils et les personnes appropriés pour une solution IoT. Le diagramme ci-dessous montre un exemple de graphe spatial utilisant l’ontologie d’un bâtiment intelligent.

![Création d’un graphe spatial Digital Twins][1]

<a id="model" />

Le graphe spatial regroupe des espaces, des appareils, des capteurs et des utilisateurs. Chacun est lié à l’ensemble d’une manière qui modélise le monde réel : le lieu 43 possède quatre étages, chacun ayant plusieurs zones différentes. Les utilisateurs sont associés à leurs stations de travail et disposent d’un accès à des parties du graphe.  Par exemple, un administrateur peut être autorisé à apporter des modifications au graphe spatial, tandis qu’un visiteur peut être autorisé uniquement à voir certaines données du bâtiment.

## <a name="digital-twins-object-models"></a>Modèles objet Digital Twins

Les modèles objet Digital Twins prennent en charge ces catégories d’objets principales :

- Les **espaces** sont des emplacements virtuels ou physiques, par exemple `Tenant`, `Customer`, `Region` et `Venue`.
- Les **appareils** sont des parties virtuelles ou physiques du matériel, par exemple `AwesomeCompany Device` et `Raspberry Pi 3`.
- Les **capteurs** sont des objets qui détectent des événements, par exemple `AwesomeCompany Temperature Sensor` et `AwesomeCompany Presence Sensor`.
- Les **utilisateurs** identifient les occupants et leurs caractéristiques.

Les autres catégories d’objets sont les suivantes :

- Les **ressources** sont attachées à un espace et représentent généralement des ressources Azure destinées à être utilisées par des objets dans le graphe spatial, par exemple `IoTHub`.
- Les **objets blob** sont liés à des objets (tels que des espaces, des appareils, des capteurs et des utilisateurs) et sont utilisés en tant que fichiers avec le type mime et des métadonnées, par exemple `maps`, `pictures` et `manuals`.
- Les **types étendus** sont des énumérations extensibles qui enrichissent les entités avec des caractéristiques spécifiques, par exemple `SpaceType` et `SpaceSubtype`.
- Les **ontologies** représentent un ensemble de types étendus, par exemple `Default`, `Building`, `BACnet` et `EnergyGrid`.
- Les **valeurs et clés de propriété** sont des caractéristiques personnalisées d’espaces, d’appareils, de capteurs et d’utilisateurs. Elles peuvent servir en plus des caractéristiques intégrées, par exemple `DeltaProcessingRefreshTime` en tant que clé et `10` en tant que valeur.
- Les **rôles** sont des ensembles d’autorisations affectées aux utilisateurs et appareils dans le graphe spatial, par exemple `Space Administrator`, `User Administrator` et `Device Administrator`.
- Les **attributions de rôles** constituent l’association entre un rôle et un objet dans le graphe spatial, par exemple, le fait d’accorder à un utilisateur ou à un principal de service l’autorisation de gérer un espace dans le graphe spatial.
- Les **magasins de clés de sécurité** fournissent les clés de sécurité pour tous les appareils dans la hiérarchie sous un objet d’espace donné, un appareil pouvant ainsi communiquer de manière sécurisée avec le service Digital Twins.
- Les **fonctions définies par l’utilisateur** ou **fonctions UDF** permettent de personnaliser le traitement des données de télémétrie des capteurs dans le graphe spatial. Par exemple, une fonction UDF peut définir une valeur de capteur, exécuter une logique personnalisée en fonction des lectures du capteur, définir la sortie vers un espace, attacher des métadonnées à un espace et envoyer des notifications quand des conditions prédéfinies sont remplies. Les fonctions UDF peuvent être écrites en JavaScript.
- Les **matchers** sont des objets qui déterminent les fonctions UDF à exécuter pour un message de télémétrie donné.
- Les **points de terminaison** sont les emplacements où peuvent être routés les messages de télémétrie et les événements Digital Twins, par exemple `Event Hub`, `Service Bus` et `Event Grid`.

<a id="graph" />

## <a name="spatial-intelligence-graph"></a>Graphe d’intelligence spatiale

Le **graphe spatial** est le graphe hiérarchique des espaces, appareils et personnes définis dans le **modèle objet Digital Twins**. Le graphe spatial prend en charge l’_héritage_, le _filtrage_, le _parcours_, la _scalabilité_ et l’_extensibilité_. Les utilisateurs peuvent gérer leur graphe spatial et interagir avec ce dernier au moyen d’une collection d’API REST (voir ci-après).

L’utilisateur qui déploie un service Digital Twins dans son abonnement devient l’administrateur général du nœud racine, ce qui lui confère automatiquement l’accès total à la structure toute entière. Cet utilisateur peut alors provisionner des espaces dans le graphe à l’aide de l’API `Space`. Les appareils peuvent être provisionnés à l’aide de l’API `Device`, les capteurs à l’aide d’API `Sensor`, etc. Nous offrons également des [outils open source](https://github.com/Azure-Samples/digital-twins-samples-csharp) pour le provisionnement du graphe en bloc.

L’_héritage_ du graphe s’applique aux autorisations et aux propriétés qui descendent d’un nœud parent jusqu’à tous les nœuds qui se trouvent dessous. Par exemple, quand un rôle est affecté à un utilisateur sur un nœud donné, l’utilisateur a les autorisations de ce rôle sur ce nœud et sur tous les nœuds en dessous. En outre, chaque clé de propriété et type étendu définis pour un nœud donné sont hérités par tous les nœuds sous ce nœud.

Le _filtrage_ du graphe permet aux utilisateurs d’affiner les résultats d’une demande par ID, nom, types, sous-types, espace parent, espaces associés, types de données de capteur, clés et valeurs de propriété, parcours, niveau minimal, niveau maximal et autres paramètres de filtre OData.

Le _parcours_ du graphe permet aux utilisateurs de parcourir le graphe spatial dans ses largeur et profondeur. Pour la profondeur, le graphe peut être parcouru de haut en bas ou de bas en haut, à l’aide des paramètres de navigation `traverse`, `minLevel` et `maxLevel`. Pour la largeur, l’utilisateur peut parcourir le graphe afin d’obtenir des nœuds frères directement attachés à un espace parent ou à un de ses descendants. Quand vous interrogez un objet, vous pouvez obtenir tous les objets connexes liés à cet objet à l’aide du paramètre `includes` des API GET.

Azure Digital Twins garantissant la _scalabilité_ du graphe, il peut traiter vos charges de travail réelles. Vous pouvez utiliser Digital Twins pour représenter de grands portefeuilles de biens immobiliers, une infrastructure, des appareils, des capteurs, des données de télémétrie et bien plus encore.

L’_extensibilité_ du graphe permet aux utilisateurs de personnaliser les modèles objet Digital Twins sous-jacents avec de nouveaux types et de nouvelles ontologies. Les données de vos jumeaux numériques peuvent également être enrichies avec des valeurs et propriétés extensibles.

### <a name="spatial-intelligence-graph-management-apis"></a>API de gestion du graphe d’intelligence spatiale

Une fois que vous avez déployé Azure Digital Twins à partir du [portail Azure](https://portal.azure.com), l’URL [Swagger](https://swagger.io/tools/swagger-ui/) des API de gestion est générée automatiquement, puis apparaît dans la section **Vue d’ensemble** du portail Azure dans le format suivant :

```plaintext
https://yourInstanceName.yourLocation.azuresmartspaces.net/management/swagger
```

| Nom de l’attribut personnalisé | Remplacer par |
| --- | --- |
| `yourInstanceName` | Nom de votre instance Azure Digital Twins |
| `yourLocation` | Région de serveur dans laquelle votre instance est hébergée |

 L’image ci-dessous montre le format complet de l’URL :

![API de gestion dans le portail Digital Twins][2]

Pour plus d’informations sur l’utilisation des graphes d’intelligence spatiale, visitez l’aperçu rapide des API de gestion Azure Digital Twins.

> [!TIP]
> Vous pouvez obtenir un premier aperçu de Swagger qui illustre le jeu de fonctionnalités de l’API.
> Pour ce faire, rendez-vous à l’adresse [docs.westcentralus.azuresmartspaces.net/management/swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

Découvrez-en plus sur l’[utilisation de Swagger](how-to-use-swagger.md).

Tous les appels d’API doivent être authentifiés à l’aide d’[OAuth](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code). Les API suivent les [conventions indiquées dans les directives relatives aux API REST Microsoft](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md). La plupart des API qui retournent des collections prennent en charge les options de requête système [OData](http://www.odata.org/getting-started/basic-tutorial/#queryData).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la connectivité des appareils et sur l’envoi de messages de télémétrie au service Azure Digital Twins, consultez [Entrée de télémétrie et connectivité des appareils Azure Digital Twins](concepts-device-ingress.md).

Pour en savoir plus sur les limitations de l’API de gestion, consultez [Gestion et limitations de l’API Azure Digital Twins](concepts-service-limits.md).

<!-- Images -->
[1]: media/concepts/digital-twins-spatial-graph-building.png
[2]: media/concepts/digital-twins-spatial-graph-management-api-url.png
