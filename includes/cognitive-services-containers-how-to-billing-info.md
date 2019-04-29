---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 04/16/2019
ms.openlocfilehash: e92d1c65d9601c23e7e785f07e2de3e43ea6612b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60598915"
---
Requêtes dans le conteneur sont facturées au niveau de tarification de la ressource Azure utilisé pour le `<ApiKey>`.

Les conteneurs de Services cognitifs ne sont pas concédés sous licence pour s’exécuter sans être connectés au point de terminaison de facturation pour le contrôle. Les clients doivent activer les conteneurs de communiquer des informations de facturation avec point de terminaison de facturation en permanence. Les conteneurs Cognitive Services n’envoient pas de données relatives aux clients (par exemple, l’image ou le texte en cours d’analyse) à Microsoft. 

### <a name="connecting-to-azure"></a>Connexion à Azure

Le conteneur doit les valeurs d’argument facturation à exécuter. Ces valeurs permettent le conteneur pour se connecter au point de terminaison de facturation. La conteneur crée des rapports sur l'utilisation toutes les 10 à 15 minutes. Si le conteneur ne connecte pas au sein de la fenêtre de temps autorisée vers Azure, le conteneur continuera à fonctionner, mais elle ne traite pas les requêtes jusqu'à ce que le point de terminaison de facturation est restauré. La connexion est exécutée 10 fois sur le même intervalle de temps de 10 à 15 minutes. S’il ne peut pas se connecter au point de terminaison de facturation dans les 10 tentatives, le conteneur s’arrête en cours d’exécution. 

### <a name="billing-arguments"></a>Arguments de facturation

Les trois options suivantes doivent être spécifiées avec les valeurs valides dans l’ordre pour la `docker run` commande pour démarrer le conteneur :

| Option | Description |
|--------|-------------|
| `ApiKey` | Clé API de la ressource Service cognitif utilisée pour le suivi des informations de facturation.<br/>La valeur de cette option doit être définie sur une clé API pour la ressource approvisionnée spécifiée dans `Billing`. |
| `Billing` | Clé API de la ressource Service cognitif utilisée pour le suivi des informations de facturation.<br/>La valeur de cette option doit être définie au point de terminaison URI d’une ressource Azure configurée.|
| `Eula` | Indique que vous avez accepté la licence pour le conteneur.<br/>La valeur de cette option doit être `accept`. |


