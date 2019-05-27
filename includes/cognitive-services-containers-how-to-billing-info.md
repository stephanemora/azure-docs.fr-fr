---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 04/16/2019
ms.openlocfilehash: 05961f8dd2788179a15e6bfe094484cf4770067b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66124341"
---
Les requêtes sur le conteneur sont facturées au niveau tarifaire de la ressource Azure utilisée pour `<ApiKey>`.

Les conteneurs Azure Cognitives Services ne sont pas concédés sous licence dans le but de s’exécuter sans être connectés au point de terminaison de facturation pour le contrôle. Vous devez configurer les conteneurs de manière à ce qu’ils communiquent les informations de facturation au point de terminaison de facturation à tout moment. Les conteneurs Cognitive Services n’envoient pas de données client, telles que l’image ou le texte analysé, à Microsoft. 

### <a name="connect-to-azure"></a>Connexion à Azure

Le conteneur a besoin des valeurs d’arguments de facturation pour s’exécuter. Ces valeurs permettent au conteneur de se connecter au point de terminaison de facturation. Le conteneur crée des rapports sur l’utilisation toutes les 10 à 15 minutes. Si le conteneur ne se connecte pas à Azure dans la fenêtre de temps imparti, il continue de s’exécuter, mais ne traite pas les requêtes tant que le point de terminaison de facturation n’est pas restauré. Une tentative de connexion est effectuée 10 fois, toutes les 10 à 15 minutes. S’il ne parvient pas à se connecter au point de terminaison de facturation au cours de ces 10 essais, le conteneur arrête son exécution. 

### <a name="billing-arguments"></a>Arguments de facturation

Des valeurs valides doivent spécifier les trois options suivantes pour que la commande `docker run` démarre le conteneur.

| Option | Description |
|--------|-------------|
| `ApiKey` | Clé API de la ressource Cognitive Services utilisée pour le suivi des informations de facturation.<br/>La valeur de cette option doit être définie sur une clé API pour la ressource approvisionnée spécifiée dans `Billing`. |
| `Billing` | Point de terminaison de la ressource Cognitive Services utilisé pour le suivi des informations de facturation.<br/>La valeur de cette option doit être définie sur l’URI de point de terminaison d’une ressource Azure provisionnée.|
| `Eula` | Indique que vous avez accepté la licence pour le conteneur.<br/>La valeur de cette option doit être `accept`. |


