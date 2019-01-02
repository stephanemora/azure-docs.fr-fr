---
title: Limites de service pour les niveaux tarifaires et les références SKU - Recherche Azure
description: Limites de service permettant de planifier la capacité et limites maximales des requêtes et réponses de la Recherche Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 98902c7a27d769b59b20d4560b2cda21bfcff6c6
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310217"
---
# <a name="service-limits-in-azure-search"></a>Limites de service d’Azure Search
Les limites maximales du stockage, des charges de travail et des quantités d’index, de documents et autres objets varient selon que vous [provisionnez le service Recherche Azure](search-create-service-portal.md) avec les niveaux tarifaires **Gratuit**, **De base** ou **Standard**.

+ **Gratuit** est un service partagé multi-locataire qui est fourni avec votre abonnement Azure.

+ Le niveau **De base** fournit des ressources informatiques dédiées aux charges de production à petite échelle.

+ Le niveau **Standard** est exécuté sur des ordinateurs dédiés, avec une capacité de stockage et de traitement beaucoup plus grande, et ce, à chaque niveau. Le niveau Standard se décompose en quatre catégories : S1, S2, S3 et S3 HD.

  La catégorie S3 HD (S3 High Density) est conçue pour des charges de travail spécifiques : [une architecture multilocataire](search-modeling-multitenant-saas-applications.md) et de grandes quantités de petits index (un million de documents par index, trois mille index par service). Elle ne fournit pas la [fonctionnalité d’indexeur](search-indexer-overview.md). Dans S3 HD, l’ingestion des données doit tirer parti de l’approche push en appelant des API pour envoyer (push) les données de la source vers l’index. 

> [!NOTE]
> Un service est approvisionné à un niveau spécifique. Si vous avez besoin de passer au niveau supérieur pour obtenir plus de capacité, vous devez provisionner un nouveau service (la mise à niveau sur place n’est pas disponible). Pour en savoir plus, consultez [Choisir une référence (SKU) ou un niveau tarifaire](search-sku-tier.md). Pour en savoir plus sur le réglage de capacité dans un service que vous avez déjà approvisionné, consultez [Mettre à l’échelle des niveaux de ressources pour interroger et indexer les charges de travail](search-capacity-planning.md).
>

## <a name="subscription-limits"></a>Limites d’abonnement
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>Limites de stockage
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>Limites d’index

