---
title: Modèles DTDL
titleSuffix: Azure Digital Twins
description: Découvrez comment Azure Digital Twins utilise des modèles personnalisés pour décrire les entités de votre environnement.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: ad6d006157424c995ea94e89163d69a0db242ce1
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109789679"
---
# <a name="understand-twin-models-in-azure-digital-twins"></a>Comprendre les modèles de jumeau dans Azure Digital Twins

L’une des principales caractéristiques d’Azure Digital Twins est la possibilité de définir votre propre vocabulaire et à créer votre graphique de jumeau dans les termes définis par votre entreprise. Cette fonctionnalité est fournie via les **modèles** spécifiés par l’utilisateur. Vous pouvez considérer les modèles comme des noms dans une description de votre monde. 

Un modèle est comparable à une **classe** dans un langage de programmation orienté objet, définissant une forme de données pour un concept particulier dans votre environnement de travail réel. Les modèles ont des noms (par exemple, *Room* ou *TemperatureSensor*) et contiennent des éléments tels que des propriétés, de la télémétrie ou des événements, ainsi que des commandes décrivant ce que peut faire ce type d’entité dans votre environnement. Plus tard, vous utiliserez ces modèles pour créer des [jumeaux numériques](concepts-twins-graph.md) représentant des entités spécifiques répondant à une description de ce type.

Les modèles Azure Digital Twins sont représentés avec le **langage DTDL (Digital Twins Definition Language)** basé sur JSON-LD.  

## <a name="digital-twin-definition-language-dtdl-for-models"></a>Langage DTDL (Digital Twin Definition Language) pour les modèles

Les modèles pour Azure Digital Twins sont définis à l’aide du langage de définition Digital Twins (DTDL). 

Vous pouvez afficher les spécifications de langage complètes pour DTDL dans GitHub : [Langage de définition Digital Twins (DTDL) - version 2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).

Le langage DTDL est basé sur JSON-LD et est indépendant du langage de programmation. Le langage DTDL n’est pas exclusif d’Azure Digital Twins. Il est également utilisé pour représenter des données d’appareil dans d’autres services IoT tels que [IoT Plug-and-Play](../iot-pnp/overview-iot-plug-and-play.md). Azure Digital Twins utilise DTDL **version 2** (l’utilisation de DTDL version 1 avec Azure Digital Twins est désormais déconseillée). 

Le reste de cet article résume la façon dont la langue est utilisée dans Azure Digital Twins.

### <a name="azure-digital-twins-dtdl-implementation-specifics"></a>Spécificités de l’implémentation du langage DTDL dans Azure Digital Twins

Les services qui utilisent le langage DTDL n’implémentent pas tous exactement les mêmes fonctionnalités de ce langage. Par exemple, la technologie IoT Plug-and-Play n’utilise pas les fonctionnalités DTDL destinées aux graphiques, tandis qu’Azure Digital Twins n’implémente pas actuellement de commandes DTDL. 

Pour qu’un modèle DTDL soit compatible avec Azure Digital Twins, il doit répondre à ces exigences :

* Tous les éléments DTDL de niveau supérieur dans un modèle doivent être de type *interface*. Cela est dû au fait que les API de modèle Azure Digital Twins peuvent recevoir des objets JSON représentant une interface ou un tableau d’interfaces. Par conséquent, aucun autre type d’élément DTDL n’est autorisé au niveau supérieur.
* Le langage DTDL pour Azure Digital Twins ne doit pas définir de *commandes*.
* Azure Digital Twins n’autorise qu’un seul niveau d’imbrication de composant. Cela signifie qu’une interface utilisée en tant que composant ne peut pas avoir de composants. 
* Les interfaces ne peuvent pas être définies comme incluses dans d’autres interfaces DTDL. Elles doivent être définies en tant qu’entités de niveau supérieur distinctes avec leurs propres ID. Ensuite, si une autre interface souhaite inclure cette interface en tant que composant ou par héritage, elle peut référencer son ID.

En outre, Azure Digital Twins ne tient pas compte de l’attribut `writable` sur les propriétés ou les relations. Bien que cet attribut puisse être défini en fonction des spécifications DTDL, la valeur n’est pas utilisée par Azure Digital Twins. Au lieu de cela, les propriétés ou les relations sont toujours traitées comme accessibles en écriture par les clients externes qui ont des autorisations d’écriture générales sur le service Azure Digital Twins.

## <a name="elements-of-a-model"></a>Éléments d’un modèle

Dans une définition de modèle, l’élément de code de niveau supérieur est une **interface**. Celle-ci encapsule le modèle entier, le reste du modèle étant défini dans l’interface. 

