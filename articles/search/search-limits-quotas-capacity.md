---
title: Limites de service pour les niveaux tarifaires et les références SKU - Recherche Azure
description: Limites de service permettant de planifier la capacité et limites maximales des requêtes et réponses de la Recherche Azure.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: heidist
ms.openlocfilehash: 2d3b74476def5bdf46a6292996f0af9162b20b43
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71947777"
---
# <a name="service-limits-in-azure-search"></a>Limites de service d’Azure Search
Les limites maximales du stockage, des charges de travail et des quantités d’index, de documents et autres objets varient selon que vous [approvisionnez le service Recherche Azure](search-create-service-portal.md) avec les niveaux tarifaires **Gratuit**, **De base**, **Standard** ou **Stockage optimisé**.

+ **Gratuit** est un service partagé multi-locataire qui est fourni avec votre abonnement Azure. Les requêtes d’indexation et d’interrogation s’exécutent sur les réplicas et les partitions utilisés par d’autres locataires.

+ **De base** : fournit des ressources de calcul dédiées pour des charges de travail de production à plus petite échelle, mais partage une infrastructure réseau avec d’autres locataires.

+ Le niveau **Standard** est exécuté sur des ordinateurs dédiés, avec une capacité de stockage et de traitement beaucoup plus grande, et ce, à chaque niveau. Le niveau Standard se décompose en quatre catégories : S1, S2, S3 et S3 HD.

+ **Stockage optimisé** s’exécute sur des ordinateurs dédiés avec plus de stockage total, de bande passante de stockage et de mémoire que **Standard**. Stockage optimisé est disponible en deux niveaux : L1 et L2

