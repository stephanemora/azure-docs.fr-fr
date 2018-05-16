---
title: Configurer et surveiller des appareils IoT à grande échelle avec Azure IoT Hub | Microsoft Docs
description: Utiliser des configurations d’appareil automatiques Azure IoT Hub pour assigner une configuration à plusieurs appareils
services: iot-hub
documentationcenter: ''
author: ChrisGMsft
manager: timlt
editor: ''
ms.service: iot-hub
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2018
ms.author: chrisgre
ms.openlocfilehash: 7146fba69857c3a612ce1b3dbb83387c1f3068d6
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="configure-and-monitor-iot-devices-at-scale---preview"></a>Configurer et surveiller des appareils IoT à grande échelle : préversion

La gestion automatique des appareils dans Azure IoT Hub automatise une grande partie des tâches répétitives et complexes liées à la gestion de grandes flottes d’appareils pendant tout leur cycle de vie. Avec la gestion automatique des appareils, vous pouvez cibler un ensemble d’appareils en fonction de leurs propriétés, définir la configuration souhaitée et permettre à IoT Hub de mettre à jour les appareils chaque fois qu’ils se trouvent dans l’étendue.  Cette opération est effectuée à l’aide d’une configuration d’appareil automatique, qui vous permet également de récapituler les données d’achèvement et de conformité, de gérer la fusion et les conflits et de déployer les configurations de façon progressive.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Les configurations d’appareil automatiques reposent sur la mise à jour d’un ensemble de jumeaux d’appareil avec des propriétés souhaitées et sur la création d’un rapport récapitulatif basé sur les propriétés signalées du jumeau d’appareil.  Cette approche introduit un nouveau document JSON et de classe appelé _Configuration_ qui comprend trois parties :

* La **condition cible** définit l’étendue des jumeaux d’appareil à mettre à jour. La condition cible est spécifiée en tant que requête sur les balises de jumeaux d’appareil et/ou sur les propriétés signalées.

* Le **contenu cible** définit les propriétés souhaitées à ajouter ou à mettre à jour dans les jumeaux d’appareil ciblés. Le contenu inclut un chemin de la section des propriétés souhaitées à changer.

* Les **métriques** définissent les nombres récapitulatifs des différents états de configuration tels que **Réussite**, **En cours** et **Erreur**. Les métriques personnalisées sont spécifiées en tant que requêtes sur les propriétés signalées du jumeau d’appareil.  Les métriques système sont des métriques par défaut qui mesurent l’état de la mise à jour des jumeaux, telles que le nombre de jumeaux d’appareil qui sont ciblés et le nombre de jumeaux qui ont été correctement mis à jour. 

## <a name="implement-device-twins-to-configure-devices"></a>Implémenter des jumeaux d’appareil pour configurer des appareils

Les configurations d’appareil automatiques nécessitent l’utilisation de jumeaux d’appareil pour synchroniser l’état entre les appareils et le cloud.  Reportez-vous à [Comprendre et utiliser les jumeaux d’appareil dans IoT Hub][lnk-device-twin] pour obtenir des conseils sur l’utilisation des jumeaux d’appareil.

## <a name="identify-devices-using-tags"></a>Identifier les appareils à l’aide de balises

Avant de pouvoir créer une configuration, vous devez spécifier les appareils concernés. Azure IoT Hub identifie les appareils à l’aide de balises dans le jumeau d’appareil. Chaque appareil peut avoir plusieurs balises, et vous pouvez les définir comme bon vous semble pour votre solution. Par exemple, si vous gérez des appareils à différents emplacements, vous pouvez ajouter les balises suivantes à un jumeau d’appareil :

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="create-a-configuration"></a>Créer une configuration

1. Accédez à votre IoT Hub dans le [portail Azure][lnk-portal]. 
1. Sélectionnez **Configuration de l’appareil (préversion)**.
1. Sélectionnez **Ajouter une configuration**.

La création d’une configuration nécessite cinq étapes. Les sections suivantes les décrivent en détail. 

### <a name="step-1-name-and-label"></a>Étape 1 : nom et étiquette

1. Donnez à votre configuration un nom unique comportant au plus 128 lettres minuscules. Évitez les espaces et les caractères non valides suivants : `& ^ [ ] { } \ | " < > /`.
1. Ajoutez des étiquettes pour faciliter le suivi de vos configurations. Les étiquettes sont des paires **Nom**, **Valeur** qui décrivent votre configuration. Par exemple, `HostPlatform, Linux` ou `Version, 3.0.1`.
1. Sélectionnez **Suivant** pour passer à l’étape 2. 

