---
title: Gestion des coûts avec Azure Metrics Advisor
titleSuffix: Azure Applied AI Services
description: Découvrir la gestion des coûts et la tarification pour Azure Metrics Advisor
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 07/06/2021
ms.author: mbullwin
ms.openlocfilehash: bc376e8bc69845af245c66c045ed345f758e410e
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130063922"
---
# <a name="azure-metrics-advisor-cost-management"></a>Gestion des coûts d’Azure Metrics Advisor

Azure Metrics Advisor supervise les performances des moteurs de croissance de votre organisation, notamment le chiffre d’affaires des ventes et les opérations de fabrication. Identifiez et résolvez rapidement les problèmes grâce à une combinaison puissante d’outils de supervision en quasi-temps réel, en adaptant les modèles à votre scénario et en offrant une analyse granulaire avec des diagnostics et des alertes. Seules les séries chronologiques qui sont analysées par le service vous sont facturées. Aucun engagement initial ni frais minimum ne sont appliqués.

> [!NOTE]
> Cet article explique comment le tarif est calculé pour vous aider à planifier et à gérer les coûts lors de l’utilisation d’Azure Metric Advisor. Les prix cités dans cet article ne reflètent pas des prix réels et sont fournis à titre d’exemple uniquement. Pour obtenir les dernières informations sur la tarification, consultez la [page officielle sur la tarification de Metrics Advisor](https://azure.microsoft.com/pricing/details/metrics-advisor/).  

## <a name="key-points-about-cost-management-and-pricing"></a>Points clés sur la gestion des coûts et la tarification

- Le nombre de **séries chronologiques distinctes** analysées pendant un mois vous est facturé. Si un point de données est analysé pour une série chronologique, il est également calculé.
- Le nombre de séries chronologiques distinctes est **indépendante** de sa granularité. Une série chronologique horaire et une série chronologique quotidienne sont facturées au même tarif. 
- La facture est établie conformément à la structure tarifaire à plusieurs niveaux indiquée ci-dessous. Le premier jour du mois suivant initialise une nouvelle fenêtre de statistiques.  
- Plus vous intégrez un nombre important de séries chronologiques à analyser, plus le prix pour chaque série chronologique baisse. 

**Gardez à l’esprit que les prix ci-dessous sont donnés à titre d’exemple uniquement**. Pour obtenir les dernières informations de tarification, consultez la [page officielle sur la tarification de Metrics Advisor](https://azure.microsoft.com/pricing/details/metrics-advisor/).

| Série chronologique analysée par mois| Coût par série chronologique |
|--------|-----|
| Gratuit : 25 premières séries chronologiques | $- |
| 26 à 1 000 séries chronologiques | 0,75 $ |
| 1 000 à 5 000 séries chronologiques | 0,50 $ |
| 5 000 à 20 000 séries chronologiques | 0,25 $|
| 20 000 à 50 000 séries chronologiques| 0,10 $|
| >50 000 séries chronologiques | 0,05 $ |


Pour vous aider à acquérir des connaissances de base sur Metrics Advisor et à commencer à explorer le service, un montant inclus est offert pour vous permettre d’analyser gratuitement jusqu’à 25 séries chronologiques. 

## <a name="pricing-examples"></a>Exemples de tarification

### <a name="example-1"></a>Exemple 1
<!-- introduce statistic window-->

Au cours du premier mois, un client a intégré un flux de données avec 25 séries chronologiques à analyser la première semaine. Ensuite, le client intègre un autre flux de données avec 30 séries chronologiques la deuxième semaine. Mais au cours de la troisième semaine, il supprime 30 séries chronologiques qui avaient été intégrées pendant la deuxième semaine. Il y a alors **55** séries chronologiques distinctes analysées au cours du premier mois. **30** séries sont donc facturées au client (les 25 séries chronologiques du niveau gratuit sont exclues), ce qui correspond au niveau 1. Le coût mensuel est : 30 x 0,75 $ = **22,5 $** . 

| Niveau de volume | Coût par série chronologique | Coût mensuel | 
| ------------| ----------------- | ----------- |
| 30 premières séries chronologiques gratuites (55-25) | 0,75 $ | 22,5 $ |
| **Total = 30 séries chronologiques** | | **22,5 $ par mois** |

Le deuxième mois, le client n’a ni intégré ni supprimé de série chronologique. 25 séries chronologiques sont analysées au cours du deuxième mois. Aucun coût n’est appliqué. 

### <a name="example-2"></a>Exemple 2
<!-- introduce how time series is calculated-->

Un planificateur d’activités doit effectuer le suivi du chiffre d’affaires de l’entreprise en tant qu’indicateur de santé. En règle générale, il existe un modèle hebdomadaire. Le client intègre les métriques dans Metrics Advisor pour analyser les anomalies. Metrics Advisor est en mesure d’effectuer l’apprentissage du modèle à partir des données d’historique et d’effectuer la détection sur des points de données de suivi. Une chute soudaine peut être détectée comme une anomalie, ce qui peut indiquer un problème sous-jacent, comme une panne de service ou une offre promotionnelle qui ne rencontre pas le succès escompté. Un pic inattendu peut également être détecté comme une anomalie, ce qui peut indiquer une campagne marketing très réussie ou un client majeur qui vient d’être gagné. 

Les métriques sont analysés sur **100 catégories de produits** et **10 régions**, puis le nombre de séries chronologiques distinctes analysées est calculé comme suit : 

```
1(Revenue) * 100 product categories * 10 regions = 1,000 analyzed time series
```

Conformément au modèle de tarification à plusieurs niveaux décrit ci-dessus, 1 000 séries chronologiques par mois sont facturées : (1 000-25) x 0,75 $ = **731,25 $** . 

| Niveau de volume | Coût par série chronologique | Coût mensuel | 
| ------------| ----------------- | ----------- |
| 975 premières séries chronologiques gratuites (1 000-25) | 0,75 $ | 731,25 $ |
| **Total = 30 séries chronologiques** | | **731,25 $ par mois** |

### <a name="example-3"></a>Exemple 3
<!-- introduce cost for multiple metrics and -->

Après avoir validé les résultats de la détection sur les métriques du chiffre d’affaires, le client souhaite intégrer deux métriques supplémentaires à analyser. Il s’agit d’une part des coûts, d’autre part des DAU (utilisateurs actifs quotidiens) de son site web. Le client souhaite également ajouter une nouvelle dimension avec **20 canaux**. Au cours du mois, 10 des 100 catégories de produits sont supprimées au bout de la première semaine et ne sont plus analysées ensuite. En outre, 10 nouvelles catégories de produits sont introduites au cours de la troisième semaine du mois, et les séries chronologiques correspondantes sont analysées pour la moitié du mois. Le nombre de séries chronologiques distinctes analysées est calculé comme suit : 

```    
3(Revenue, cost and DAU) * 110 product categories * 10 regions * 20 channels = 66,000 analyzed time series
```

Conformément au modèle de tarification à plusieurs niveaux décrit ci-dessus, 66 000 séries chronologiques par mois correspondent au niveau 5 et sont facturées **10 281,25 $** . 

| Niveau de volume | Coût par série chronologique | Coût mensuel | 
| ------------| ----------------- | ----------- |
| 975 premières séries chronologiques gratuites (1 000-25) | 0,75 $ | 731,25 $ |
| 4 000 séries chronologiques suivantes | 0,50 $ | 2 000 $ |
| 15 000 séries chronologiques suivantes | 0,25 $ | 3 750 $ |
| 30 000 séries chronologiques suivantes | 0,10 $ | 3 000 USD |
| 16 000 séries chronologiques suivantes | 0,05 $ | 800 $ |
| **Total = 65 975 séries chronologiques** | | **10281,25 $ par mois** |

## <a name="next-steps"></a>Étapes suivantes

- [Gérer vos flux de données](how-tos/manage-data-feeds.md)
- [Configurations pour différentes sources de données](data-feeds-from-different-sources.md)
- [Configurer des métriques et affiner la configuration de la détection](how-tos/configure-metrics.md)


