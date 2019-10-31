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
ms.date: 10/22/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: c300a6477daa5759a68d5d11d40b1a71b46bd808
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793838"
---
# <a name="paper-entity"></a>Entité Article

<sub> * Les attributs suivants sont propres à l’entité Article. (Ty = '0') </sub>

Nom | Description | Type | Opérations
--- | --- | --- | ---
AA.AfId | ID de l’affiliation de l’auteur | Int64 | Égal à
AA.AfN | Nom de l’affiliation de l’auteur | Chaîne | Equals, StartsWith
AA.AuId | ID de l’auteur | Int64 | Égal à
AA.AuN | Nom normalisé de l’auteur | Chaîne | Equals, StartsWith
AA.DAuN | Nom d’origine de l’auteur | Chaîne | Aucun
AA.DAfN | Nom d’origine de l’affiliation | Chaîne | Aucun
AA.S | Position numérique dans la liste des auteurs | Int32 | Égal à
CC | Nombre de citations | Int32 | Aucun  
C.CId | ID de la série de conférences | Int64 | Égal à
C.CN | Nom de la série de conférences | Chaîne | Equals, StartsWith
D | Date de publication au format AAAA-MM-JJ | Date | Equals, IsBetween
E | Métadonnées étendues (voir le tableau ci-dessous) | Chaîne | N/A  
ECC | Estimation du nombre de citations | Int32 | Aucun
F.DFN | Nom du champ d’étude d’origine | Chaîne | Aucun
F.FId | ID du champ d’étude | Int64 | Égal à
F.FN | Nom normalisé du champ d’étude normalisé | Chaîne | Equals, StartsWith
Id | ID du document | Int64 | Égal à
J.JId | ID du journal | Int64 | Égal à
J.JN | Nom du journal | Chaîne | Equals, StartsWith
Pt | Type de publication (0 : inconnu, 1 : article de journal, 2 : brevet, 3 : document de conférence, 4 : Chapitre de livre, 5 : Livre, 6 : Entrée de référence de livre, 7 : Jeu de données, 8 : Référentiel | Chaîne | Égal à
RId | Liste des ID de document référencés | Int64[] | Égal à
Ti | Titre normalisé | Chaîne | Equals, StartsWith
W | Mots uniques dans le titre | String[] | Égal à
O | Année de publication | Int32 | Equals, IsBetween

## <a name="extended-metadata-attributes"></a>Attributs de métadonnées étendues ##

Nom | Description               
--- | ---
BT | Type de document BibTex (« a » : Article de journal, « b » : Livre, « c » : Chapitre de livre, « p » : Papier de conférence)
BV | Nom du site BibTex
CC | Contextes de citation : liste des ID de l’article référencé et contexte correspondant dans l’article (par exemple, [{123:["les renards bruns sont connus pour leurs sauts comme cela est mentionné dans l’article 123", "les chiens paresseux est une appellation erronée ancienne comme cela est indiqué dans l’article 123"]})
DN | Titre du papier d’origine
DOI | Digital Object Identifier : identificateur d’objet numérique
FP | Première page de papier dans une publication
I | Numéro de publication
IA | Inverted Abstract : résumé inversé
IA.IndexLength | Nombre d’éléments dans l’index (nombre de mots du résumé)
IA.InvertedIndex | Liste des mots du résumé et position de chaque mot dans le résumé d’origine (par exemple, [{"les":[0, 15, 30]}, {"renards":[1]}, {"bruns":2]}])
LP | Première page de papier dans une publication
PB | Publisher
S | Sources : liste des sources web de l’article, triés par rang statique
S.Ty | Type de source (1 : HTML, 2 : Texte, 3 : PDF, 4 : DOC, 5 : PPT, 6 : XLS, 7 : PS)
S.U | URL de la source
V | Volume de publication
VFN | Nom complet du journal ou de la salle conférence
VSN | Nom abrégé du journal ou de la salle conférence
