---
title: Mise à l’échelle Azure IoT Hub | Microsoft Docs
description: Comment mettre à l’échelle votre IoT Hub pour gérer le débit de messages prévu et les fonctionnalités souhaitées. Inclut un résumé du débit pris en charge pour chaque niveau et des options pour le partitionnement.
author: wesmc7777
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2018
ms.author: wesmc
ms.openlocfilehash: 49e0db690818e67f96f5bcefa4f581b1db6da451
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125574"
---
# <a name="choose-the-right-iot-hub-tier-for-your-solution"></a>Choisir le niveau IoT Hub correspondant à votre solution

Chaque solution IoT étant différente, Azure IoT Hub offre plusieurs options en fonction de la tarification et de la mise à l’échelle. Cet article vous aide à évaluer vos besoins IoT Hub. Pour plus d’informations sur les niveaux IoT Hub, consultez [Tarification IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub).

Pour déterminer le niveau IoT Hub adapté à votre solution, vous devez vous poser deux questions :

**Quelles sont les fonctionnalités que je prévois d’utiliser ?**

Azure IoT Hub propose deux niveaux : de base et standard. Ils diffèrent quant au nombre de fonctionnalités qu’ils prennent en charge. Si votre solution IoT est basée sur la collecte de données à partir d’appareils et sur leur analyse de manière centralisée, le niveau de base est probablement adapté à vos besoins. Si vous souhaitez utiliser des configurations plus complexes pour le contrôle à distance des appareils IoT ou distribuer une partie de vos charges de travail sur les appareils eux-mêmes, envisagez plutôt le niveau standard. Pour une description détaillée des fonctionnalités incluses dans chaque niveau, consultez [Niveaux de base et standard](#basic-and-standard-tiers).

**Quel volume de données vais-je déplacer au quotidien ?**

Chaque niveau IoT Hub est disponible en trois tailles, en fonction du débit de données à traiter au quotidien. Ces tailles sont identifiées au moyen de chiffre : 1, 2 et 3. Par exemple, chaque unité d’un IoT Hub de niveau 1 peut gérer 400 000 messages par jour, alors qu’une unité de niveau 3 peut en gérer 300 000 000. Pour plus d’informations sur les règles de données, consultez [Débit de messages](#message-throughput).

## <a name="basic-and-standard-tiers"></a>Niveaux de base et standard

Le niveau standard de IoT Hub active toutes les fonctionnalités. Il est requis pour toutes les solutions IoT qui comptent utiliser les fonctionnalités de communication bidirectionnelle. Le niveau de base active un sous-ensemble de fonctionnalités. Il est destiné aux solutions IoT qui nécessitent uniquement une communication unidirectionnelle, des appareils vers le cloud. Ces deux niveaux offrent les mêmes fonctionnalités de sécurité et d’authentification.

Dans un niveau, vous ne pouvez choisir qu’un seul type [d’édition](https://azure.microsoft.com/pricing/details/iot-hub/) par hub IoT. Par exemple, vous pouvez créer un hub IoT avec plusieurs unités de S1, mais pas avec plusieurs unités de différentes éditions, telles que S1 et B3, ou S1 et S2.

| Fonctionnalité | Niveau de base | Niveaux gratuit et Standard |
| ---------- | ---------- | ------------- |
| [Télémétrie appareil-à-cloud](iot-hub-devguide-messaging.md) | Oui | Oui |
| [Identité par appareil](iot-hub-devguide-identity-registry.md) | Oui | Oui |
| [Routage de messages](iot-hub-devguide-messages-read-custom.md) et [intégration Event Grid](iot-hub-event-grid.md) | Oui | Oui |
| [Protocoles HTTP, AMQP et MQTT](iot-hub-devguide-protocols.md) | Oui | Oui |
| [Service Device Provisioning](../iot-dps/about-iot-dps.md) | Oui | Oui |
| [Surveillance et diagnostics](iot-hub-monitor-resource-health.md) | Oui | Oui |
| [Messages de cloud-à-appareil](iot-hub-devguide-c2d-guidance.md) |   | Oui |
| [Jumeaux d’appareil](iot-hub-devguide-device-twins.md), [Jumeaux de module](iot-hub-devguide-module-twins.md) et [Gestion des appareils](iot-hub-device-management-overview.md) |   | Oui |
| [Flux d’appareils (préversion)](iot-hub-device-streams-overview.md) |   | Oui |
| [Azure IoT Edge](../iot-edge/about-iot-edge.md) |   | Oui |

IoT Hub propose également un niveau gratuit à des fins de test et d’évaluation. Il possède toutes les fonctionnalités du niveau standard, mais ses allocations en termes de messages sont limitées. Vous ne pouvez pas faire évoluer le niveau gratuit vers le niveau de base ou standard.

## <a name="partitions"></a>Partitions

Les Azure IoT Hubs contiennent de nombreux composants de base [Azure Event Hubs](../event-hubs/event-hubs-features.md), y compris des [partitions](../event-hubs/event-hubs-features.md#partitions). Les flux d’événements pour IoT Hubs sont généralement renseignés à l'aide des données de télémétrie entrantes signalées par différents appareils IoT. Le partitionnement du flux d’événements est utilisé pour réduire les conflits qui se produisent lors des opérations simultanées de lecture et d'écriture dans les flux d’événements.

Le nombre de partitions est choisi lors de la création de l'IoT Hub et ne peut pas être modifié. La limite de partition maximale pour le niveau de base IoT Hub et IoT Hub de niveau standard est 32. La plupart des hubs IoT n’ont besoin que de 4 partitions. Pour plus d’informations sur les partitions, consultez le Forum aux questions relatif aux Event Hubs [De combien de partitions ai-je besoin ?](../event-hubs/event-hubs-faq.md#how-many-partitions-do-i-need)

## <a name="tier-upgrade"></a>Évolution des niveaux

Une fois votre IoT Hub créé, vous pouvez le faire évoluer et passer du niveau de base au niveau standard sans interrompre vos opérations existantes. Pour plus d’informations, consultez [Comment mettre à niveau votre IoT Hub](iot-hub-upgrade.md).

La configuration des partitions n'évolue pas lorsque vous migrez du niveau De base vers le niveau Standard.

## <a name="iot-hub-rest-apis"></a>API REST IoT Hub

La différence de fonctionnalités prises en charge entre les niveaux de base et standard de IoT Hub réside dans le fait que certains appels d’API ne fonctionnent pas avec des hubs utilisant le niveau de base. Le tableau suivant présente les API disponibles :

| API | Niveau de base | Niveaux gratuit et Standard |
| --- | ---------- | ------------- |
| [Supprimer un appareil](https://docs.microsoft.com/rest/api/iothub/service/deletedevice) | Oui | Oui |
| [Obtenir un appareil](https://docs.microsoft.com/rest/api/iothub/service/getdevice) | Oui | Oui |
| Supprimer le module | Oui | Oui |
| Obtenir le module | Oui | Oui |
| [Obtenir les statistiques de Registre](https://docs.microsoft.com/rest/api/iothub/service/getdeviceregistrystatistics) | Oui | Oui |
| [Obtenir les statistiques de services](https://docs.microsoft.com/rest/api/iothub/service/getservicestatistics) | Oui | Oui |
| [Créer ou mettre à jour un appareil](https://docs.microsoft.com/rest/api/iothub/service/createorupdatedevice) | Oui | Oui |
| Placer le module | Oui | Oui |
| [Interroger IoT Hub](https://docs.microsoft.com/rest/api/iothub/service/queryiothub) | Oui | Oui |
| Interroger les modules | Oui | Oui |
| [Créer l’URI SAS de téléchargement des fichiers](https://docs.microsoft.com/rest/api/iothub/device/createfileuploadsasuri) | Oui | Oui |
| [Recevoir une notification d’appareil lié](https://docs.microsoft.com/rest/api/iothub/device/receivedeviceboundnotification) | Oui | Oui |
| [Envoyer un événement d’appareil](https://docs.microsoft.com/rest/api/iothub/device/senddeviceevent) | Oui | Oui |
| Envoyer un événement de module | Oui | Oui |
| [Mettre à jour l’état de chargement des fichiers](https://docs.microsoft.com/rest/api/iothub/device/updatefileuploadstatus) | Oui | Oui |
| [Opération sur l’appareil en bloc](/rest/api/iot-dps/runbulkenrollmentgroupoperation/runbulkenrollmentgroupoperation) | Oui, à l’exception des fonctionnalités IoT Edge | Oui | 
| [Purger la file d’attente de commandes](https://docs.microsoft.com/rest/api/iothub/service/purgecommandqueue) |   | Oui |
| [Obtenir un jumeau d’appareil](https://docs.microsoft.com/rest/api/iothub/service/gettwin) |   | Oui |
| Obtenir un jumeau de module |   | Oui |
| [Appeler une méthode d’appareil](https://docs.microsoft.com/rest/api/iothub/service/invokedevicemethod) |   | Oui |
| [Mettre à jour le jumeau d’appareil](https://docs.microsoft.com/rest/api/iothub/service/updatetwin) |   | Oui | 
| Mettre à jour le jumeau de module |   | Oui | 
| [Abandonner une notification d’appareil lié](https://docs.microsoft.com/rest/api/iothub/device/abandondeviceboundnotification) |   | Oui |
| [Terminer une notification d’appareil lié](https://docs.microsoft.com/rest/api/iothub/device/completedeviceboundnotification) |   | Oui |
| [Annuler un travail](https://docs.microsoft.com/rest/api/iothub/service/canceljob) |   | Oui |
| [Créer un travail](https://docs.microsoft.com/rest/api/iothub/service/createjob) |   | Oui |
| [Obtenir un travail](https://docs.microsoft.com/rest/api/iothub/service/getjob) |   | Oui |
| [Tâches de requête](https://docs.microsoft.com/rest/api/iothub/service/queryjobs) |   | Oui |

## <a name="message-throughput"></a>Débit de messages

La meilleure façon de dimensionner une solution IoT Hub consiste à évaluer le trafic sur une base par unité. Prenez plus particulièrement en compte le débit maximal requis pour les catégories d’opérations suivantes :

* Messages appareil-à-cloud
* Messages Cloud vers appareil
* Opérations du registre d’identité

Le trafic se mesure en fonction du nombre d’unités et non du nombre de hubs. Une instance IoT Hub de niveau 1 ou 2 peut avoir jusqu’à 200 unités associées. Une instance IoT Hub de niveau 3 peut avoir jusqu’à 10 unités. Une fois que votre IoT Hub créé, vous pouvez modifier le nombre d’unités ou changer de niveau, sans interrompre vos opérations existantes. Pour plus d’informations, consultez [Comment mettre à niveau votre IoT Hub](iot-hub-upgrade.md).

Voici un exemple du trafic de chaque niveau. Les messages appareil-à-cloud suivent les règles de débit soutenu suivantes :

| Niveau | Débit soutenu | Vitesse de transmission soutenue |
| --- | --- | --- |
| B1, S1 |Jusqu’à 1 111 Ko/minute par unité<br/>(1,5 Go/jour/unité) |Moyenne de 278 messages/minute par unité<br/>(400 000 messages/jour par unité) |
| B2, S2 |Jusqu’à 16 Mo/minute par unité<br/>(22,8 Go/jour/unité) |Moyenne de 4167 messages/minute par unité<br/>(6 millions de messages/jour par unité) |
| B3, S3 |Jusqu’à 814 Mo/minute par unité<br/>(1144,4 Go/jour/unité) |Moyenne de 208 333 messages/minute par unité<br/>(300 millions de messages/jour par unité) |

Outre ces informations sur le débit, consultez les [quotas et limitations IoT Hub](iot-hub-devguide-quotas-throttling.md) et concevez votre solution en conséquence.

### <a name="identity-registry-operation-throughput"></a>Débit des opérations de registre d’identité

Les opérations de registre d’identité IoT Hub ne sont pas censées être des opérations d’exécution, car elles sont principalement liées à l’approvisionnement d’appareils.

Pour obtenir les pics de performances spécifiques, consultez [Quotas et limitations IoT Hub](iot-hub-devguide-quotas-throttling.md).

## <a name="auto-scale"></a>Mise à l’échelle automatique

Si vous approchez le nombre maximal autorisé de messages pour votre hub IoT, vous pouvez utiliser ces [étapes pour une mise à l’échelle automatique](https://azure.microsoft.com/resources/samples/iot-hub-dotnet-autoscale/), afin d’incrémenter une unité IoT Hub dans le même niveau IoT Hub.

## <a name="sharding"></a>Partitionnement

Un hub IoT unique peut recevoir des millions d’appareils, mais votre solution nécessite parfois des caractéristiques de performances spécifiques qu’un seul hub IoT ne peut pas assurer. Dans ce cas, vous pouvez partitionner vos appareils sur plusieurs IoT Hubs. L’utilisation de plusieurs IoT Hubs permet de simplifier les pics de trafic et d’obtenir le débit requis ou les taux d’opération requis.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les fonctionnalités de IoT Hub et les détails des performances, consultez [tarification IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub) ou [quotas et limitations IoT Hub](iot-hub-devguide-quotas-throttling.md).

* Pour modifier votre niveau IoT Hub, suivez les étapes décrites dans [Comment mettre à niveau votre IoT Hub](iot-hub-upgrade.md).
