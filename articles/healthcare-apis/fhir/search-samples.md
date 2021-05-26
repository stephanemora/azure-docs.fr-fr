---
title: Rechercher l’API Azure pour FHIR dans les exemples
description: Comment effectuer une recherche à l’aide de différents paramètres de recherche, modificateurs et autres outils de recherche FHIR
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 05/21/2021
ms.author: cavoeg
ms.openlocfilehash: 6e3a074c24305209047fbd3e741fdb81256374e5
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110460100"
---
# <a name="fhir-search-examples"></a>Exemples de recherche FHIR

Voici quelques exemples d’utilisation des opérations de recherche FHIR, y compris les paramètres de recherche et les modificateurs, la recherche de chaîne et d’inversion de chaîne, la recherche composite, l’affichage du jeu d’entrée suivant pour les résultats de recherche et la recherche avec une `POST` demande. Pour plus d’informations sur la recherche, consultez [vue d’ensemble de la recherche de FHIR](overview-of-search.md).
   
## <a name="search-result-parameters"></a>Paramètres des résultats de la recherche

### <a name="_include"></a>_include

`_include` recherche parmi les ressources celles qui incluent le paramètre spécifié de la ressource. Par exemple, vous pouvez effectuer une recherche dans l’ensemble des `MedicationRequest` ressources pour rechercher uniquement celles qui incluent des informations sur les préscriptions pour un patient spécifique, qui est le `reference` paramètre `patient` . Dans l’exemple ci-dessous, cela permet d’extraire tous les `MedicationRequests` patients qui sont référencés à partir de `MedicationRequests` :

```rest
 GET [your-fhir-server]/MedicationRequest?_include=MedicationRequest:patient

```

> [!NOTE]
> **_include** et **_revinclude** sont limités à 100 éléments.

### <a name="_revinclude"></a>_revinclude

`_revinclude` vous permet d’effectuer une recherche dans la direction opposée `_include` . Par exemple, vous pouvez rechercher des patients, puis inverser tous les rencontres qui font référence aux patients :

```rest
GET [your-fhir-server]/Patient?_revinclude=Encounter:subject

```
### <a name="_elements"></a>_elements

`_elements` réduit le résultat de la recherche à un sous-ensemble de champs afin de réduire la taille de la réponse en omettant les données inutiles. Le paramètre accepte une liste séparée par des virgules d’éléments de base :

```rest
GET [your-fhir-server]/Patient?_elements=identifier,active

```

Dans cette requête, vous obtenez une offre groupée de patients, mais chaque ressource n’inclut que les identificateurs et l’état actif du patient. Les ressources de cette réponse retournée contiendront la `meta.tag` valeur `SUBSETTED` pour indiquer qu’il s’agit d’un jeu de résultats incomplet.

## <a name="search-modifiers"></a>Modificateurs de recherche

### <a name="not"></a>: non

`:not` vous permet de rechercher des ressources pour lesquelles un attribut n’a pas la valeur true. Par exemple, vous pouvez rechercher des patients dont le sexe n’est pas féminin :

```rest
GET [your-fhir-server]/Patient?gender:not=female

```

En guise de valeur de retour, vous obtiendriez toutes les entrées patients pour lesquelles le sexe n’est pas femelle, y compris les valeurs vides (entrées déterminées sans sexe). Cela est différent de la recherche de patients dont le sexe est masculin, car cela n’inclut pas les entrées sans sexe spécifique.

### <a name="missing"></a>: manquant

`:missing` retourne toutes les ressources qui n’ont pas de valeur pour l’élément spécifié lorsque la valeur est `true` , et retourne toutes les ressources qui contiennent l’élément spécifié lorsque la valeur est `false` . Pour les éléments de type de données simples, `:missing=true` correspond à toutes les ressources où l’élément est présent avec les extensions, mais a une valeur vide. Par exemple, si vous souhaitez rechercher toutes les `Patient` ressources qui contiennent des informations sur la date de naissance, vous pouvez effectuer les opérations suivantes :

```rest
GET [your-fhir-server]/Patient?birthdate:missing=true

```

### <a name="exact"></a>: exact
`:exact` est utilisé pour les `string` paramètres et retourne des résultats qui correspondent exactement au paramètre, comme dans la casse et la concaténation de caractères.

```rest
GET [your-fhir-server]/Patient?name:exact=Jon

```

Cette requête retourne des `Patient` ressources dont le nom est identique à celui de `Jon` . Si la ressource avait des patients avec des noms tels que `Jonathan` ou `joN` , la recherche ignore et ignore la ressource, car elle ne correspond pas exactement à la valeur spécifiée.

