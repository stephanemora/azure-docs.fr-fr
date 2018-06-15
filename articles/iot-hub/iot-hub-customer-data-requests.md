---
title: Fonctionnalités de demande de données client
description: Résumé des fonctionnalités de demande de données client
author: dominicbetts
ms.author: dobett
manager: timlt
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 73da48d449a7cc5cdca598c8aef176952909ed85
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634921"
---
# <a name="summary-of-customer-data-request-features"></a>Résumé des fonctionnalités de demande de données client

Le service Azure IoT Hub est un service cloud basé sur des API REST conçu pour les clients d’entreprise qui permet une communication bidirectionnelle sécurisée entre des millions d’appareils et un service Azure partitionné.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Un administrateur client assigne un identificateur (ID) d’appareil à chaque appareil. Les données d’appareil reposent sur l’ID d’appareil assigné. Microsoft ne conserve aucune information et n’a pas accès aux données permettant la corrélation entre l’ID d’appareil et l’utilisateur.

La plupart des appareils gérés dans Azure IoT Hub ne sont pas des appareils personnels. Par exemple, il peut s’agir d’un thermostat de bureau ou d’un robot d’usine. Toutefois, les clients peuvent décider que certains appareils sont personnels et gérer eux-mêmes leurs propres méthodes de suivi des ressources ou de l’inventaire afin d’associer des appareils à des personnes. Azure IoT Hub gère et stocke toutes les données associées aux appareils comme s’il s’agissait de données personnelles.

Les administrateurs clients peuvent utiliser le Portail Azure ou les API REST du service pour répondre aux demandes d’informations en exportant ou en supprimant les données associées à un ID d’appareil.

Si vous utilisez la fonctionnalité de routage du service Azure IoT Hub pour transférer les messages des appareils vers d’autres services, l’administrateur client doit effectuer les demandes de données pour chaque point de terminaison de routage afin d’exécuter une demande complète pour un appareil donné. Pour plus d’informations, consultez la documentation de référence de chaque point de terminaison. Pour plus d’informations sur les points de terminaison pris en charge, consultez l’article [Référence - Points de terminaison IoT Hub](iot-hub-devguide-endpoints.md).

Si vous utilisez la fonctionnalité d’intégration à Azure Event Grid du service Azure IoT Hub, les demandes de données doivent être effectuées par l’administrateur client pour chaque abonné à ces événements. Pour plus d’informations, consultez l’article [Réagir aux événements IoT Hub en utilisant Event Grid](iot-hub-event-grid.md).

Si vous utilisez la fonctionnalité d’intégration à Azure Monitor du service Azure IoT Hub pour créer des journaux de diagnostic, l’administrateur client doit exécuter les demandes de données sur les journaux stockés. Pour plus d’informations, consultez l’article [Surveiller l’intégrité d’Azure IoT Hub](iot-hub-monitor-resource-health.md).

## <a name="deleting-customer-data"></a>Suppression des données client

Les administrateurs clients peuvent utiliser le panneau des appareils IoT de l’extension Azure IoT Hub dans le Portail Azure pour supprimer un appareil, ce qui supprime les données associées à cet appareil.

Il est également possible d’effectuer des opérations de suppression pour les appareils à l’aide des API REST. Pour plus d’informations, consultez l’article [Device Api - Delete Device](https://docs.microsoft.com/rest/api/iothub/deviceapi/deletedevice) (API d’appareil - Supprimer un appareil).

## <a name="exporting-customer-data"></a>Exportation des données client

Les administrateurs clients peuvent utiliser les opérations de copie et de collage dans le panneau des appareils IoT de l’extension Azure IoT Hub dans le Portail Azure pour exporter les données associées à un appareil.

Il est également possible d’effectuer des opérations d’exportation pour les appareils à l’aide des API REST. Pour plus d’informations, consultez l’article [Device Api - Get Device](https://docs.microsoft.com/rest/api/iothub/deviceapi/getdevice) (API d’appareil - Obtenir un appareil).

> [!NOTE]
> Lorsque vous utilisez les services d’entreprise Microsoft, Microsoft génère des informations appelées journaux générés par le système. Certains journaux générés par le système Azure IoT Hub ne sont pas accessibles ni exportables par les administrateurs clients. Ces journaux consignent les actions factuelles exécutées au sein du service, ainsi que les données de diagnostic relatives aux différents appareils.

## <a name="links-to-additional-documentation"></a>Liens vers la documentation complémentaire

La documentation complète sur les API d’appareil Azure IoT Hub est accessible à l’adresse [https://docs.microsoft.com/rest/api/iothub/deviceapi](https://docs.microsoft.com/rest/api/iothub/deviceapi).