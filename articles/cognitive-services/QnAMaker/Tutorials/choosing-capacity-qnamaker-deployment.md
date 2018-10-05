---
title: Capacité de ressources pour un déploiement - QnA Maker
titleSuffix: Azure Cognitive Services
description: Guide permettant de choisir la capacité de votre déploiement QnA Maker
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: 01548cf2de8db8f4dc9984598a5e5544bf97fd49
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47432645"
---
# <a name="choosing-capacity-for-your-qna-maker-deployment"></a>Choisir la capacité de votre déploiement QnA Maker

Le service QnA Maker prend une dépendance sur trois ressources Azure :
1.  App Service (pour l’exécution)
2.  Recherche Azure (pour le stockage des QnA)
3.  App Insights (facultatif, pour le stockage des journaux de conversation et les données de télémétrie)

Avant de créer votre service QnA Maker, vous devez décider quel niveau des services ci-dessus vous convient. 

En général, il existe trois paramètres dont vous devez tenir compte :
1. **Le débit dont vous avez besoin à partir du service** : sélectionnez le [plan d’application](https://azure.microsoft.com/en-in/pricing/details/app-service/plans/) pour votre App Service en fonction de vos besoins. Vous pouvez faire [monter en puissance](https://docs.microsoft.com/azure/app-service/web-sites-scale) ou faire descendre en puissance l’application. Ceci doit également influencer votre sélection de la référence SKU de la Recherche Azure. Consultez plus d’informations [ici](https://docs.microsoft.com/azure/search/search-sku-tier).

2. **La taille et le nombre de bases de connaissances** : choisissez la [référence SKU de Recherche Azure](https://azure.microsoft.com/en-in/pricing/details/search/) adaptée à votre scénario. Vous pouvez publier N-1 bases de connaissances dans un niveau spécifique, où N correspond au nombre maximal d’index autorisé dans le niveau. Vérifiez également la taille maximale et le nombre de documents autorisés par niveau.

3. **Le nombre de documents en tant que sources** : la référence SKU Gratuite du service d’administration de QnA Maker limite à trois le nombre de documents (d’une taille de 1 Mo chacun) que vous pouvez gérer par le biais du portail et des API. La référence SKU Standard n’a aucune limite en termes de nombre de documents que vous pouvez gérer. Vous trouverez plus de détails [ici](https://aka.ms/qnamaker-pricing).

Le tableau suivant vous donne des indications d’ordre général.

|                        | Administration de QnA Maker | App Service | Recherche Azure | Limites                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| Expérimentation        | Référence SKU Gratuite             | Niveau Gratuit   | Niveau Gratuit    | Publier jusqu’à 2 Ko, taille de 50 Mo  |
| Environnement de test/développement   | Référence SKU standard         | Partagé      | De base        | Publier jusqu’à 14 Ko, taille de 2 Go    |
| Environnement de production | Référence SKU standard         | De base       | standard     | Publier jusqu’à 49 Ko, taille de 25 Go |

Pour mettre à niveau votre pile QnA Maker, consultez [Mettre à niveau votre service QnA Maker](../How-To/upgrade-qnamaker-service.md).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Mettre à niveau votre service QnA Maker](../How-To/upgrade-qnamaker-service.md)