### <a name="contains"></a>: contient
`:contains` est utilisé pour les `string` paramètres et recherche des ressources avec des correspondances partielles de la valeur spécifiée n’importe où dans la chaîne du champ dans lequel la recherche est effectuée. `contains` ne respecte pas la casse et autorise la concaténation de caractères. Par exemple :

```rest
GET [your-fhir-server]/Patient?address:contains=Meadow

```

Cette requête retourne toutes les `Patient` ressources avec des `address` champs qui ont des valeurs qui contiennent la chaîne « pré ». Cela signifie que vous pouvez avoir des adresses qui incluent des valeurs telles que « intégrateurs » ou « 59 pâturage ST » retournées en tant que résultats de la recherche.

## <a name="chained-search"></a>Recherche chaînée 

Pour effectuer une série d’opérations de recherche couvrant plusieurs paramètres de référence, vous pouvez « chaîner » la série de paramètres de référence en les ajoutant à la demande du serveur, un par un, à l’aide d’un point `.` . Par exemple, si vous souhaitez afficher toutes les `DiagnosticReport` ressources avec une `subject` référence à une `Patient` ressource qui comprend un particulier `name` :  

```rest
 GET [your-fhir-server]/DiagnosticReport?subject:Patient.name=Sarah

```

Cette demande retourne toutes les ressources avec l’objet du patient nommé « Sarah ». Le point `.` après le champ `Patient` effectue la recherche chaînée sur le paramètre de référence du `subject` paramètre.

Une autre utilisation courante d’une recherche régulière (et non une recherche chaînée) consiste à rechercher tous les rencontres pour un patient spécifique. `Patient`les s comportent souvent un ou plusieurs `Encounter` s avec un objet. Pour rechercher toutes les `Encounter` ressources pour un `Patient` avec le fourni `id` :

```rest
GET [your-fhir-server]/Encounter?subject=Patient/78a14cbe-8968-49fd-a231-d43e6619399f

```

À l’aide de la recherche chaînée, vous pouvez rechercher toutes les `Encounter` ressources qui correspondent à une information particulière `Patient` , par exemple `birthdate` :

```rest
GET [your-fhir-server]/Encounter?subject:Patient.birthdate=1987-02-20

```

Cela permet de ne pas simplement Rechercher `Encounter` des ressources pour un seul patient, mais sur tous les patients ayant la valeur de date de naissance spécifiée. 

En outre, la recherche chaînée peut être effectuée plusieurs fois dans une demande à l’aide du symbole `&` , qui vous permet de Rechercher plusieurs conditions dans une demande. Dans ce cas, la recherche chaînée « indépendamment » recherche chaque paramètre, au lieu de rechercher les conditions qui répondent uniquement à toutes les conditions à la fois. Il s’agit d’une opération ou, et non d’une opération et. Par exemple, si vous souhaitez obtenir tous les patients ayant un praticien ayant un nom ou un état particulier :

```rest
GET [your-fhir-server]/Patient?general-practitioner.name=Sarah&general-practitioner.address-state=WA

```

Cela retourne toutes les `Patient` ressources qui ont « Sarah » comme `generalPractitioner` , et toutes les `Patient` ressources qui ont `generalPractitioner` l’adresse avec l’État wa. En d’autres termes, vous pouvez avoir Sarah de l’État NY et facturer à partir de l’État WA à la fois comme résultats renvoyés. La recherche chaînée n’a pas besoin de respecter toutes les conditions et est évaluée individuellement pour chaque paramètre.

Pour les scénarios dans lesquels la recherche doit être une opération et qui couvre toutes les conditions en tant que groupe, reportez-vous à l’exemple de **recherche composite** ci-dessous.

## <a name="reverse-chain-search"></a>Recherche de chaîne inversée

La recherche de chaîne vous permet de rechercher des ressources en fonction des propriétés des ressources auxquelles elles font référence. L’utilisation de la recherche de chaîne inverse vous permet d’effectuer l’autre procédure. Vous pouvez rechercher des ressources en fonction des propriétés des ressources qui y font référence, à l’aide du `_has` paramètre. Par exemple, `Observation` la ressource a un paramètre de recherche `patient` qui fait référence à une ressource patient. Pour rechercher toutes les ressources patients référencées par `Observation` avec un spécifique `code` :

```rest
GET [base]/Patient?_has:Observation:patient:code=527

```

Cette requête retourne les ressources patient qui sont référencées par `Observation` le code `527` . 

En outre, la recherche de chaîne inverse peut avoir une structure récursive. Par exemple, si vous souhaitez rechercher tous les patients dont `Observation` l’observation est un événement d’audit d’un utilisateur spécifique `janedoe` , vous pouvez effectuer les opérations suivantes :

