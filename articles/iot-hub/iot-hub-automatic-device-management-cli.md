---
title: Gestion automatique des appareils à grande échelle avec Azure IoT Hub (CLI) | Microsoft Docs
description: La gestion automatique des appareils Azure IoT Hub permet d’attribuer une configuration à plusieurs appareils IoT
author: ChrisGMsft
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: chrisgre
ms.openlocfilehash: 0302146634904ccf1d87220d3a24553149e10372
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59012964"
---
# <a name="automatic-iot-device-management-at-scale-using-the-azure-cli"></a>Gestion automatique des appareils IoT à grande échelle à l’aide de l’interface CLI Azure

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Gestion automatique des appareils dans Azure IoT Hub automatise une grande partie des tâches répétitives et complexes de la gestion de périphérique grand flottes. Avec la gestion automatique des appareils, vous pouvez cibler un ensemble de périphériques en fonction de leurs propriétés, définir une configuration souhaitée et puis laisser à IoT Hub de mettre à jour les appareils lorsqu’elles arriveront dans l’étendue. Cette mise à jour est effectuée à l’aide un _configuration automatique d’unité_, qui vous permet de vous Résumez la saisie semi-automatique et de conformité, de fusion de handle et de conflits et déployer des configurations dans une approche progressive.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Fonctionnement de la gestion automatique des appareils en mettant à jour d’un ensemble de représentations d’appareil avec les propriétés souhaitées et en signalant un résumé qui repose sur le jumeau d’appareil des propriétés signalées.  Il introduit une nouvelle classe et un document JSON appelé un *Configuration* qui comprend trois parties :

* La **condition cible** définit l’étendue des jumeaux d’appareil à mettre à jour. La condition cible est spécifiée en tant que requête sur les balises de jumeaux d’appareil et/ou sur les propriétés signalées.

* Le **contenu cible** définit les propriétés souhaitées à ajouter ou à mettre à jour dans les jumeaux d’appareil ciblés. Le contenu inclut un chemin de la section des propriétés souhaitées à changer.

* Les **métriques** définissent les nombres récapitulatifs des différents états de configuration tels que **Réussite**, **En cours** et **Erreur**. Les métriques personnalisées sont spécifiées en tant que requêtes sur les propriétés signalées du jumeau d’appareil.  Les métriques système sont les mesures par défaut qui mesurent l’état de mise à jour de représentations, telles que le nombre de représentations d’appareils qui sont ciblés et le nombre de représentations de qui ont été correctement mis à jour. 

## <a name="cli-prerequisites"></a>Prérequis pour l’interface CLI

