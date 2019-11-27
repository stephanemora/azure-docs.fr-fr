---
title: Attributs d’entité Papier – API Connaissances universitaires
titlesuffix: Azure Cognitive Services
description: Découvrez les attributs que vous pouvez utiliser avec l’entité Papier dans l’API Connaissances universitaires.
services: cognitive-services
author: DarrinEide
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: fc3bb5987c31fe718951a3cae02ed7c4ddc29957
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123085"
---
# <a name="paper-entity"></a>Entité Article

> [!NOTE]
> Les attributs suivants sont propres à l’entité Article. (Ty = '0')

Nom | Description | Type | Opérations
--- | --- | --- | ---
AA.AfId | ID de l’affiliation de l’auteur | Int64 | Égal à
AA.AfN | Nom de l’affiliation de l’auteur | Chaîne | Equals, StartsWith
AA.AuId | ID de l’auteur | Int64 | Égal à
AA.AuN | Nom normalisé de l’auteur | Chaîne | Equals, StartsWith
AA.DAuN | Nom d’origine de l’auteur | Chaîne | Aucun
AA.DAfN | Nom d’origine de l’affiliation | Chaîne | Aucun
AA.S | Position numérique dans la liste des auteurs | Int32 | Égal à
BT | Type de document BibTex (« a » : Article de journal, « b » : Livre, « c » : Chapitre de livre, « p » : Papier de conférence) | Chaîne | Aucun
BV | Nom du site BibTex | Chaîne | Aucun
C.CId | ID de la série de conférences | Int64 | Égal à
C.CN | Nom de la série de conférences | Chaîne | Equals, StartsWith
CC | Nombre de citations | Int32 | Aucun  
CitCon | Contextes de citation</br></br>Liste des ID de l’article référencé et contexte correspondant dans l’article (par exemple, [{123:["les renards bruns sont connus pour leurs sauts comme cela est mentionné dans l’article 123", "les chiens paresseux est une appellation erronée ancienne comme cela est indiqué dans l’article 123"]}) | Personnalisée | Aucun
D | Date de publication au format AAAA-MM-JJ | Date | Equals, IsBetween
DN | Titre du papier d’origine | Chaîne | Aucun
DOI | Digital Object Identifier : identificateur d’objet numérique | Chaîne | Equals, StartsWith
E | Métadonnées étendues</br></br>**IMPORTANT** : Cet attribut est déprécié et uniquement pris en charge pour les applications héritées. Une demande individuelle de cet attribut (c’est-à-dire, attributes=Id,Ti,E) entraîne le retour de tous les attributs des métadonnées étendues dans une *chaîne JSON sérialisée*.</br></br>Tous les attributs contenus dans les métadonnées étendues sont désormais disponibles sous la forme d’un attribut de niveau supérieur et peuvent être demandés en tant que tel (c’est-à-dire, attributes=Id,Ti,DOI,IA). | [Étendu](#extended) | Aucun
ECC | Estimation du nombre de citations | Int32 | Aucun
F.DFN | Nom du champ d’étude d’origine | Chaîne | Aucun
F.FId | ID du champ d’étude | Int64 | Égal à
F.FN | Nom normalisé du champ d’étude normalisé | Chaîne | Equals, StartsWith
FP | Première page de papier dans une publication | Chaîne | Égal à
I | Numéro de publication | Chaîne | Égal à
IA | Résumé inversé | [InvertedAbstract](#invertedabstract) | Aucun
Id | ID du document | Int64 | Égal à
J.JId | ID du journal | Int64 | Égal à
J.JN | Nom du journal | Chaîne | Equals, StartsWith
LP | Première page de papier dans une publication | Chaîne | Égal à
PB | Publisher | Chaîne | Aucun
Pt | Type de publication (0 : inconnu, 1 : article de journal, 2 : brevet, 3 : document de conférence, 4 : Chapitre de livre, 5 : Livre, 6 : Entrée de référence de livre, 7 : Jeu de données, 8 : Référentiel | Chaîne | Égal à
RId | Liste des ID de document référencés | Int64[] | Égal à
S | Liste des URL sources de l’article, triées par pertinence | [Source](#source)[] | Aucun
Ti | Titre normalisé | Chaîne | Equals, StartsWith
V | Volume de publication | Chaîne | Égal à
VFN | Nom complet du journal ou de la salle conférence | Chaîne | Aucun
VSN | Nom abrégé du journal ou de la salle conférence | Chaîne | Aucun
W | Mots uniques dans le titre | String[] | Égal à
O | Année de publication | Int32 | Equals, IsBetween

## <a name="extended"></a>Étendu
> [!IMPORTANT]
> Cet attribut est déprécié et uniquement pris en charge pour les applications héritées. Une demande individuelle de cet attribut (c’est-à-dire, attributes=Id,Ti,E) entraîne le retour de tous les attributs des métadonnées étendues dans une *chaîne JSON sérialisée*.</br></br>Tous les attributs contenus dans les métadonnées étendues sont désormais disponibles sous la forme d’un attribut de niveau supérieur et peuvent être demandés en tant que tel (c’est-à-dire, attributes=Id,Ti,DOI,IA).

> [!IMPORTANT]
> Prise en charge de la demande d’attributs étendus individuels à l’aide de l’étendue « E. », c.-à-d. que « E.DN » est déprécié. Même si elle est toujours techniquement prise en charge, la demande d’attributs étendus individuels à l’aide de l’étendue « E. » entraîne le retour de la valeur d’attribut à deux emplacements dans la réponse JSON, à savoir en tant qu’élément de l’objet « E » et en tant qu’attribut de niveau supérieur.

Nom | Description | Type | Opérations
--- | --- | --- | ---
BT | Type de document BibTex (« a » : Article de journal, « b » : Livre, « c » : Chapitre de livre, « p » : Papier de conférence) | Chaîne | Aucun
BV | Nom du site BibTex | Chaîne | Aucun
CC | Contextes de citation</br></br>Liste des ID de l’article référencé et contexte correspondant dans l’article (par exemple, [{123:["les renards bruns sont connus pour leurs sauts comme cela est mentionné dans l’article 123", "les chiens paresseux est une appellation erronée ancienne comme cela est indiqué dans l’article 123"]}) | Personnalisée | Aucun
DN | Titre du papier d’origine | Chaîne | Aucun
DOI | Digital Object Identifier : identificateur d’objet numérique | Chaîne | Aucun
FP | Première page de papier dans une publication | Chaîne | Aucun
I | Numéro de publication | Chaîne | Aucun
IA | Inverted Abstract : résumé inversé | [InvertedAbstract](#invertedabstract) | Aucun
LP | Première page de papier dans une publication | Chaîne | Aucun
PB | Publisher | Chaîne | Aucun
S | Liste des URL sources de l’article, triées par pertinence | [Source](#source)[] | Aucun
V | Volume de publication | Chaîne | Aucun
VFN | Nom complet du journal ou de la salle conférence | Chaîne | Aucun
VSN | Nom abrégé du journal ou de la salle conférence | Chaîne | Aucun

## <a name="invertedabstract"></a>InvertedAbstract

> [!IMPORTANT]
> Les attributs InvertedAbstract ne peuvent pas être demandés directement en tant qu’attribut de retour. Si vous avez besoin d’un attribut individuel, vous devez demander l’attribut « IA » de niveau supérieur, autrement dit, pour obtenir IA.IndexLength, demandez attributes=IA.

Nom | Description | Type | Opérations
--- | --- | --- | ---
IndexLength | Nombre d’éléments dans l’index (nombre de mots du résumé) | Int32 | Aucun
InvertedIndex | Liste des mots du résumé et position de chaque mot dans le résumé d’origine (par exemple, [{"les":[0, 15, 30]}, {"renards":[1]}, {"bruns":[2]}]) | Personnalisée | Aucun

## <a name="source"></a>Source

> [!IMPORTANT]
> Les attributs Source ne peuvent pas être demandés directement en tant qu’attribut de retour. Si vous avez besoin d’un attribut individuel, vous devez demander l’attribut « S » de niveau supérieur, autrement dit, pour obtenir S.U, demandez attributes=S.

Nom | Description | Type | Opérations
--- | --- | --- | ---
Ty | Type d’URL source (1 : HTML, 2 : Texte, 3 : PDF, 4 : DOC, 5 : PPT, 6 : XLS, 7 : PS) | Chaîne | Aucun
U | URL de la source | Chaîne | Aucun
