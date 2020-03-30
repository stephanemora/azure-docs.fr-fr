---
title: Traitement des données et fonctions définies par l’utilisateur - Azure Digital Twins | Microsoft Docs
description: Présentation du traitement des données, des détecteurs et des fonctions définies par l’utilisateur avec Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2020
ms.openlocfilehash: 75ed2029582438ede43687addfd54c0a187e0120
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79231377"
---
# <a name="data-processing-and-user-defined-functions"></a>Traitement des données et fonctions définies par l’utilisateur

Azure Digital Twins propose des fonctionnalités de calcul avancées. Les développeurs peuvent définir et exécuter des fonctions personnalisées sur des messages de télémétrie entrants, afin d’envoyer des événements à des points de terminaison prédéfinis.

## <a name="data-processing-flow"></a>Flux de traitement des données

Lorsque les appareils ont envoyé des données de télémétrie à Azure Digital Twins, les développeurs peuvent traiter ces données en quatre phases : *validation*, *détection*, *calcul* et *distribution*.

[![Flux de traitement des données Azure Digital Twins](media/concepts/digital-twins-data-processing-flow.png)](media/concepts/digital-twins-data-processing-flow.png#lightbox)

1. La phase de validation convertit le message de télémétrie entrant en un format d’[objet de transfert de données](https://docs.microsoft.com/aspnet/web-api/overview/data/using-web-api-with-entity-framework/part-5) généralement bien compris. Cette phase exécute également la validation de l’appareil et du capteur.
1. La phase Détection recherche la ou les fonctions définies par l’utilisateur qui doivent être exécutées. Les détecteurs prédéfinis recherchent les fonctions définies par l’utilisateur en se basant sur les informations relatives à l’appareil, au capteur et à l’espace qui sont issues du message de télémétrie entrant.
1. La phase Calcul exécute la ou les fonctions définies par l’utilisateur qui ont été détectées dans la phase précédente. Ces fonctions peuvent lire et mettre à jour les valeurs calculées présentes sur les nœuds d’un graphe spatial, et peuvent émettre des notifications personnalisées.
1. La phase de distribution achemine toutes les notifications personnalisées, de la phase de calcul vers les points de terminaison définis dans le graphe.

## <a name="data-processing-objects"></a>Objets de traitement des données

Dans Azure Digital Twins, le traitement des données consiste à définir les trois objets suivants : les *détecteurs*, les *fonctions définies par l’utilisateur* et les *attributions de rôles*.

[![Flux de traitement des objets Azure Digital Twins](media/concepts/digital-twins-user-defined-functions.png)](media/concepts/digital-twins-user-defined-functions.png#lightbox)

### <a name="matchers"></a>Détecteurs

Les détecteurs définissent un ensemble de conditions qui évaluent les actions qui auront lieu en fonction des données de télémétrie entrantes du capteur. Les conditions pour déterminer une correspondance incluent les propriétés du capteur, l’appareil parent du capteur et l’espace parent du capteur. Les conditions sont exprimées sous forme de comparaisons à un [chemin JSON](https://jsonpath.com/), comme dans cet exemple :

- tous les capteurs de type de données **température** représentés par la valeur de chaîne avec séquence d’échappement `\"Temperature\"`
- Avoir un port qui comprend `01`
- qui appartient aux appareils dont la clé de propriété étendue **Fabricant** a la valeur de chaîne avec séquence d’échappement `\"Contoso\"`
- qui appartiennent aux espaces du type spécifié par la chaîne avec séquence d’échappement `\"Venue\"`
- Qui sont descendants du parent **SpaceId**`DE8F06CA-1138-4AD7-89F4-F782CC6F69FD`

```JSON
{
  "id": "23535afafd-f39b-46c0-9b0c-0dd3892a1c30",
  "name": "My custom matcher",
  "spaceId": "DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "description": "All sensors of datatype Temperature with 01 in their port that belong to devices with the extended property key Manufacturer set to the value Contoso and that belong to spaces of type Venue that are somewhere below space Id DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "conditions": [
    {
      "id": "43898sg43-e15a-4e9c-abb8-2gw464364",
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    },
    {
      "id": "wt3th44-e15a-35sg-seg3-235wf3ga463",
      "target": "Sensor",
      "path": "$.port",
      "value": "01",
      "comparison": "Contains"
    },
    {
      "id": "735hs33-e15a-37jj-23532-db901d550af5",
      "target": "SensorDevice",
      "path": "$.properties[?(@.name == 'Manufacturer')].value",
      "value": "\"Contoso\"",
      "comparison": "Equals"
    },
    {
      "id": "222325-e15a-49fg-5744-463643644",
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
> - La charge utile JSON est identique à celle retournée par :
>   - `/sensors/{id}?includes=properties,types` pour le capteur.
>   - `/devices/{id}?includes=properties,types,sensors,sensorsproperties,sensorstypes` pour l’appareil parent du capteur.
>   - `/spaces/{id}?includes=properties,types,location,timezone` pour l’espace parent du capteur.
> - Les comparaisons ne respectent pas la casse.

### <a name="user-defined-functions"></a>Fonctions définies par l’utilisateur

Une fonction définie par l’utilisateur est une fonction personnalisée exécutée dans un environnement isolé d’Azure Digital Twins. Les fonctions définies par l’utilisateur ont accès au message brut de télémétrie du capteur tel qu’il a été reçu. Elles ont également accès au graphique spatial et au service de répartiteur. Lorsque la fonction définie par l’utilisateur est inscrite dans le graphique, vous devez créer un détecteur (détaillé [plus haut](#matchers)) pour spécifier à quel moment exécuter la fonction. Par exemple, lorsqu’Azure Digital Twins reçoit de nouvelles données de télémétrie provenant d’un capteur donné, la fonction définie par l’utilisateur trouvée peut calculer une moyenne mobile à partir des dernières données de capteur.

Les fonctions définies par l’utilisateur peuvent être rédigées en JavaScript. Les méthodes d’assistance permettant d’interagir avec le graphe dans l’environnement d’exécution défini par l’utilisateur. Les développeurs peuvent exécuter des extraits de code personnalisés par rapport aux messages de télémétrie de capteur. Voici quelques exemples :

- Définir les données du capteur directement dans l’objet capteur du graphe
- Effectuer une action en fonction des données du capteur, au sein de l’espace du graphe
- Créer une notification lorsque certaines conditions sont remplies pour des données de capteur entrantes
- Attacher des métadonnées de graphe aux données du capteur avant d’envoyer une notification

Pour en savoir plus, consultez la section [Utilisation des fonctions définies par l’utilisateur](./how-to-user-defined-functions.md).

#### <a name="examples"></a>Exemples

Le [dépôt GitHub pour l’exemple en C# Digital Twins](https://github.com/Azure-Samples/digital-twins-samples-csharp/) contient quelques exemples de fonctions définies par l’utilisateur :
- [Cette fonction](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) recherche des valeurs de dioxyde de carbone, de mouvement et de température pour déterminer si un espace est disponible avec ces valeurs dans la plage. Les [didacticiels pour Digital Twins](tutorial-facilities-udf.md) explorent cette fonction de façon plus détaillée. 
- [Cette fonction](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/multiplemotionsensors.js) recherche des données provenant de plusieurs capteurs de mouvement, et détermine que l’espace est disponible si aucun d'eux ne détecte de mouvement. Vous pouvez facilement remplacer la fonction définie par l’utilisateur utilisée dans le [guide de démarrage rapide](quickstart-view-occupancy-dotnet.md) ou dans les [didacticiels](tutorial-facilities-setup.md), en apportant les modifications mentionnées dans la section comments du fichier. 

### <a name="role-assignment"></a>Attribution de rôle

Les actions d’une fonction définie par l’utilisateur sont soumises au [contrôle d’accès en fonction du rôle d’Azure Digital Twins](./security-role-based-access-control.md), afin de sécuriser les données au sein du service. Les attributions de rôles définissent quelles fonctions définies par l’utilisateur ont les autorisations appropriées pour interagir avec le graphique spatial et ses entités. Par exemple, une fonction définie par l’utilisateur peut avoir la capacité et l’autorisation de *CRÉER*, *LIRE*, *METTRE À JOUR*, ou *SUPPRIMER* des données graphiques sous un espace donné. Le niveau d’accès d’une fonction définie par l’utilisateur est vérifié lorsque la fonction demande des données au graphe ou tente une action. Pour en savoir plus, consultez la section [Contrôle d’accès en fonction du rôle](./security-create-manage-role-assignments.md).

Un détecteur peut déclencher une fonction définie par l’utilisateur à laquelle aucun rôle n’est attribué. Dans ce cas, la fonction ne peut pas lire les données du graphique.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur le routage des événements et des messages de télémétrie vers d’autres services Azure, consultez [Routage des événements et des messages](./concepts-events-routing.md).

- Pour plus d’informations sur la création des détecteurs, des fonctions définies par l’utilisateur et des attributions de rôles, lisez le [Guide pratique pour l’utilisation des fonctions définies par l’utilisateur](./how-to-user-defined-functions.md).

- Consultez la [documentation sur les informations de référence de la bibliothèque cliente](./reference-user-defined-functions-client-library.md).
