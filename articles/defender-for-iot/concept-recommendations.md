---
title: Recommandations de sécurité
description: Découvrez le concept des recommandations de sécurité et la manière dont elles sont utilisées dans Defender pour IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/25/2021
ms.author: shhazam
ms.openlocfilehash: d11154e26e2ffe97a1ea102a11dddb746db9433a
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809267"
---
# <a name="security-recommendations"></a>Recommandations de sécurité

Defender pour IoT analyse vos ressources Azure et appareils IoT, et fournit des suggestions de sécurité visant à réduire votre surface d’attaque.
Les recommandations de sécurité sont actionnables et visent à aider les clients à se conformer aux bonnes pratiques de sécurité.

Vous trouverez dans cet article une liste de recommandations qui peuvent être déclenchées sur votre hub IoT et/ou sur des appareils IoT.

## <a name="agent-based-recommendations"></a>Recommandations basées sur un agent :

Les recommandations portant sur les appareils offrent des insights et des suggestions d’amélioration de la posture de sécurité des appareils.

| severity | Name | source de données | Description |
|--|--|--|--|
| Moyenne | Ports ouverts sur l’appareil | Module de sécurité classique | Un point de terminaison d’écoute a été trouvé sur l’appareil. |
| Moyenne | Stratégie de pare-feu permissive détectée dans l’une des chaînes | Module de sécurité classique | Une stratégie de pare-feu autorisée a été trouvée (ENTRÉE/SORTIE). Une stratégie de pare-feu doit par défaut refuser tout le trafic et définir des règles pour autoriser la communication nécessaire à l’appareil. |
| Moyenne | Règle de pare-feu permissive détectée dans la chaîne d’entrée | Module de sécurité classique | Une règle contenant un modèle permissif pour un large éventail d’adresses IP ou de ports a été trouvée dans le pare-feu. |
| Moyenne | Règle de pare-feu permissive détectée dans la chaîne de sortie | Module de sécurité classique | Une règle contenant un modèle permissif pour un large éventail d’adresses IP ou de ports a été trouvée dans le pare-feu. |
| Moyenne | Échec de l’opération de validation du système par rapport à la référence | Module de sécurité classique | L’appareil n’est pas conforme aux [benchmarks CIS Linux](https://www.cisecurity.org/cis-benchmarks/). |

### <a name="agent-based-operational-recommendations"></a>Recommandations opérationnelles basées sur un agent

Les recommandations opérationnelles offrent des insights et des suggestions d’amélioration de la configuration de l’agent de sécurité.

| severity | Name | source de données | Description |
|--|--|--|--|
| Faible | L’agent envoie des messages inutilisés | Module de sécurité classique | La taille d’au moins 10 % des messages de sécurité était inférieure à 4 Ko au cours des dernières 24 heures. |
| Faible | Configuration du jumeau de sécurité non optimale | Module de sécurité classique | La configuration du jumeau de sécurité n’est pas optimale. |
| Faible | Conflit de configuration du jumeau de sécurité | Module de sécurité classique | Des conflits ont été identifiés dans la configuration du jumeau de sécurité. |  |


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
