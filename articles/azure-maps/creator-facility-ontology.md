---
title: Ontologie des installations dans Microsoft Azure Maps Creator
description: L’ontologie des installations décrit les définitions de la classe de fonctionnalités pour Azure Maps Creator
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/14/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philMea
zone_pivot_groups: facility-ontology-schema
ms.openlocfilehash: 63e9702f8bebb449518002e18a824c65d81fab80
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112076453"
---
# <a name="facility-ontology"></a>Ontologie des installations

L’ontologie des installations définit la manière dont Azure Maps Creator stocke en interne les données d’installation dans un jeu de données Creator.  Outre la définition de la structure de données de l’installation interne, l’ontologie des installations est également exposée en externe via l’API WFS. Lorsque l’API WFS est utilisée pour interroger des données d’installation dans un jeu de données, le format de réponse est défini par l’ontologie fournie à ce jeu de données.

À un niveau élevé, l’ontologie des installations divise le jeu de données en classes de fonctionnalités. Toutes les classes de fonctionnalités partagent un ensemble de propriétés commun, telles que `ID` et `Geometry`.  Outre l’ensemble de propriétés commun, chaque classe de fonctionnalités définit un ensemble de propriétés. Chaque propriété est définie par son type de données et ses contraintes. Certaines classes de fonctionnalités ont des propriétés dépendantes d’autres classes de fonctionnalités. Les propriétés dépendantes sont évaluées par rapport à l’`ID` d’une autre classe de fonctionnalités.  

## <a name="changes-and-revisions"></a>Modifications et révisions

:::zone pivot="facility-ontology-v1"