### <a name="step-2-specify-settings"></a>Étape 2 : Spécifier les paramètres

Cette section spécifie le contenu cible à définir dans les jumeaux d’appareil ciblés. Il existe deux entrées pour chaque ensemble de paramètres. La première est le chemin du jumeau d’appareil, qui est le chemin de la section JSON dans les propriétés désirées du jumeau qui seront définies.  La seconde est le contenu JSON à insérer dans cette section. Par exemple, définissez le chemin du jumeau d’appareil comme suit :

![Définir le chemin du jumeau d’appareil et le contenu](./media/iot-hub-auto-device-config/create-configuration-full-browser.png)

Vous pouvez également définir des paramètres individuels en spécifiant le chemin complet dans le chemin du jumeau d’appareil et la valeur dans le contenu sans crochets. Par exemple, définissez le chemin du jumeau d’appareil sur `properties.desired.chiller-water.temperature` et le contenu sur `66`.

Si deux ou plusieurs configurations ciblent le même chemin de jumeau d’appareil, le contenu de la configuration ayant la priorité la plus élevée s’applique (la priorité est définie à l’étape 4).

Si vous souhaitez supprimer une propriété, définissez-la sur `null`.

Vous pouvez ajouter des paramètres en sélectionnant **Ajouter un paramètre de jumeau d’appareil**.

### <a name="step-3-specify-metrics-optional"></a>Étape 3 : Spécifier les métriques (facultatif)

Les métriques fournissent des nombres récapitulatifs des différents états qu’un appareil peut signaler après l’application d’un contenu de configuration. Par exemple, vous pouvez créer une métrique pour les modifications de paramètres en attente, une métrique pour les erreurs et une métrique pour les modifications de paramètres réussies.

1. Entrez un nom pour **Nom de métrique**
1. Entrez une requête pour **Critères de la métrique**.  La requête est basée sur les propriétés signalées du jumeau d’appareil.  La métrique représente le nombre de lignes retournées par la requête.

Par exemple : `SELECT deviceId FROM devices WHERE properties.reported.chillerWaterSettings.status='pending'`

Vous pouvez inclure une clause spécifiant que la configuration a été appliquée, par exemple : `SELECT deviceId FROM devices WHERE configurations.yourconfigname.status='Applied'`


### <a name="step-4-target-devices"></a>Étape 4 : cibler des appareils

Utilisez la propriété tags à partir de vos jumeaux d’appareil pour cibler des appareils spécifiques qui doivent recevoir cette configuration.  Vous pouvez également cibler des appareils en fonction des propriétés signalées du jumeau d’appareil.

Étant donné que plusieurs configurations peuvent cibler le même appareil, vous devez donner à chaque configuration un numéro de priorité. En cas de conflit, la configuration avec la priorité la plus élevée prévaut. 

1. Entrez un entier positif pour la **Priorité** de la configuration. La valeur numérique la plus élevée est considérée comme la priorité la plus élevée. Si deux configurations ont le même numéro de priorité, celle qui a été créée le plus récemment prévaut. 
1. Entrez une **Condition cible** pour déterminer quels sont les appareils ciblés par cette configuration. La condition est basée sur les balises de jumeau d’appareil ou sur les propriétés signalées du jumeau d’appareil et doit correspondre au format de l’expression. Par exemple, `tags.environment='test'` ou `properties.reported.chillerProperties.model='4000x'`. 
1. Sélectionnez **Suivant** pour passer à l’étape finale.

### <a name="step-5-review-configuration"></a>Étape 5 : Passer en revue la configuration

Passez en revue les informations de votre configuration, puis sélectionnez **Envoyer**.

## <a name="monitor-a-configuration"></a>Surveiller une configuration

Pour afficher les détails d’une configuration et surveiller les appareils qui l’exécutent, effectuez les étapes suivantes :

1. Accédez à votre IoT Hub dans le [portail Azure][lnk-portal]. 
1. Sélectionnez **Configuration de l’appareil (préversion)**.
1. Inspectez la liste des configurations. Pour chaque configuration, vous pouvez voir les détails suivants :
   * **ID** : nom de la configuration.
   * **Condition cible** : requête utilisée pour définir les appareils ciblés.
   * **Priorité** : numéro de priorité de la configuration.
   * **Heure de création** : horodatage de création de la configuration. Cet horodatage sert à départager deux configurations ayant la même priorité. 
   * **Métriques système** : métriques qui sont calculées par IoT Hub et qui ne peuvent pas être personnalisées par les développeurs. « Ciblé » spécifie le nombre de jumeaux d’appareil remplissant la condition cible. « Appliqué » spécifie le nombre de jumeaux d’appareil qui ont été modifiés par la configuration ; cela peut inclure des modifications partielles dans le cas où une configuration distincte de priorité plus élevée a également apporté des modifications. 
   * **Métriques personnalisées** : métriques qui ont été spécifiées par le développeur sous forme de requêtes portant sur les propriétés signalées du jumeau d’appareil.  Vous pouvez définir jusqu’à cinq métriques personnalisées par configuration. 
   
