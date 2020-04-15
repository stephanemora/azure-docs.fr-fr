---
title: Plan de continuité d’activité - QnA Maker
description: L’objectif principal du plan de continuité d’activité est de créer un point de terminaison de base de connaissances résilient qui garantit qu’il n’y a aucun temps d’arrêt du bot ou de l’application qui la consomme.
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: 5d1501ecc42fe948959075cec7d728f6c9df908a
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887064"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Créer un plan de continuité d’activité pour votre service QnA Maker

L’objectif principal du plan de continuité d’activité est de créer un point de terminaison de base de connaissances résilient qui garantit qu’il n’y a aucun temps d’arrêt du bot ou de l’application qui la consomme.

## <a name="business-continuity-with-traffic-manager"></a>Continuité d'activité avec Traffic Manager

> [!div class="mx-imgBorder"]
> ![Plan de continuité d’activité QnA Maker](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

L’idée générale représentée plus haut est la suivante :

1. Configurez deux [services QnA Maker](set-up-qnamaker-service-azure.md) parallèles dans des [régions Azure associées](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

2. [Sauvegardez](../../../app-service/manage-backup.md) votre service d'application QnA Maker principal et [restaurez-le](../../../app-service/web-sites-restore.md) dans la configuration secondaire. Ainsi, les deux configurations fonctionneront avec les mêmes nom d'hôte et clés.

3. Synchronisez les index de recherche Azure principal et secondaire. Utilisez l’exemple GitHub [ici](https://github.com/pchoudhari/QnAMakerBackupRestore) pour savoir comment sauvegarder/restaurer les index Azure.

4. Sauvegardez Application Insights avec [exportation continue](../../../application-insights/app-insights-export-telemetry.md).

5. Une fois que les piles principale et secondaire ont été configurées, utilisez [Gestionnaire de trafic](../../../traffic-manager/traffic-manager-overview.md) pour configurer les deux points de terminaison et définir une méthode de routage.

6. Vous devez créer un certificat Transport Layer Security (TLS), anciennement Secure Sockets Layer (SSL), pour le point de terminaison de votre gestionnaire de trafic. [Liez le certificat TLS/SSL](../../../app-service/configure-ssl-bindings.md) dans vos services d’application.

7. Enfin, utilisez le point de terminaison du gestionnaire de trafic dans votre bot ou dans votre application.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Choisir une capacité](./improve-knowledge-base.md)