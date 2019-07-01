---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 04/16/2019
ms.openlocfilehash: bfda8b83f1bedf11151ba89b58c95347aa35839a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052046"
---
Les requêtes sur le conteneur sont facturées au niveau tarifaire de la ressource Azure utilisée pour `<ApiKey>`.

Les conteneurs Azure Cognitives Services ne sont pas concédés sous licence dans le but de s’exécuter sans être connectés au point de terminaison de facturation pour le contrôle. Vous devez configurer les conteneurs de manière à ce qu’ils communiquent les informations de facturation au point de terminaison de facturation à tout moment. Les conteneurs Cognitive Services n’envoient pas de données client, telles que l’image ou le texte analysé, à Microsoft. 

### <a name="connect-to-azure"></a>Connexion à Azure

Le conteneur a besoin des valeurs d’arguments de facturation pour s’exécuter. Ces valeurs permettent au conteneur de se connecter au point de terminaison de facturation. Le conteneur crée des rapports sur l’utilisation toutes les 10 à 15 minutes. Si le conteneur ne se connecte pas à Azure dans la fenêtre de temps imparti, il continue de s’exécuter, mais ne traite pas les requêtes tant que le point de terminaison de facturation n’est pas restauré. Une tentative de connexion est effectuée 10 fois, toutes les 10 à 15 minutes. S’il ne parvient pas à se connecter au point de terminaison de facturation au cours de ces 10 essais, le conteneur arrête son exécution. 

### <a name="billing-arguments"></a>Arguments de facturation

Pour que la commande `docker run` démarre le conteneur, des valeurs valides doivent spécifier les trois options suivantes :

| Option | Description |
|--------|-------------|
| `ApiKey` | Clé API de la ressource Cognitive Services utilisée pour le suivi des informations de facturation.<br/>La valeur de cette option doit être définie sur une clé API pour la ressource provisionnée spécifiée dans `Billing`. |
| `Billing` | Point de terminaison de la ressource Cognitive Services utilisé pour le suivi des informations de facturation.<br/>La valeur de cette option doit être définie sur l’URI de point de terminaison d’une ressource Azure provisionnée.|
| `Eula` | Indique que vous avez accepté la licence pour le conteneur.<br/>La valeur de cette option doit être définie sur **accepter**. |


