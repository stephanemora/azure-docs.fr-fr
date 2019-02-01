---
title: Choisir un niveau tarifaire ou une référence SKU pour le service Recherche Azure | Recherche Azure
description: 'Le service Recherche Azure peut être configuré sur les références SKU suivantes : Gratuit, De base et Standard, sachant que la référence Standard est disponible sous différentes configurations de ressources et différents niveaux de capacité.'
services: search
author: HeidiSteen
manager: cgronlun
tags: azure-portal
ms.service: search
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: cf2359834aa79b1d3fef8b65e4ef4191eb6ff867
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55467439"
---
# <a name="choose-a-pricing-tier-for-azure-search"></a>Choisir un niveau tarifaire pour Recherche Azure

Dans Recherche Azure, une [ressource est créée](search-create-service-portal.md) à un niveau tarifaire ou une référence SKU qui est fixe pour la durée de vie du service. Les niveaux suivants existent : **Gratuit**, **De base** ou **Standard**, sachant que **Standard** est disponible dans plusieurs configurations et capacités. La plupart des clients commencent par le niveau **Gratuit** à des fins d’évaluation, puis passent au niveau **Standard** pour des déploiements de développement et de production. Au niveau **Gratuit**, vous pouvez effectuer tous les démarrages rapides et suivre tous les tutoriels, notamment ceux qui concernent la recherche cognitive nécessitant de nombreuses ressources. 

Les niveaux reflètent les caractéristiques du matériel qui héberge le service (plutôt que les fonctionnalités) et se distinguent en fonction de ce qui suit :

+ Nombre d’index que vous pouvez créer
+ Taille et la vitesse des partitions (stockage physique)

Bien que tous les niveaux, y compris le niveau **Gratuit**, offrent généralement la parité des fonctionnalités, de plus grandes charges de travail peuvent exiger des niveaux supérieurs. Par exemple, l’indexation de la [recherche cognitive](cognitive-search-concept-intro.md) a des compétences à long terme qui dépassent le délai d’attente sur un service gratuit, sauf si le jeu de données est petit.

> [!NOTE] 
> Les [indexeurs](search-indexer-overview.md), qui ne sont pas disponibles sur S3HD, sont une exception à la parité des fonctionnalités.
>

Au sein d’un niveau, vous pouvez [ajuster les ressources de réplica et de partition](search-capacity-planning.md) pour optimiser les performances. Vous pouvez commencer avec deux ou trois ressources de chaque, puis augmenter temporairement votre puissance de calcul en cas de lourde charge de travail d’indexation. La possibilité d’optimiser les niveaux des ressources ajoute à la flexibilité, mais complique également un peu votre analyse. Vous devrez peut-être faire des essais pour voir si un niveau inférieur avec plus de ressources/réplicas offre de meilleures valeurs et performances qu’un niveau supérieur avec moins de ressources. Pour en savoir plus sur quand et pourquoi vous devriez ajuster la capacité, consultez [Considérations sur les performances et l’optimisation](search-performance-optimization.md).