1. Sélectionnez la configuration que vous souhaitez surveiller.  
1. Inspectez les détails de la configuration. Vous pouvez utiliser les onglets pour afficher des détails spécifiques sur les appareils qui ont reçu la configuration : 
   * **Condition cible** : appareils qui remplissent la condition cible. 
   * **Métriques** : liste des métriques système et des métriques personnalisées.  Vous pouvez voir la liste des appareils pris en compte pour chaque métrique en sélectionnant la métrique dans la liste déroulante, puis en sélectionnant **Afficher les appareils**.
   * **Paramètres de jumeau d’appareil** : paramètres de jumeau d’appareil qui sont définis par la configuration. 
   * **Étiquettes de configuration** : paires clé-valeur utilisées pour décrire une configuration.  Les étiquettes n’ont aucun impact sur les fonctionnalités. 

## <a name="modify-a-configuration"></a>Modifier une configuration

Quand vous modifiez une configuration, les modifications sont répliquées immédiatement sur tous les appareils ciblés. 

Si vous mettez à jour la condition cible, les mises à jour suivantes se produisent :
* Si un jumeau d’appareil ne remplissait pas l’ancienne condition cible, mais qu’il remplit la nouvelle condition cible et que cette configuration a la priorité la plus élevée pour ce jumeau d’appareil, cette configuration est appliquée au jumeau d’appareil. 
* Si un jumeau d’appareil ne remplit plus la condition cible, les paramètres de la configuration sont supprimés et le jumeau d’appareil est modifié par la configuration ayant la priorité la plus élevée suivante. 
* Si un jumeau d’appareil exécutant cette configuration ne remplit plus la condition cible et ne remplit pas la condition cible des autres configurations, les paramètres de la configuration sont supprimés et aucune autre modification n’est effectuée sur le jumeau. 

Pour modifier une configuration, effectuez les étapes suivantes : 

1. Accédez à votre IoT Hub dans le [portail Azure][lnk-portal]. 
1. Sélectionnez **Configuration de l’appareil (préversion)**. 
1. Sélectionnez la configuration que vous souhaitez modifier. 
1. Mettez à jour les champs suivants : 
   * Condition cible 
   * Étiquettes 
   * Priorité 
   * Mesures
1. Sélectionnez **Enregistrer**.
1. Suivez les étapes fournies dans [Surveiller une configuration][anchor-monitor] pour observer le déploiement des modifications. 

## <a name="delete-a-configuration"></a>Supprimer une configuration

Quand vous supprimez une configuration, tous les jumeaux d’appareil prennent leur configuration suivante dans l’ordre de priorité. Si des jumeaux d’appareil ne remplissent la condition cible d’aucune autre configuration, aucun autre paramètre n’est appliqué. 

1. Accédez à votre IoT Hub dans le [portail Azure][lnk-portal]. 
1. Sélectionnez **Configuration de l’appareil (préversion)**. 
1. Utilisez la case à cocher pour sélectionner la configuration à supprimer. 
1. Sélectionnez **Supprimer**.
1. Une invite vous demande de confirmer.

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à configurer et à surveiller des appareils IoT à grande échelle. Suivez ces liens pour en savoir plus sur la gestion de Azure IoT Hub :

* [Gestion de vos identités d’appareil IoT Hub en bloc][lnk-bulkIDs]
* [Métriques d’IoT Hub][lnk-metrics]
* [Surveillance des opérations][lnk-monitor]

Pour explorer davantage les capacités de IoT Hub, consultez :

* [Guide du développeur d’IoT Hub][lnk-devguide]
* [Déploiement d’une IA sur des appareils de périphérie avec Azure IoT Edge][lnk-iotedge]

Pour savoir comment utiliser le service d’approvisionnement des appareils IoT Hub afin d’activer l’approvisionnement sans contact et juste-à-temps, consultez : 

* [Service Azure IoT Hub Device Provisioning][lnk-dps]

[lnk-device-twin]: iot-hub-devguide-device-twins.md
[lnk-bulkIDs]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dps]: https://azure.microsoft.com/documentation/services/iot-dps
[lnk-portal]: https://portal.azure.com
