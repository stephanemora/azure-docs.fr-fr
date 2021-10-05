---
title: Recommandations de sécurité pour IoT Hub
description: Découvrez le concept des recommandations de sécurité et comment elles sont utilisées dans Defender pour IoT Hub.
ms.topic: conceptual
ms.date: 09/26/2021
ms.openlocfilehash: 6e4af6e4cadbd45e91219d2fce6d87c4fb555ece
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2021
ms.locfileid: "129083331"
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
| Élevé | Informations d’identification d’authentification identiques utilisées par plusieurs appareils | IoT Hub | Des informations d’authentification IoT Hub sont utilisées par plusieurs appareils, Cela peut indiquer qu’un appareil illégitime emprunte l’identité d’un appareil légitime et expose également le risque d’emprunt d’identité d’appareil par un acteur malveillant. |
| Élevé | Autorisations de haut niveau configurées dans le jumeau de modèle IoT Edge pour le module IoT Edge | IoT Hub | Le module IoT Edge est configuré pour s’exécuter en mode privilégié, avec des fonctionnalités Linux étendues ou avec un accès réseau au niveau de l’hôte (envoyer/recevoir des données à l’ordinateur hôte). |
| Moyenne | Principal de service non utilisé avec le référentiel ACR | IoT Hub | Le schéma d’authentification utilisé pour extraire un module IoT Edge à partir d’un référentiel ACR n’utilise pas l’authentification du principal de service. |
| Moyenne | Mise à niveau de la suite de chiffrement TLS requise | IoT Hub | Configurations TLS non sécurisées détectées. Mise à niveau immédiate de la suite de chiffrement TLS recommandée. |
| Moyenne | La stratégie de filtre IP par défaut devrait être de refuser | IoT Hub | La configuration du filtre IP doit avoir des règles définies pour le trafic autorisé et refuser tout autre trafic par défaut. |
| Moyenne | La règle de filtre IP inclut une grande plage d’adresses IP | IoT Hub | La plage d’adresses IP source d’une des règles de filtre IP autorisable est trop grande. Des règles trop permissives risquent d’exposer le hub IoT à des personnes malveillantes. |
| Moyenne | SecurityGroup a des paramètres de module incohérents | IoT Hub | Dans ce groupe de sécurité d’appareils, un appareil anormal a des paramètres de module IoT Edge incohérents par rapport au reste du groupe de sécurité. |
| Faible | La mémoire du Hub IoT Edge peut être optimisée | IoT Hub | Optimisez l’utilisation de la mémoire de votre IoT Edge Hub en désactivant les en-têtes de protocole pour tous les protocoles qui ne sont pas utilisés par les modules Edge dans votre solution. |
| Faible | Aucune journalisation configurée pour le module IoT Edge | IoT Hub | La journalisation est désactivée pour ce module de IoT Edge. |

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble](overview.md) du service Defender pour IoT
- Découvrez comment [accéder à vos données de sécurité](how-to-security-data-access.md)
- En savoir plus sur [l’examen d’un appareil](how-to-investigate-device.md)
