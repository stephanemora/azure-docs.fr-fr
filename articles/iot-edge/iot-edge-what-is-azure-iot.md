---
title: Solutions Azure pour l’Internet des objets (IoT Edge) | Microsoft Docs
description: Découvrez une vue d’ensemble d’un exemple d’architecture de solution IoT et sa relation avec les appareils, le service Azure IoT Hub, les kits de développement logiciel (SDK) d’appareils Azure IoT, les kits de développement logiciel de services Azure IoT et autres services Azure.
author: dominicbetts
manager: timlt
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 09/15/2017
ms.author: dobett
ms.openlocfilehash: 56b7bfe02ddb0f2c909b2f363c37308527ec8db1
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37029087"
---
[!INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="next-steps"></a>Étapes suivantes

Azure IoT Edge est un service Azure qui permet les analyses et le traitement des données en périphérie. Avec IoT Edge, vous pouvez valoriser vos appareils avec le code basé sur le conteneur qui inclut une logique extraite directement à partir des services Azure que vous utilisez déjà, ou votre propre code spécifique à la solution. Cela permet à vos appareils :

* D’agir en tant qu’appareils de passerelle, d’agréger et de traiter des données à partir de plusieurs appareils feuilles.
* D’effectuer la détection d’anomalies et de réagir aux modifications de l’environnement sans avoir à attendre les instructions du cloud.
* De réduire les coûts de bande passante et de stockage par le prétraitement des données et de renvoyer les résultats. 

IoT Edge inclut également une interface de cloud qui permet la gestion à distance des périphériques. Sans avoir à accéder physiquement à vos appareils, vous pouvez déployer du code, surveiller l’état et le mettre à jour. Vous pouvez gérer à distance les appareils uniques ou créer des déploiements qui affectent les grands ensembles de périphériques que vous définissez. Pour plus d’informations, consultez [Understand IoT Edge deployments for single devices or at scale][lnk-deployment] (Comprendre les déploiements IoT Edge pour les appareils uniques ou à l’échelle).

Pour en savoir plus sur les composants qui d’IoT Edge, consultez [How Azure IoT Edge works][lnk-overview] (Fonctionnement d’Azure IoT Edge).

Si vous n’avez jamais utilisé Azure IoT Hub auparavant, vous pouvez commencer par consulter la [Présentation du service Azure IoT Hub][lnk-iot-hub].

[lnk-deployment]: module-deployment-monitoring.md
[lnk-overview]: about-iot-edge.md
[lnk-iot-hub]: ../iot-hub/iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: ../iot-hub/iot-hub-device-management-overview.md