* Un [hub IoT](../iot-hub/iot-hub-create-using-cli.md) dans votre abonnement Azure. 
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) dans votre environnement. Vous devez utiliser Azure CLI version 2.0.24 ou ultérieure. Utilisez `az –-version` pour valider. Cette version prend en charge les commandes d’extension az et introduit l’infrastructure de la commande Knack. 
* [Extension IoT pour Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

## <a name="implement-device-twins-to-configure-devices"></a>Implémenter des jumeaux d’appareil pour configurer des appareils

Les configurations d’appareil automatiques nécessitent l’utilisation de jumeaux d’appareil pour synchroniser l’état entre les appareils et le cloud.  Reportez-vous à [Comprendre et utiliser les jumeaux d’appareil dans IoT Hub](iot-hub-devguide-device-twins.md) pour obtenir des conseils sur l’utilisation des jumeaux d’appareil.

## <a name="identify-devices-using-tags"></a>Identifier les appareils à l’aide de balises

Avant de créer une configuration, vous devez spécifier les périphériques que vous souhaitez affecter. Azure IoT Hub identifie les appareils à l’aide de balises dans le jumeau d’appareil. Chaque appareil peut avoir plusieurs balises, et vous pouvez les définir comme bon vous semble pour votre solution. Par exemple, si vous gérez des appareils dans différents emplacements, ajoutez les balises suivantes à une représentation d’appareil :

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="define-the-target-content-and-metrics"></a>Définir le contenu cible et les métriques

Le contenu de la cible et de la métrique de requêtes sont spécifiées comme des propriétés souhaitées au jeu et les propriétés signalées pour mesurer de représentations de documents JSON qui décrivent l’appareil.  Pour créer une configuration d’appareil automatique avec Azure CLI, enregistrez le contenu cible et les métriques localement dans des fichiers .txt. Vous utilisez les chemins d’accès de fichier dans une section ultérieure lorsque vous exécutez la commande pour appliquer la configuration à votre appareil. 

Voici un exemple de contenu cible de base :

```json
{
  "content": {
    "deviceContent": {
      "properties.desired.chillerWaterSettings": {
        "temperature": 38,
        "pressure": 78
      }
    }
}
```

Voici des exemples de requêtes de métrique :

```json
{
  "queries": {
    "Compliant": "select deviceId from devices where configurations.[[chillersettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'",
    "Error": "select deviceId from devices where configurations.[[chillersettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='error'",
    "Pending": "select deviceId from devices where configurations.[[chillersettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='pending'"
  }
}
```

## <a name="create-a-configuration"></a>Créer une configuration

Vous configurez des appareils cibles en créant une configuration composée du contenu cible et des métriques. 

Utilisez la commande suivante pour créer une configuration :

```cli
   az iot hub configuration create --config-id [configuration id] \
     --labels [labels] --content [file path] --hub-name [hub name] \
     --target-condition [target query] --priority [int] \
     --metrics [metric queries]
```

* --**config-id** : nom de la configuration à créer dans le hub IoT. Donnez à votre configuration un nom unique comportant au plus 128 lettres minuscules. Évitez les espaces et les caractères non valides suivants : `& ^ [ ] { } \ | " < > /`.

* --**labels** : ajoutez des étiquettes pour faciliter le suivi de votre configuration. Les étiquettes sont des paires Nom, Valeur qui décrivent votre déploiement. Par exemple, `HostPlatform, Linux` ou `Version, 3.0.1`.

* --**content** : JSON inline ou chemin du contenu cible à définir en tant que propriétés de jumeau souhaitées. 

* --**hub-name** : nom du hub IoT dans lequel la configuration sera créée. Le hub doit être dans l’abonnement actuel. Basculez vers l’abonnement souhaité avec la commande `az account set -s [subscription name]`.

* --**target-condition** : entrez une condition cible pour déterminer quels sont les appareils ciblés par cette configuration. La condition est basée sur les balises de jumeau d’appareil ou sur les propriétés souhaitées du jumeau d’appareil et doit correspondre au format de l’expression. Par exemple, `tags.environment='test'` ou `properties.desired.devicemodel='4000x'`. 

* --**priority** : entier positif. Si deux ou plusieurs configurations sont ciblées sur le même appareil, la configuration ayant la valeur numérique la plus élevée pour Priority s’applique.

* --**metrics** : chemin des requêtes de métriques. Métriques fournissent un résumé des nombres de divers états auxquels un appareil peut rapporter après avoir appliqué le contenu de la configuration. Par exemple, vous pouvez créer une métrique pour les modifications de paramètres en attente, une métrique pour les erreurs et une métrique pour les modifications de paramètres réussies. 

## <a name="monitor-a-configuration"></a>Surveiller une configuration

Utilisez la commande suivante pour afficher le contenu d’une configuration :

```cli
az iot hub configuration show --config-id [configuration id] \
  --hub-name [hub name]
```

* --**config-id** : nom de la configuration existante dans le hub IoT.

* --**hub-name** : nom du hub IoT dans lequel la configuration existe. Le hub doit être dans l’abonnement actuel. Basculez vers l’abonnement souhaité avec la commande `az account set -s [subscription name]`.

Inspectez la configuration dans la fenêtre de commande. La propriété **metrics** répertorie un nombre pour chaque métrique évaluée par chaque hub :

* **targetedCount** : métrique système qui spécifie le nombre de jumeaux d’appareil dans le hub IoT qui correspondent à la condition de ciblage.

* **appliedCount** : métrique système qui spécifie le nombre d’appareils pour lesquels le contenu cible a été appliqué.

* **Votre mesure personnalisée** -toutes les mesures que vous avez définies sont les métriques de l’utilisateur.

Vous pouvez afficher une liste d’ID d’appareil ou d’objets pour chacune des métriques à l’aide de la commande suivante :

```cli
az iot hub configuration show-metric --config-id [configuration id] \
   --metric-id [metric id] --hub-name [hub name] --metric-type [type] 
```

* --**config-id** : nom du déploiement qui existe dans le hub IoT.

* --**metric-id** : nom de la métrique pour laquelle vous souhaitez voir la liste des ID d’appareil, par exemple `appliedCount`.

* --**hub-name** : nom du hub IoT dans lequel le déploiement existe. Le hub doit être dans l’abonnement actuel. Basculez vers l’abonnement souhaité avec la commande `az account set -s [subscription name]`.

* --**metric-type** : le type de métrique peut être `system` ou `user`.  Les métriques système sont `targetedCount` et `appliedCount`. Toutes les autres métriques sont des métriques utilisateur.

## <a name="modify-a-configuration"></a>Modifier une configuration

Quand vous modifiez une configuration, les modifications sont répliquées immédiatement sur tous les appareils ciblés. 

Si vous mettez à jour la condition cible, les mises à jour suivantes se produisent :

* Si un jumeau d’appareil ne remplissait pas l’ancienne condition cible, mais qu’il remplit la nouvelle condition cible et que cette configuration a la priorité la plus élevée pour ce jumeau d’appareil, cette configuration est appliquée au jumeau d’appareil. 

* Si un jumeau d’appareil ne remplit plus la condition cible, les paramètres de la configuration sont supprimés et le jumeau d’appareil est modifié par la configuration ayant la priorité la plus élevée suivante. 

* Si un jumeau d’appareil exécutant cette configuration ne remplit plus la condition cible et ne remplit pas la condition cible des autres configurations, les paramètres de la configuration sont supprimés et aucune autre modification n’est effectuée sur le jumeau. 

Utilisez la commande suivante pour mettre à jour une configuration :

```cli
az iot hub configuration update --config-id [configuration id] \
   --hub-name [hub name] --set [property1.property2='value']
```

* --**config-id** : nom de la configuration existante dans le hub IoT.

* --**hub-name** : nom du hub IoT dans lequel la configuration existe. Le hub doit être dans l’abonnement actuel. Basculez vers l’abonnement souhaité avec la commande `az account set -s [subscription name]`.

* --**set** : met à jour une propriété dans la configuration. Vous pouvez mettre à jour les propriétés suivantes :

    * targetCondition : par exemple, `targetCondition=tags.location.state='Oregon'`

    * étiquettes 

    * priority

## <a name="delete-a-configuration"></a>Supprimer une configuration

Quand vous supprimez une configuration, tous les jumeaux d’appareil prennent leur configuration suivante dans l’ordre de priorité. Si des jumeaux d’appareil ne remplissent la condition cible d’aucune autre configuration, aucun autre paramètre n’est appliqué. 

Utilisez la commande suivante pour supprimer une configuration :

```cli
az iot hub configuration delete --config-id [configuration id] \
   --hub-name [hub name] 
```
* --**config-id** : nom de la configuration existante dans le hub IoT.

* --**hub-name** : nom du hub IoT dans lequel la configuration existe. Le hub doit être dans l’abonnement actuel. Basculez vers l’abonnement souhaité avec la commande `az account set -s [subscription name]`.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à configurer et surveiller des appareils IoT à grande échelle. Suivez ces liens pour en savoir plus sur la gestion de Azure IoT Hub :

* [Gestion de vos identités d’appareil IoT Hub en bloc](iot-hub-bulk-identity-mgmt.md)
* [Métriques d’IoT Hub](iot-hub-metrics.md)
* [Surveillance des opérations](iot-hub-operations-monitoring.md)

Pour explorer davantage les capacités de IoT Hub, consultez :

* [Guide du développeur d’IoT Hub](iot-hub-devguide.md)
* [Déploiement d’une IA sur des appareils de périmètre avec Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)

Pour savoir comment utiliser le service d’approvisionnement des appareils IoT Hub afin d’activer l’approvisionnement sans contact et juste-à-temps, consultez : 

* [Service Azure IoT Hub Device Provisioning](/azure/iot-dps)
