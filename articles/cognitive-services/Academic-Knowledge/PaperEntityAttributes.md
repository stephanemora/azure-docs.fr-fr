---
title: Attributs d’entité Papier – API Connaissances universitaires
titlesuffix: Azure Cognitive Services
description: Découvrez les attributs que vous pouvez utiliser avec l’entité Papier dans l’API Connaissances universitaires.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: ae238a3c3af091b764147fcde8aa2a98986da92e
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50158888"
---
# <a name="paper-entity"></a>Entité Article

<sub> * Les attributs suivants sont propres à l’entité Article. (Ty = '0') </sub>


NOM    |Description                                        |type       | Opérations
------- | ------------------------------------------------- | --------- | ----------------------------
ID      |L’ID d’entité                                          |Int64      |Égal à
Ti      |Titre du papier                                        |Chaîne     |Equals,<br/>StartsWith
L       |Code langue de l’article séparé par « \@@@ »            |Chaîne     |Égal à
O       |Année de l’article                                         |Int32      |Equals,<br/>IsBetween
D       |Date de l’article                                         |Date       |Equals,<br/>IsBetween
CC      |Nombre de citations                                     |Int32      |Aucun  
ECC     |Estimation du nombre de citations                           |Int32      |Aucun
AA.AuN  |Nom de l’auteur                                        |Chaîne     |Equals,<br/>StartsWith
AA.AuId |ID de l’auteur                                          |Int64      |Égal à
AA.AfN  |Nom de l’affiliation de l’auteur                            |Chaîne     |Equals,<br/>StartsWith
AA.AfId |ID de l’affiliation de l’auteur                              |Int64      |Égal à
AA.S    |Ordre de l’auteur de l’article                         |Int32      |Égal à
F.FN    |Nom du champ d’étude                                |Chaîne     |Equals,<br/>StartsWith
F.FId   |ID du champ d’étude                                  |Int64      |Égal à
J.JN    |Nom du journal                                       |Chaîne     |Equals,<br/>StartsWith
J.JId   |ID du journal                                         |Int64      |Égal à
C.CN    |Nom de la série de conférences                             |Chaîne     |Equals,<br/>StartsWith
C.CId   |ID de la série de conférences                               |Int64      |Égal à
RId     |ID des articles référencés                              |Int64[]    |Égal à
W       |Mots figurant dans le titre et le résumé de l’article                |String[]   |Égal à
E       |Métadonnées étendues (voir le tableau ci-dessous)                |Chaîne     |Aucun  
        


## <a name="extended-metadata-attributes"></a>Attributs de métadonnées étendues ##

NOM    | Description               
--------|---------------------------    
DN      | Nom d’affichage de l’article 
S       | Sources : liste des sources web de l’article, triés par rang statique
S.Ty    | Type de source (1 : HTML, 2 : Texte, 3 : PDF, 4 : DOC, 5 : PPT, 6 : XLS, 7 : PS)
S.U     | URL de la source
VFN     | Venue Full Name : nom complet du journal ou de la conférence
VSN     | Venue Short Name : nom abrégé du journal ou de la conférence
V       | Volume : volume du journal
BV      | Nom du journal
BT      | 
PB      | Abréviations du journal
I       | Issue : numéro du journal
FP      | FirstPage : première page de l’article
LP      | LastPage : dernière page de l’article
DOI     | Digital Object Identifier : identificateur d’objet numérique
CC      | Contextes de citation : liste des ID de l’article référencé et contexte correspondant dans l’article (par exemple, [{123:["les renards bruns sont connus pour leurs sauts comme cela est mentionné dans l’article 123", "les chiens paresseux est une appellation erronée ancienne comme cela est indiqué dans l’article 123"]})
IA      | Inverted Abstract : résumé inversé
IA.IndexLength| Nombre d’éléments dans l’index (nombre de mots du résumé)
IA.InvertedIndex| Liste des mots du résumé et position de chaque mot dans le résumé d’origine (par exemple, [{"les":[0, 15, 30]}, {"renards":[1]}, {"bruns":2]}])
