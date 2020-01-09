---
title: Parcourir les API – Azure Digital Twins | Microsoft Docs
description: Découvrez les modèles courants d’interrogation des API de gestion Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/27/2019
ms.openlocfilehash: 86ade45cd00e82e8787a117c23003d2a74750cf0
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552166"
---
# <a name="how-to-use-azure-digital-twins-management-apis"></a>Guide pratique pour utiliser les API de gestion Azure Digital Twins

Les API de gestion Azure Digital Twins fournissent de puissantes fonctionnalités pour vos applications IoT. Cet article montre comment naviguer dans la structure des API.  

## <a name="api-summary"></a>Résumé des API

La liste suivante montre les composants des API Digital Twins.

* [/spaces](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces) : ces API interagissent avec les emplacements physiques dans votre installation. Elles vous aident à créer, supprimer et gérer les mappages numériques de vos emplacements physiques sous la forme d’un [graphe spatial](concepts-objectmodel-spatialgraph.md#spatial-intelligence-graph).

* [/devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices) : ces API interagissent avec les appareils dans votre installation. Ces appareils peuvent gérer un ou plusieurs capteurs. Il peut s’agir par exemple de votre téléphone, d’un pod de capteur Raspberry Pi, d’une passerelle Lora, et ainsi de suite.

* [/sensors](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Sensors) : ces API vous aident à communiquer avec les capteurs associés à vos appareils et vos emplacements physiques. Les capteurs enregistrent et envoient des valeurs ambiantes, qui peuvent ensuite être utilisées pour manipuler votre environnement spatial.  

* [/resources](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Resources) : ces API vous aident à configurer les ressources, par exemple un hub IoT, pour votre instance Digital Twins.

* [/types](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Types) : ces API vous permettent d’associer des types étendus à vos objets Digital Twins, afin d’ajouter des caractéristiques spécifiques à ces objets. Ces types facilitent le filtrage et le regroupement d’objets dans l’interface utilisateur et les fonctions personnalisées qui traitent vos données de télémétrie. *DeviceType*, *SensorType*, *SensorDataType*, *SpaceType*, *SpaceSubType*, *SpaceBlobType* et *SpaceResourceType* sont des exemples de types étendus.

* [/ontologies](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Ontologies) : ces API vous aident à gérer des ontologies, qui sont des collections de types étendus. Les ontologies fournissent des noms pour les types d’objets en fonction de l’espace physique qu’ils représentent. Par exemple, l’ontologie *BACnet* fournit des noms spécifiques pour *sensortypes*, *datatypes*, *datasubtypes* et *dataunittypes*. Les ontologies sont gérées et créées par le service. Les utilisateurs peuvent charger et décharger des ontologies. Quand une ontologie est chargée, tous ses noms de types associés sont activés et prêts à être provisionnés dans votre graphe spatial. 

* [/propertyKeys](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/PropertyKeys) : vous pouvez utiliser ces API pour créer des propriétés personnalisées pour vos *espaces*, *appareils*, *utilisateurs* et *capteurs*. Ces propriétés sont créées en tant que paires clé/valeur. Vous pouvez définir le type de données pour ces propriétés en définissant leur *PrimitiveDataType*. Par exemple, vous pouvez définir une propriété nommée *BasicTemperatureDeltaProcessingRefreshTime* de type *uint* pour vos capteurs, puis attribuer une valeur à cette propriété pour chacun de vos capteurs. Vous pouvez également ajouter des contraintes pour ces valeurs lors de la création de la propriété, telles que des plages d’adresses *Min* et *Max*, ainsi que des valeurs autorisées en tant que *ValidationData*.

* [/matchers](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Matchers) : ces API vous permettent de spécifier les conditions que vous souhaitez évaluer à partir de vos données d’appareil entrantes. Pour plus d’informations, consultez [cet article](concepts-user-defined-functions.md#matchers). 

* [/userDefinedFunctions](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/UserDefinedFunctions) : ces API vous permettent de créer, supprimer ou mettre à jour une fonction personnalisée qui s’exécute quand les conditions définies par les *matchers* se produisent, afin de traiter les données provenant de votre installation. Pour plus d’informations sur ces fonctions personnalisées, également appelées *fonctions définies par l’utilisateur*, consultez [cet article](concepts-user-defined-functions.md#user-defined-functions). 

* [/endpoints](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Endpoints) : ces API vous permettent de créer des points de terminaison afin que votre solution Digital Twins puisse communiquer avec d’autres services Azure pour le stockage et l’analytique des données. Pour plus d’informations, consultez [cet article](concepts-events-routing.md). 

* [/keyStores](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/KeyStores) : ces API vous permettent de gérer les magasins de clés de sécurité pour vos espaces. Ces magasins peuvent contenir une collection de clés de sécurité. Ils vous permettent de récupérer facilement les dernières clés valides.

* [/users](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Users) : ces API vous permettent d’associer des utilisateurs à vos espaces, afin de localiser ces personnes en cas de besoin. 

* [/system](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/System) : ces API vous permettent de gérer les paramètres à l’échelle du système, tes que les types par défaut des espaces et des capteurs. 

* [/roleAssignments](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/RoleAssignments) : ces API vous permettent d’associer des rôles à des entités telles qu’ID d’utilisateur, ID de fonction définie par l’utilisateur, et ainsi de suite. Chaque attribution de rôle comprend l’ID de l’entité à associer, le type d’entité, l’ID du rôle à associer, l’ID de locataire et un chemin qui définit la limite supérieure de la ressource à laquelle l’entité peut accéder avec cette association. Pour plus d’informations, consultez [cet article](security-role-based-access-control.md).


## <a name="api-navigation"></a>Navigation parmi les API

Les API Digital Twins prennent en charge le filtrage et la navigation dans l’ensemble de votre graphe spatial avec les paramètres suivants :

- **spaceId** : l’API filtrera les résultats par l’ID d’espace spécifié. En outre, l’indicateur booléen **useParentSpace** s’applique aux API [/spaces](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces). Il indique que l’ID d’espace spécifié fait référence à l’espace parent plutôt qu’à l’espace actuel. 

- **minLevel** et **maxLevel** : les espaces racines sont considérés comme étant au niveau 1. Les espaces avec un espace parent au niveau *n* sont au niveau *n+1*. Avec ces valeurs définies, vous pouvez filtrer les résultats à des niveaux spécifiques. Une fois définies, il s’agit de valeurs inclusives. Les appareils, capteurs et autres objets sont considérés comme étant au même niveau que leur espace le plus proche. Pour obtenir tous les objets à un niveau donné, affectez la même valeur aux options **minLevel** et **maxLevel**.

- **minRelative** et **maxRelative** : quand ces filtres sont donnés, le niveau correspondant est relatif au niveau de l’ID d’espace spécifié :
   - Le niveau relatif *0* est au même niveau que l’ID d’espace spécifié
   - Le niveau relatif *1* représente les espaces au même niveau que les enfants de l’ID d’espace spécifié. Le niveau relatif *n* représente les espaces inférieurs à l’espace spécifié de *n* niveaux
   - Le niveau relatif *-1* représente les espaces au même niveau que l’espace parent de l’espace spécifié

- **traverse** : vous permet de traverser dans les deux sens à partir d’un ID d’espace donné, tel que spécifié par les valeurs suivantes.
   - **Aucun** : cette valeur par défaut filtre d’après l’ID d’espace donné.
   - **Down** : filtre d’après l’ID d’espace donné et ses descendants. 
   - **Up** : filtre d’après l’ID d’espace donné et ses ancêtres. 
   - **Span** : filtre une partie horizontale du graphe spatial, au même niveau que l’ID d’espace donné. Il faut pour cela que **minRelative** ou **maxRelative** ait la valeur true. 


### <a name="examples"></a>Exemples

La liste suivante présente quelques exemples de navigation parmi les API [/devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices). Notez que l’espace réservé `YOUR_MANAGEMENT_API_URL` fait référence à l’URI des API Digital Twins au format `https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/api/v1.0/`, où `YOUR_INSTANCE_NAME` est le nom de votre instance Azure Digital Twins et `YOUR_LOCATION` correspond à la région où votre instance est hébergée.

- `YOUR_MANAGEMENT_API_URL/devices?maxLevel=1` retourne tous les appareils connectés aux espaces racines.
- `YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4` retourne tous les appareils connectés aux espaces de niveaux 2, 3 ou 4.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId` retourne tous les appareils connectés directement à mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down` retourne tous les appareils connectés à mySpaceId ou l’un de ses descendants.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true` retourne tous les appareils connectés aux descendants de mySpaceId, à l’exclusion de mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true` retourne tous les appareils connectés à des enfants immédiats de mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Up&maxLevel=-1&maxRelative=true` retourne tous les appareils connectés à l’un des ancêtres de mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&maxLevel=5` retourne tous les appareils connectés aux descendants de mySpaceId situés à un niveau inférieur ou égal à 5.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true` retourne tous les appareils connectés aux espaces situés au même niveau que mySpaceId.


## <a name="odata-support"></a>Prise en charge d’OData

La plupart des API qui retournent des collections, comme un appel GET sur /spaces, prennent en charge le sous-ensemble suivant des options de requête système [OData](https://www.odata.org/getting-started/basic-tutorial/#queryData) génériques :  

* **$filter**
* **$orderby** 
* **$top**
* **$skip** : si vous souhaitez afficher l’ensemble de la collection, vous devez la demander en entier dans un même appel, puis exécuter la pagination dans votre application. 

> [!NOTE]
> Certaines options OData (telles que les options **$count**, **$expand** et **$search**) ne sont pas prises en charge actuellement.

### <a name="examples"></a>Exemples

La liste suivante décrit plusieurs requêtes avec une syntaxe OData valide :

- `YOUR_MANAGEMENT_API_URL/devices?$top=3&$orderby=Name desc`
- `YOUR_MANAGEMENT_API_URL/keystores?$filter=endswith(Description,'space')`
- `YOUR_MANAGEMENT_API_URL/devices?$filter=TypeId eq 2`
- `YOUR_MANAGEMENT_API_URL/resources?$filter=StatusId ne 1`
- `YOUR_MANAGEMENT_API_URL/users?$top=4&$filter=endswith(LastName,'k')&$orderby=LastName`
- `YOUR_MANAGEMENT_API_URL/spaces?$orderby=Name desc&$top=3&$filter=substringof('Floor',Name)`
 
## <a name="next-steps"></a>Étapes suivantes

Pour découvrir quelques modèles de requête d’API courants, consultez [Guide pratique pour interroger des API Azure Digital Twins pour des tâches courantes](./how-to-query-common-apis.md).

Pour en savoir plus sur vos points de terminaison d’API, consultez [Comment utiliser Digital Twins Swagger](./how-to-use-swagger.md).

Pour connaître la syntaxe OData et les opérateurs de comparaison disponibles, voir [Opérateurs de comparaison OData dans la Recherche cognitive Azure](../search/search-query-odata-comparison-operators.md).
