---
title: Créer un plan de continuité d’activité pour votre service QnA Maker - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Comment créer un plan de continuité d’activité pour votre service QnA Maker
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: ca6e54b8a8ca8b38e8ef6b1a148f8b2c54bd43da
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35369577"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Créer un plan de continuité d’activité pour votre service QnA Maker

L’objectif principal du plan de continuité d’activité est de créer un point de terminaison de base de connaissances résilient qui garantit qu’il n’y a aucun temps d’arrêt du bot ou de l’application qui la consomme.

![Plan de continuité d’activité QnA Maker](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

L’idée générale représentée plus haut est la suivante :

1. Configurez deux [services QnA Maker](../How-To/set-up-qnamaker-service-azure.md) parallèles dans des [régions Azure associées](https://docs.microsoft.com/en-us/azure/best-practices-availability-paired-regions).

2. Synchronisez les index de recherche Azure principal et secondaire. Utilisez l’exemple github [ici](https://github.com/pchoudhari/QnAMakerBackupRestore) pour voir comment la sauvegarde-restauration Azure s’indexe.

3. Sauvegardez Application Insights avec [exportation continue](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-export-telemetry).

4. Une fois que les piles principale et secondaire ont été configurées, utilisez [Gestionnaire de trafic](https://docs.microsoft.com/en-us/azure/traffic-manager/) pour configurer les deux points de terminaison et définir une méthode de routage.

5. Vous devez créer un certificat SSL pour le point de terminaison de votre gestionnaire de trafic. [Liez le certificat SSL](https://docs.microsoft.com/en-us/azure/app-service/app-service-web-tutorial-custom-ssl) dans vos services d’application.

6. Enfin, utilisez le point de terminaison du gestionnaire de trafic dans votre bot ou dans votre application.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Choisir la capacité pour le déploiement de votre service QnA Maker](../Tutorials/choosing-capacity-qnamaker-deployment.md)
