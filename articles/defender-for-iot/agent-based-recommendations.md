---
title: Recommandations basées sur des agents
titleSuffix: Azure Defender for IoT
description: Découvrez le concept des recommandations de sécurité et comment elles sont utilisées pour les appareils Defender pour IoT.
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
ms.date: 02/16/2021
ms.author: shhazam
ms.openlocfilehash: c7407db1460c2d0a83f2262a3348c81b4de300a7
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100640986"
---
# <a name="security-recommendations-for-iot-devices"></a>Recommandations de sécurité pour les appareils IoT

Defender pour IoT analyse vos ressources Azure et appareils IoT, et fournit des suggestions de sécurité visant à réduire votre surface d’attaque.
Les recommandations de sécurité sont actionnables et visent à aider les clients à se conformer aux bonnes pratiques de sécurité.

Vous trouverez dans cet article une liste de recommandations qui peuvent être déclenchées sur vos appareils IoT.

## <a name="agent-based-recommendations"></a>Recommandations basées sur des agents

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

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble](overview.md) du service Defender pour IoT
- Découvrez comment [accéder à vos données de sécurité](how-to-security-data-access.md)
- En savoir plus sur [l’examen d’un appareil](how-to-investigate-device.md)