Facility 1.0 contient des révisions pour les définitions de classe de fonctionnalités Facility pour les [services Azure Maps](https://aka.ms/AzureMaps).

:::zone-end

:::zone pivot="facility-ontology-v2"

Facility 2.0 contient des révisions pour les définitions de classe de fonctionnalités Facility pour les [services Azure Maps](https://aka.ms/AzureMaps).

:::zone-end

### <a name="major-changes"></a>Modifications majeures

:::zone pivot="facility-ontology-v1"

Correction des vérifications de validation de contrainte suivantes :

* Vérification de la validation des contraintes pour l’exclusivité de `isObstruction = true` *ou* la présence de `obstructionArea` pour les classes de fonctionnalités `lineElement` et `areaElement`.

* Vérification de la validation des contraintes pour l’exclusivité de `isRoutable = true` *ou* la présence de `routeThroughBehavior` pour la classe de fonctionnalités `category`.
:::zone-end

:::zone pivot="facility-ontology-v2"

* Ajout d’une classe de fonctionnalités de structure pour contenir les murs, les colonnes, etc.
* Nettoyage des attributs conçus pour enrichir les scénarios de routage. Le moteur de routage actuel ne les prend pas en charge.

:::zone-end

## <a name="unit"></a>unité

La classe de fonctionnalités `unit` définit une zone physique et sans chevauchement qui peut être occupée et parcourue par un agent de navigation. Une classe `unit` peut être un couloir, une salle, une cour, etc.

**Type de géométrie** : polygone

:::zone pivot="facility-ontology-v1"

| Propriété           | Type                        | Obligatoire | Description                                                  |
|--------------------|-----------------------------|----------|--------------------------------------------------------------|
|`originalId`        | string     |true      | ID dérivé des données du client. La longueur maximale autorisée est de 1 000 caractères.|
|`externalId`        | string     |true      | ID utilisé par le client pour associer la fonctionnalité à une autre fonctionnalité dans un jeu de données différent, par exemple dans une base de données interne. La longueur maximale autorisée est de 1 000 caractères.|
|`categoryId`        | [category.Id](#category)     |true      | ID d’une fonctionnalité [`category`](#category).|
|`isOpenArea`        | booléen (La valeur par défaut est `null`.)                    |false     | Indique si l’unité est une zone ouverte. Si la propriété est définie sur `true`, les [structures](#structure) n’entourent pas la limite d’unité et un agent de navigation peut entrer dans la classe `unit` sans avoir besoin d’un [`opening`](#opening). Par défaut, les unités sont entourées de barrières physiques et sont ouvertes uniquement lorsqu’une fonctionnalité d’ouverture est placée sur la limite de l’unité. Si des murs sont nécessaires dans une unité de zone ouverte, ils peuvent être représentés par [`lineElement`](#lineelement) ou [`areaElement`](#areaelement) avec une propriété `isObstruction` égale à `true`.|
|`navigableBy`        | enum ["pedestrian", "wheelchair", "machine", "bicycle", "automobile", "hiredAuto", "bus", "railcar", "emergency", "ferry", "boat"]  | false      |Indique les types d’agents de navigation pouvant traverser l’unité. Si la propriété n’est pas spécifiée, l’unité est supposée pouvoir être parcourue par n’importe quel agent de navigation. |
|`isRoutable`        | booléen (La valeur par défaut est `null`.)                      | false    |  Détermine si l’unité fait partie du graphique de routage. Si la propriété est définie sur `true`, l’unité peut être utilisée comme source/destination ou nœud intermédiaire dans l’expérience de routage. |
|`routeThroughBehavior`        | enum ["disallowed", "allowed", "preferred"] |  false     | Détermine si la navigation dans l’unité est autorisée. Si la propriété n’est pas spécifiée, elle hérite sa valeur de la fonctionnalité de catégorie référencée dans la propriété `categoryId`. Si elle est spécifiée, elle remplace la valeur donnée dans sa fonctionnalité de catégorie. |
|`nonPublic`        |  boolean| false       | Si elle est définie sur `true`, l’unité est navigable uniquement par des utilisateurs avec privilèges.  La valeur par défaut est `false`. |
| `levelId`          | [level.Id](#level)        | true     | ID d’une fonctionnalité de niveau. |
|`occupants`         |  tableau de [directoryInfo.Id](#directoryinfo) |    false |    ID des fonctionnalités [directoryInfo](#directoryinfo). Permettent de représenter au moins un occupant dans la fonctionnalité. |
|`addressId`         | [directoryInfo.Id](#directoryinfo) | true     | ID d’une fonctionnalité [directoryInfo](#directoryinfo). Permet de représenter l’adresse de la fonctionnalité.|
|`addressRoomNumber`         |  [directoryInfo.Id](#directoryinfo) | true     | Numéro de salle/unité/appartement/suite de l’unité.|
|`name` |    string |    false |    Nom de la fonctionnalité dans la langue locale. La longueur maximale autorisée est de 1 000 caractères. |
|`nameSubtitle` |    string |    false |   Sous-titre qui s’affiche sous la propriété `name` de la fonctionnalité. Peut servir à afficher le nom dans une autre langue, etc.  La longueur maximale autorisée est de 1 000 caractères.|
|`nameAlt` |    string |    false |   Autre nom utilisé pour la fonctionnalité. La longueur maximale autorisée est de 1 000 caractères. |
|`anchorPoint` |   [Point](/rest/api/maps/wfs/get-feature-preview#featuregeojson) | false | [Géométrie de point GeoJSON](/rest/api/maps/wfs/get-feature-preview#featuregeojson) qui représente la fonctionnalité comme un point. Peut servir à positionner l’étiquette de la fonctionnalité.|

:::zone-end

:::zone pivot="facility-ontology-v2"

| Propriété           | Type                        | Obligatoire | Description                                                  |
|--------------------|-----------------------------|----------|--------------------------------------------------------------|
|`originalId`        | string     |true      | ID dérivé des données du client. La longueur maximale autorisée est de 1 000 caractères.|
|`externalId`        | string     |true      | ID utilisé par le client pour associer la fonctionnalité à une autre fonctionnalité dans un jeu de données différent, par exemple dans une base de données interne. La longueur maximale autorisée est de 1 000 caractères.|
|`categoryId`        | [category.Id](#category)     |true      | ID d’une fonctionnalité [`category`](#category).|
|`isOpenArea`        | booléen (La valeur par défaut est `null`.)                    |false     | Indique si l’unité est une zone ouverte. Si la propriété est définie sur `true`, les [structures](#structure) n’entourent pas la limite d’unité et un agent de navigation peut entrer dans la classe `unit` sans avoir besoin d’un [`opening`](#opening). Par défaut, les unités sont entourées de barrières physiques et sont ouvertes uniquement lorsqu’une fonctionnalité d’ouverture est placée sur la limite de l’unité. Si des murs sont nécessaires dans une unité de zone ouverte, ils peuvent être représentés par [`lineElement`](#lineelement) ou [`areaElement`](#areaelement) avec une propriété `isObstruction` égale à `true`.|
|`isRoutable`        | booléen (La valeur par défaut est `null`.)                     | false    |  Détermine si l’unité fait partie du graphique de routage. Si la propriété est définie sur `true`, l’unité peut être utilisée comme source/destination ou nœud intermédiaire dans l’expérience de routage. |
| `levelId`          | [level.Id](#level)        | true     | ID d’une fonctionnalité de niveau. |
|`occupants`         |  tableau de [directoryInfo.Id](#directoryinfo) |    false |    ID des fonctionnalités [directoryInfo](#directoryinfo). Permettent de représenter au moins un occupant dans la fonctionnalité. |
|`addressId`         | [directoryInfo.Id](#directoryinfo) | true     | ID d’une fonctionnalité [directoryInfo](#directoryinfo). Permet de représenter l’adresse de la fonctionnalité.|
|`addressRoomNumber`         |  [directoryInfo.Id](#directoryinfo) | true     | Numéro de salle/unité/appartement/suite de l’unité.|
|`name` |    string |    false |    Nom de la fonctionnalité dans la langue locale.  La longueur maximale autorisée est de 1 000 caractères.|
|`nameSubtitle` |    string |    false |   Sous-titre qui s’affiche sous la propriété `name` de la fonctionnalité. Peut servir à afficher le nom dans une autre langue, etc.  La longueur maximale autorisée est de 1 000 caractères.|
|`nameAlt` |    string |    false |   Autre nom utilisé pour la fonctionnalité.  La longueur maximale autorisée est de 1 000 caractères.|
|`anchorPoint` |   [Point](/rest/api/maps/wfs/get-feature-preview#featuregeojson) | false | [Géométrie de point GeoJSON](/rest/api/maps/wfs/get-feature-preview#featuregeojson) qui représente la fonctionnalité comme un point. Peut servir à positionner l’étiquette de la fonctionnalité.|

:::zone-end

:::zone pivot="facility-ontology-v2"

## <a name="structure"></a>structure

La classe de fonctionnalités `structure` définit une zone physique et non superposée qui ne peut pas être parcourue. Il peut s’agir d’un mur, d’une colonne, etc.

**Type de géométrie** : polygone

| Propriété  | Type | Obligatoire | Description |
|-----------|------|----------|-------------|
|`originalId`        | string     |true      | ID dérivé des données du client. La longueur maximale autorisée est de 1 000 caractères.|
|`externalId`        | string     |true      | ID utilisé par le client pour associer la fonctionnalité à une autre fonctionnalité dans un jeu de données différent, par exemple dans une base de données interne. La longueur maximale autorisée est de 1 000 caractères.|
|`categoryId`        | [category.Id](#category)      |true      | ID d’une fonctionnalité [`category`](#category).|
| `levelId`          |  [level.Id](#level)            | true     | ID d’une fonctionnalité [`level`](#level). |
|`name` |    string |    false |    Nom de la fonctionnalité dans la langue locale. La longueur maximale autorisée est de 1 000 caractères. |
|`nameSubtitle` |    string |    false |   Sous-titre qui s’affiche sous la propriété `name` de la fonctionnalité. Peut servir à afficher le nom dans une autre langue, etc. La longueur maximale autorisée est de 1 000 caractères. |
|`nameAlt` |    string |    false |   Autre nom utilisé pour la fonctionnalité.  La longueur maximale autorisée est de 1 000 caractères.|
|`anchorPoint` |   [Point](/rest/api/maps/wfs/get-feature-preview#featuregeojson) | false | [Géométrie de point GeoJSON](/rest/api/maps/wfs/get-feature-preview#featuregeojson) qui représente la fonctionnalité comme un point. Peut servir à positionner l’étiquette de la fonctionnalité.|

:::zone-end

## <a name="zone"></a>zone

La classe de fonctionnalités `zone` définit une zone virtuelle, telle qu’une zone Wi-Fi ou un point de rassemblement d’urgence. Les zones peuvent être utilisées comme destinations, mais ne sont pas destinées au trafic.

**Type de géométrie** : polygone

| Propriété  | Type | Obligatoire | Description |
|-----------|------|----------|-------------|
|`originalId`        | string     |true      | ID dérivé des données du client. La longueur maximale autorisée est de 1 000 caractères.|
|`externalId`        | string     |true      | ID utilisé par le client pour associer la fonctionnalité à une autre fonctionnalité dans un jeu de données différent, par exemple dans une base de données interne. La longueur maximale autorisée est de 1 000 caractères.|
|`categoryId`        | [category.Id](#category)      |true      | ID d’une fonctionnalité [`category`](#category).|
| `setId`          | string         | true     |Obligatoire pour les fonctionnalités de zone qui représentent des zones à plusieurs niveaux. `setId` est l’ID unique d’une zone qui s’étend sur plusieurs niveaux. `setId` permet à une zone avec une couverture variable sur différents étages d’être représentée par une géométrie différente sur différents niveaux. `setId` peut être n’importe quelle chaîne et respecte la casse. Recommandation : `setId` doit être un GUID.  La longueur maximale autorisée est de 1 000 caractères.|
| `levelId`          |  [level.Id](#level)             | true     | ID d’une fonctionnalité [`level`](#level). |
|`name` |    string |    false |    Nom de la fonctionnalité dans la langue locale.  La longueur maximale autorisée est de 1 000 caractères.|
|`nameSubtitle` |    string |    false |   Sous-titre qui s’affiche sous la propriété `name` de la fonctionnalité. Peut servir à afficher le nom dans une autre langue, etc.  La longueur maximale autorisée est de 1 000 caractères.|
|`nameAlt` |    string |    false |   Autre nom utilisé pour la fonctionnalité. La longueur maximale autorisée est de 1 000 caractères. |
|`anchorPoint` |  [Point](/rest/api/maps/wfs/get-feature-preview#featuregeojson) | false | [Géométrie de point GeoJSON](/rest/api/maps/wfs/get-feature-preview#featuregeojson) qui représente la fonctionnalité comme un point. Peut servir à positionner l’étiquette de la fonctionnalité.|

## <a name="level"></a>niveau

La fonctionnalité de classe `level` définit une zone d’un immeuble à une hauteur définie. Par exemple, le sol d’un bâtiment, qui contient un ensemble de fonctionnalités, telles que [`units`](#unit).  

**Type de géométrie** : à plusieurs polygones

| Propriété  | Type | Obligatoire | Description |
|-----------|------|----------|-------------|
|`originalId`        | string     |true      | ID dérivé des données du client. La longueur maximale autorisée est de 1 000 caractères.|
|`externalId`        | string     |true      | ID utilisé par le client pour associer la fonctionnalité à une autre fonctionnalité dans un jeu de données différent, par exemple dans une base de données interne. La longueur maximale autorisée est de 1 000 caractères.|
|`categoryId`        | [category.Id](#category)    |true      | ID d’une fonctionnalité [`category`](#category).|
| `ordinal`          | entier        | true     | Numéro de niveau. Utilisé par la fonctionnalité [`verticalPenetration`](#verticalpenetration) pour déterminer l’ordre relatif des sols pour faciliter le sens du déplacement. La pratique générale consiste à commencer par 0 pour le sol. Ajoutez +1 pour chaque étage vers le haut et -1 pour chaque étage vers le bas. Cette propriété peut être modélisée avec n’importe quel nombre, à condition que les étages physiques les plus élevés soient représentés par des valeurs ordinales plus élevées. |
| `abbreviatedName`          | string        | false     | Nom de niveau abrégé à quatre caractères, comme celui qui se trouve sur le bouton d’un ascenseur.  La longueur maximale autorisée est de 1 000 caractères.|
| `heightAboveFacilityAnchor`          | double         | false     | Distance verticale du sol du niveau au-dessus de [`facility.anchorHeightAboveSeaLevel`](#facility), en mètres. |
| `verticalExtent`          | double         | false     | Étendue verticale du niveau, en mètres. Si cette propriété n’est pas spécifiée, sa valeur par défaut est [`facility.defaultLevelVerticalExtent`](#facility).|
|`name` |    string |    false |    Nom de la fonctionnalité dans la langue locale.  La longueur maximale autorisée est de 1 000 caractères.|
|`nameSubtitle` |    string |    false |   Sous-titre qui s’affiche sous la propriété `name` de la fonctionnalité. Peut servir à afficher le nom dans une autre langue, etc.  La longueur maximale autorisée est de 1 000 caractères.|
|`nameAlt` |    string |    false |   Autre nom utilisé pour la fonctionnalité.  La longueur maximale autorisée est de 1 000 caractères.|
|`anchorPoint` |   [Point](/rest/api/maps/wfs/get-feature-preview#featuregeojson) | false | [Géométrie de point GeoJSON](/rest/api/maps/wfs/get-feature-preview#featuregeojson) qui représente la fonctionnalité comme un point. Peut servir à positionner l’étiquette de la fonctionnalité.|

## <a name="facility"></a>installation

La classe de fonctionnalités `facility` définit la zone du site, l’encombrement du bâtiment, etc.

**Type de géométrie** : à plusieurs polygones

| Propriété  | Type | Obligatoire | Description |
|-----------|------|----------|-------------|
|`originalId`        | string     |true      | ID dérivé des données du client. La longueur maximale autorisée est de 1 000 caractères.|
|`externalId`        | string     |true      | ID utilisé par le client pour associer la fonctionnalité à une autre fonctionnalité dans un jeu de données différent, par exemple dans une base de données interne. La longueur maximale autorisée est de 1 000 caractères.|
|`categoryId`        | [category.Id](#category)      |true      | ID d’une fonctionnalité [`category`](#category).|
|`occupants`         | tableau de [directoryInfo.Id](#directoryinfo) |    false |    ID des fonctionnalités [directoryInfo](#directoryinfo). Permettent de représenter au moins un occupant dans la fonctionnalité. |
|`addressId`         | [directoryInfo.Id](#directoryinfo)  | true     | ID d’une fonctionnalité [directoryInfo](#directoryinfo). Permet de représenter l’adresse de la fonctionnalité.|
|`addressRoomNumber`         |  [directoryInfo.Id](#directoryinfo)| true     | Numéro de salle/unité/appartement/suite de l’unité.|
|`name` |    string |    false |    Nom de la fonctionnalité dans la langue locale. La longueur maximale autorisée est de 1 000 caractères. |
|`nameSubtitle` |    string |    false |   Sous-titre qui s’affiche sous la propriété `name` de la fonctionnalité. Peut servir à afficher le nom dans une autre langue, etc. La longueur maximale autorisée est de 1 000 caractères. |
|`nameAlt` |    string |    false |   Autre nom utilisé pour la fonctionnalité.  La longueur maximale autorisée est de 1 000 caractères.|
|`anchorPoint` |  [Point](/rest/api/maps/wfs/get-feature-preview#featuregeojson) | false | [Géométrie de point GeoJSON](/rest/api/maps/wfs/get-feature-preview#featuregeojson) qui représente la fonctionnalité comme un point. Peut servir à positionner l’étiquette de la fonctionnalité.|
|`anchorHeightAboveSeaLevel` |  double | false | Hauteur du point d’ancrage au-dessus du niveau de la mer, en mètres. Le niveau de la mer est défini par EGM 2008.|
|`defaultLevelVerticalExtent` |  double| false | Valeur par défaut de l’étendue verticale des niveaux, en mètres.|

## <a name="verticalpenetration"></a>verticalPenetration

La fonctionnalité de classe `verticalPenetration` définit une zone qui, lorsqu’elle est utilisée dans un ensemble, représente une méthode de navigation verticale entre les niveaux. Elle peut être utilisée pour modéliser des escaliers, des ascenseurs, etc. La géométrie peut chevaucher des unités et d’autres fonctionnalités de pénétration verticale.

**Type de géométrie** : polygone

:::zone pivot="facility-ontology-v1"

| Propriété  | Type | Obligatoire | Description |
|-----------|------|----------|-------------|
|`originalId`        | string     |true      | ID dérivé des données du client. La longueur maximale autorisée est de 1 000 caractères.|
|`externalId`        | string     |true      | ID utilisé par le client pour associer la fonctionnalité à une autre fonctionnalité dans un jeu de données différent, par exemple dans une base de données interne. La longueur maximale autorisée est de 1 000 caractères.|
|`categoryId`        | [category.Id](#category)      |true      | ID d’une fonctionnalité [`category`](#category).|
| `setId`          | string       | true     | Les fonctionnalités de pénétration verticale doivent être utilisées dans des ensembles pour connecter plusieurs niveaux. Les fonctionnalités de pénétration verticale dans le même ensemble sont considérées comme identiques. `setId` peut être n’importe quelle chaîne et respecte la casse. L’utilisation d’un GUID comme `setId` est recommandée.  La longueur maximale autorisée est de 1 000 caractères.|
| `levelId`          | [level.Id](#level)         | true     | ID d’une fonctionnalité de niveau. |
|`direction`         |  string enum ["Both", "lowToHigh", "highToLow", "Closed"]| false     | Sens du déplacement autorisé sur cette fonctionnalité. L’attribut ordinal sur la fonctionnalité [`level`](#level) permet de déterminer le sens vers le haut ou le bas.|
|`navigableBy`        | enum ["pedestrian", "wheelchair", "machine", "bicycle", "automobile", "hiredAuto", "bus", "railcar", "emergency", "ferry", "boat"]  | false      |Indique les types d’agents de navigation pouvant traverser l’unité. Si la propriété n’est pas spécifiée, l’unité peut être parcourue par n’importe quel agent de navigation. |
|`nonPublic`        |  boolean| false       | Si elle est définie sur `true`, l’unité est navigable uniquement par des utilisateurs avec privilèges.  La valeur par défaut est `false`. |
|`name` |    string |    false |    Nom de la fonctionnalité dans la langue locale.  La longueur maximale autorisée est de 1 000 caractères.|
|`nameSubtitle` |    string |    false |   Sous-titre qui s’affiche sous la propriété `name` de la fonctionnalité. Peut servir à afficher le nom dans une autre langue, etc.  La longueur maximale autorisée est de 1 000 caractères.|
|`nameAlt` |    string |    false |   Autre nom utilisé pour la fonctionnalité. La longueur maximale autorisée est de 1 000 caractères. |
|`anchorPoint` |  [Point](/rest/api/maps/wfs/get-feature-preview#featuregeojson) | false | [Géométrie de point GeoJSON](/rest/api/maps/wfs/get-feature-preview#featuregeojson) qui représente la fonctionnalité comme un point. Peut servir à positionner l’étiquette de la fonctionnalité.|

:::zone-end

:::zone pivot="facility-ontology-v2"

| Propriété  | Type | Obligatoire | Description |
|-----------|------|----------|-------------|
|`originalId`        | string     |true      | ID dérivé des données du client. La longueur maximale autorisée est de 1 000 caractères.|
|`externalId`        | string     |true      | ID utilisé par le client pour associer la fonctionnalité à une autre fonctionnalité dans un jeu de données différent, par exemple dans une base de données interne. La longueur maximale autorisée est de 1 000 caractères.|
|`categoryId`        | [category.Id](#category)      |true      | ID d’une fonctionnalité [`category`](#category).|
| `setId`          | string       | true     | Les fonctionnalités de pénétration verticale doivent être utilisées dans des ensembles pour connecter plusieurs niveaux. Les fonctionnalités de pénétration verticale dans le même ensemble sont connectées. `setId` peut être n’importe quelle chaîne et respecte la casse. L’utilisation d’un GUID comme `setId` est recommandée. La longueur maximale autorisée est de 1 000 caractères. |
| `levelId`          | [level.Id](#level)         | true     | ID d’une fonctionnalité de niveau. |
|`direction`         |  string enum ["Both", "lowToHigh", "highToLow", "Closed"]| false     | Sens du déplacement autorisé sur cette fonctionnalité. L’attribut ordinal sur la fonctionnalité [`level`](#level) permet de déterminer le sens vers le haut ou le bas.|
|`name` |    string |    false |    Nom de la fonctionnalité dans la langue locale.  La longueur maximale autorisée est de 1 000 caractères.|
|`nameSubtitle` |    string |    false |   Sous-titre qui s’affiche sous la propriété `name` de la fonctionnalité. Peut servir à afficher le nom dans une autre langue, etc.  La longueur maximale autorisée est de 1 000 caractères.|
|`nameAlt` |    string |    false |   Autre nom utilisé pour la fonctionnalité. La longueur maximale autorisée est de 1 000 caractères. |
|`anchorPoint` |  [Point](/rest/api/maps/wfs/get-feature-preview#featuregeojson) | false | [Géométrie de point GeoJSON](/rest/api/maps/wfs/get-feature-preview#featuregeojson) qui représente la fonctionnalité comme un point. Peut servir à positionner l’étiquette de la fonctionnalité.|

:::zone-end

## <a name="opening"></a>ouverture

La fonctionnalité de classe `opening` définit une limite parcourue entre deux unités, ou une propriété `unit` et une propriété `verticalPenetration`.

**Type de géométrie** : LineString

:::zone pivot="facility-ontology-v1"

| Propriété  | Type | Obligatoire | Description |
|-----------|------|----------|-------------|
|`originalId`        | string     |true      | ID dérivé des données du client. La longueur maximale autorisée est de 1 000 caractères.|
|`externalId`        | string     |true      | ID utilisé par le client pour associer la fonctionnalité à une autre fonctionnalité dans un jeu de données différent, par exemple dans une base de données interne. La longueur maximale autorisée est de 1 000 caractères.|
|`categoryId`        |[category.Id](#category)     |true      | ID d’une fonctionnalité de catégorie.|
| `levelId`          | [level.Id](#level)        | true     | ID d’une fonctionnalité de niveau. |
| `isConnectedToVerticalPenetration` | boolean | false | Indique si cette fonctionnalité est connectée à une fonctionnalité `verticalPenetration` de l’un de ses côtés. La valeur par défaut est `false`. |
|`navigableBy`        | enum ["pedestrian", "wheelchair", "machine", "bicycle", "automobile", "hiredAuto", "bus", "railcar", "emergency", "ferry", "boat"]  | false      |Indique les types d’agents de navigation pouvant traverser l’unité. Si la propriété n’est pas spécifiée, l’unité peut être parcourue par n’importe quel agent de navigation. |
| `accessRightToLeft`| enum [ "prohibited", "digitalKey", "physicalKey", "keyPad", "guard", "ticket", "fingerprint", "retina", "voice", "face", "palm", "iris", "signature", "handGeometry", "time", "ticketChecker", "other"] | false | Méthode d’accès lors du passage par l’ouverture de droite à gauche. La gauche et la droite sont déterminées par les vertex de la géométrie de la fonctionnalité, en cas de position sur le premier sommet et face au deuxième vertex. L’omission de cette propriété signifie qu’il n’existe aucune restriction d’accès.|
| `accessLeftToRight`| enum [ "prohibited", "digitalKey", "physicalKey", "keyPad", "guard", "ticket", "fingerprint", "retina", "voice", "face", "palm", "iris", "signature", "handGeometry", "time", "ticketChecker", "other"] | false | Méthode d’accès lors du passage par l’ouverture de gauche à droite. La gauche et la droite sont déterminées par les vertex de la géométrie de la fonctionnalité, en cas de position sur le premier sommet et face au deuxième vertex. L’omission de cette propriété signifie qu’il n’existe aucune restriction d’accès.|
| `isEmergency` | boolean | false | Si `true`, l’ouverture est navigable uniquement pendant les urgences. La valeur par défaut est `false` |
|`anchorPoint` | [Point](/rest/api/maps/wfs/get-feature-preview#featuregeojson) | false | [Géométrie de point GeoJSON](/rest/api/maps/wfs/get-feature-preview#featuregeojson) qui représente la fonctionnalité comme un point. Peut servir à positionner l’étiquette de la fonctionnalité.|

:::zone-end

:::zone pivot="facility-ontology-v2"

| Propriété  | Type | Obligatoire | Description |
|-----------|------|----------|-------------|
|`originalId`        | string     |true      | ID dérivé des données du client. La longueur maximale autorisée est de 1 000 caractères.|
|`externalId`        | string     |true      | ID utilisé par le client pour associer la fonctionnalité à une autre fonctionnalité dans un jeu de données différent, par exemple dans une base de données interne. La longueur maximale autorisée est de 1 000 caractères.|
|`categoryId`        |[category.Id](#category)     |true      | ID d’une fonctionnalité de catégorie.|
| `levelId`          | [level.Id](#level)        | true     | ID d’une fonctionnalité de niveau. |
|`anchorPoint` | [Point](/rest/api/maps/wfs/get-feature-preview#featuregeojson) | false | [Géométrie de point GeoJSON](/rest/api/maps/wfs/get-feature-preview#featuregeojson) qui représente la fonctionnalité comme un point. Peut servir à positionner l’étiquette de la fonctionnalité.|

:::zone-end

## <a name="directoryinfo"></a>directoryInfo

La fonctionnalité de classe d’objet `directoryInfo` définit le nom, l’adresse, le numéro de téléphone, le site web et les heures de fonctionnement d’une unité, d’une installation ou d’un occupant d’une unité ou d’une installation.

**Type de géométrie** : aucun

| Propriété  | Type | Obligatoire | Description |
|-----------|------|----------|-------------|
|`originalId`        | string     |true      | ID dérivé des données du client. La longueur maximale autorisée est de 1 000 caractères.|
|`externalId`        | string     |true      | ID utilisé par le client pour associer la fonctionnalité à une autre fonctionnalité dans un jeu de données différent, par exemple dans une base de données interne. La longueur maximale autorisée est de 1 000 caractères.|
|`streetAddress`        |string    |false    |Partie rue de l’adresse.  La longueur maximale autorisée est de 1 000 caractères. |
|`unit`        |string    |false    |Partie numéro d’unité de l’adresse.  La longueur maximale autorisée est de 1 000 caractères. |
|`locality`|    string|    false    |Localité de l’adresse. Par exemple : ville, municipalité, village. La longueur maximale autorisée est de 1 000 caractères.|
|`adminDivisions`|    string|    false    |Partie administrative de l’adresse, du plus petit au plus grand (comté, état, pays). Par exemple : ["King", "Washington", "USA" ] ou ["West Godavari", "Andhra Pradesh", "IND" ]. La longueur maximale autorisée est de 1 000 caractères.|
|`postalCode`|    string |    false    |Partie code postal de l’adresse. La longueur maximale autorisée est de 1 000 caractères.|
|`name` |    string |    false |    Nom de la fonctionnalité dans la langue locale.  La longueur maximale autorisée est de 1 000 caractères.|
|`nameSubtitle` |    string |    false |   Sous-titre qui s’affiche sous la propriété `name` de la fonctionnalité. Peut servir à afficher le nom dans une autre langue, etc. La longueur maximale autorisée est de 1 000 caractères. |
|`nameAlt` |    string |    false |   Autre nom utilisé pour la fonctionnalité. La longueur maximale autorisée est de 1 000 caractères. |
|`phoneNumber` |    string |    false |    Numéro de téléphone. |
|`website` |    string |    false |  URL du site web. La longueur maximale autorisée est de 1 000 caractères. |
|`hoursOfOperation` |    string |    false |   Heures de fonctionnement sous forme de texte après la [spécification d’Open Street Map](https://wiki.openstreetmap.org/wiki/Key:opening_hours/specification). La longueur maximale autorisée est de 1 000 caractères. |

## <a name="pointelement"></a>pointElement

La fonctionnalité de classe `pointElement` définit une fonctionnalité de point dans une unité, telle qu’une trousse de premier secours ou une tête d’extincteur.

**Type de géométrie** : à plusieurs points

| Propriété  | Type | Obligatoire | Description |
|-----------|------|----------|-------------|
|`originalId`        | string     |true      | ID dérivé des données du client. La longueur maximale autorisée est de 1 000 caractères.|
|`externalId`        | string     |true      | ID utilisé par le client pour associer la fonctionnalité à une autre fonctionnalité dans un jeu de données différent, par exemple dans une base de données interne. La longueur maximale autorisée est de 1 000 caractères.|
|`categoryId`        |[category.Id](#category)      |true      | ID d’une fonctionnalité [`category`](#category).|
| `unitId`          | string     | true     | ID d’une fonctionnalité [`unit`](#unit) qui contient cette fonctionnalité.  La longueur maximale autorisée est de 1 000 caractères.|
| `isObstruction`          | booléen (La valeur par défaut est `null`.) | false     | Si cette propriété est définie sur `true`, cette fonctionnalité représente une obstruction à éviter lors du routage via la fonctionnalité d’unité conteneur. |
|`name` |    string |    false |    Nom de la fonctionnalité dans la langue locale.  La longueur maximale autorisée est de 1 000 caractères.|
|`nameSubtitle` |    string |    false |   Sous-titre qui s’affiche sous la propriété `name` de la fonctionnalité. Peut servir à afficher le nom dans une autre langue, etc. La longueur maximale autorisée est de 1 000 caractères. |
|`nameAlt` |    string |    false |   Autre nom utilisé pour la fonctionnalité.  La longueur maximale autorisée est de 1 000 caractères.|

## <a name="lineelement"></a>lineElement

La fonctionnalité de classe `lineElement` définit une fonctionnalité de ligne dans une unité, telle qu’un mur ou une fenêtre de division.

**Type de géométrie** : LinearMultiString

| Propriété  | Type | Obligatoire | Description |
|-----------|------|----------|-------------|
|`originalId`        | string     |true      | ID dérivé des données du client. La longueur maximale autorisée est de 1 000 caractères.|
|`externalId`        | string     |true      | ID utilisé par le client pour associer la fonctionnalité à une autre fonctionnalité dans un jeu de données différent, par exemple dans une base de données interne. La longueur maximale autorisée est de 1 000 caractères.|
|`categoryId`        |[category.Id](#category)      |true      | ID d’une fonctionnalité [`category`](#category).|
| `unitId`          | string     | true     | ID d’une fonctionnalité [`unit`](#unit) qui contient cette fonctionnalité. La longueur maximale autorisée est de 1 000 caractères. |
| `isObstruction`          | booléen (La valeur par défaut est `null`.)| false     | Si cette propriété est définie sur `true`, cette fonctionnalité représente une obstruction à éviter lors du routage via la fonctionnalité d’unité conteneur. |
|`name` |    string |    false |    Nom de la fonctionnalité dans la langue locale. La longueur maximale autorisée est de 1 000 caractères. |
|`nameSubtitle` |    string |    false |   Sous-titre qui s’affiche sous la propriété `name` de la fonctionnalité. Peut servir à afficher le nom dans une autre langue, etc. La longueur maximale autorisée est de 1 000 caractères. |
|`nameAlt` |    string |    false |   Autre nom utilisé pour la fonctionnalité. La longueur maximale autorisée est de 1 000 caractères. |
|`anchorPoint` |  [Point](/rest/api/maps/wfs/get-feature-preview#featuregeojson) | false | [Géométrie de point GeoJSON](/rest/api/maps/wfs/get-feature-preview#featuregeojson) qui représente la fonctionnalité comme un point. Peut servir à positionner l’étiquette de la fonctionnalité.|
|`obstructionArea` |   [Polygon](/rest/api/maps/wfs/get-feature-preview#featuregeojson)| false | Géométrie simplifiée (lorsque la géométrie de ligne est compliquée) de la fonctionnalité qui doit être évitée lors du routage. Nécessite la définition de `isObstruction` sur true.|

## <a name="areaelement"></a>areaElement

La fonctionnalité de classe `areaElement` définit une fonctionnalité de polygone dans une unité, telle qu’une zone ouverte sur ce qui suit, un obstacle comme une île dans une unité.

**Type de géométrie** : à plusieurs polygones

| Propriété  | Type | Obligatoire | Description |
|-----------|------|----------|-------------|
|`originalId`        | string     |true      | ID dérivé des données du client. La longueur maximale autorisée est de 1 000 caractères.|
|`externalId`        | string     |true      | ID utilisé par le client pour associer la fonctionnalité à une autre fonctionnalité dans un jeu de données différent, par exemple dans une base de données interne. La longueur maximale autorisée est de 1 000 caractères.|
|`categoryId`        |[category.Id](#category)      |true      | ID d’une fonctionnalité [`category`](#category).|
| `unitId`          | string     | true     | ID d’une fonctionnalité [`unit`](#unit) qui contient cette fonctionnalité. La longueur maximale autorisée est de 1 000 caractères. |
| `isObstruction`          | boolean | false     | Si cette propriété est définie sur `true`, cette fonctionnalité représente une obstruction à éviter lors du routage via la fonctionnalité d’unité conteneur. |
|`obstructionArea` |  geometry: ["Polygon","MultiPolygon" ]| false | Géométrie simplifiée (lorsque la géométrie de ligne est compliquée) de la fonctionnalité qui doit être évitée lors du routage. Nécessite la définition de `isObstruction` sur true.|
|`name` |    string |    false |    Nom de la fonctionnalité dans la langue locale. La longueur maximale autorisée est de 1 000 caractères. |
|`nameSubtitle` |    string |    false |   Sous-titre qui s’affiche sous la propriété `name` de la fonctionnalité. Peut servir à afficher le nom dans une autre langue, etc.  La longueur maximale autorisée est de 1 000 caractères.|
|`nameAlt` |    string |    false |   Autre nom utilisé pour la fonctionnalité.  La longueur maximale autorisée est de 1 000 caractères.|
|`anchorPoint` |  [Point](/rest/api/maps/wfs/get-feature-preview#featuregeojson) | false | [Géométrie de point GeoJSON](/rest/api/maps/wfs/get-feature-preview#featuregeojson) qui représente la fonctionnalité comme un point. Peut servir à positionner l’étiquette de la fonctionnalité.|

## <a name="category"></a>catégorie

La fonctionnalité de classe `category` définit des noms de catégorie. Par exemple : "room.conference".

**Type de géométrie** : aucun

:::zone pivot="facility-ontology-v1"

| Propriété  | Type | Obligatoire | Description |
|-----------|------|----------|-------------|
|`originalId`        | string     |true      | ID d’origine de la catégorie dérivé des données du client. La longueur maximale autorisée est de 1 000 caractères.|
|`externalId`        | string     |true      | ID utilisé par le client pour associer la catégorie à une autre catégorie dans un jeu de données différent, par exemple dans une base de données interne. La longueur maximale autorisée est de 1 000 caractères.|
|`name` |    string |    true |   Nom de la catégorie. Suggestion : utiliser « . » pour représenter la hiérarchie des catégories. Par exemple : "room.conference", "room.privateoffice". La longueur maximale autorisée est de 1 000 caractères. |
| `routeThroughBehavior` | boolean | false | Détermine si une fonctionnalité peut être utilisée pour le trafic.|
|`isRoutable`        | booléen (La valeur par défaut est `null`.)                  | false    |  Détermine si une fonctionnalité doit faire partie du graphique de routage. Si la propriété est définie sur `true`, l’unité peut être utilisée comme source/destination ou nœud intermédiaire dans l’expérience de routage. |

:::zone-end

:::zone pivot="facility-ontology-v2"

| Propriété  | Type | Obligatoire | Description |
|-----------|------|----------|-------------|
|`originalId`        | string     |true      | ID d’origine de la catégorie dérivé des données du client.  La longueur maximale autorisée est de 1 000 caractères.|
|`externalId`        | string     |true      | ID utilisé par le client pour associer la catégorie à une autre catégorie dans un jeu de données différent, par exemple dans une base de données interne. La longueur maximale autorisée est de 1 000 caractères.|
|`name` |    string |    true |   Nom de la catégorie. Suggestion : utiliser « . » pour représenter la hiérarchie des catégories. Par exemple : "room.conference", "room.privateoffice". La longueur maximale autorisée est de 1 000 caractères. |

:::zone-end
