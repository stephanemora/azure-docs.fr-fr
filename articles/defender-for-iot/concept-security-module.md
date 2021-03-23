---
title: Micro-agent Defender-IoT et jumeaux d’appareil
description: Découvrez le concept de jumeaux de micro-agent Defender-IoT et leur utilisation dans Defender pour IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2019
ms.author: mlottner
ms.openlocfilehash: 552da329b90b102a13ef53158ec81be87684c1fc
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103493184"
---
# <a name="defender-iot-micro-agent"></a>Micro-agent Defender-IoT

Cet article explique la manière dont Defender pour IoT utilise les jumeaux et les modules d’appareils.

## <a name="device-twins"></a>Jumeaux d’appareil

Pour les solutions IoT intégrées à Azure, les jumeaux d’appareil jouent un rôle essentiel dans la gestion d’appareils et l’automatisation des processus.

Defender pour IoT offre une intégration complète à votre plateforme de gestion d’appareils IoT existants, ce qui vous permet de gérer l’état de sécurité de vos appareils tout en utilisant les fonctionnalités existantes de contrôle des appareils. L’intégration est obtenue par l’utilisation du mécanisme de jumelage IoT Hub.

En savoir plus sur le concept de [jumeaux d’appareils](../iot-hub/iot-hub-devguide-device-twins.md) dans Azure IoT Hub.

## <a name="defender-iot-micro-agent-twins"></a>Jumeaux de micro-agent Defender-IoT

Defender pour IoT maintient un jumeau de micro-agent Defender-IoT pour chaque appareil dans le service.
Le jumeau de micro-agent Defender-IoT contient toutes les informations relatives à la sécurité de l’appareil pour chaque appareil spécifique de votre solution.
Les propriétés de sécurité d’appareil sont conservées dans un jumeau de micro-agent Defender-IoT dédié, pour une communication plus sûre et pour permettre des mises à jour et une maintenance nécessitant moins de ressources.

Consultez [Créer un jumeau de micro-agent Defender-IoT](quickstart-create-security-twin.md) et [Configurer des agents de sécurité](how-to-agent-configuration.md) pour découvrir comment créer, personnaliser et configurer le jumeau. Consultez [Comprendre les jumeaux de module IoT Hub](../iot-hub/iot-hub-devguide-module-twins.md) pour en savoir plus sur le concept des jumeaux de module dans IoT Hub.

## <a name="see-also"></a>Voir aussi

- [Vue d’ensemble de Defender pour IoT](overview.md)
- [Déployer des agents de sécurité](how-to-deploy-agent.md)
- [Méthode d'authentification des agents de sécurité](concept-security-agent-authentication-methods.md)