```rest
GET [base]/Patient?_has:Observation:patient:_has:AuditEvent:entity:user=janedoe

``` 

> [!NOTE]
> Dans l’API Azure pour FHIR et le serveur FHIR Open source avec Cosmos, la recherche chaînée et la recherche chaînée par chaîne est une implémentation MVP. Pour effectuer une recherche chaînée sur Cosmos DB, l’implémentation parcourt l’expression de recherche et émet des sous-requêtes pour résoudre les ressources correspondantes. Cette opération est effectuée pour chaque niveau de l’expression. Si une requête retourne plus de 100 résultats, une erreur est générée. Par défaut, la recherche chaînée se trouve derrière un indicateur de fonctionnalité. Pour utiliser la recherche chaînée sur Cosmos DB, utilisez l’en-tête x-ms-Enable-chained-Search : true.

## <a name="composite-search"></a>Recherche composite

Pour rechercher des ressources qui répondent à plusieurs conditions à la fois, utilisez la recherche composite qui joint une séquence de valeurs de paramètre uniques à un symbole `$` . Le résultat retourné serait l’intersection des ressources qui correspondent à toutes les conditions spécifiées par les paramètres de recherche joints. Ces paramètres de recherche sont appelés paramètres de recherche composites et définissent un nouveau paramètre qui combine les différents paramètres dans une structure imbriquée. Par exemple, si vous souhaitez rechercher toutes les `DiagnosticReport` ressources qui contiennent `Observation` avec une valeur de potassium inférieure ou égale à 9,2 :

```rest
GET [your-fhir-server]/DiagnosticReport?result.code-value-quantity=2823-3$lt9.2

``` 

Cette requête spécifie le composant contenant un code de `2823-3` , qui est dans ce cas du potassium. À la suite du `$` symbole, il spécifie la plage de la valeur du composant à l’aide `lt` de « inférieur ou égal à » et `9.2` pour la plage de valeurs de potassium. 

## <a name="search-the-next-entry-set"></a>Rechercher le jeu d’entrées suivant

Le nombre maximal d’entrées pouvant être retournées par une seule requête de recherche est de 1000. Toutefois, vous pouvez avoir plus de 1000 entrées qui correspondent à la requête de recherche, et vous souhaiterez peut-être Voir le jeu d’entrées suivant après les 1000 premières entrées retournées. Dans ce cas, vous devez utiliser la valeur du jeton de continuation `url` dans `searchset` comme dans le `Bundle` résultat ci-dessous :

```json
    "resourceType": "Bundle",
    "id": "98731cb7-3a39-46f3-8a72-afe945741bd9",
    "meta": {
        "lastUpdated": "2021-04-22T09:58:16.7823171+00:00"
    },
    "type": "searchset",
    "link": [
        {
            "relation": "next",
            "url": "[your-fhir-server]/Patient?_sort=_lastUpdated&ct=WzUxMDAxNzc1NzgzODc5MjAwODBd"
        },
        {
            "relation": "self",
            "url": "[your-fhir-server]/Patient?_sort=_lastUpdated"
        }
    ],

```

Et vous pouvez obtenir une demande d’accès pour l’URL fournie sous le champ `relation: next` :

```rest
GET [your-fhir-server]/Patient?_sort=_lastUpdated&ct=WzUxMDAxNzc1NzgzODc5MjAwODBd

```

Le jeu d’entrées suivant est retourné pour les résultats de votre recherche. `searchset`Est le jeu complet d’entrées de résultat de recherche, et le jeton de continuation `url` est le lien fourni par le serveur pour vous permettre de récupérer les entrées qui n’apparaissent pas sur le premier jeu, car la restriction sur le nombre maximal d’entrées retourné pour une requête de recherche.

## <a name="search-using-post"></a>Rechercher à l’aide de la publication

Tous les exemples de recherche mentionnés ci-dessus ont utilisé des `GET` demandes. Vous pouvez également effectuer des opérations de recherche à l’aide de `POST` requêtes à l’aide de `_search` :

```rest
POST [your-fhir-server]/Patient/_search?_id=45

```

Cette requête retourne toutes les `Patient` ressources ayant la `id` valeur 45. Tout comme dans les demandes d’extraction, le serveur détermine le jeu de ressources qui répond à la ou aux conditions et renvoie une ressource de regroupement dans la réponse HTTP.

Voici un autre exemple de recherche à l’aide de la publication où les paramètres de requête sont soumis en tant que corps de formulaire :

```rest
POST [your-fhir-server]/Patient/_search
content-type: application/x-www-form-urlencoded

name=John

```
## <a name="next-steps"></a>Étapes suivantes

>[!div class="nextstepaction"]
>[Vue d’ensemble de la recherche FHIR](overview-of-search.md)