> [!NOTE]
> À compter du 1er juillet, tous les niveaux seront à la disposition générale, y compris le niveau À stockage optimisé. Tous les tarifs sont disponibles sur la page [Détails de la tarification](https://azure.microsoft.com/pricing/details/search/).

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

| Ressource | Gratuit | De base&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| -------- | ---- | ------------------- | --- | --- | --- | --- | --- | --- |
| Nombre maximal d’index |3 |5 ou 15 |50 |200 |200 |1 000 par partition ou 3 000 par service |10 |10 |
| Nombre maximal de champs simples par index |1 000 |100 |1 000 |1 000 |1 000 |1 000 |1 000 |1 000 |
| Nombre maximal de champs de collection complexe par index |40 |40 |40 |40 |40 |40 |40 |40 |
| Nombre maximal d’éléments dans toutes les collections complexes par document |3000 |3000 |3000 |3000 |3000 |3000 |3000 |3000 |
| Profondeur maximale des champs complexes |10 |10 |10 |10 |10 |10 |10 |10 |
| Nombre maximal de [générateurs de suggestions](https://docs.microsoft.com/rest/api/searchservice/suggesters) par index |1 |1 |1 |1 |1 |1 |1 |1 |
| Nombre maximal de [profils de score](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) par index |100 |100 |100 |100 |100 |100 |100 |100 |
| Nombre maximal de fonctions par profil |8 |8 |8 |8 |8 |8 |8 |8 |

<sup>1</sup> Les services de base créés avant décembre 2017 présentent des limites inférieures (5 au lieu de 15) sur les index. Le niveau De base est la seule référence soumise à une limite inférieure de 100 champs par index.

<a name="document-limits"></a>

## <a name="document-limits"></a>Limites du document 

Depuis octobre 2018, il n’y a plus de limites au nombre de documents<sup>1</sup> pour tout nouveau service créé, quels que soient le niveau facturable (De base, S1, S2, S3, S3 HD) dans une région. Si la plupart des régions prennent en charge des nombres illimités de documents depuis novembre/décembre 2017, cinq régions ont continué d’imposer de telles limites. Selon le moment et le lieu de création d’un service de recherche, vous pourriez exécuter un service toujours sujet à des limites de nombre de documents.

Pour savoir si votre service a un nombre limité de documents, consultez la vignette Utilisation dans la page de présentation de votre service. Les nombres de documents sont illimités ou soumis à une limite établie en fonction du niveau.

  ![Mosaïque Utilisation](media/search-limits-quotas-capacity/portal-usage-tile.png)

<sup>1</sup> Même s’il n’existe aucune limite de nombre de documents spécifique d’une référence SKU, chaque index reste soumis à une limite maximale de sécurité pour garantir la stabilité du service. Cette limite provient de Lucene. Chaque document Azure Search est indexé en interne en tant qu’un ou plusieurs documents Lucene. Le nombre de documents Lucene par document de recherche Azure dépend du nombre total d’éléments dans les champs de collection complexes. Chaque élément est indexé en tant que document Lucene distinct. Par exemple, un document comportant 3 éléments dans un champ de collection complexe est indexé en tant que 4 documents Lucene : 1 pour le document et 3 pour les éléments. Le nombre maximal de documents Lucene est approximativement de 25 milliards par index.

### <a name="regions-previously-having-document-limits"></a>Régions précédemment sujettes à des limites de nombre de documents

Si le portail indique une limite de nombre de documents, soit votre service a été créé avant fin 2017, soit il a été créé sur un centre de données utilisant des clusters de moindre capacité pour l’hébergement des services Recherche Azure :

+ Australie Est
+ Asie Est
+ Inde centrale
+ OuJapon Est
+ Centre-USA Ouest

Pour les services soumis aux limites de document, les limites maximales suivantes s’appliquent :

|  Gratuit | De base | S1 | S2 | S3 | S3&nbsp;HD |
|-------|-------|----|----|----|-------|
|  10 000 |1&nbsp;million |15 millions par partition ou 180 millions par service |60 millions par partition ou 720 millions par service |120 millions par partition ou 1,4 milliard par service |1 million par index ou 200 millions par partition |

Si votre service est sujet à des limites qui vous bloquent, créez un service, puis republiez tout le contenu sur ce service. Il n’existe aucun mécanisme de réapprovisionnement homogène de votre service sur du nouveau matériel en coulisses.

> [!Note] 
> Pour les services haute densité S3 créés après fin 2017, la limite à 200 millions de documents par partition a été retirée, mais pas la limite à 1 million.


### <a name="document-size-limits-per-api-call"></a>Limites de taille de document par appel d’API

La taille maximale d’un document lors de l’appel d’une API d’index est d’environ 16 mégaoctets.

La taille du document est en fait une limite de taille du corps de requête de l’API d’index. Étant donné que vous pouvez transmettre en une seule fois un lot de plusieurs documents à l’API d’index, la limite de taille dépend en réalité du nombre de documents présents dans le lot. Pour un lot comprenant un seul document, la taille maximale du document est de 16 Mo de JSON.

Pour réduire la taille du document, pensez à exclure de la requête les données non requêtables. Les images et autres données binaires ne sont pas directement requêtables et ne doivent pas être stockées dans l’index. Pour intégrer les données non requêtables dans les résultats de la recherche, définissez un champ sans possibilité de recherche qui stocke une référence URL à la ressource.

## <a name="indexer-limits"></a>Limites de l’indexeur

Les durées d’exécution maximales existent pour fournir équilibre et stabilité au service dans son ensemble, mais l’indexation des jeux de données volumineux peut prendre plus de temps que la valeur maximale ne le permet. Si un travail d’indexation ne peut pas être terminé dans le délai maximal autorisé, essayez de l’exécuter selon une planification. Le planificateur effectue le suivi de l’état de l’indexation. Si une tâche d’indexation planifiée est interrompue pour une raison quelconque, à la prochaine exécution planifiée, l’indexeur peut repartir de là où il s’était arrêté.


| Ressource | Gratuit&nbsp;<sup>1</sup> | De base&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|L1 |L2 |
| -------- | ----------------- | ----------------- | --- | --- | --- | --- | --- | --- |
| Nombre maximal d’indexeurs |3 |5 ou 15|50 |200 |200 |N/A |10 |10 |
| Nombre maximal de sources de données |3 |5 ou 15 |50 |200 |200 |N/A |10 |10 |
| Compétences maximales <sup>4</sup> |3 |5 ou 15 |50 |200 |200 |N/A |10 |10 |
| Charge d’indexation maximale par appel |10 000 documents |Limité uniquement par le nombre maximal de documents |Limité uniquement par le nombre maximal de documents |Limité uniquement par le nombre maximal de documents |Limité uniquement par le nombre maximal de documents |N/A |Aucune limite |Aucune limite |
| Planification minimale | 5 minutes |5 minutes |5 minutes |5 minutes |5 minutes |5 minutes |5 minutes | 5 minutes |
| Durée maximale d’exécution <sup>5</sup> | 1-3 minutes |24 heures |24 heures |24 heures |24 heures |N/A  |24 heures |24 heures |
| Valeur maximale pour l’exécution de recherches cognitives ou de l’indexation d’objets blob avec analyse d’images <sup>5</sup> | 3-10 minutes |2 heures |2 heures |2 heures |2 heures |N/A  |2 heures |2 heures |
| Indexeur d’objets blob : taille maximale des objets blob, en Mo |16 |16 |128 |256 |256 |N/A  |256 |256 |
| Indexeur d’objets blob : nombre maximal de caractères du contenu extrait d’un objet blob |32 000 |64 000 |4&nbsp;millions |4&nbsp;millions |4&nbsp;millions |N/A |4&nbsp;millions |4&nbsp;millions |

<sup>1</sup> Les services du niveau Gratuit bénéficient d’une durée d’exécution maximale de l’indexeur de 3 minutes pour les sources d’objets blob, et de 1 minute pour toutes les autres sources de données. Pour l’indexation de l’intelligence artificielle qui appelle la méthode dans Cognitive Services, les services gratuits sont limités à 20 transactions gratuites par jour, une transaction étant définie comme un document qui traverse le pipeline d’enrichissement.

<sup>2</sup> Les services de base créés avant décembre 2017 présentent des limites inférieures (5 au lieu de 15) sur les index, les sources de données et les ensembles de compétences.

<sup>3</sup> Les services S3 HD ne comprennent pas de prise en charge de l’indexeur.

<sup>4</sup> Nombre maximal de 30 compétences par group de compétences.

<sup>5</sup> Les charges de travail de recherche cognitive et d’analyse d’images dans l’indexation d’objets blob Azure ont des durées d’exécution plus courtes que l’indexation de texte standard. L’analyse d’images et le traitement en langage naturel sont gourmands en ressources et consomment une quantité disproportionnée de la puissance de traitement disponible. Leur durée d’exécution a été réduite pour donner aux autres travaux de la file d’attente la possibilité de s’exécuter.  

## <a name="synonym-limits"></a>Limites des synonymes

Le nombre maximal de mappages de synonymes autorisé varie en fonction du niveau de tarification. Chaque règle peut avoir jusqu’à 20 expansions, où une expansion est un terme equivalvent. Par exemple, pour le mot « chat », l’association avec « minou », « félin » et « felis » (le genre des chats) est comptée comme 3 expansions.

| Ressource | Gratuit | De base | S1 | S2 | S3 | S3-HD |L1 | L2 |
| -------- | -----|------ |----|----|----|-------|---|----|
| Mappages de synonymes maximum |3 |3|5\. |10 |20 |20 | 10 | 10 |
| Nombre maximal de règles par mappage |5 000 |20000|20000 |20000 |20000 |20000 | 20000 | 20000  |

## <a name="queries-per-second-qps"></a>Requêtes par seconde

Les estimations du nombre de requêtes par seconde doivent être développées indépendamment par chaque client. La taille et la complexité des index et des requêtes ainsi que la quantité de trafic sont les principaux facteurs qui déterminent le nombre de requêtes par seconde. Si ces facteurs sont inconnus, il est impossible d’établir des estimations significatives.

Les estimations sont plus prévisibles si elles sont calculées sur des services qui s’exécutent sur des ressources dédiées (niveaux De base et Standard). Vous pouvez mieux estimer les requêtes par seconde, car vous contrôlez davantage de paramètres. Pour obtenir de l’aide sur la manière d’aborder les estimations, consultez [Performances et optimisation de Recherche Azure](search-performance-optimization.md).

Pour les niveaux Stockage optimisé, attendez-vous à un plus faible débit des requêtes et à une latence plus élevée que les niveaux Standard.  La méthodologie pour estimer les performances des requêtes est la même que pour les niveaux Standard.

## <a name="data-limits-cognitive-search"></a>Limites de données (recherche cognitive)

Un [pipeline de recherche cognitive](cognitive-search-concept-intro.md) qui effectue des appels à une ressource Analyse de texte pour la [reconnaissance d’entité](cognitive-search-skill-entity-recognition.md), l’[extraction d’expressions clés](cognitive-search-skill-keyphrases.md), l’[analyse des sentiments](cognitive-search-skill-sentiment.md) et la [détection de la langue](cognitive-search-skill-language-detection.md) est soumis à des limites de données. La taille maximale d’un enregistrement doit être de 50 000 caractères telle que mesurée par [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Si vous avez besoin de découper vos données avant de les envoyer à l’Analyseur des sentiments, utilisez la [compétence Fractionnement du texte](cognitive-search-skill-textsplit.md).

## <a name="throttling-limits"></a>Limites de la limitation

Les requêtes de recherche et les demandes d’indexation sont limitées dès que le système s’approche de la capacité maximale. Le comportement de la limitation varie en fonction des API. Les API de requête (recherche/suggestion/saisie semi-automatique) et les API d’indexation se limitent dynamiquement en fonction de la charge du service. Les API d’indexation sont soumises à des limites de taux de requêtes statiques. 

Limites de taux de requêtes statiques pour les opérations liées à un index :

+ Lister les index (GET /indexes) : 5 par seconde par unité de recherche
+ Obtenir un index (GET /indexes/myindex) : 10 par seconde par unité de recherche
+ Créer un index (POST /indexes) : 12 par minute par unité de recherche
+ Créer ou mettre à jour un index (PUT /indexes/myindex) : 6 par seconde par unité de recherche
+ Supprimer un index (DELETE /indexes/myindex) : 12 par minute par unité de recherche 

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
Les clés API sont utilisées pour l’authentification de service. Il existe deux types de clé API. Les clés d’administration sont spécifiées dans l’en-tête de la demande et accordent un accès complet en lecture et en écriture au service. Les clés de requête sont en lecture seule, spécifiées dans l’URL et généralement distribuées aux applications clientes.

* 2 clés administrateur maximum par service
* 50 clés de requête maximum par service
