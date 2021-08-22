---
title: Recommandations de sécurité pour IoT Hub
description: Découvrez le concept des recommandations de sécurité et comment elles sont utilisées dans Defender pour IoT Hub.
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: a9e33248354aab659694e39df605cc070fdaaf73
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113015062"
---
# <a name="security-recommendations-for-iot-hub"></a>Recommandations de sécurité pour IoT Hub

Defender pour IoT analyse vos ressources Azure et appareils IoT, et fournit des suggestions de sécurité visant à réduire votre surface d’attaque.
Les recommandations de sécurité sont actionnables et visent à aider les clients à se conformer aux bonnes pratiques de sécurité.

Vous trouverez dans cet article une liste de recommandations qui peuvent être déclenchées sur votre hub IoT.

## <a name="built-in-recommendations-in-iot-hub"></a>Recommandations intégrées dans IoT Hub

Les alertes de recommandation offrent des insights et des suggestions d’actions visant à améliorer la posture de sécurité de l’environnement.

| severity | Name | source de données | Description |
|--|--|--|--|
| Élevé | Informations d’authentification identiques utilisées par plusieurs appareils | IoT Hub | Des informations d’authentification IoT Hub sont utilisées par plusieurs appareils, Ce processus peut indiquer qu’un appareil illégitime usurpe l’identité d’un appareil légitime. Les doublons d’informations d’identification augmentent le risque d’emprunt d’identité de l’appareil par un acteur malveillant. |
| Moyenne | La stratégie de filtre IP par défaut devrait être de refuser | IoT Hub | La configuration du filtre IP devrait comporter des règles concernant le trafic autorisé et, par défaut, refuser le reste du trafic. |
| Moyenne | Grande plage d’adresses IP dans une règle de filtre IP | IoT Hub | La plage d’adresses IP source d’une des règles de filtre IP Autoriser est trop grande. Des règles trop permissives risquent d’exposer le hub IoT à des personnes malveillantes. |
| Faible | Activer les journaux de diagnostic dans IoT Hub | IoT Hub | Activez les journaux d’activité et conservez-les pendant jusqu’à un an. Le fait de conserver les journaux permet de recréer les pistes d’activités à des fins d’investigation en cas d’incident de sécurité ou de compromission du réseau. |

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble](overview.md) du service Defender pour IoT
- Découvrez comment [accéder à vos données de sécurité](how-to-security-data-access.md)
- En savoir plus sur [l’examen d’un appareil](how-to-investigate-device.md)
