---
title: Ajouter des analyseurs linguistiques – Recherche Azure
description: Analyse de texte lexicale multilingue pour les requêtes et les index formulés dans une langue autre que l’anglais dans la Recherche Azure.
ms.date: 01/31/2019
services: search
ms.service: search
ms.topic: conceptual
author: Yahnoosh
ms.author: jlembicz
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: b5c562994c169a8c5d51ee31a9606c5c40162603
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2019
ms.locfileid: "56007607"
---
# <a name="add-language-analyzers-to-an-azure-search-index"></a>Ajouter des analyseurs linguistiques à un index de Recherche Azure

Un *analyseur linguistique* est un composant spécifique d’un [moteur de recherche en texte intégral](https://docs.microsoft.com/azure/search/search-lucene-query-architecture) qui effectue une analyse lexicale suivant les règles linguistiques de la langue cible. Chaque champ offrant une possibilité de recherche dispose d’une propriété `analyzer`. Si votre index contient des chaînes traduites, par exemple des champs distincts pour le texte en anglais et le texte en chinois, vous pouvez spécifier des analyseurs linguistiques sur chaque champ pour accéder aux riches fonctionnalités linguistiques de ces analyseurs.  

La Recherche Azure prend en charge 35 analyseurs qui s’appuient sur Lucene et 50 analyseurs exploitant la technologie propriétaire Microsoft de traitement du langage naturel utilisée dans Office et Bing.

## <a name="compare-language-analyzer-types"></a>Comparer les types d’analyseurs linguistiques 

Certains développeurs peuvent préférer la solution open source, plus familière et simple de Lucene. Les analyseurs linguistiques Lucene sont plus rapides, mais les analyseurs Microsoft proposent des fonctionnalités avancées, comme la lemmatisation, la décomposition en mots (dans les langues comme l’allemand, le danois, le néerlandais, le suédois, le norvégien, l’estonien, le finnois, le hongrois et le slovaque) et la reconnaissance d’entités (URL, adresses e-mail, dates, numéros). Si possible, vous devez comparer les analyseurs Microsoft et Lucene pour savoir lequel vous convient le mieux. 

L’indexation avec les analyseurs Microsoft est en moyenne trois fois plus lente qu’avec leurs équivalents Lucene, en fonction de la langue. Les performances de recherche ne doivent pas être trop affectées pour les requêtes de taille moyenne. 

### <a name="english-analyzers"></a>Analyseurs pour l’anglais

L’analyseur par défaut est Lucene Standard, qui fonctionne bien pour l’anglais, mais peut-être pas aussi bien que l’analyseur d’anglais de Lucene ou l’analyseur d’anglais de Microsoft. 
 
+ L'analyseur d'anglais de Lucene est une extension de l'analyseur standard. Il supprime la marque du possessif (« ’s ») à la fin des mots, applique l’algorithme de recherche de radical de Porter et supprime les mots vides de l’anglais.  

+ L’analyseur d’anglais de Microsoft procède par lemmatisation plutôt que par recherche de radical. Il gère donc beaucoup mieux les formes fléchies et irrégulières, ce qui donne des résultats de recherche plus pertinents. 

 > [!Tip]
 > La [démonstration de l’analyseur Recherche](https://alice.unearth.ai/) présente une comparaison point par point des résultats produits par l’analyseur Lucene standard, l’analyseur linguistique anglais de Lucene et de l’outil de traitement du langage naturel anglais de Microsoft. Pour chaque recherche, les résultats de chaque analyseur sont affichés dans des volets adjacents.

## <a name="analyzer-configuration"></a>Configuration des analyseurs

Pour chaque champ dans la définition d'index, vous pouvez affecter à la propriété `analyzer` un nom d'analyseur qui spécifie la langue et le fournisseur. Le même analyseur sera appliqué lors de l’indexation et de la recherche pour ce champ. Il peut par exemple y avoir des champs distincts pour des descriptions d'hôtels en anglais, en français et en espagnol côte à côte dans le même index.  

Utilisez le paramètre de requête **searchFields** pour spécifier le champ de langue dans vos requêtes. Vous pouvez consulter des exemples de requêtes comportant la propriété analyzer dans la Recherche dans les documents. 

Pour plus d’informations sur les propriétés d’index, voir [Créer un index &#40;API REST du service Recherche Azure&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index). Pour plus d’informations sur l’analyse dans la Recherche Azure, voir [Analyseurs dans la Recherche Azure](https://docs.microsoft.com/azure/search/search-analyzers).

## <a name="analyzer-list"></a>Liste d’analyseurs  
 Voici la liste des langues prises en charge avec les noms d’analyseur Lucene et Microsoft.  

|Langage|Nom de l’analyseur Microsoft|Nom de l’analyseur Lucene|  
|--------------|-----------------------------|--------------------------|  
|Arabe|ar.microsoft|ar.lucene|  
|Arménien||hy.lucene|  
|Bangla|bn.microsoft||  
|Basque||eu.lucene|  
|Bulgare|bg.microsoft|bg.lucene|  
|Catalan|ca.microsoft|ca.lucene|  
|Chinois simplifié|zh-Hans.microsoft|zh-Hans.lucene|  
|Chinois traditionnel|zh-Hant.microsoft|zh-Hant.lucene|  
|Croate|hr.microsoft||  
|Tchèque|cs.microsoft|cs.lucene|  
|Danois|da.microsoft|da.lucene|  
|Néerlandais|nl.microsoft|nl.lucene|  
|Anglais|en.microsoft|en.lucene|  
|Estonien|et.Microsoft||  
|Finnois|fi.microsoft|fi.lucene|  
|Français|fr.Microsoft|fr.lucene|  
|Galicien||gl.lucene|  
|Allemand|de.Microsoft|de.lucene|  
|Grec|el.microsoft|el.lucene|  
|Goudjrati|gu.microsoft||  
|Hébreu|he.microsoft||  
|Hindi|hi.microsoft|hi.lucene|  
|Hongrois|hu.microsoft|hu.lucene|  
|Islandais|is.microsoft||  
|Indonésien|id.microsoft|id.lucene|  
|Irlandais||ga.lucene|  
|Italien|it.microsoft|it.lucene|  
|Japonais|ja.Microsoft|ja.lucene|  
|Kannada|ka.microsoft||  
|Coréen|ko.microsoft|ko.lucene|  
|Letton|lv.microsoft|lv.lucene|  
|Lituanien|lt.microsoft||  
|Malayalam|ml.microsoft||  
|Malais (latin)|ms.microsoft||  
|Marathi|mr.microsoft||  
|Norvégien|nb.Microsoft|no.lucene|  
|Persan||fa.lucene|  
|Polonais|pl.microsoft|pl.lucene|  
|Portugais (Brésil)|pt-Br.microsoft|pt-Br.lucene|  
|Portugais (Portugal)|pt-Pt.microsoft|pt-Pt.lucene|  
|Pendjabi|pa.microsoft||  
|Roumain|ro.microsoft|ro.lucene|  
|Russe|ru.microsoft|ru.lucene|  
|Serbe (cyrillique)|sr-cyrillic.microsoft||  
|Serbe (latin)|sr-latin.microsoft||  
|Slovaque|sk.microsoft||  
|Slovène|sl.microsoft||  
|Espagnol|es.Microsoft|es.lucene|  
|Suédois|sv.microsoft|sv.lucene|  
|Tamoul|ta.microsoft||  
|Télougou|te.microsoft||  
|Thaï|th.microsoft|th.lucene|  
|Turc|tr.microsoft|tr.lucene|  
|Ukrainien|uk.microsoft||  
|Ourdou|ur.microsoft||  
|Vietnamien|vi.microsoft||  

 Tous les analyseurs dont le nom est annoté **lucene** s’appuient sur les [analyseurs linguistiques d’Apache Lucene](https://lucene.apache.org/core/4_9_0/core/overview-summary.html ).

## <a name="see-also"></a>Voir aussi  
 [Créer un index &#40;API REST du service Recherche Azure&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  
 [Classe AnalyzerName](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername)  
 [Vidéo : module 7 de la présentation MVA de la Recherche Azure](https://channel9.msdn.com/Series/Adding-Microsoft-Azure-Search-to-Your-Websites-and-Apps/07).  

