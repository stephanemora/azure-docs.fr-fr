---
title: Alertes de sécurité personnalisées pour IoT Hub
description: Découvrez les alertes de sécurité personnalisables et les actions de correction recommandées en utilisant les fonctionnalités et le service Defender pour IoT Hub.
ms.topic: conceptual
ms.date: 2/16/2021
ms.openlocfilehash: d7a58bcdb759c3f31290cc7930eba6ca52fcc17b
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784728"
---
# <a name="defender-for-iot-hub-custom-security-alerts"></a>Alertes de sécurité personnalisées Defender pour IoT Hub

Defender pour IoT analyse continuellement votre solution IoT à l’aide d’analyses avancées et d’informations sur les menaces pour vous alerter en cas d’activités malveillantes.

Nous vous encourageons à créer des alertes personnalisées en fonction de votre connaissance du comportement attendu de l’appareil afin de veiller à ce que les alertes servent d'indicateurs efficaces en cas de compromission potentielle au sein du déploiement et du paysage uniques de votre organisation.

Vous pouvez définir les alertes Defender pour IoT suivantes selon le comportement attendu de votre hub IoT. Pour plus d’informations sur la personnalisation de chaque alerte, consultez [Créer des alertes personnalisées](quickstart-create-custom-alerts.md).

## <a name="built-in-custom-alerts-in-the-iot-hub"></a>Alertes intégrées et personnalisées dans IoT Hub

| severity | Nom de l’alerte | Source de données | Description | Correction suggérée |
|--|--|--|--|--|
| Faible | Alerte personnalisée : le nombre de messages cloud-à-appareil dans le protocole AMQP est en dehors de la plage autorisée | IoT Hub | Le nombre de messages cloud-à-appareil (protocole AMQP) dans une fenêtre de temps spécifique est en dehors de la plage autorisée actuellement configurée. |  |
| Faible | Alerte personnalisée : le nombre de messages cloud-à-appareil rejetés dans le protocole AMQP est en dehors de la plage autorisée | IoT Hub | Le nombre de messages cloud-à-appareil (protocole AMQP) rejetés par l’appareil dans une fenêtre de temps spécifique est en dehors de la plage autorisée actuellement configurée. |  |
| Faible | Alerte personnalisée : le nombre de messages appareil-à-cloud dans le protocole AMQP est en dehors de la plage autorisée | IoT Hub | Le nombre de messages appareil-à-cloud (protocole AMQP) dans une fenêtre de temps spécifique est en dehors de la plage autorisée actuellement configurée. |  |
| Faible | Alerte personnalisée : le nombre d’appels de méthode directe est en dehors de la plage autorisée | IoT Hub | Le nombre d’appels de méthode directe dans une fenêtre de temps spécifique est en dehors de la plage autorisée actuellement configurée. |  |
| Faible | Alerte personnalisée : le nombre de chargements de fichiers est en dehors de la plage autorisée | IoT Hub | Le nombre de nombre de chargements de fichiers dans une fenêtre de temps spécifique est en dehors de la plage autorisée actuellement configurée. |  |
| Faible | Alerte personnalisée : le nombre de messages cloud-à-appareil dans le protocole HTTP est en dehors de la plage autorisée | IoT Hub | La quantité de messages cloud-à-appareil (protocole HTTP) dans une fenêtre de temps n’est pas dans la plage autorisée configurée |
| Faible | Alerte personnalisée : le nombre de messages cloud-à-appareil rejetés dans le protocole HTTP est en dehors de la plage autorisée | IoT Hub | Le nombre de messages cloud-à-appareil (protocole HTTP) dans une fenêtre de temps spécifique est en dehors de la plage autorisée actuellement configurée. |
| Faible | Alerte personnalisée : le nombre de messages appareil-à-cloud dans le protocole HTTP est en dehors de la plage autorisée | IoT Hub | Le nombre de messages appareil-à-cloud (protocole HTTP) dans une fenêtre de temps spécifique est en dehors de la plage autorisée actuellement configurée. |  |
| Faible | Alerte personnalisée : le nombre de messages cloud-à-appareil dans le protocole MQTT est en dehors de la plage autorisée | IoT Hub | Le nombre de messages cloud-à-appareil (protocole MQTT) dans une fenêtre de temps spécifique est en dehors de la plage autorisée actuellement configurée. |  |
| Faible | Alerte personnalisée : le nombre de messages cloud-à-appareil rejetés dans le protocole MQTT est en dehors de la plage autorisée | IoT Hub | Le nombre de messages cloud-à-appareil (protocole MQTT) rejetés par l’appareil dans une fenêtre de temps spécifique est en dehors de la plage autorisée actuellement configurée. |
| Faible | Alerte personnalisée : le nombre de messages appareil-à-cloud dans le protocole MQTT est en dehors de la plage autorisée | IoT Hub | Le nombre de messages appareil-à-cloud (protocole MQTT) dans une fenêtre de temps spécifique est en dehors de la plage autorisée actuellement configurée. |
| Faible | Alerte personnalisée : le nombre de vidages de file d’attente de commandes est en dehors de la plage autorisée | IoT Hub | Le nombre de vidages de file d’attente de commandes dans une fenêtre de temps spécifique est en dehors de la plage autorisée actuellement configurée. |  |
| Faible | Alerte personnalisée : le nombre de mises à jour de jumeau de module est en dehors de la plage autorisée | IoT Hub | Le nombre de mises à jour de jumeau de module dans une fenêtre de temps spécifique est en dehors de la plage autorisée actuellement configurée. |
| Faible | Alerte personnalisée : le nombre d’opérations non autorisées est en dehors de la plage autorisée | IoT Hub | Le nombre d’opérations non autorisées dans une fenêtre de temps spécifique est en dehors de la plage autorisée actuellement configurée. |

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [personnaliser une alerte](quickstart-create-custom-alerts.md)
- [Vue d’ensemble](overview.md) du service Defender pour IoT
- Découvrez comment [accéder à vos données de sécurité](how-to-security-data-access.md)
- En savoir plus sur [l’examen d’un appareil](how-to-investigate-device.md)
