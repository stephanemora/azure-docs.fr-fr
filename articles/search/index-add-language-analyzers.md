---
title: Ajouter des analyseurs de langue à des champs de chaîne
titleSuffix: Azure Cognitive Search
description: Analyse de texte lexicale multilingue pour les requêtes et les index formulés dans une langue autre que l’anglais dans Recherche cognitive Azure.
manager: nitinme
author: Yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/10/2019
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
ms.openlocfilehash: ea7a62210f48b216d3f98f6359447eacf15cf821
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75460803"
---
# <a name="add-language-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Ajouter des analyseurs de langue à des champs de chaîne dans l’index de Recherche cognitive Azure

Un *analyseur linguistique* est un type spécifique d’[analyseur de texte](search-analyzers.md) qui effectue une analyse lexicale selon les règles linguistiques de la langue cible. Chaque champ pouvant faire l’objet d’une recherche dispose d’une propriété **analyzer**. Si votre index contient des chaînes traduites, par exemple des champs distincts pour le texte en anglais et le texte en chinois, vous pouvez spécifier des analyseurs linguistiques sur chaque champ pour accéder aux riches fonctionnalités linguistiques de ces analyseurs.  

Recherche cognitive Azure prend en charge 35 analyseurs qui s’appuient sur Lucene et 50 analyseurs exploitant la technologie propriétaire Microsoft de traitement du langage naturel utilisée dans Office et Bing.

## <a name="comparing-analyzers"></a>Comparaison des analyseurs

Certains développeurs peuvent préférer la solution open source, plus familière et simple de Lucene. Les analyseurs linguistiques Lucene sont plus rapides, mais les analyseurs Microsoft proposent des fonctionnalités avancées, comme la lemmatisation, la décomposition en mots (dans les langues comme l’allemand, le danois, le néerlandais, le suédois, le norvégien, l’estonien, le finnois, le hongrois et le slovaque) et la reconnaissance d’entités (URL, adresses e-mail, dates, numéros). Si possible, vous devez comparer les analyseurs Microsoft et Lucene pour savoir lequel vous convient le mieux. 

L’indexation avec les analyseurs Microsoft est en moyenne trois fois plus lente qu’avec leurs équivalents Lucene, en fonction de la langue. Les performances de recherche ne doivent pas être trop affectées pour les requêtes de taille moyenne. 

### <a name="english-analyzers"></a>Analyseurs pour l’anglais

L’analyseur par défaut est Lucene Standard, qui fonctionne bien pour l’anglais, mais peut-être pas aussi bien que l’analyseur d’anglais de Lucene ou l’analyseur d’anglais de Microsoft. 
 
+ L'analyseur d'anglais de Lucene est une extension de l'analyseur standard. Il supprime la marque du possessif (« ’s ») à la fin des mots, applique l’algorithme de recherche de radical de Porter et supprime les mots vides de l’anglais.  

+ L’analyseur d’anglais de Microsoft procède par lemmatisation plutôt que par recherche de radical. Il gère donc beaucoup mieux les formes fléchies et irrégulières, ce qui donne des résultats de recherche plus pertinents. 

## <a name="configuring-analyzers"></a>Configuration des analyseurs

Les analyseurs linguistiques sont utilisés en l’état. Pour chaque champ de la définition d’index, vous pouvez attribuer à la propriété **analyzer** un nom d’analyseur qui spécifie la langue et la pile linguistique (Microsoft ou Lucene). Le même analyseur sera appliqué lors de l’indexation et de la recherche pour ce champ. Il peut par exemple y avoir des champs distincts pour des descriptions d'hôtels en anglais, en français et en espagnol côte à côte dans le même index.

> [!NOTE]
> Il n’est pas possible d’utiliser un autre analyseur de langage au moment de l’indexation qu’au moment de la requête pour un champ. Cette fonctionnalité est réservée aux [analyseurs personnalisés](index-add-custom-analyzers.md). C’est la raison pour laquelle, si vous tentez de définir les propriétés **searchAnalyzer** ou **indexAnalyzer** sur le nom d’un analyseur de langage, l’API REST renvoie une réponse d’erreur. Vous devez utiliser la propriété **analyzer** à la place.

Utilisez le paramètre de requête **searchFields** pour spécifier le champ de langue dans vos requêtes. Vous pouvez consulter les exemples de requêtes qui contiennent la propriété analyzer dans [Recherche dans les documents](https://docs.microsoft.com/rest/api/searchservice/search-documents). 

Pour plus d’informations sur les propriétés d’index, voir [Créer un index &#40;API REST Recherche cognitive Azure&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index). Pour plus d’informations sur l’analyse dans Recherche cognitive Azure, voir [Analyseurs dans Recherche cognitive Azure](https://docs.microsoft.com/azure/search/search-analyzers).

<a name="language-analyzer-list"></a>

## <a name="language-analyzer-list"></a>Liste d’analyseurs linguistiques 
 Voici la liste des langues prises en charge avec les noms d’analyseur Lucene et Microsoft.  

|Langage|Nom de l’analyseur Microsoft|Nom de l’analyseur Lucene|  
|--------------|-----------------------------|--------------------------|  
|Arabe|ar.microsoft|ar.lucene|  
|Arménien||hy.lucene|  
|Bangla|bn.microsoft||  
|Basque||eu.lucene|  
|Bulgare|bg.microsoft|bg.lucene|  
|Catalan|ca.microsoft|ca.lucene|  
|Chinois (simplifié)|zh-Hans.microsoft|zh-Hans.lucene|  
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
|Kannada|kn.microsoft||  
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

 Tous les analyseurs dont le nom est annoté **lucene** s’appuient sur les [analyseurs linguistiques d’Apache Lucene](https://lucene.apache.org/core/6_6_1/core/overview-summary.html ).

## <a name="see-also"></a>Voir aussi  

+ [Créer un index &#40;API REST Recherche cognitive Azure&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  

+ [Classe AnalyzerName](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername)  

