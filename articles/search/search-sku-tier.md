---
title: Choisir un niveau tarifaire ou référence (SKU) pour le service Recherche Azure | Microsoft Docs
description: 'Azure Search peut être configuré sur ces niveaux de référence (SKU) : Gratuit, De base et Standard, où Standard est disponible dans différentes configurations de ressources et différents niveaux de capacité.'
services: search
author: HeidiSteen
manager: cgronlun
tags: azure-portal
ms.service: search
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: heidist
ms.openlocfilehash: f7cf471a69395cef0aef7d5dd2e3c77218bf97a3
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/08/2018
ms.locfileid: "39715278"
---
# <a name="choose-a-pricing-tier-for-azure-search"></a>Choisir un niveau tarifaire pour Recherche Azure

Dans Azure Search, un [service est configuré](search-create-service-portal.md) sur un niveau tarifaire ou une SKU spécifique. Vous avez le choix entre les options suivantes : **Gratuit**, **De base** ou **Standard**, où **Standard** est disponible dans plusieurs configurations et capacités. 

Cet article vise à vous aider à choisir une option. Il complète la [page de tarification](https://azure.microsoft.com/pricing/details/search/) et la page [Limites de service](search-limits-quotas-capacity.md) avec un résumé des concepts de facturation et des modèles de consommation associés à différents niveaux. Il recommande également une approche itérative afin d’identifier le niveau répondant le mieux à vos besoins. 

Les niveaux déterminent la capacité, et non les fonctionnalités. Si la capacité d’un niveau tarifaire est trop basse, vous devrez provisionner un nouveau service au niveau supérieur, puis [recharger vos index](search-howto-reindex.md). Il n’existe aucune mise à niveau sur place du même service d’une référence (SKU) à un autre.

La disponibilité des fonctionnalités n’est pas une considération majeure en ce qui concerne les niveaux. Tous les niveaux, y compris le niveau **Gratuit**, offrent la parité des fonctionnalités, à l’exception de la prise en charge de l’indexeur pour S3 HD. Toutefois, les contraintes de ressources et d’indexation peuvent limiter l’étendue de l’utilisation des fonctionnalités. Par exemple, l’indexation de [recherche cognitive](cognitive-search-concept-intro.md) a des compétences à long terme qui dépassent le délai d’attente sur un service gratuit, sauf si le jeu de données est très petit.

> [!TIP]
> La plupart des clients commencent avec le niveau **Gratuit** à des fins d’évaluation, puis passent au niveau **Standard** à des fins de développement. Une fois que vous avez choisi un niveau et [provisionné un service de recherche](search-create-service-portal.md), vous pouvez [augmenter le nombre de réplicas et de partitions](search-capacity-planning.md) afin d’optimiser les performances. Pour découvrir quand et pourquoi vous pourriez ajuster la capacité, consultez [Considérations sur les performances et l’optimisation](search-performance-optimization.md).
>

## <a name="billing-concepts"></a>Concepts de facturation

Pour bien sélectionner votre niveau, vous devez comprendre des concepts tels que les définitions de capacité, les limites de service et les unités de service. 

### <a name="capacity"></a>Capacité

La capacité est structurée sous forme de *réplicas* et de *partitions*. 

+ Les réplicas sont des instances du service de recherche, chaque réplica hébergeant une copie avec équilibrage de charge d’un index. Par exemple, un service avec six réplicas a six copies de chaque index chargé dans le service. 

+ Les partitions stockent des index et fractionnent automatiquement les données de recherche : deux partitions fractionnent votre index en deux, trois partitions en trois, et ainsi de suite. En termes de capacité, la *taille de partition* est la principale caractéristique qui différencie les niveaux.

> [!NOTE]
> Tous les niveaux **Standard** prennent en charge des [combinaisons flexibles de réplicas et de partitions](search-capacity-planning.md#chart) afin que vous puissiez [pondérer votre système pour le stockage ou la vitesse](search-performance-optimization.md) en changeant l’équilibrage. Le niveau **De base** offre jusqu’à trois réplicas pour la haute disponibilité, mais une seule partition. Les niveaux **Gratuit** ne fournissent pas de ressources dédiées : les ressources de calcul sont partagées par plusieurs services gratuits.

### <a name="search-units"></a>Unités de recherche

Le concept de facturation le plus important à comprendre est l’*unité de recherche* (SU, Search Unit). Il s’agit de l’unité de facturation pour Recherche Azure. Étant donné que le fonctionnement de Recherche Azure dépend à la fois des réplicas et des partitions, cela n’a aucun sens de facturer d’après l’un ou l’autre. Au lieu de cela, la facturation est basée sur un composite de ces deux facteurs. L’unité de recherche est le produit des réplicas et des partitions utilisées par un service : (R x P = SU). Au minimum, chaque service commence avec 1 SU (un réplica multiplié par une partition), mais un modèle plus réaliste pourrait être un service avec trois réplicas et trois partitions, facturé comme 9 SU. 

Bien que chaque niveau offre une capacité supérieure progressive, vous pouvez mettre une partie de la capacité totale en ligne et conserver le reste en réserve. En termes de facturation, c’est le nombre de partitions et de réplicas que vous mettez en ligne, calculé à l’aide de la formule SU, qui détermine ce que vous payez réellement.

Le taux de facturation est un taux horaire par SU, chaque niveau ayant un taux différent. Consultez la [Tarification](https://azure.microsoft.com/pricing/details/search/)pour connaître les coûts pour chaque niveau.

### <a name="limits"></a>limites

Les services hébergent des ressources, telles que des index, des indexeurs, et ainsi de suite. Chaque niveau impose des [limites de service](search-limits-quotas-capacity.md) quant à la quantité de ressources que vous pouvez créer. Ainsi, le plafond du nombre d’index (et d’autres objets) est la deuxième caractéristique qui différencie les niveaux. À mesure que vous consultez chaque option dans le portail, notez les limites du nombre d’index. D’autres ressources, telles que les indexeurs, sources de données et compétences, sont soumises à des limites d’index.

## <a name="consumption-patterns"></a>Modèles de consommation

La plupart des clients commencent avec le niveau **Gratuit**, qu’ils conservent indéfiniment, puis ils choisissent l’un des niveaux **Standard** pour les charges de travail de développement ou de production. 

![Niveaux de Recherche Azure](./media/search-sku-tier/tiers.png "Niveaux tarifaires de Recherche Azure")

À chaque extrémité, **De base** et **S3 HD** existent pour les modèles de consommation importants mais atypiques. Le niveau **De base** convient aux charges de travail de production de petite taille : il offre un contrat SLA, des ressources dédiées, une haute disponibilité, mais un stockage modeste de 2 Go maximum. Ce niveau a été conçu pour les clients qui sous-utilisent régulièrement la capacité disponible. À l’autre extrémité, le niveau **S3 HD** convient aux charges de travail typiques des éditeurs de logiciels indépendants, des partenaires, des [solutions multi-locataires](search-modeling-multitenant-saas-applications.md) ou à toute configuration exigeant un grand nombre de petits index. Le choix entre les niveaux **De base** et **S3 HD** est souvent évident, mais si vous souhaitez une confirmation vous pouvez publier un message sur [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) ou [contacter le Support Azure](https://azure.microsoft.com/support/options/) afin d’obtenir des instructions.

Quant aux niveaux Standard plus courants, **S1-S3** offrent des niveaux de capacité progressifs, avec des points d’inflexion sur la taille de partition et des valeurs maximales en ce qui concerne le nombre d’index, d’indexeurs et de ressources corollaires :

|  | S1 | S2 | S3 |  |  |  |  |
|--|----|----|----|--|--|--|--|
| Taille de partition|  25 Go | 100 Go | 250 Go |  |  |  |  |
| Limites d’index et d’indexeur| 50 | 200 | 200 |  |  |  |  |

**S1** est un choix courant quand les ressources dédiées et les partitions multiples deviennent une nécessité. Avec des partitions de 25 Go pour 12 partitions maximum, la limite par service avec **S1** est de 300 Go au total si vous optimisez les partitions sur les réplicas (pour obtenir des compositions plus équilibrées, consultez [Allouer des partitions et des réplicas](search-capacity-planning.md#chart)).

Les pages de portail et de tarification mettent l’accent sur le stockage et la taille de partition, mais pour chaque niveau toutes les caractéristiques de calcul (capacité du disque, vitesse, unités centrales) augmentent de manière linéaire avec le prix. Un réplica **S2** est plus rapide que **S1**, et **S3** est plus rapide que **S2**. Les niveaux **S3** ne respectent pas le modèle de tarification de calcul généralement linéaire ; ils offrent des E/S disproportionnellement plus rapides. Si vous prévoyez que les E/S constitueront le goulot d’étranglement, **S3** vous offrira davantage d’IOPS que les niveaux inférieurs.

Les niveaux **S3** et **S3 HD** sont soutenus par une infrastructure haute capacité identique, mais chacun d’eux atteint sa limite maximale de différentes façons. **S3** cible un plus petit nombre d’index très volumineux. Par conséquent, sa limite maximale est liée à la ressource (2,4 To pour chaque service). **S3 HD** cible un grand nombre de très petits index. Avec 1 000 index, **S3 HD** atteint ses limites sous la forme de contraintes d’index. Si vous êtes un client **S3 HD** qui nécessite plus de 1 000 index, contactez le Support Microsoft pour obtenir plus d’informations sur la marche à suivre.

> [!NOTE]
> Auparavant, les limites relatives aux documents constituaient un facteur important. Elles ne sont plus applicables pour la plupart des services Recherche Azure provisionnés après janvier 2018. Pour plus d’informations sur les conditions pour lesquelles les limites de document s’appliquent encore, consultez [Limites de service : Limites du document](search-limits-quotas-capacity.md#document-limits).
>

## <a name="evaluate-capacity"></a>Évaluer la capacité

La capacité et les coûts d’exécution du service vont de pair. Les niveaux imposent des limites à deux niveaux (stockage et ressources). Vous devez donc réfléchir à ces deux facteurs, car celui qui est atteint en premier constitue la limite effective. 

Ce sont généralement les exigences professionnelles qui imposent le nombre d’index dont vous aurez besoin. Par exemple, un index global pour un référentiel de documents volumineux, ou peut-être plusieurs index basés sur la région, l’application ou une niche professionnelle.

Pour déterminer la taille d’un index, vous devez en [créer un](search-create-index-portal.md). La structure des données dans Recherche Azure est principalement un [index inversé](https://en.wikipedia.org/wiki/Inverted_index), qui a des caractéristiques différentes des données sources. Pour un index inversé, la taille et la complexité sont déterminées par le contenu, et pas nécessairement par la quantité de données fournies. Une source de données de grande taille avec une redondance très conséquente peut générer un index plus petit qu’un jeu de données plus petit ayant du contenu très variable.  Par conséquent, il est rarement possible de déduire la taille de l’index d’après celle du jeu de données d’origine.

### <a name="step-1-develop-rough-estimates-using-the-free-tier"></a>Étape 1 : Développer des estimations approximatives avec le niveau Gratuit

Une approche possible pour l’estimation de la capacité consiste à utiliser le niveau **Gratuit**. Souvenez-vous que le niveau **Gratuit** offre jusqu’à trois index, 50 Mo de stockage et deux minutes de temps d’indexation. Il peut être difficile d’effectuer une projection de taille d’index avec ces contraintes, mais l’exemple suivant illustre une approche :

+ [Créer un service gratuit](search-create-service-portal.md)
+ Préparer un petit jeu de données représentatif (disons cinq mille documents et une taille d’échantillon de dix pour cent)
+ [Générer un index initial](search-create-index-portal.md) et notez sa taille dans le portail (disons 30 Mo)

En supposant que l’échantillon soit représentatif et constitué de dix pour cent de la source de données complète, un index de 30 Mo devient environ 300 Mo si tous les documents sont indexés. Vous pourriez doubler ce chiffre préliminaire afin de budgéter pour deux index (développement et production), soit un total de 600 Mo de besoins de stockage. Ces exigences étant facilement satisfaites avec le niveau **De base**, vous devez commencer par là.

### <a name="step-2-develop-refined-estimates-using-a-billable-tier"></a>Étape 2 : Développer des estimations affinées avec le niveau facturable

Certains clients préfèrent commencer avec des ressources dédiées capables de prendre en charge un échantillonnage et des temps de traitement plus élevés, puis développer des estimations réalistes en ce qui concerne la quantité d’index, la taille et les volumes de requêtes durant le développement. Initialement, un service est provisionné d’après la meilleure hypothèse puis, à mesure que le projet de développement évolue, les équipes savent généralement si le service existant offre trop ou pas assez de capacité pour les charges de production prévues. 

1. [Passez en revue les limites de service de chaque niveau](https://docs.microsoft.com/en-us/azure/search/search-limits-quotas-capacity#index-limits) afin de déterminer si les niveaux inférieurs peuvent prendre en charge la quantité d’index dont vous avez besoin. Avec les niveaux **De base**-**S1**- **S2**, les limites d’index sont respectivement de 15-50-200.

1. [Créez un service à un niveau facturable](search-create-service-portal.md) :

    + Commencez à un faible niveau, **De base** ou **S1**, si vous êtes au début de votre processus d’apprentissage.
    + Commencez à un niveau élevé, **S2** ou même **S3**, s’il est évident que vous aurez besoin de charges de requêtes et d’indexation à grande échelle.

1. [Générez un index initial](search-create-index-portal.md) pour déterminer comment les données sources se traduisent par un index. C’est l’unique façon d’estimer la taille de l’index.

1. [Surveillez le stockage, les limites de service, le volume de requêtes et la latence](search-monitor-usage.md) dans le portail. Le portail indique le nombre de requêtes par seconde, les requêtes limitées et la latence de recherche. Toutes ces valeurs peuvent vous aider à décider si vous êtes au niveau approprié. Outre les métriques du portail, vous pouvez configurer l’analyse approfondie, telle que l’analyse à l’aide de rapports générés interactifs, en activant l’[analytique du trafic des recherches](search-traffic-analytics.md). 

Le nombre et la taille des index sont tout aussi pertinents pour votre analyse, car les limites maximales sont atteintes en cas d’utilisation totale du stockage (partitions) ou quand les valeurs limites maximales sont atteintes pour les ressources (index, indexeurs et ainsi de suite), selon ce qui se produit en premier. Le portail vous aide à effectuer le suivi de ces deux facteurs en affichant l’utilisation actuelle et les limites maximales côte à côte dans la page de présentation.

> [!NOTE]
> Les besoins de stockage peuvent être augmentés de façon excessive si les documents contiennent des données superflues. Dans l’idéal, les documents contiennent uniquement les données dont vous avez besoin pour l’expérience de recherche. Les données binaires ne sont pas interrogeables et doivent être stockées séparément (dans une table ou un objet blob de stockage Azure) avec un champ dans l’index pour conserver une référence URL aux données externes. La taille maximale d’un document individuel est de 16 Mo (ou moins si vous chargez en bloc plusieurs documents en une seule demande). Pour plus d’informations, consultez [Limites de service de Recherche Azure](search-limits-quotas-capacity.md).
>

**Considérations relatives au volume de requêtes**

Le nombre de requêtes par seconde est une métrique qui prend de l’importance lors du réglage des performances, mais ce n’est généralement pas un facteur lors du choix du niveau, sauf si vous vous attendez à ce que le volume de requêtes soit très élevé dès le départ.

Tous les niveaux standard peuvent fournir un équilibre entre le nombre de réplicas et le nombre de partitions, avec la prise en charge d’un délai d’exécution de requête plus rapide grâce à des réplicas supplémentaires pour le chargement de partitions supplémentaires et d’équilibrage en vue d’un traitement parallèle. Vous pouvez régler les performances une fois le service provisionné.

Les clients qui prévoient des volumes de requêtes élevés et soutenus dès le début doivent envisager d’adopter les niveaux plus élevés, qui sont pris en charge par du matériel plus puissant. Vous pouvez ensuite mettre des partitions et des réplicas hors connexion, ou même passer à un service de niveau inférieur, si ces volumes de requêtes ne se matérialisent pas. Pour plus d’informations sur la façon de calculer le débit de requête, consultez [Performances et optimisation de Recherche Azure](search-performance-optimization.md).


**Contrats de niveau de service**

Le niveau **Gratuit** et les fonctionnalités de préversion ne sont pas fournis avec des [Contrats de niveau de service (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Pour tous les niveaux facturables, les SLA entrent en vigueur lorsque vous approvisionnez une redondance suffisante pour votre service. Un SLA de requête (lecture) requiert au moins deux réplicas. Un SLA de requête et d’indexation (lecture-écriture) nécessite au moins trois réplicas. Le nombre de partitions n’est pas pris en compte dans les SLA. 

## <a name="tips-for-tier-evaluation"></a>Conseils pour l’évaluation du niveau

+ Apprenez à créer des index efficaces et à identifier les méthodologies d’actualisation ayant le moins d’impact. Nous vous recommandons l’[analytique du trafic des recherches](search-traffic-analytics.md) pour tirer parti des informations obtenues sur l’activité des requêtes.

+ Autorisez la génération de métriques autour des requêtes et recueillez des données relatives aux modèles d’utilisation (requêtes pendant les heures de bureau, indexation pendant les heures creuses), et utilisez ces données pour faciliter les décisions de provisionnement de service ultérieures. Bien que cela ne soit pas pratique à un niveau horaire ou quotidien, vous pouvez ajuster les partitions et les ressources de manière dynamique afin de prendre en charge les changements de volumes de requêtes planifiés, ou les changements non planifiés mais soutenus si les niveaux se maintiennent suffisamment longtemps pour que des mesures s’imposent.

+ N’oubliez pas que le seul inconvénient du sous-provisionnement est que vous devrez peut-être supprimer un service si les exigences réelles sont supérieures à celles estimées. Pour éviter toute interruption de service, vous devrez créer un service dans le même abonnement à un niveau supérieur, et l’exécuter côte à côte jusqu’à ce que toutes les applications et requêtes ciblent le nouveau point de terminaison.

## <a name="next-steps"></a>Étapes suivantes

Commencez avec un niveau **Gratuit** et créez un index initial à l’aide d’un sous-ensemble de vos données afin de bien comprendre ses caractéristiques. La structure des données dans Recherche Azure est un index inversé. La taille et la complexité d’un index inversé sont déterminées par le contenu. Souvenez-vous que le contenu hautement redondant a tendance à générer un index plus petit que le contenu très irrégulier. Par conséquent, ce sont les caractéristiques du contenu plutôt que la taille du jeu de données qui déterminent les besoins de stockage d’index.

Une fois que vous avez une idée initiale de la taille de l’index, [provisionnez un service facturable](search-create-service-portal.md) à l’un des niveaux présentés dans cet article, **De base** ou **Standard**. Assouplissez les contraintes artificielles sur les sous-ensembles de données et [reconstruisez votre index](search-howto-reindex.md) afin d’inclure toutes les données pour lesquelles vous souhaitez que la recherche soit réellement possible.

[Allouez des partitions et des réplicas](search-capacity-planning.md) en fonction des besoins, afin d’obtenir les performances et l’échelle requises.

Si les performances et la capacité conviennent, vous avez terminé. Dans le cas contraire, recréez un service de recherche à un autre niveau correspondant mieux à vos besoins.

> [!NOTE]
> Pour poser des questions et obtenir de l’aide, publiez un message sur [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) ou [contactez le Support technique Azure](https://azure.microsoft.com/support/options/).