| Ressource | Gratuit | De base&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD |
| -------- | ---- | ------------------- | --- | --- | --- | --- |
| Nombre maximal d’index |3 |5 ou 15 |50 |200 |200 |1 000 par partition ou 3 000 par service |
| Nombre maximal de champs par index |1 000 |100 |1 000 |1 000 |1 000 |1 000 |
| Nombre maximal de [générateurs de suggestions](https://docs.microsoft.com/rest/api/searchservice/suggesters) par index |1 |1 |1 |1 |1 |1 |
| Nombre maximal de [profils de score](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) par index |100 |100 |100 |100 |100 |100 |
| Nombre maximal de fonctions par profil |8 |8 |8 |8 |8 |8 |

<sup>1</sup> La limite des services De base créés après fin 2017 a augmenté et atteint 15 index, sources de données et indexeurs. Celle des services créés avant est à 5. Le niveau De base est la seule référence soumise à une limite inférieure de 100 champs par index.

<a name="document-limits"></a>

## <a name="document-limits"></a>Limites du document 

Depuis octobre 2018, il n’y a plus de limites au nombre de documents pour tout nouveau service créé, quels que soient le niveau facturable (De base, S1, S2, S3, S3 HD) et la région. Si la plupart des régions prennent en charge des nombres illimités de documents depuis novembre/décembre 2017, cinq régions ont continué d’imposer de telles limites. Selon le moment et le lieu de création d’un service de recherche, vous pourriez exécuter un service toujours sujet à des limites de nombre de documents.

Pour savoir si votre service a un nombre limité de documents, consultez la vignette Utilisation dans la page de présentation de votre service. Les nombres de documents sont illimités ou soumis à une limite établie en fonction du niveau.

  ![Mosaïque Utilisation](media/search-limits-quotas-capacity/portal-usage-tile.png)

### <a name="regions-previously-having-document-limits"></a>Régions précédemment sujettes à des limites de nombre de documents

Si le portail indique une limite de nombre de documents, soit votre service a été créé avant fin 2017, soit il a été créé sur un centre de données utilisant des clusters de moindre capacité pour l’hébergement des services Recherche Azure :

+ Australie Est
+ Est de l'Asie
+ Inde centrale
+ Ouest du Japon
+ Centre-Ouest des États-Unis

Pour les services soumis aux limites de document, les limites maximales suivantes s’appliquent :

|  Gratuit | De base | S1 | S2 | S3 | S3&nbsp;HD |
|-------|-------|----|----|----|-------|
|  10 000 |1 million |15 millions par partition ou 180 millions par service |60 millions par partition ou 720 millions par service |120 millions par partition ou 1,4 milliard par service |1 million par index ou 200 millions par partition |

Si votre service est sujet à des limites qui vous bloquent, créez un service, puis republiez tout le contenu sur ce service. Il n’existe aucun mécanisme de réapprovisionnement homogène de votre service sur du nouveau matériel en coulisses.

> [!Note] 
> Pour les services haute densité S3 créés après fin 2017, la limite à 200 millions de documents par partition a été retirée, mais pas la limite à 1 million.


### <a name="document-size-limits-per-api-call"></a>Limites de taille de document par appel d’API

La taille maximale d’un document lors de l’appel d’une API d’index est d’environ 16 mégaoctets.

La taille du document est en fait une limite de taille du corps de requête de l’API d’index. Étant donné que vous pouvez transmettre en une seule fois un lot de plusieurs documents à l’API d’index, la limite de taille dépend en réalité du nombre de documents présents dans le lot. Pour un lot comprenant un seul document, la taille maximale du document est de 16 Mo de JSON.

Pour réduire la taille du document, pensez à exclure de la requête les données non requêtables. Les images et autres données binaires ne sont pas directement requêtables et ne doivent pas être stockées dans l’index. Pour intégrer les données non requêtables dans les résultats de la recherche, définissez un champ sans possibilité de recherche qui stocke une référence URL à la ressource.

## <a name="indexer-limits"></a>Limites de l’indexeur

La limite des services De base créés après 2017 a augmenté pour atteindre 15 index, sources de données, compétences et indexeurs.

Les opérations gourmandes en ressources, comme l’analyse d’image dans l’indexation des objets blob Azure ou le traitement en langage naturel dans la recherche cognitive, ont des durées maximales d’exécution plus courtes, afin de permettre la prise en charge des autres travaux d’indexation. Si un travail d’indexation ne peut pas être terminé dans le délai maximal autorisé, essayez de l’exécuter selon une planification. Le planificateur effectue le suivi de l’état de l’indexation. Si une tâche d’indexation planifiée est interrompue pour une raison quelconque, à la prochaine exécution planifiée, l’indexeur peut repartir de là où il s’était arrêté.

| Ressource | Gratuit&nbsp;<sup>1</sup> | De base&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|
| -------- | ----------------- | ----------------- | --- | --- | --- | --- |
| Nombre maximal d’indexeurs |3 |5 ou 15|50 |200 |200 |N/A |
| Nombre maximal de sources de données |3 |5 ou 15 |50 |200 |200 |N/A |
| Compétences maximales <sup>4</sup> |3 |5 ou 15 |50 |200 |200 |N/A |
| Charge d’indexation maximale par appel |10 000 documents |Limité uniquement par le nombre maximal de documents |Limité uniquement par le nombre maximal de documents |Limité uniquement par le nombre maximal de documents |Limité uniquement par le nombre maximal de documents |N/A |
| Durée maximale d’exécution <sup>5</sup> | 1-3 minutes |24 heures |24 heures |24 heures |24 heures |N/A  |
| Valeur maximale pour l’exécution de recherches cognitives ou de l’indexation d’objets blob avec analyse d’images <sup>5</sup> | 3-10 minutes |2 heures |2 heures |2 heures |2 heures |N/A  |
| Indexeur d’objets blob : taille maximale des objets blob, en Mo |16 |16 |128 |256 |256 |N/A  |
| Indexeur d’objets blob : nombre maximal de caractères du contenu extrait d’un objet blob |32 000 |64 000 |4 millions |4 millions |4 millions |N/A |

<sup>1</sup> Les services du niveau Gratuit bénéficient d’une durée d’exécution maximale de l’indexeur de 3 minutes pour les sources d’objets blob, et de 1 minute pour toutes les autres sources de données.

<sup>2</sup> La limite des services De base créés après fin 2017 a augmenté pour atteindre 15 index, sources de données et indexeurs. Celle des services créés avant est à 5.

<sup>3</sup> Les services S3 HD ne comprennent pas de prise en charge de l’indexeur.

<sup>4</sup> Nombre maximal de 30 compétences par group de compétences.

<sup>5</sup> Les charges de travail de recherche cognitive et d’analyse d’images dans l’indexation d’objets blob Azure ont des durées d’exécution plus courtes que l’indexation de texte standard. L’analyse d’images et le traitement en langage naturel sont gourmands en ressources et consomment une quantité disproportionnée de la puissance de traitement disponible. Leur durée d’exécution a été réduite pour donner aux autres travaux de la file d’attente la possibilité de s’exécuter.  

## <a name="queries-per-second-qps"></a>Requêtes par seconde

Les estimations du nombre de requêtes par seconde doivent être développées indépendamment par chaque client. La taille et la complexité des index et des requêtes ainsi que la quantité de trafic sont les principaux facteurs qui déterminent le nombre de requêtes par seconde. Si ces facteurs sont inconnus, il est impossible d’établir des estimations significatives.

Les estimations sont plus prévisibles si elles sont calculées sur des services qui s’exécutent sur des ressources dédiées (niveaux De base et Standard). Vous pouvez mieux estimer les requêtes par seconde, car vous contrôlez davantage de paramètres. Pour obtenir de l’aide sur la manière d’aborder les estimations, consultez [Performances et optimisation de Recherche Azure](search-performance-optimization.md).

## <a name="api-request-limits"></a>Limites de requête d’API
* 16 Mo maximum par requête <sup>1</sup>
* La longueur maximale d’une URL est de 8 Ko
* 1 000 documents maximum par lot de charges, de fusions ou de suppressions d’index
* 32 champs maximum dans la clause $orderby
* La taille maximale des termes de recherche du texte encodé en UTF-8 est de 32 766 octets (32 Ko moins 2 octets)

<sup>1</sup> Dans la Recherche Azure, le corps d’une requête est soumis à une limite supérieure de 16 Mo. Cela signifie qu’une limite pratique est imposée au contenu des champs individuels ou des collections qui ne font pas l’objet de limites théoriques (pour plus d’informations sur la composition et les restrictions des champs, consultez [Types de données pris en charge](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)).

## <a name="api-response-limits"></a>Limites de réponse d’API
* 1 000 documents maximum retournés par page de résultats de recherche
* 100 suggestions maximum retournées par requête d’API de suggestion

## <a name="api-key-limits"></a>Limites de clés API
Les clés API sont utilisées pour l'authentification de service. Il existe deux types de clé API. Les clés d’administration sont spécifiées dans l’en-tête de la demande et accordent un accès complet en lecture et en écriture au service. Les clés de requête sont en lecture seule, spécifiées dans l’URL et généralement distribuées aux applications clientes.

* 2 clés administrateur maximum par service
* 50 clés de requête maximum par service
