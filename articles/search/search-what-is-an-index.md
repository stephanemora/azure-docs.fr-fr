---
title: Définition et concepts d’index - Recherche Azure
description: Introduction aux termes et aux concepts des index dans Recherche Azure, notamment à la structure physique d’un index inversé.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
services: search
ms.service: search
ms.topic: conceptual
ms.date: 11/08/2017
ms.custom: seodec2018
ms.openlocfilehash: 5a39021367c2f51125876081e9174eb372d7b9c9
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54353156"
---
# <a name="indexes-and-indexing-overview-in-azure-search"></a>Index et vue d'ensemble de l'indexation dans Recherche Azure

Dans Recherche Azure, un *index* est une banque permanente de *documents* et d'autres éléments utilisés pour la recherche filtrée et en texte intégral sur un service Recherche Azure. Un document correspond à une unité de données pouvant faire l’objet d’une recherche dans votre index. Par exemple, un détaillant de commerce électronique peut posséder un document pour chaque article qu’il vend, un organisme d’information peut posséder un document par article, et ainsi de suite. Pour comparer avec des éléments de base de données plus familiers, d’un point de vue conceptuel, un *index* est similaire à une *table*, et les *documents* équivalent plus ou moins aux *lignes* d’une table.

Lorsque vous ajoutez ou chargez des documents, ou lorsque vous soumettez des requêtes de recherche à Recherche Azure, vous envoyez vos demandes à un index spécifique de votre service de recherche. Le processus consistant à ajouter des documents dans un index s'appelle l'*indexation*.

## <a name="field-types-and-attributes-in-an-azure-search-index"></a>Types et attributs de champ dans un index Azure Search
Lorsque vous définissez votre schéma, vous devez spécifier le nom, le type et les attributs de chaque champ de votre index. Le type de champ classifie les données stockées dans ce champ. Les attributs sont définis sur des champs individuels pour spécifier la façon dont le champ est utilisé. Les tableaux ci-après énumèrent les types et les attributs que vous pouvez spécifier.

### <a name="field-types"></a>Types de champ
| type | Description |
| --- | --- |
| *Edm.String* |Texte pouvant éventuellement être tokenisé pour la recherche en texte intégral (césure de mots, recherche de radical, etc). |
| *Collection(Edm.String)* |Liste de chaînes pouvant être éventuellement tokenisées pour la recherche en texte intégral. En théorie, il n’existe pas de limite supérieure quant au nombre d’éléments d’une collection, mais la limite supérieure de 16 Mo sur la taille de charge utile s’applique aux collections. |
| *Edm.Boolean* |Contient des valeurs true/false. |
| *Edm.Int32* |Valeurs entières 32 bits. |
| *Edm.Int64* |Valeurs entières 64 bits. |
| *Edm.Double* |Données numériques à double précision. |
| *Edm.DateTimeOffset* |Dates et heures représentées au format OData V4 (par exemple, `yyyy-MM-ddTHH:mm:ss.fffZ` ou `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`). |
| *Edm.GeographyPoint* |Point représentant un emplacement géographique de la planète. |

Pour plus d’informations sur les types de données pris en charge par le service Recherche Azure, consultez [cet article](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types).

### <a name="field-attributes"></a>Attributs de champ
| Attribut | Description |
| --- | --- |
| *Clé* |Chaîne fournissant un ID unique à chaque document utilisé pour rechercher des documents. Chaque index doit avoir une clé. Un seul champ peut être la clé, et son type doit être défini sur Edm.String. |
| *Affichable dans les résultats d’une recherche* |Définit si un champ peut être retourné dans un résultat de recherche. |
| *Filtrable* |Permet d’utiliser le champ dans des requêtes de filtre. |
| *Triable* |Permet à une requête de trier les résultats de recherche à l’aide de ce champ. |
| *À choix multiple* |Permet d’utiliser un champ pour le filtrage autonome dans une structure de [navigation par facettes](search-faceted-navigation.md) par un utilisateur. En général, les champs contenant des valeurs répétitives que vous pouvez utiliser pour regrouper plusieurs documents (par exemple, plusieurs documents appartenant à une seule marque ou catégorie de service) sont les mieux adaptés en tant que facettes. |
| *Possibilité de recherche* |Indique que le champ peut faire l’objet d’une recherche en texte intégral. |

Pour plus d’informations sur les attributs d’index du service Recherche Azure, consultez [cet article](https://docs.microsoft.com/rest/api/searchservice/Create-Index).

## <a name="guidance-for-defining-an-index-schema"></a>Instructions de définition d’un schéma d’index
Lorsque vous concevez votre index, prenez le temps lors de la phase de planification de réfléchir chaque décision. Il est important de ne perdre de vue ni votre expérience de recherche ni vos besoins métiers lorsque vous concevez votre index, chaque champ devant être associé à des [attributs corrects](https://docs.microsoft.com/rest/api/searchservice/Create-Index). La modification d’un index une fois déployé implique la reconstruction et le rechargement des données.

Si vos besoins en stockage de données changent au fil du temps, vous pouvez augmenter ou diminuer la capacité en ajoutant ou en supprimant des partitions. Pour plus d’informations, consultez [Gérer votre service de recherche dans Azure](search-manage.md) ou [Limites de service](search-limits-quotas-capacity.md).

