---
title: Traitement des données et fonctions définies par l’utilisateur avec Azure Digital Twins | Microsoft Docs
description: Présentation du traitement des données, des détecteurs et des fonctions définies par l’utilisateur avec Azure Digital Twins
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: alinast
ms.openlocfilehash: b7561848ffd0158e22e97530774112dcee2a9864
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323721"
---
# <a name="data-processing-and-user-defined-functions"></a>Traitement des données et fonctions définies par l’utilisateur

Azure Digital Twins propose des fonctionnalités de calcul avancées. Les développeurs peuvent définir et exécuter des fonctions personnalisées sur des messages de télémétrie entrants, afin d’envoyer des événements à des points de terminaison prédéfinis.

## <a name="data-processing-flow"></a>Flux de traitement des données

Une fois que les appareils ont envoyé des données de télémétrie à Digital Twins, les développeurs peuvent traiter ces données en quatre phases : _validation_, _détection_, _calcul_ et _distribution_ :

![Flux de traitement des données Digital Twins][1]

1. La phase _Validation_ convertit le message de télémétrie entrant en un format [`data transfer object`](https://en.wikipedia.org/wiki/Data_transfer_object) généralement bien compris. Cette phase exécute également la validation de l’appareil et du capteur.
1. La phase _Détection_ recherche la ou les fonctions définies par l’utilisateur qui doivent être exécutées. Les détecteurs prédéfinis recherchent les fonctions définies par l’utilisateur en se basant sur les informations relatives à l’appareil, au capteur et à l’espace qui sont issues du message de télémétrie entrant.
1. La phase _Calcul_ exécute la ou les fonctions définies par l’utilisateur qui ont été détectées dans la phase précédente. Ces fonctions peuvent lire et mettre à jour les valeurs calculées présentes sur les nœuds d’un graphe spatial, et peuvent émettre des notifications personnalisées.
1. La phase _Distribution_ achemine toutes les notifications personnalisées, de la phase de calcul vers les points de terminaison définis dans le graphe.

## <a name="data-processing-objects"></a>Objets de traitement des données

Dans Azure Digital Twins, le traitement des données consiste à définir les trois objets suivants : les _détecteurs_, les _fonctions définies par l’utilisateur_ et les _attributions de rôles_ :

![Flux de traitement des données Digital Twins][2]

### <a name="matchers"></a>Détecteurs

Les _détecteurs_ définissent un ensemble de conditions qui évaluent les actions qui auront lieu en fonction des données de télémétrie entrantes du capteur. Ces conditions peuvent inclure les propriétés du capteur, l’appareil parent du capteur et l’espace parent du capteur. Les conditions sont exprimées sous forme de comparaisons à un [chemin JSON](http://jsonpath.com/), comme dans l’exemple ci-dessous :

- Tous les capteurs dont le type de données est `Temperature`.
- Avoir un port qui comprend `01`.
- Qui appartient aux appareils dont la clé de propriété étendue `Manufacturer` a la valeur `GoodCorp`.
- Qui appartient aux espaces de type `Venue`.
- Qui sont descendants du parent `SpaceId` `DE8F06CA-1138-4AD7-89F4-F782CC6F69FD`.

```JSON
{
  "SpaceId": "DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "Name": "My custom matcher",
  "Description": "All sensors of datatype Temperature with 01 in their port that belong to devices with the extended property key Manufacturer set to the value GoodCorp and that belong to spaces of type Venue that are somewhere below space Id DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "Conditions": [
    {
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    },
    {
      "target": "Sensor",
      "path": "$.port",
      "value": "01",
      "comparison": "Contains"
    },
    {
      "target": "SensorDevice",
      "path": "$.properties[?(@.name == 'Manufacturer')].value",
      "value": "\"GoodCorp\"",
      "comparison": "Equals"
    },
    {
      "target": "SensorSpace",
      "path": "$.type",
      "value": "\"Venue\"",
      "comparison": "Equals"
    }
  ]
}
```

> [!IMPORTANT]
> - Les chemins JSON respectent la casse.
> - La charge utile JSON est identique à celle retournée par :
>   - `/sensors/{id}?includes=properties,types` pour le capteur.
>   - `/devices/{id}?includes=properties,types,sensors,sensorsproperties,sensorstypes` pour l’appareil parent du capteur.
>   - `/spaces/{id}?includes=properties,types,location,timezone` pour l’espace parent du capteur.
> - Les comparaisons ne respectent pas la casse.

### <a name="user-defined-functions"></a>Fonctions définies par l’utilisateur

Une _fonction définie par l’utilisateur_, ou _UDF_, est une fonction personnalisée qui est exécutée dans un environnement isolé au sein d’Azure Digital Twins. Les fonctions définies par l’utilisateur ont accès au message brut de télémétrie du capteur tel qu’il a été reçu, ainsi qu’au graphe spatial et au service de distribution. Une fois que la fonction définie par l’utilisateur est inscrite dans le graphe, vous devez créer un détecteur (détaillé plus haut) pour spécifier à quel moment exécuter la fonction. Lorsque Digital Twins reçoit de nouvelles données de télémétrie provenant d’un capteur donné, la fonction définie par l’utilisateur trouvée peut calculer une moyenne mobile à partir des dernières données de capteur, par exemple.

Ces fonctions peuvent être écrites en JavaScript et permettre aux développeurs d’exécuter des extraits de code personnalisés sur des messages de télémétrie de capteur. Il existe également des méthodes d’assistance permettant d’interagir avec le graphe dans l’environnement d’exécution défini par l’utilisateur. Avec les fonctions définies par l’utilisateur, les développeurs peuvent :

- Définir les données du capteur directement dans l’objet capteur du graphe
- Effectuer une action en fonction des données du capteur, au sein de l’espace du graphe
- Créer une notification lorsque certaines conditions sont remplies pour des données de capteur entrantes
- Attacher des métadonnées de graphe aux données du capteur avant d’envoyer une notification

Pour plus d’informations, reportez-vous au [Guide pratique pour définir des fonctions définies par l’utilisateur](how-to-user-defined-functions.md).

### <a name="role-assignment"></a>Attribution de rôle

Les actions d’une fonction définie par l’utilisateur sont soumises au contrôle d’accès en fonction du rôle de Digital Twins, afin de sécuriser les données au sein du service. Avec les attributions de rôles, une fonction définie par l’utilisateur est garantie de disposer des autorisations appropriées pour interagir avec le graphe spatial. Par exemple, une fonction définie par l’utilisateur peut tenter de créer, lire, mettre à jour ou supprimer des données de graphe dans un espace donné. Le niveau d’accès d’une fonction définie par l’utilisateur est vérifié lorsque la fonction demande des données au graphe ou tente une action. Pour plus d’informations, consultez [Contrôle d’accès en fonction du rôle](security-create-manage-role-assignments.md).

Un détecteur peut déclencher une fonction définie par l’utilisateur à laquelle aucun rôle n’est attribué. Dans ce cas, la fonction ne pourra pas lire les données du graphe.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le routage des événements et des messages de télémétrie vers d’autres services Azure, consultez [Routage des événements et des messages](concepts-events-routing.md).

Pour plus d’informations sur la création des détecteurs, des fonctions définies par l’utilisateur et des attributions de rôles, lisez le [Guide pratique pour l’utilisation des fonctions définies par l’utilisateur](how-to-user-defined-functions.md).

<!-- Images -->
[1]: media/concepts/digital-twins-data-processing-flow.png
[2]: media/concepts/digital-twins-user-defined-functions.png
