---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2019
ms.openlocfilehash: 200e2dfd2dd4f9aedd9256b307491a0b207ea124
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57964135"
---
Requêtes dans le conteneur sont facturées au niveau de tarification de la ressource Azure utilisé pour le `<ApiKey>`.

Les conteneurs de Services cognitifs ne sont pas concédés sous licence pour s’exécuter sans être connectés au point de terminaison de facturation pour le contrôle. Les clients doivent activer les conteneurs de communiquer des informations de facturation avec point de terminaison de facturation en permanence. Les conteneurs Cognitive Services n’envoient pas de données relatives aux clients (par exemple, l’image ou le texte en cours d’analyse) à Microsoft. 

### <a name="connecting-to-azure"></a>Connexion à Azure

Le conteneur doit les valeurs d’argument facturation à exécuter. Ces valeurs permettent le conteneur pour se connecter au point de terminaison de facturation. Le conteneur crée des rapports sur l’utilisation toutes les 10 à 15 minutes. Si le conteneur ne connecte pas au sein de la fenêtre de temps autorisée vers Azure, le conteneur continuera à fonctionner, mais elle ne traite pas les requêtes jusqu'à ce que le point de terminaison de facturation est restauré. La connexion est exécutée 10 fois sur le même intervalle de temps de 10 à 15 minutes. S’il ne peut pas se connecter au point de terminaison de facturation dans les 10 tentatives, le conteneur s’arrête en cours d’exécution. 

### <a name="billing-arguments"></a>Arguments de facturation

Les trois options suivantes doivent être spécifiées avec les valeurs valides dans l’ordre pour la `docker run` commande pour démarrer le conteneur :

| Option | Description |
|--------|-------------|
| `ApiKey` | Clé API de la ressource Service cognitif utilisée pour le suivi des informations de facturation.<br/>La valeur de cette option doit être définie sur une clé API pour la ressource approvisionnée spécifiée dans `Billing`. |
| `Billing` | Clé API de la ressource Service cognitif utilisée pour le suivi des informations de facturation.<br/>La valeur de cette option doit être définie sur l’URI de point de terminaison d’une ressource LUIS Azure provisionnée.|
| `Eula` | Indique que vous avez accepté la licence pour le conteneur.<br/>La valeur de cette option doit être `accept`. |


