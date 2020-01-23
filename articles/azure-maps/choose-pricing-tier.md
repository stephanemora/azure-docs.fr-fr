---
title: Choisir le bon niveau tarifaire | Microsoft Azure Maps
description: Dans cet article, vous allez découvrir les différents niveaux tarifaires proposés par Microsoft Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 01/02/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 5ff774f9848db948058075a98504e6c13db5467a
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911762"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Choix du bon niveau tarifaire dans Azure Maps

Azure Maps propose deux niveaux tarifaires. Cet article a pour objectif de vous aider à choisir le niveau tarifaire qui répond le mieux à vos besoins. Pour choisir le niveau tarifaire qui vous convient, posez-vous les deux questions suivantes :

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Quelles fonctionnalités géospatiales ai-je besoin d’utiliser ?
Le niveau tarifaire S0 vous convient si les principales API géospatiales répondent aux besoins de votre service. Si vous souhaitez des fonctionnalités plus avancées pour votre application, le niveau tarifaire S1 vous convient sans doute le mieux. Les images aériennes+hybrides, le calcul d’itinéraires par lots ou le géocodage par lots sont des exemples de fonctionnalités. Le tableau des **fonctionnalités du niveau tarifaire** qui suit vous donne une meilleure idée des besoins de votre application. Il vous permet également de choisir un niveau tarifaire plus approprié pour votre application.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Combien d’utilisateurs simultanés dois-je pouvoir prendre en charge ? 
Les niveaux tarifaires S0 et S1 gèrent différents débits de données. Avant de choisir un niveau tarifaire Azure Maps, posez-vous des questions. Par exemple, « combien d’utilisateurs simultanés dois-je pouvoir prendre en charge ? » Le niveau tarifaire S0 gère jusqu’à **50 requêtes par seconde**. Le niveau tarifaire S1 gère **plus de 50 requêtes par seconde**.

### <a name="pricing-tier-capabilities"></a>Fonctionnalités des niveaux tarifaires

| Fonctionnalité                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Recherche (géocodage av/ar, points d’intérêt)  |        ✓           |     ✓    |
| Géocodage par lots (préversion)              |                   |     ✓    |
| Polygones de recherche          |                   |     ✓    |
| Routage                                 |        ✓           |     ✓    |
| Plage de routes                    |                   |     ✓    |
| Calcul d’itinéraires par lots (préversion)                |                   |     ✓    |
| Calcul d’itinéraires par matrice (préversion)               |                   |     ✓    |
| Rendu                                  |        ✓           |     ✓    |
| Imagerie aérienne+hybride    |            |     ✓    |
| Trafic                                 |        ✓           |     ✓    |
| Fuseaux horaires                              |        ✓           |     ✓    |
| Géolocalisation (préversion)                |        ✓           |     ✓    |
| Données (préversion)               |                   |     ✓    |
| Spatial (préversion)               |                   |     ✓    |
| Geofencing (préversion)               |                   |     ✓    |



Ces points supplémentaires sont intéressants :
* Quel type d’entreprise possédez-vous ?
* À quel point l’application dont le développement est en cours est-elle critique ?

Pour mieux comprendre les niveaux tarifaires S0 et S1, consultez le tableau **Niveau tarifaire et clients ciblés**. Pour plus d’informations, consultez la page [Tarification Azure Maps](https://azure.microsoft.com/pricing/details/azure-maps/). 

### <a name="pricing-tier-targeted-customers"></a>Niveau tarifaire et clients ciblés

| Niveau tarifaire  |     Clients ciblés                                                                |
|---------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>Le niveau tarifaire S0 convient aux clients qui ont une PME. Ce niveau tarifaire vous convient si vous n’attendez pas des volumes élevés d’utilisateurs simultanés. Il est également adapté si les principales API géospatiales présentées dans le tableau précédent répondent aux besoins de votre service. Ce niveau est en disponibilité générale. Il convient à toutes les phases de production des applications, du développement « preuve de concept » à la phase de test initiale, en passant par les applications en production et les applications déployées.<p>|
| S1            |    <p>Le niveau tarifaire S1 convient aux clients qui ont besoin d’une assistance pour les grandes entreprises, les applications stratégiques ou les gros volumes d’utilisateurs simultanés. Il convient également aux clients qui ont besoin de services géospatiaux avancés.</p>|

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur l’affichage et la modification des niveaux tarifaires :

> [!div class="nextstepaction"] 
> [Gérer un niveau tarifaire](how-to-manage-pricing-tier.md)