<!---
The purpose of this article is to help you choose a tier. It supplements the [pricing page](https://azure.microsoft.com/pricing/details/search/) and [Service Limits](search-limits-quotas-capacity.md) page with a digest of billing concepts and consumption patterns associated with various tiers. It also recommends an iterative approach for understanding which tier best meets your needs. 
--->

## <a name="how-billing-works"></a>Comment la facturation fonctionne

Dans Recherche Azure, il existe quatre façons d’engendrer des coûts lorsque vous créez une ressource de recherche dans le portail :

* Ajout de réplicas et de partitions utilisés pour les tâches d’interrogation et d’indexation régulières. Vous démarrez avec une ressource de chaque, mais vous pouvez en augmenter une, ou les deux, pour ajouter de la capacité tout en choisissant de payer pour les niveaux supplémentaires de ressources. 
* Frais de sortie de données lors de l’indexation. Lors de l’extraction des données à partir d’une source de données Azure SQL Database ou Cosmos DB, des frais de transaction vous sont facturés pour ces ressources.
* Pour la [recherche cognitive](cognitive-search-concept-intro.md) seulement, l’extraction d’images au cours du décodage de document est facturée en fonction du nombre d’images extraites à partir de vos documents. L’extraction de texte est actuellement gratuite.
* Pour la [recherche cognitive](cognitive-search-concept-intro.md) uniquement, les enrichissements en fonction des [compétences cognitives intégrées](cognitive-search-predefined-skills.md) sont facturés par rapport à une ressource Cognitive Services. Les enrichissements sont facturés au même tarif que si vous aviez effectué la tâche directement avec Cognitive Services.

Si vous n’utilisez pas la [recherche cognitive](cognitive-search-concept-intro.md) ni les [indexeurs de Recherche Azure](search-indexer-overview.md), vos seuls coûts sont liés aux réplicas et aux partitions en cours d’utilisation, pour des charges de travail de requête et d’indexation habituelles.

### <a name="billing-for-general-purpose-indexing-and-queries"></a>Facturation des requêtes et de l’indexation à usage général

Pour les opérations de Recherche Azure, la notion de facturation la plus importante à saisir est l’*Unité Recherche* (SU, Search Unit). Comme Recherche Azure dépend à la fois des réplicas et des partitions pour l’indexation et les requêtes, facturer uniquement par rapport à l’un ou à l’autre n’aurait aucun sens. Au lieu de cela, la facturation est basée sur un composite de ces deux facteurs. 

L’unité de recherche est le produit des *réplicas* et des *partitions* utilisés par un service : **`(R X P = SU)`**

Chaque service commence avec une Unité Recherche (un réplica multiplié par une partition) au minimum. Quel que soit le service, le maximum est de 36 unités de recherche, ce nombre pouvant être atteint de différentes manières : 6 partitions x 6 réplicas, ou 3 partitions x 12 réplicas, par exemple. Il est courant de ne pas utiliser la capacité totale. Par exemple, un service de 3 réplicas et 3 partitions, facturé comme 9 unités de recherche. 

Le taux de facturation est un **taux horaire par unité de recherche**, chaque niveau ayant un taux qui augmente progressivement. Les niveaux supérieurs sont fournis avec des partitions plus volumineuses et plus rapides, contribuant à un taux horaire globalement plus élevé pour ces niveaux. Consultez la [Tarification](https://azure.microsoft.com/pricing/details/search/)pour connaître les coûts pour chaque niveau. 

La plupart des clients mettent seulement une partie de la capacité totale en ligne et gardent le reste en réserve. En termes de facturation, c’est le nombre de partitions et de réplicas que vous mettez en ligne, calculé à l’aide de la formule d’unité de recherche (SU), qui détermine le taux horaire que vous payez réellement.

### <a name="billing-for-image-extraction-in-cognitive-search"></a>Facturation de l’extraction d’image dans la recherche cognitive

Si vous extrayez des images à partir de fichiers dans un pipeline d’indexation de recherche cognitive, vous êtes facturé pour cette opération dans votre facture Recherche Azure. Le paramètre qui déclenche l’extraction d’image est **imageAction** dans une [configuration d’indexeur](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters). Si **imageAction** est défini sur « none » (aucune, la valeur par défaut), aucuns frais ne sont imputés pour l’extraction d’image.

Les tarifs sont susceptibles de changer, mais sont toujours dans la page [Détails des tarifs](https://azure.microsoft.com/pricing/details/search/) de Recherche Azure. 

### <a name="billing-for-built-in-skills-in-cognitive-search"></a>Facturation des compétences intégrées à la recherche cognitive

Lorsque vous configurez un pipeline d’enrichissement, toute [compétence intégrée](cognitive-search-predefined-skills.md) utilisée dans le pipeline s’appuie sur des modèles Machine Learning. Ces modèles sont fournis par Cognitive Services. L’utilisation de ces modèles lors de l’indexation est facturée au même tarif que si vous aviez demandé la ressource directement.

Par exemple, supposez un pipeline constitué de la reconnaissance optique de caractères (OCR) sur des fichiers JPEG d’image numérisée, où le texte résultant est envoyé dans un index Recherche Azure pour des requêtes de recherche de forme libre. Votre pipeline d’indexation inclurait un indexeur avec la [compétence de reconnaissance optique de caractères](cognitive-search-skill-ocr.md), et cette compétence serait [associée à une ressource Cognitive Services](cognitive-search-attach-cognitive-services.md). Lorsque vous exécutez l’indexeur, des frais sont imputés sur votre facture Ressources cognitives pour l’exécution de la reconnaissance optique des caractères.

## <a name="tips-for-reducing-costs"></a>Conseils de réduction des coûts

Vous ne pouvez pas arrêter le service pour réduire la facture. Les ressources dédiées sont opérationnelles 24h/24, 7j/7, allouées pour votre usage exclusif pendant pour la durée de vie de votre service. La seule façon de réduire une facture est de réduire les réplicas et les partitions à un faible niveau fournissant toujours des performances acceptables et la [conformité SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Un levier de réduction des coûts est choix d’un niveau à taux horaire inférieur. Le taux horaire S1 est inférieur aux taux S2 ou S3. En supposant que vous provisionnez un service destiné à la limite inférieure de vos prévisions de charge, si vous dépassez le service, vous pouvez créer un second service de niveau supérieur, reconstruire vos index sur ce second service, puis supprimer le premier. 

Si vous avez planifié la capacité des serveurs sur place, vous savez qu'il est courant de « racheter » pour pouvoir faire face à la croissance prévue. Mais avec un service cloud, vous pouvez réaliser des économies beaucoup plus substantielles, parce que vous n'êtes pas lié à un achat spécifique. Vous pouvez toujours passer à un service de niveau supérieur si le service actuel est insuffisant.

### <a name="capacity-drill-down"></a>Exploration hiérarchique des capacités

Dans Recherche Azure, la capacité est structurée sous forme de *réplicas* et de *partitions*. 

+ Les réplicas sont des instances du service de recherche, chaque réplica hébergeant une copie avec équilibrage de charge d’un index. Par exemple, un service avec six réplicas a six copies de chaque index chargé dans le service. 

+ Les partitions stockent des index et fractionnent automatiquement les données de recherche : deux partitions fractionnent votre index en deux, trois partitions en trois, et ainsi de suite. En termes de capacité, la *taille de partition* est la principale caractéristique qui différencie les niveaux.

> [!NOTE]
> Tous les niveaux **Standard** prennent en charge des [combinaisons flexibles de réplicas et de partitions](search-capacity-planning.md#chart) afin que vous puissiez [pondérer votre système pour le stockage ou la vitesse](search-performance-optimization.md) en changeant l’équilibrage. Le niveau **De base** offre jusqu’à trois réplicas pour la haute disponibilité, mais une seule partition. Les niveaux **Gratuit** ne fournissent pas de ressources dédiées : les ressources de calcul sont partagées par plusieurs services gratuits.

### <a name="more-about-service-limits"></a>En savoir plus sur les limites de service

Les services hébergent des ressources, telles que des index, des indexeurs, et ainsi de suite. Chaque niveau impose des [limites de service](search-limits-quotas-capacity.md) quant à la quantité de ressources que vous pouvez créer. Ainsi, le plafond du nombre d’index (et d’autres objets) est la deuxième caractéristique qui différencie les niveaux. À mesure que vous consultez chaque option dans le portail, notez les limites du nombre d’index. D’autres ressources, telles que les indexeurs, sources de données et compétences, sont soumises à des limites d’index.

## <a name="consumption-patterns"></a>Modèles de consommation

La plupart des clients commencent avec le niveau **Gratuit**, qu’ils conservent indéfiniment, puis ils choisissent l’un des niveaux **Standard** pour les charges de travail de développement ou de production. 

![Niveaux de Recherche Azure](./media/search-sku-tier/tiers.png "Niveaux tarifaires de Recherche Azure")

À chaque extrémité, **De base** et **S3 HD** existent pour les modèles de consommation importants mais atypiques. Le niveau **De base** convient aux charges de travail de production de petite taille : il offre un contrat SLA, des ressources dédiées, une haute disponibilité, mais un stockage modeste de 2 Go maximum. Ce niveau a été conçu pour les clients qui sous-utilisent régulièrement la capacité disponible. À l’autre extrémité, le niveau **S3 HD** convient aux charges de travail typiques des éditeurs de logiciels indépendants, des partenaires, des [solutions multi-locataires](search-modeling-multitenant-saas-applications.md) ou à toute configuration exigeant un grand nombre de petits index. Le choix entre les niveaux **De base** et **S3 HD** est souvent évident, mais si vous souhaitez une confirmation vous pouvez publier un message sur [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) ou [contacter le Support Azure](https://azure.microsoft.com/support/options/) afin d’obtenir des instructions.

Quant aux niveaux Standard plus courants, **S1-S3** offrent des niveaux de capacité progressifs, avec des points d’inflexion sur la taille de partition et des valeurs maximales en ce qui concerne le nombre d’index, d’indexeurs et de ressources corollaires :

|  | S1 | S2 | S3 |  |  |  |  |
|--|----|----|----|--|--|--|--|
| Taille de partition|  25 Go | 100 Go | 200 Go |  |  |  |  |
| Limites d’index et d’indexeur| 50 | 200 | 200 |  |  |  |  |

**S1** est un choix courant quand les ressources dédiées et les partitions multiples deviennent une nécessité. Avec des partitions de 25 Go pour 12 partitions maximum, la limite par service avec **S1** est de 300 Go au total si vous optimisez les partitions sur les réplicas (pour obtenir des compositions plus équilibrées, consultez [Allouer des partitions et des réplicas](search-capacity-planning.md#chart)).

Les pages de portail et de tarification mettent l’accent sur le stockage et la taille de partition, mais pour chaque niveau toutes les caractéristiques de calcul (capacité du disque, vitesse, unités centrales) augmentent de manière linéaire avec le prix. Un réplica **S2** est plus rapide que **S1**, et **S3** est plus rapide que **S2**. Les niveaux **S3** ne respectent pas le modèle de tarification de calcul généralement linéaire ; ils offrent des E/S disproportionnellement plus rapides. Si vous prévoyez que les E/S constitueront le goulot d’étranglement, **S3** vous offrira davantage d’IOPS que les niveaux inférieurs.

Les niveaux **S3** et **S3 HD** sont soutenus par une infrastructure haute capacité identique, mais chacun d’eux atteint sa limite maximale de différentes façons. **S3** cible un plus petit nombre d’index très volumineux. Par conséquent, sa limite maximale est liée à la ressource (2,4 To pour chaque service). **S3 HD** cible un grand nombre de très petits index. Avec 1 000 index, **S3 HD** atteint ses limites sous la forme de contraintes d’index. Si vous êtes un client **S3 HD** qui nécessite plus de 1 000 index, contactez le Support Microsoft pour obtenir plus d’informations sur la marche à suivre.

> [!NOTE]
> Auparavant, les limites relatives aux documents constituaient un facteur important, mais elles ne sont plus applicables aux nouveaux services. Pour plus d’informations sur les conditions dans lesquelles les limites de document s’appliquent encore, consultez [Limites de service : limites du document](search-limits-quotas-capacity.md#document-limits).
>

## <a name="evaluate-capacity"></a>Évaluer la capacité

La capacité et les coûts d’exécution du service vont de pair. Les niveaux imposent des limites à deux niveaux (stockage et ressources). Vous devez donc réfléchir à ces deux facteurs, car celui qui est atteint en premier constitue la limite effective. 

Ce sont généralement les exigences professionnelles qui imposent le nombre d’index dont vous aurez besoin. Par exemple, un index global pour un référentiel de documents volumineux, ou peut-être plusieurs index basés sur la région, l’application ou une niche professionnelle.

Pour déterminer la taille d’un index, vous devez en [créer un](search-create-index-portal.md). La structure des données dans Recherche Azure est principalement un [index inversé](https://en.wikipedia.org/wiki/Inverted_index), qui a des caractéristiques différentes des données sources. Pour un index inversé, la taille et la complexité sont déterminées par le contenu, et pas nécessairement par la quantité de données fournies. Une source de données de grande taille avec une redondance très conséquente peut générer un index plus petit qu’un jeu de données plus petit ayant du contenu très variable. Par conséquent, il est rarement possible de déduire la taille de l’index d’après celle du jeu de données d’origine.

> [!NOTE] 
> Bien que l’estimation des besoins futurs en matière d’index et de stockage semble très approximative, elle en vaut la peine. Si la capacité d’un niveau tarifaire est trop basse, vous devrez provisionner un nouveau service au niveau supérieur, puis [recharger vos index](search-howto-reindex.md). Il n’existe aucune mise à niveau sur place du même service d’une référence (SKU) à un autre.
>

### <a name="step-1-develop-rough-estimates-using-the-free-tier"></a>Étape 1 : Développer des estimations approximatives avec le niveau Gratuit

Une approche possible pour l’estimation de la capacité consiste à utiliser le niveau **Gratuit**. Souvenez-vous que le niveau **Gratuit** offre jusqu’à trois index, 50 Mo de stockage et deux minutes de temps d’indexation. Il peut être difficile d’effectuer une projection de taille d’index avec ces contraintes, mais l’exemple suivant illustre une approche :

+ [Créer un service gratuit](search-create-service-portal.md)
+ Préparer un petit jeu de données représentatif (disons cinq mille documents et une taille d’échantillon de dix pour cent)
+ [Générer un index initial](search-create-index-portal.md) et notez sa taille dans le portail (disons 30 Mo)

En supposant que l’échantillon soit représentatif et constitué de dix pour cent de la source de données complète, un index de 30 Mo devient environ 300 Mo si tous les documents sont indexés. Vous pourriez doubler ce chiffre préliminaire afin de budgéter pour deux index (développement et production), soit un total de 600 Mo de besoins de stockage. Ces exigences étant facilement satisfaites avec le niveau **De base**, vous devez commencer par là.

### <a name="step-2-develop-refined-estimates-using-a-billable-tier"></a>Étape 2 : Développer des estimations affinées avec le niveau facturable

Certains clients préfèrent commencer avec des ressources dédiées capables de prendre en charge un échantillonnage et des temps de traitement plus élevés, puis développer des estimations réalistes en ce qui concerne la quantité d’index, la taille et les volumes de requêtes durant le développement. Initialement, un service est provisionné d’après la meilleure hypothèse puis, à mesure que le projet de développement évolue, les équipes savent généralement si le service existant offre trop ou pas assez de capacité pour les charges de production prévues. 

1. [Passez en revue les limites de service de chaque niveau](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#index-limits) afin de déterminer si les niveaux inférieurs peuvent prendre en charge la quantité d’index dont vous avez besoin. Avec les niveaux **De base**-**S1**- **S2**, les limites d’index sont respectivement de 15-50-200.

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

Le nombre de requêtes par seconde est une métrique qui prend de l’importance lors du réglage des performances, mais ce n’est généralement pas un facteur lors du choix du niveau, sauf si vous vous attendez à ce que le volume de requêtes soit élevé dès le départ.

Les niveaux standard peuvent fournir un équilibre entre le nombre de réplicas et le nombre de partitions, avec la prise en charge d’un délai d’exécution de requête plus rapide grâce à des réplicas supplémentaires pour le chargement de partitions supplémentaires et d’équilibrage en vue d’un traitement parallèle. Vous pouvez régler les performances une fois le service provisionné.

Les clients qui prévoient des volumes de requêtes élevés et soutenus dès le début doivent envisager d’adopter les niveaux plus élevés, qui sont pris en charge par du matériel plus puissant. Vous pouvez ensuite mettre des partitions et des réplicas hors connexion, ou même passer à un service de niveau inférieur, si ces volumes de requêtes ne se matérialisent pas. Pour plus d’informations sur la façon de calculer le débit de requête, consultez [Performances et optimisation de Recherche Azure](search-performance-optimization.md).


**Contrats de niveau de service**

Le niveau **Gratuit** et les fonctionnalités de préversion ne sont pas fournis avec des [Contrats de niveau de service (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Pour tous les niveaux facturables, les SLA entrent en vigueur lorsque vous approvisionnez une redondance suffisante pour votre service. Un SLA de requête (lecture) requiert au moins deux réplicas. Un SLA de requête et d’indexation (lecture-écriture) nécessite au moins trois réplicas. Le nombre de partitions n’est pas pris en compte dans les SLA. 

## <a name="tips-for-tier-evaluation"></a>Conseils pour l’évaluation du niveau

+ Apprenez à créer des index efficaces et à identifier les méthodologies d’actualisation ayant le moins d’impact. Nous vous recommandons l’[analytique du trafic des recherches](search-traffic-analytics.md) pour tirer parti des informations obtenues sur l’activité des requêtes.

+ Autorisez la génération de métriques autour des requêtes et recueillez des données relatives aux modèles d’utilisation (requêtes pendant les heures de bureau, indexation pendant les heures creuses), et utilisez ces données pour faciliter les décisions de provisionnement de service ultérieures. Bien que cela ne soit pas pratique à une cadence horaire ou quotidienne, vous pouvez ajuster les partitions et les ressources de manière dynamique afin de prendre en charge les changements de volumes de requêtes planifiés, ou les changements non planifiés mais soutenus si les niveaux se maintiennent suffisamment longtemps pour que des mesures s’imposent.

+ N’oubliez pas que le seul inconvénient du sous-provisionnement est que vous devrez peut-être supprimer un service si les exigences réelles sont supérieures à celles estimées. Pour éviter toute interruption de service, vous devrez créer un service dans le même abonnement à un niveau supérieur, et l’exécuter côte à côte jusqu’à ce que toutes les applications et requêtes ciblent le nouveau point de terminaison.

## <a name="next-steps"></a>Étapes suivantes

Commencez avec un niveau **Gratuit** et créez un index initial à l’aide d’un sous-ensemble de vos données afin de bien comprendre ses caractéristiques. La structure des données dans Recherche Azure est un index inversé. La taille et la complexité d’un index inversé sont déterminées par le contenu. Souvenez-vous que le contenu hautement redondant a tendance à générer un index plus petit que le contenu très irrégulier. Par conséquent, ce sont les caractéristiques du contenu plutôt que la taille du jeu de données qui déterminent les besoins de stockage d’index.

Une fois que vous avez une idée initiale de la taille de l’index, [provisionnez un service facturable](search-create-service-portal.md) à l’un des niveaux présentés dans cet article, **De base** ou **Standard**. Assouplissez les contraintes artificielles sur les sous-ensembles de données et [reconstruisez votre index](search-howto-reindex.md) afin d’inclure toutes les données pour lesquelles vous souhaitez que la recherche soit réellement possible.

[Allouez des partitions et des réplicas](search-capacity-planning.md) en fonction des besoins, afin d’obtenir les performances et l’échelle requises.

Si les performances et la capacité conviennent, vous avez terminé. Dans le cas contraire, recréez un service de recherche à un autre niveau correspondant mieux à vos besoins.

> [!NOTE]
> Pour poser des questions et obtenir de l’aide, publiez un message sur [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) ou [contactez le Support technique Azure](https://azure.microsoft.com/support/options/).
