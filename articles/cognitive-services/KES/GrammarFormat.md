---
title: Format de grammaire dans l’API Service d’exploration des connaissances | Microsoft Docs
description: Découvrez le format de grammaire dans l’API Service d’exploration des connaissances (KES) de Cognitive Services.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: b64025be2f5a9708162da475c1f037d7f253d2c6
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/06/2018
ms.locfileid: "37865751"
---
# <a name="grammar-format"></a>Format de grammaire
La grammaire est un fichier XML qui spécifie le jeu pondéré des requêtes en langage naturel que le service peut interpréter, ainsi que la façon dont ces requêtes en langage naturel sont traduites en expressions de requête sémantique.  La syntaxe de grammaire est basée sur [SRGS](http://www.w3.org/TR/speech-grammar/), une norme W3C pour les grammaires de reconnaissance vocale, avec des extensions pour prendre en charge l’intégration des index de données et les fonctions sémantiques.

Le présent article décrit chacun des éléments syntaxiques qui peuvent être utilisés dans une grammaire.  Pour obtenir une grammaire complète qui illustre l’utilisation de ces éléments en contexte, consultez [cet exemple](#example).

### <a name="grammar-element"></a>Élément grammar 
L’élément `grammar` est l’élément de niveau supérieur dans le XML de spécification de la grammaire.  L’attribut obligatoire `root` spécifie le nom de la règle racine qui définit le point de départ de la grammaire.

```xml
<grammar root="GetPapers">
```

### <a name="import-element"></a>Élément import
L’élément `import` importe une définition de schéma à partir d’un fichier externe pour activer les références d’attributs. L’élément doit être un enfant de l’élément `grammar` de niveau supérieur et il doit apparaître avant tout élément `attrref`. L’attribut obligatoire `schema` spécifie le nom d’un fichier de schéma situé dans le même répertoire que le fichier XML de grammaire. L’élément obligatoire `name` spécifie l’alias de schéma utilisé par les éléments `attrref` ultérieurs lors du référencement d’attributs définis dans ce schéma.

```xml
  <import schema="academic.schema" name="academic"/>
```

### <a name="rule-element"></a>Élément rule
L’élément `rule` définit une règle de grammaire, une unité structurelle qui spécifie un jeu d’expressions de requête que le système peut interpréter.  L’élément doit être un enfant de l’élément `grammar` de niveau supérieur.  L’attribut obligatoire `id` spécifie le nom de la règle, qui est référencé à partir d’éléments `grammar` ou `ruleref`.

Un élément `rule` définit un ensemble d’extensions autorisées.  Les jetons de texte correspondent directement à la requête d’entrée.  Les éléments `item` spécifient les répétitions et modifient les probabilités d’interprétation.  Les éléments `one-of` indiquent des choix alternatifs.  Les éléments `ruleref` permettent de construire des expansions plus complexes à partir d’expansions simples.  Les éléments `attrref` autorisent les correspondances par rapport aux valeurs d’attributs de l’index.  Les éléments `tag` spécifient la sémantique de l’interprétation et peuvent modifier la probabilité d’interprétation.

```xml
<rule id="GetPapers">...</rule>
```

### <a name="example-element"></a>Élément example
L’élément facultatif `example` spécifie des exemples d’expressions qui peuvent être acceptées par la définition `rule` contenante.  Vous pouvez l’utiliser à des fins de documentation et/ou de tests automatisés.

```xml
<example>papers about machine learning by michael jordan</example>
```

### <a name="item-element"></a>Élément item
L’élément `item` regroupe une séquence de constructions de grammaire.  Vous pouvez l’utiliser pour indiquer les répétitions de la séquence d’expansion, ou pour spécifier des alternatives conjointement avec l’élément `one-of`.

Quand un élément `item` n’est pas un enfant d’un élément `one-of`, il peut spécifier la répétition de la séquence englobée en assignant l’attribut `repeat` à une valeur de compteur.  Une valeur de compteur « *n* » (où *n* est un entier) indique que la séquence doit avoir lieu exactement *n* fois.  Une valeur de compteur « *m*-*n* » permet à la séquence d’apparaître entre *m* et *n* fois inclus.  Une valeur de compteur « *m* » indique que la séquence doit apparaître au moins *m* fois.  L’attribut facultatif `repeat-logprob` peut être utilisé afin de modifier la probabilité d’interprétation pour chaque répétition supplémentaire au-delà de la valeur minimale.

```xml
<item repeat="1-" repeat-logprob="-10">...</item>
```

Quand des éléments `item` apparaissent en tant qu’enfants d’un élément `one-of`, ils définissent le jeu d’alternatives d’expansion.  Dans ce cas, l’attribut facultatif `logprob` spécifie la probabilité logarithmique relative parmi les différents choix.  Étant donné une probabilité *p* comprise entre 0 et 1, la probabilité logarithmique correspondante peut être calculée comme log(*p*), où log() est la fonction logarithme naturel.  Si vous ne spécifiez rien, `logprob` prend par défaut la valeur 0, qui ne modifie pas la probabilité d’interprétation.  Notez que la probabilité logarithmique est toujours une valeur à virgule flottante négative ou 0.

```xml
<one-of>
  <item>by</item>
  <item logprob="-0.5">written by</item>
  <item logprob="-1">authored by</item>
</one-of>
```

### <a name="one-of-element"></a>Élément one-of
L’élément `one-of` spécifie des expansions alternatives parmi l’un des éléments `item` enfants.  Seuls des éléments `item` peuvent apparaître à l’intérieur d’un élément `one-of`.  Vous pouvez spécifier les probabilités relatives parmi les différents choix à l’aide de l’attribut `logprob` dans chaque `item` enfant.

```xml
<one-of>
  <item>by</item>
  <item logprob="-0.5">written by</item>
  <item logprob="-1">authored by</item>
</one-of>
```

### <a name="ruleref-element"></a>Élément ruleref
L’élément `ruleref` spécifie les expansions valides par le biais de références à un autre élément `rule`.  Grâce aux éléments `ruleref`, vous pouvez générer des expressions plus complexes à partir de règles simples.  L’attribut obligatoire `uri` indique le nom de l’élément `rule` référencé à l’aide de la syntaxe « #*nom_règle* ».  Pour capturer la sortie sémantique de la règle référencée, utilisez l’attribut facultatif `name` pour spécifier le nom d’une variable à laquelle la sortie sémantique est affectée.
 
```xml
<ruleref uri="#GetPaperYear" name="year"/>
```

### <a name="attrref-element"></a>Élément attrref
L’élément `attrref` référence un attribut d’index, ce qui permet la mise en correspondance par rapport à des valeurs d’attributs observées dans l’index.  L’attribut obligatoire `uri` spécifie le nom du schéma d’index et le nom de l’attribut à l’aide de la syntaxe « *nom_schéma*#*nom_attribut* ».  Il doit y avoir un élément `import` précédent qui importe le schéma nommé *nom_schéma*.  Le nom d’attribut est le nom d’un attribut défini dans le schéma correspondant.

Outre la mise en correspondance de l’entrée d’utilisateur, l’élément `attrref` retourne également comme sortie un objet de requête structuré qui sélectionne le sous-ensemble des objets dans l’index correspondant à la valeur d’entrée.  Utilisez l’attribut facultatif `name` pour spécifier le nom de la variable où la sortie d’objet de requête doit être stockée.  L’objet de requête peut être composé avec d’autres objets de requête pour former des expressions plus complexes.  Pour plus d’informations, consultez [Interprétation sémantique](SemanticInterpretation.md).  

```xml
<attrref uri="academic#Keyword" name="keyword"/>
```

#### <a name="query-completion"></a>Complétion de requête 
Pour prendre en charge les complétions de requête lors de l’interprétation des requêtes utilisateur partielles, chaque attribut référencé doit inclure « starts_with » en tant qu’opération dans la définition de schéma.  Étant donné un préfixe de requête utilisateur, `attrref` établira une correspondance avec toutes les valeurs dans l’index qui complètent le préfixe, et générera chaque valeur complète en tant qu’interprétation distincte de la grammaire.  

Exemples :
* La mise en correspondance de `<attrref uri="academic#Keyword" name="keyword"/>` par rapport au préfixe de requête « dat » génère une interprétation pour les ouvrages relatifs à « database », une interprétation pour les ouvrages relatifs à « data mining », et ainsi de suite.
* La mise en correspondance de `<attrref uri="academic#Year" name="year"/>` par rapport au préfixe de requête « 200 » génère une interprétation pour les ouvrages de l’année « 2000 », une interprétation pour les ouvrages de l’année « 2001 », et ainsi de suite.

#### <a name="matching-operations"></a>Opérations de mise en correspondance
En plus de la mise en correspondance exacte, certains types d’attributs prennent également en charge les correspondances de préfixe et d’inégalité par le biais de l’attribut facultatif `op`.  Si aucun objet de l’index ne contient une valeur qui correspond, le chemin de grammaire est bloqué et le service ne génère aucune interprétation traversant ce chemin de grammaire.   L’attribut `op` prend par défaut la valeur « eq ».

```xml
in <attrref uri="academic#Year" name="year"/>
before <attrref uri="academic#Year" op="lt" name="year"/
```

Le tableau suivant répertorie les valeurs `op` prises en charge pour chaque type d’attribut.  Leur utilisation exige que l’opération d’index correspondante soit comprise dans la définition d’attribut de schéma.

| Type d’attribut | Valeur op | Description | Opération d’index
|----|----|----|----|
| Chaîne | eq | Correspondance exacte de chaîne | equals |
| Chaîne | starts_with | Correspondance de préfixe de chaîne | starts_with |
| Int32, Int64, Double | eq |  Correspondance d’égalité numérique | equals |
| Int32, Int64, Double | lt, le, gt, ge | Correspondance d’inégalité numérique (<, <=, >, >=) | is_between |
| Int32, Int64, Double | starts_with | Correspondance de préfixe de valeur en notation décimale | starts_with |

Exemples :
* `<attrref uri="academic#Year" op="lt" name="year"/>` établit une correspondance avec la chaîne d’entrée « 2000 » et retourne tous les ouvrages publiés avant l’année 2000 non comprise.
* `<attrref uri="academic#Year" op="lt" name="year"/>` n’établit pas de correspondance avec la chaîne d’entrée « 20 », car l’index ne contient aucun ouvrage publié avant l’année 20.
* `<attrref uri="academic#Keyword" op="starts_with" name="keyword"/>` établit une correspondance avec la chaîne d’entrée « dat » et retourne dans une interprétation unique les ouvrages relatifs à « database », « data mining », et ainsi de suite.  Il s’agit d’un cas d’usage rare.
* `<attrref uri="academic#Year" op="starts_with" name="year"/>` établit une correspondance avec la chaîne d’entrée « 20 » et retourne dans une interprétation unique les ouvrages publiés en 200-299, 2000-2999, et ainsi de suite.  Il s’agit d’un cas d’usage rare.

### <a name="tag-element"></a>Élément tag
L’élément `tag` spécifie comment un chemin à travers la grammaire doit être interprété.  Il contient une séquence d’instructions se terminant par des points-virgules.  Une instruction peut être une assignation d’un littéral ou d’une variable à une autre variable.  Elle peut également assigner la sortie d’une fonction avec 0, 1 ou plusieurs paramètres à une variable.  Chaque paramètre de fonction peut être spécifié à l’aide d’un littéral ou d’une variable.  Si la fonction ne retourne pas de sortie, l’assignation est omise.  L’étendue de la variable est locale à la règle qui la contient.

```xml
<tag>x = 1; y = x;</tag>
<tag>q = All(); q = And(q, q2);</tag>
<tag>AssertEquals(x, 1);</tag>
```

Chaque `rule` dans la grammaire a une variable prédéfinie nommée « out », qui représente la sortie sémantique de la règle.  Sa valeur est calculée en évaluant chacune des instructions sémantiques traversées par le chemin par le biais de l’élément `rule` correspondant à l’entrée de requête utilisateur.  La valeur assignée à la variable « out » à la fin de l’évaluation est la sortie de sémantique de la règle.  La sortie sémantique de l’interprétation d’une requête utilisateur par rapport à la grammaire est la sortie sémantique de la règle racine.

Certaines instructions peuvent modifier la probabilité d’un chemin d’interprétation en introduisant un décalage de probabilité logarithmique additif.  Certaines instructions peuvent rejeter totalement l’interprétation si les conditions spécifiées ne sont pas remplies.

Pour obtenir la liste des fonctions sémantiques prises en charge, consultez [Fonctions sémantiques](SemanticInterpretation.md#semantic-functions).

## <a name="interpretation-probability"></a>Probabilité d’interprétation
La probabilité d’un chemin d’interprétation par le biais de la grammaire est la probabilité logarithmique cumulée de tous les éléments `<item>` et fonctions sémantiques rencontrés en chemin.  Elle décrit la probabilité relative de mise en correspondance d’une séquence d’entrée particulière.

Étant donné une probabilité *p* comprise entre 0 et 1, la probabilité logarithmique correspondante peut être calculée comme log(*p*), où log() est la fonction logarithme naturel.  L’utilisation de probabilités logarithmiques permet au système d’accumuler la probabilité jointe d’un chemin d’interprétation par simple addition.  Elle évite également le dépassement négatif de capacité en virgule flottante courant avec ce type de calcul de probabilité jointe.  Notez que, par sa conception, la probabilité logarithmique est toujours une valeur à virgule flottante négative ou 0, les valeurs supérieures indiquant une probabilité plus élevée.

<a name="example"></a>
## <a name="example"></a>Exemples
Voici un exemple de code XML tiré du domaine de publications académiques qui illustre les différents éléments d’une grammaire :

```xml
<grammar root="GetPapers">

  <!-- Import academic data schema-->
  <import schema="academic.schema" name="academic"/>
  
  <!-- Define root rule-->
  <rule id="GetPapers">
    <example>papers about machine learning by michael jordan</example>
    
    papers
    <tag>
      yearOnce = false;
      isBeyondEndOfQuery = false;
      query = All();
    </tag>
  
    <item repeat="1-" repeat-logprob="-10">
      <!-- Do not complete additional attributes beyond end of query -->
      <tag>AssertEquals(isBeyondEndOfQuery, false);</tag>
        
      <one-of>
        <!-- about <keyword> -->
        <item logprob="-0.5">
          about <attrref uri="academic#Keyword" name="keyword"/>
          <tag>query = And(query, keyword);</tag>
        </item>
        
        <!-- by <authorName> [while at <authorAffiliation>] -->
        <item logprob="-1">
          by <attrref uri="academic#Author.Name" name="authorName"/>
          <tag>authorQuery = authorName;</tag>
          <item repeat="0-1" repeat-logprob="-1.5">
            while at <attrref uri="academic#Author.Affiliation" name="authorAffiliation"/>
            <tag>authorQuery = And(authorQuery, authorAffiliation);</tag>
          </item>
          <tag>
            authorQuery = Composite(authorQuery);
            query = And(query, authorQuery);
          </tag>
        </item>
        
        <!-- written (in|before|after) <year> -->
        <item logprob="-1.5">
          <!-- Allow this grammar path to be traversed only once -->
          <tag>
            AssertEquals(yearOnce, false);
            yearOnce = true;
          </tag>
          <ruleref uri="#GetPaperYear" name="year"/>
          <tag>query = And(query, year);</tag>
        </item>
      </one-of>

      <!-- Determine if current parse position is beyond end of query -->
      <tag>isBeyondEndOfQuery = GetVariable("IsBeyondEndOfQuery", "system");</tag>
    </item>
    <tag>out = query;</tag>
  </rule>
  
  <rule id="GetPaperYear">
    <tag>year = All();</tag>
    written
    <one-of>
      <item>
        in <attrref uri="academic#Year" name="year"/>
      </item>
      <item>
        before
        <one-of>
          <item>[year]</item>
          <item><attrref uri="academic#Year" op="lt" name="year"/></item>
        </one-of>
      </item>
      <item>
        after
        <one-of>
          <item>[year]</item>
          <item><attrref uri="academic#Year" op="gt" name="year"/></item>
        </one-of>
      </item>
    </one-of>
    <tag>out = year;</tag>
  </rule>
</grammar>
```