Une interface de modèle DTDL peut contenir de zéro à un grand nombre des champs suivants :
* **Propriété** : certaines propriétés sont des champs de données représentant l’état d’une entité ; c’est par exemple le cas des propriétés dans de nombreux langages de programmation orientés objet. Les propriétés ont un stockage de sauvegarde et peuvent être lues à tout moment.
* **Télémétrie** : les champs de télémétrie représentent des mesures ou des événements, et sont souvent utilisés pour décrire des lectures de capteur d’appareil. Contrairement aux propriétés, la télémétrie n’est pas stockée sur un jumeau numérique. Il s’agit d’une série d’événements de données limités dans le temps qui doivent être traités au fur et à mesure qu’ils se produisent. Pour plus d’informations sur les différences entre les propriétés et la télémétrie, consultez la section [Propriétés et télémétrie](#properties-vs-telemetry) ci-dessous.
* **Composant** : les composants vous permettent, le cas échéant, de créer votre interface de modèle en tant qu’assembly d’autres interfaces. Un exemple de composant est une interface *frontCamera*, ainsi qu’une autre interface de composant *backCamera*, utilisées pour la définition d’un modèle de *téléphone*. Vous devez commencer par définir une interface pour le composant *frontCamera*, comme s’il s’agissait de son propre modèle, puis la référencer lors de la définition de *téléphone*.

    Utilisez un composant pour décrire un élément qui fait partie intégrante de votre solution mais n’a pas besoin d’identité séparée, et qu’il n’est pas nécessaire de créer, supprimer ou réorganiser de manière indépendante dans le graphique de jumeaux. Si vous souhaitez que les entités aient des existences indépendantes dans le graphique de jumeaux, représentez-les en tant que jumeaux numériques séparés de différents modèles connectés par des *relations* (voir la puce suivante).
    
    >[!TIP] 
    >Des composants peuvent également être utilisés pour une organisation, afin de regrouper des ensembles de propriétés connexes à l’intérieur d’une interface de modèle. Dans ce cas, vous pouvez considérer chaque composant comme un espace de noms, ou « dossier », à l’intérieur de l’interface.
* **Relation** : les relations vous permettent de représenter la manière dont un jumeau numérique peut être impliqué avec d’autres jumeaux numériques. Elles peuvent avoir différents sens sémantiques. Par exemple *contains* (« floor contains room »), *cools* (« hvac cools room »), *isBilledTo* (« compressor is billed to user »), etc. Les relations permettent à la solution de fournir un graphique d’entités associées. Les relations peuvent également avoir leurs propres [propriétés](#properties-of-relationships).

> [!NOTE]
> La [spec pour le langage DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) définit également des **commandes**, c’est-à-dire des méthodes qui peuvent être exécutées sur un jumeau numérique (comme une commande de réinitialisation ou d’allumage/extinction d’un ventilateur). Cependant, les *commandes ne sont actuellement pas prises en charge dans Azure Digital Twins*.

### <a name="properties-vs-telemetry"></a>Propriétés et télémétrie

Voici quelques conseils supplémentaires sur la distinction entre les champs DTDL **Propriétés** et **Télémétrie** dans Azure Digital Twins.

Les différences entre les propriétés et la télémétrie pour les modèles Azure Digital Twins sont les suivantes :
* Les **propriétés** sont supposées avoir un stockage de sauvegarde. Cela signifie que vous pouvez lire une propriété à tout moment et en récupérer la valeur. Si la propriété est accessible en écriture, vous pouvez également stocker une valeur dans la propriété.  
* La **télémétrie** ressemble davantage à un flux d’événements. Il s’agit d’un ensemble de messages de données qui ont des durées de vie courtes. Si vous ne configurez pas l’écoute pour l’événement et les actions à entreprendre lorsqu’il se produit, il n’y a plus de trace de l’événement par la suite. Vous ne pouvez pas y revenir et le lire plus tard. 
  - En termes C#, la télémétrie est comme un événement C#. 
  - En termes IoT, la télémétrie est généralement une mesure unique envoyée par un appareil.

La **télémétrie** est souvent utilisée avec les appareils IoT, car de nombreux appareils ne sont pas en mesure de stocker les valeurs de mesure qu’ils génèrent, ou n’en ont pas besoin. Ils les envoient simplement sous forme de flux d’événements de « télémétrie ». Dans ce cas, vous ne pouvez pas interroger l’appareil à tout moment pour connaître la valeur la plus récente du champ de télémétrie. Au lieu de cela, vous devez écouter les messages envoyés par l’appareil et effectuer des actions à mesure que les messages arrivent. 

Par conséquent, lors de la conception d’un modèle dans Azure Digital Twins, vous utiliserez probablement des **propriétés** dans la plupart des cas pour modéliser vos jumeaux. Cela vous permet de disposer du stockage de sauvegarde et de lire et d’interroger les champs de données.

La télémétrie et les propriétés collaborent souvent pour traiter l’entrée de données provenant des appareils. Comme toute entrée dans Azure Digital Twins se fait via des [API](concepts-apis-sdks.md), vous utiliserez généralement votre fonction d’entrée pour lire les événements de télémétrie ou de propriété des appareils, et vous définirez une propriété dans Azure Digital Twins en réponse. 

Vous pouvez également publier un événement de télémétrie à partir de l’API Azure Digital Twins. Comme pour toute autre télémétrie, il s’agit d’un événement de courte durée qui nécessite un écouteur pour le traiter.

#### <a name="properties-of-relationships"></a>Propriétés des relations

DTDL permet également aux **relations** d’avoir leurs propres propriétés. Lors de la définition d’une relation dans un modèle DTDL, la relation peut avoir son propre champ `properties` dans lequel vous pouvez définir des propriétés personnalisées pour décrire l’état spécifique à la relation.

## <a name="model-inheritance"></a>Héritage de modèle

Il est parfois nécessaire de spécialiser un modèle. Par exemple, il peut être utile de disposer d’un modèle générique, Room, et de variantes spécialisées, ConferenceRoom et Gym. Pour exprimer la spécialisation, le langage DTDL prend en charge l’héritage : des interfaces peuvent hériter d’une ou plusieurs autres interfaces. 

L’exemple suivant revisite le modèle Planet de l’exemple de DTDL précédent en tant que sous-type d’un modèle CelestialBody plus grand. Une fois le modèle « parent » défini, il est utilisé pour élaborer le modèle « enfant » à l’aide du champ `extends`.

:::code language="json" source="~/digital-twins-docs-samples/models/CelestialBody-Planet-Crater.json":::

Dans cet exemple, le modèle CelestialBody fournit un nom, une masse et une température au modèle Planet. La section `extends` est un nom d’interface ou un tableau de noms d’interface (permettant, le cas échéant, à l’interface d’extension d’hériter de plusieurs modèles parents).

Une fois l’héritage appliqué, l’interface d’extension expose toutes les propriétés de la chaîne d’héritage entière.

L’interface d’extension ne peut pas modifier les définitions des interfaces parentes, mais simplement y faire des ajouts. Elle ne peut pas non plus redéfinir une fonctionnalité déjà définie dans ses interfaces parentes (même si les fonctionnalités sont définies pour être identiques). Par exemple, si une interface parente définit une propriété `double` *mass*, l’interface d’extension ne peut pas contenir une déclaration de *mass*, même s’il s’agit également d’un `double`.

## <a name="model-code"></a>Code du modèle

Des modèles de type de jumeau peuvent être écrits dans n’importe quel éditeur de texte. Le langage DTDL suivant la syntaxe JSON, vous devez donc stocker les modèles avec l’extension .json. L’utilisation de l’extension JSON permet à de nombreux éditeurs de texte de programmation de fournir une vérification et une mise en évidence de syntaxe de base pour vos documents DTDL. Il existe également une [extension DTDL](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) disponible pour [Visual Studio Code](https://code.visualstudio.com/).

### <a name="possible-schemas"></a>Schémas possibles

Comme pour langage DTDL, le schéma pour les attributs *Property* et *Telemetry* peuvent être composés de types primitifs standard (`integer`, `double`, `string` et `Boolean`) et d’autres types tels que `DateTime` et `Duration`. 

En plus des types primitifs, les champs *Property* et *Telemetry* peuvent avoir les types complexes suivants :
* `Object`
* `Map`
* `Enum`

Les champs *Telemetry* prennent également en charge `Array`.

### <a name="example-model"></a>Exemple de modèle

Cette section contient un exemple de modèle classique, écrit sous la forme d’une interface DTDL. Le modèle décrit des **planètes**, chacune avec un nom, une masse et une température.
 
Sachez que les planètes peuvent également interagir avec des **lunes** qui sont leurs satellites et qu’elles peuvent contenir des **cratères**. Dans l’exemple ci-dessous, le modèle Planète exprime les connexions à ces autres entités en référençant deux modèles externes, Lune et Cratère. Ces modèles sont également définis dans l’exemple de code ci-dessous, mais ils sont très simples pour ne pas porter atteinte à l’exemple de planète principal.

:::code language="json" source="~/digital-twins-docs-samples/models/Planet-Crater-Moon.json":::

Les champs du modèle sont les suivants :

| Champ | Description |
| --- | --- |
| `@id` | Identificateur pour le modèle. Doit être au format `dtmi:<domain>:<unique model identifier>;<model version number>`. |
| `@type` | Identifie le type des informations décrites. Pour une interface, le type est *interface*. |
| `@context` | Définit le [contexte](https://niem.github.io/json/reference/json-ld/context/) pour le document JSON. Les modèles doivent utiliser `dtmi:dtdl:context;2`. |
| `displayName` | [facultatif] Vous permet, le cas échéant, d’attribuer au modèle un nom convivial. |
| `contents` | Toutes les données d’interface restantes sont placées ici, sous la forme d’un tableau de définitions d’attributs. Chaque attribut doit fournir un `@type` (*Property*, *Telemetry*, *Command*, *Relationship* ou *Component*) pour identifier le type d’informations d’interface qu’il décrit, puis un ensemble de propriétés qui définissent l’attribut réel (par exemple, `name` et `schema` pour définir une *Propriété*). |

> [!NOTE]
> Notez que l’interface de composant (Crater dans cet exemple) est définie dans le même tableau que l’interface qui l’utilise (Planet). Les composants doivent être définis de cette façon dans les appels d’API, afin que l’interface soit trouvée.

## <a name="best-practices-for-designing-models"></a>Bonnes pratiques en matière de conception de modèles

Lors de la conception de modèles pour refléter les entités dans votre environnement, il peut s’avérer utile d’examiner les implications des [requêtes](concepts-query-language.md) dans votre conception. Vous avez peut-être envie de concevoir des propriétés de manière à éviter les grands jeux de résultats provenant de la traversée de graphe. Vous pouvez également modéliser les relations qui doivent être traitées dans une seule requête en tant que relations à un seul niveau.

### <a name="validating-models"></a>Validation des modèles

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="tools-for-models"></a>Outils pour modèles 

Il existe plusieurs exemples qui permettent de simplifier grandement la gestion des modèles et des ontologies. Ils sont situés dans le dépôt suivant : [Outils pour le langage DTDL (Digital Twins Definition Language)](https://github.com/Azure/opendigitaltwins-tools).

Cette section décrit plus en détail l’ensemble actuel d’échantillons.

### <a name="model-uploader"></a>Chargeur de modèles 

_**Pour le chargement de modèles vers Azure Digital Twins**_

Une fois que vous avez fini de créer, d’étendre ou de sélectionner vos modèles, vous pouvez les charger vers votre instance d’Azure Digital Twins pour les rendre accessibles dans votre solution. Pour ce faire, utilisez les [API Azure Digital Twins](concepts-apis-sdks.md), comme indiqué dans le [Guide pratique pour Gérer les modèles DTDL](how-to-manage-model.md#upload-models).

Toutefois, si vous avez de nombreux modèles à charger, ou s’ils ont de nombreuses interdépendances qui compliquent le tri des chargements individuels, vous pouvez utiliser [cet exemple de chargeur de modèles Azure Digital Twins](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/ModelUploader) pour charger de nombreux modèles à la fois . Suivez les instructions fournies avec l’exemple pour configurer et utiliser ce projet afin de charger des modèles vers votre propre instance.

### <a name="model-visualizer"></a>Visualiseur de modèles 

_**Pour la visualisation des modèles**_

Une fois que vous avez chargé des modèles dans votre instance Azure Digital Twins, vous pouvez les voir dans cette dernière. Vous pouvez voir notamment les relations d’héritage et les relations entre modèles à l’aide de l’application [Azure Digital Twins Model Visualizer](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/AdtModelVisualizer). Cet exemple est à l’état de brouillon. Nous encourageons la communauté de développement de jumeaux numériques à étendre cet exemple, et à y contribuer. 

## <a name="next-steps"></a>Étapes suivantes

* Découvrez plus en détail la création de modèles basés sur des ontologies conformes aux standards du secteur : [Concepts : Qu’est-ce qu’une ontologie ?](concepts-ontologies.md) 

* Explorez plus en détail la gestion des modèles à l’aide des opérations d’API : [Guide pratique : Gérer les modèles DTDL](how-to-manage-model.md)

* Découvrez comment les modèles permettent de créer des jumeaux numériques : [Concepts : Jumeaux numériques et graphe de jumeaux](concepts-twins-graph.md)

