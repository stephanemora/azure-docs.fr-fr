---
title: Format de schéma - API Service d’exploration des connaissances
titlesuffix: Azure Cognitive Services
description: Découvrez le format de schéma dans l’API Service d’exploration des connaissances (KES).
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 51a812762659bcc67762b82e9c120772065aab53
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60814413"
---
# <a name="schema-format"></a>Format de schéma

Le schéma est spécifié dans un fichier JSON qui décrit la structure des attributs des objets dans le fichier de données utilisé pour créer l’index.  Le schéma spécifie le nom, le type de données, les opérations facultatives et la liste facultative des synonymes de chaque attribut.  Un objet peut avoir 0 ou plusieurs valeurs pour chaque attribut.  Voici un exemple simplifié d’un domaine de publication académique :

``` json
{
  "attributes":[
    {"name":"Title", "type":"String"},
    {"name":"Year", "type":"Int32"},
    {"name":"Author", "type":"Composite"},
    {"name":"Author.Id", "type":"Int64", "operations":["equals"]},
    {"name":"Author.Name", "type":"String"},
    {"name":"Author.Affiliation", "type":"String"},
    {"name":"Keyword", "type":"String", "synonyms":"Keyword.syn"}
  ]
}
```

Les noms d’attribut sont des identificateurs sensibles à la casse commençant par une lettre et composés uniquement de lettres (A-Z), de chiffres (0-9), et d’un trait de soulignement (\_).  L’attribut réservé « logprob » est utilisé pour spécifier les probabilités logarithmiques naturelles relatives entre les objets.

## <a name="attribute-type"></a>Type d’attribut

Voici une liste des types de données d’attributs pris en charge :

| Type | Description | Opérations | Exemples |
|------|-------------|------------|---------|
| `String` | Chaîne (de 1 à 1 024 caractères) | equals, starts_with | « Hello World » |
| `Int32` | Entier 32 bits signé | equals, starts_with, is_between | 2016 |
| `Int64` | Entier 64 bits signé | equals, starts_with, is_between | 9876543210 |
| `Double` | Valeur à virgule flottante double précision | equals, starts_with, is_between | 1.602e-19 |
| `Date` | Date (du 01/01/1400 au 31/12/9999) | equals, is_between | « 14/03/2016 » |
| `Guid` | Identificateur global unique | equals | "602DD052-CC47-4B23-A16A-26B52D30C05B" |
| `Blob` | Données non indexées compressées en interne | *Aucun* | « Permettre à chaque personne et à chaque organisation sur cette planète d'accomplir davantage » |
| `Composite` | Composition de plusieurs sous-attributs| *N/A* | { "Name":"harry shum", "Affiliation":"microsoft" } |

Les attributs de chaîne sont utilisés pour représenter les valeurs de chaîne qui peuvent apparaître dans la requête utilisateur.  Ils prennent en charge l’opération de correspondance exacte *equals*, ainsi que l’opération *starts_with* pour les scénarios d’achèvement de requête, tels que la mise en correspondance de « micros » avec « microsoft ».  Le non-respect de la casse et la correspondance approximative pour gérer les fautes d’orthographe seront pris en charge dans une version ultérieure.

Int32/Int64/Les attributs doubles sont utilisés pour représenter des valeurs numériques.  L’opération *is_between* permet la prise en charge de l’inégalité (lt, le, gt, ge) au moment de l’exécution.  L’opération *starts_with* prend en charge les scénarios d’achèvement de requête, tels que la mise en correspondance de « 20 » avec « 2016 » ou « 3 » avec « 3,14 ».

Les attributs de date permettent d’encoder efficacement les valeurs de date.  L’opération *is_between* permet la prise en charge de l’inégalité (lt, le, gt, ge) au moment de l’exécution.
  
Les attributs guid permettent de représenter efficacement les valeurs GUID avec une prise en charge par défaut de l’opération *equals*.

Les attributs d’objet blob permettent d’encoder efficacement un nombre potentiellement important d’objets blob de données pour la recherche de l’exécution à partir de l’objet correspondant, sans prise en charge d’une opération d’indexation en fonction du contenu des valeurs d’objet blob.

### <a name="composite-attributes"></a>Attributs composites

Les attributs composites permettent de représenter un regroupement de valeurs d’attribut.  Le nom de chaque sous-attribut commence par le nom de l’attribut composite suivi par « . ».  Les valeurs des attributs composites sont spécifiées en tant qu’objet JSON contenant les valeurs d’attribut imbriquées.  Les attributs composites peuvent avoir plusieurs valeurs d’objet.  Toutefois, les attributs composites peuvent ne pas contenir de sous-attributs qui sont eux-mêmes des attributs composites.

Pour l’exemple de publication académique ci-dessus, cela permet au service de rechercher des articles rédigés par « Harry Shum » lorsqu’il travaillait chez « Microsoft ».  Sans attributs composites, le service ne peut que rechercher des articles dont l’un des auteurs est « Harry Shum » et dont l’un des auteurs travaille chez « Microsoft ».  Pour plus d’informations, consultez [Requêtes composites](SemanticInterpretation.md#composite-function).

## <a name="attribute-operations"></a>Opérations d’attribut

Chaque attribut est indexé par défaut pour prendre en charge toutes les opérations disponibles pour le type de données d’attribut.  Si aucune opération particulière n’est obligatoire, l’ensemble des opérations indexées peut être spécifié explicitement pour réduire la taille de l’index.  Dans l’extrait suivant de l’exemple de schéma ci-dessus, l’attribut Author.Id est indexé pour ne prendre en charge que l’opération *equals*, mais pas les autres opérations *starts_with* et *is_between*  pour les attributs Int32.
```json
{"name":"Author.Id", "type":"Int32", "operations":["equals"]}
```

Lorsqu’un attribut est référencé à l’intérieur d’une grammaire, l’opération *starts_with* doit être spécifiée dans le schéma afin que le service puisse générer des saisies semi-automatiques à partir de requêtes partielles.  

## <a name="attribute-synonyms"></a>Synonymes d’attribut

Il est souvent souhaitable de faire référence à une valeur d’attribut de chaîne particulière avec un synonyme.  Par exemple, les utilisateurs peuvent désigner « Microsoft » par « MSFT » ou « MS ».  Dans ces cas, la définition d’attribut peut spécifier le nom d’un fichier de schéma situé dans le même répertoire que le fichier de schéma.  Chaque ligne du fichier de synonymes représente une entrée de synonyme dans le format JSON suivant : `["<canonical>", "<synonym>"]`.  Dans l’exemple de schéma, « AuthorName.syn » est un fichier JSON qui contient des valeurs de synonyme pour l’attribut Author.Name.

`{"name":"Author.Name", "type":"String", "synonyms":"AuthorName.syn"}`


Une valeur canonique peut avoir plusieurs synonymes.  Plusieurs valeurs canoniques peuvent partager un synonyme commun.  Dans ce cas, le service devra résoudre l’ambiguïté par le biais de plusieurs interprétations.  Voici un exemple de fichier de synonymes AuthorName.syn correspondant au schéma ci-dessus :
```json
["harry shum","heung-yeung shum"]
["harry shum","h shum"]
["henry shum","h shum"]
...
```
