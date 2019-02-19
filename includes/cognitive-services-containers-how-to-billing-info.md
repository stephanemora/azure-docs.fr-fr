---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 002/08/2019
ms.openlocfilehash: ce7d8628c28a4a202a05aeea60e71655b4b7f1a2
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2019
ms.locfileid: "55984903"
---
Les conteneurs Cognitives Services ne sont pas concédés sous licence pour s’exécuter sans être connectés à Azure pour le contrôle. Les clients doivent configurer les conteneurs de manière à ce qu’ils communiquent les informations de facturation au service de contrôle à tout moment. Les conteneurs Cognitive Services n’envoient pas de données client (l’énoncé) à Microsoft. Le conteneur crée des rapports sur l'utilisation toutes les 10 à 15 minutes.

`docker run` utilise les arguments suivants lors de la facturation :

| Option | Description |
|--------|-------------|
| `ApiKey` | Clé API de la ressource Service cognitif utilisée pour le suivi des informations de facturation.<br/>La valeur de cette option doit être définie sur une clé API pour la ressource approvisionnée spécifiée dans `Billing`. |
| `Billing` | Clé API de la ressource Service cognitif utilisée pour le suivi des informations de facturation.<br/>La valeur de cette option doit être définie sur l’URI de point de terminaison d’une ressource LUIS Azure provisionnée.|
| `Eula` | Indique que vous avez accepté la licence pour le conteneur.<br/>La valeur de cette option doit être `accept`. |

> [!IMPORTANT]
> Les trois options doivent être spécifiées avec des valeurs valides ; sinon, le conteneur ne démarre pas.
