---
title: Plan de continuité d’activité - QnA Maker
titleSuffix: Azure Cognitive Services
description: L’objectif principal du plan de continuité d’activité est de créer un point de terminaison de base de connaissances résilient qui garantit qu’il n’y a aucun temps d’arrêt du bot ou de l’application qui la consomme.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: 0e748e81de39b2bef14b543063adeb51b8b3ecdb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73486714"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Créer un plan de continuité d’activité pour votre service QnA Maker

L’objectif principal du plan de continuité d’activité est de créer un point de terminaison de base de connaissances résilient qui garantit qu’il n’y a aucun temps d’arrêt du bot ou de l’application qui la consomme.

![Plan de continuité d’activité QnA Maker](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

L’idée générale représentée plus haut est la suivante :

1. Configurez deux [services QnA Maker](../How-To/set-up-qnamaker-service-azure.md) parallèles dans des [régions Azure associées](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

2. Synchronisez les index de recherche Azure principal et secondaire. Utilisez l’exemple GitHub [ici](https://github.com/pchoudhari/QnAMakerBackupRestore) pour savoir comment sauvegarder/restaurer les index Azure.

3. Sauvegardez Application Insights avec [exportation continue](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry).

4. Une fois que les piles principale et secondaire ont été configurées, utilisez [Gestionnaire de trafic](https://docs.microsoft.com/azure/traffic-manager/) pour configurer les deux points de terminaison et définir une méthode de routage.

5. Vous devez créer un certificat SSL pour le point de terminaison de votre gestionnaire de trafic. [Liez le certificat SSL](https://docs.microsoft.com/azure/app-service/configure-ssl-bindings) dans vos services d’application.

6. Enfin, utilisez le point de terminaison du gestionnaire de trafic dans votre bot ou dans votre application.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Choisir la capacité pour le déploiement de votre service QnA Maker](../Tutorials/choosing-capacity-qnamaker-deployment.md)
