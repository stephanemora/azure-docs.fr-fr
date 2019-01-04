---
title: Choisir le bon niveau tarifaire pour Azure Maps | Microsoft Docs
description: Découvrez les niveaux tarifaires proposés par Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 12/05/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: ee277867f449afddeb89c3fd73b5b577a68a4497
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52998381"
---
# <a name="choosing-the-right-pricing-tier-in-azure-maps"></a>Choix du bon niveau tarifaire dans Azure Maps

Azure Maps propose deux niveaux tarifaires. Cet article a pour objectif de vous aider à choisir le niveau tarifaire qui répond le mieux à vos besoins. Pour choisir le niveau tarifaire qui vous convient, posez-vous deux questions :

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Quelles fonctionnalités géospatiales ai-je besoin d’utiliser ?
Si vous pensez que les principales API géospatiales répondent à vos besoins, le niveau tarifaire S0 est celui qu’il vous faut. Si vous souhaitez des fonctionnalités plus avancées pour votre application, telles que les images aériennes+hybrides, les plages de routes ou le géocodage par batch, le niveau tarifaire S1 vous conviendra sans doute le mieux. Le tableau ci-dessous contient les **fonctionnalités associées à chaque niveau tarifaire**. Il vous permet d’avoir une meilleure idée des besoins de votre application et vous aide également à choisir le niveau tarifaire le mieux adapté à votre application.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Combien d’utilisateurs simultanés dois-je pouvoir prendre en charge ? 
Les niveaux tarifaires S0 et S1 peuvent gérer différents débits de données. Avant de choisir un niveau tarifaire Azure Maps, demandez-vous combien d’utilisateurs simultanés vous devrez prendre en charge. Le niveau tarifaire S0 peut gérer jusqu’à **50 requêtes par seconde** et le niveau tarifaire S1 peut gérer **plus de 50 requêtes par seconde**.

### <a name="pricing-tier-capabilities"></a>Fonctionnalités des niveaux tarifaires

| Fonctionnalité                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Recherche                                  |        ✓           |     ✓    |
| Routage                                 |        ✓           |     ✓    |
| Rendu                                  |        ✓           |     ✓    |
| Trafic                                 |        ✓           |     ✓    |
| Fuseaux horaires                              |        ✓           |     ✓    |
| *Imagerie aérienne hybride (préversion)     |        ✓           |     ✓    |
| *Plage de routes (préversion)                  |        ✓           |     ✓    |
| *Emplacement IP 2 (préversion)                |        ✓           |     ✓    |
| *Polygones de recherche (préversion)         |        ✓           |     ✓    |
| *Géocodage par batch (préversion)              |        ✓           |     ✓    |
| *Routage par batch (préversion)                |        ✓           |     ✓    |
| *Routage par matrice (préversion)               |        ✓           |     ✓    |

> [!Note]
> L’accès à ces fonctionnalités à partir du niveau tarifaire S0 sera déprécié après le 4 février 2019.

D’autres points de données sont intéressants, par exemple : quel type d’entreprise avez-vous ? l’application créée est-elle critique ?

Pour mieux comprendre les niveaux tarifaires S0 et S1, consultez le tableau **Niveau tarifaire et clients ciblés**. Pour plus d’informations sur les niveaux tarifaires Azure Maps, consultez [Tarification Azure Maps](https://azure.microsoft.com/pricing/details/azure-maps/). 

### <a name="pricing-tier-targeted-customers"></a>Niveau tarifaire et clients ciblés

| Niveau tarifaire  |        Clients ciblés                                                                |
|---------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>Le niveau tarifaire S0 convient aux clients qui ont une PME. Il sera adapté si vous ne prévoyez pas d’avoir un nombre élevé d’utilisateurs simultanés et si les principales API géospatiales répondent à vos besoins, comme indiqué dans le tableau ci-dessous. Ce niveau est en disponibilité générale et convient à toutes les phases de production des applications, du développement « preuve de concept » à la phase de test initiale, en passant par les applications en production et les applications déployées.<p>|
| S1            |    <p>Le niveau tarifaire S1 convient aux clients qui ont besoin d’une assistance pour les grandes entreprises, les applications stratégiques ou les gros volumes d’utilisateurs simultanés, ou qui ont besoin de services géospatiaux avancés.</p>|


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’affichage et le changement du niveau tarifaire :

> [!div class="nextstepaction"]
> [Gérer le niveau tarifaire](how-to-manage-pricing-tier.md)
