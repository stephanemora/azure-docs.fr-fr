---
title: Historique et navigation dans la version d’IoT Edge – Azure IoT Edge
description: Découvrez les nouveautés d’IoT Edge avec des informations sur les nouvelles fonctionnalités et capacités des dernières versions.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 877b488fc888b4ced8165c19bcd5c51b5f982745
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103492504"
---
# <a name="azure-iot-edge-versions-and-release-notes"></a>Versions d’Azure IoT Edge et notes de publication

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge est un produit créé à partir du projet open source IoT Edge hébergé sur GitHub. Toutes les nouvelles versions sont accessibles dans le [projet Azure IoT Edge](https://github.com/Azure/azure-iotedge). Les contributions et les rapports de bogues peuvent être effectués sur le [projet open source IoT Edge](https://github.com/Azure/iotedge).

## <a name="documented-versions"></a>Versions documentées

La documentation IoT Edge sur ce site est disponible pour deux versions différentes du produit, ce qui vous permet de choisir le contenu qui s’applique à votre environnement IoT Edge. Actuellement, les deux versions prises en charge sont les suivantes :

* **IoT Edge 1.1 (LTS)** est la première version de la prise en charge à long terme (LTS) d'IoT Edge. La documentation de cette version couvre l'ensemble des fonctionnalités de toutes les versions précédentes, jusqu'à la version 1.1. Cette version de la documentation sera stable pendant toute la durée de vie de la version 1.1, et ne reflétera pas les nouvelles fonctionnalités des versions ultérieures. La version 1.1 est la dernière version mise à la disposition générale d'IoT Edge.
* **IoT Edge 1.2 (préversion)** offre un contenu supplémentaire pour les fonctions et fonctionnalités de la dernière préversion, [1.2-rc4](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc4).

Pour plus d'informations sur les versions d'IoT Edge, consultez [Systèmes pris en charge par Azure IoT Edge](support.md).

## <a name="version-history"></a>Historique des versions

Ce tableau fournit l’historique des versions récentes des packages IoT Edge et met en évidence les mises à jour de la documentation effectuées pour chaque version.

| Notes de publication et ressources | Type | Date | Points forts |
| ------------------------ | ---- | ---- | ---------- |
| [1.2-rc4](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc1) | PRÉVERSION | Mars 2021 | Nouveaux packages IoT Edge introduits, avec de nouvelles étapes d’installation et de configuration. Pour plus d’informations, consultez [Mise à jour de la version 1.0 ou 1.1 vers la version 1.2](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12).
| [1.1](https://github.com/Azure/azure-iotedge/releases/tag/1.1.0) | Prise en charge à long terme (LTS) | Février 2021 | [Plan de prise en charge à long terme et mises à jour des systèmes pris en charge](support.md) |
| [1.2-rc1](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc1) | PRÉVERSION | Novembre 2020 | [Appareils IoT Edge derrière des passerelles](how-to-connect-downstream-iot-edge-device.md?view=iotedge-2020-11&preserve-view=true)<br>[Répartiteur MQTT IoT Edge](how-to-publish-subscribe.md?view=iotedge-2020-11&preserve-view=true) |
| [1.0.10](https://github.com/Azure/azure-iotedge/releases/tag/1.0.10) | Stable | Octobre 2020 | [Méthode directe UploadSupportBundle](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics)<br>[Charger des métriques de runtime](how-to-access-built-in-metrics.md)<br>[Priorité de routage et durée de vie](module-composition.md#priority-and-time-to-live)<br>[Ordre de démarrage du module](module-composition.md#configure-modules)<br>[Approvisionnement manuel X.509](how-to-register-device.md) |
| [1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) | Stable | Mars 2020 | [Approvisionnement automatique X.509 avec DPS](how-to-auto-provision-x509-certs.md)<br>[Méthode directe RestartModule](how-to-edgeagent-direct-method.md#restart-module)<br>[Commande support-bundle](troubleshoot.md#gather-debug-information-with-support-bundle-command) |

## <a name="next-steps"></a>Étapes suivantes

* [Afficher toutes les versions d’Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)
* [Créer ou passer en revue les demandes de fonctionnalités sur le forum de commentaires](https://feedback.azure.com/forums/907045-azure-iot-edge)