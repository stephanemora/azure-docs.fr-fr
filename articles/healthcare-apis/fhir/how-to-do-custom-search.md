---
title: Comment effectuer une recherche personnalisée dans l’API Azure pour FHIR
description: Cet article explique comment vous pouvez définir vos propres paramètres de recherche personnalisés à utiliser dans la base de données.
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 05/03/2021
ms.author: cavoeg
ms.openlocfilehash: 5453b11cb49bb48c48e6c949a00654a797c89202
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110476662"
---
# <a name="defining-custom-search-parameters"></a>Définition des paramètres de recherche personnalisés

La spécification FHIR définit un ensemble de paramètres de recherche pour toutes les ressources et tous les paramètres de recherche spécifiques à une ou plusieurs ressources. Toutefois, il existe des scénarios dans lesquels vous pouvez souhaiter Rechercher un élément dans une ressource qui n’est pas défini par la spécification FHIR comme paramètre de recherche standard. Cet article explique comment vous pouvez définir vos propres [paramètres de recherche](https://www.hl7.org/fhir/searchparameter.html) à utiliser dans l’API Azure pour FHIR.

> [!NOTE]
> Chaque fois que vous créez, mettez à jour ou supprimez un paramètre de recherche, vous devez exécuter un [travail de réindexation](how-to-run-a-reindex.md) pour permettre l’utilisation du paramètre de recherche en production. Nous décrirons ci-dessous comment vous pouvez tester les paramètres de recherche avant de réindexer l’ensemble du serveur FHIR.

## <a name="create-new-search-parameter"></a>Créer un nouveau paramètre de recherche

Pour créer un nouveau paramètre de recherche, vous devez disposer de `POST` la `SearchParameter` ressource dans la base de données. L’exemple de code ci-dessous montre comment ajouter le [SearchParameter de course du cœur des États-Unis](http://hl7.org/fhir/us/core/STU3.1.1/SearchParameter-us-core-race.html) à la `Patient` ressource.

```rest
POST {{FHIR_URL}}/SearchParameter

{
  "resourceType" : "SearchParameter",
  "id" : "us-core-race",
  "url" : "http://hl7.org/fhir/us/core/SearchParameter/us-core-race",
  "version" : "3.1.1",
  "name" : "USCoreRace",
  "status" : "active",
  "date" : "2019-05-21",
  "publisher" : "US Realm Steering Committee",
  "contact" : [
    {
      "telecom" : [
        {
          "system" : "other",
          "value" : "http://www.healthit.gov/"
        }
      ]
    }
  ],
  "description" : "Returns patients with a race extension matching the specified code.",
  "jurisdiction" : [
    {
      "coding" : [
        {
          "system" : "urn:iso:std:iso:3166",
          "code" : "US",
          "display" : "United States of America"
        }
      ]
    }
  ],
  "code" : "race",
  "base" : [
    "Patient"
  ],
  "type" : "token",
  "expression" : "Patient.extension.where(url = 'http://hl7.org/fhir/us/core/StructureDefinition/us-core-race').extension.value.code"
}

``` 

> [!NOTE]
> Le nouveau paramètre de recherche s’affiche dans l’instruction Capability du serveur FHIR une fois que vous avez publié le paramètre de recherche dans la base de données **et** que vous avez réindexé votre base de données. L’affichage `SearchParameter` de dans l’instruction Capability est le seul moyen de savoir si un paramètre de recherche est pris en charge sur votre serveur FHIR. Si vous trouvez le paramètre de recherche en recherchant le paramètre de recherche, mais que vous ne le voyez pas dans l’instruction de fonctionnalité, vous devez toujours indexer le paramètre de recherche. Vous pouvez poster plusieurs paramètres de recherche avant de déclencher une opération de réindexation.

Éléments importants d’un `SearchParameter` :

* **URL**: clé unique permettant de décrire le paramètre de recherche. De nombreuses organisations, telles que HL7, utilisent un format d’URL standard pour les paramètres de recherche qu’ils définissent, comme indiqué ci-dessus dans le paramètre de recherche course Core US.

* **code**: la valeur stockée dans le **code** est celle que vous allez utiliser lors de la recherche. Dans l’exemple ci-dessus, vous devez effectuer une recherche dans `GET {FHIR_URL}/Patient?race=<code>` pour obtenir tous les patients d’une course spécifique. Le code doit être unique pour la ou les ressources auxquelles le paramètre de recherche s’applique.

* **base**: décrit la ou les ressources auxquelles le paramètre de recherche s’applique. Si le paramètre de recherche s’applique à toutes les ressources, vous pouvez utiliser `Resource` ; sinon, vous pouvez répertorier toutes les ressources pertinentes.
 
* **type**: décrit le type de données pour le paramètre de recherche. Le type est limité par la prise en charge de l’API Azure pour FHIR. Cela signifie que vous ne pouvez pas définir un paramètre de recherche de type spécial ou définir un [paramètre de recherche composite](overview-of-search.md) , sauf s’il s’agit d’une combinaison prise en charge.

* **expression**: décrit comment calculer la valeur de la recherche. Lors de la description d’un paramètre de recherche, vous devez inclure l’expression, même si elle n’est pas requise par la spécification. Cela est dû au fait que vous avez besoin de l’expression ou de la syntaxe XPath et de l’API Azure pour FHIR ignore la syntaxe XPath.

## <a name="test-search-parameters"></a>Paramètres de recherche de test

Si vous ne pouvez pas utiliser les paramètres de recherche en production tant que vous n’exécutez pas un travail de réindexation, il existe plusieurs façons de tester vos paramètres de recherche avant de réindexer l’intégralité de la base de données. 

Tout d’abord, vous pouvez tester votre nouveau paramètre de recherche pour voir les valeurs qui seront retournées. En exécutant la commande ci-dessous sur une instance de ressource spécifique (en saisissant leur ID), vous obtenez une liste de paires de valeurs avec le nom du paramètre de recherche et la valeur stockée pour le patient spécifique. Cela inclut tous les paramètres de recherche de la ressource et vous pouvez faire défiler pour rechercher le paramètre de recherche que vous avez créé. L’exécution de cette commande ne modifie pas le comportement de votre serveur FHIR. 

```rest
GET https://{{FHIR_URL}}/{{RESOURCE}}/{{RESOUCE_ID}}/$reindex

```
Par exemple, pour rechercher tous les paramètres de recherche pour un patient :

```rest
GET https://{{FHIR_URL}}/Patient/{{PATIENT_ID}}/$reindex

```

Le résultat aura l’aspect suivant :

```json
{
  "resourceType": "Parameters",
  "id": "8be24e78-b333-49da-a861-523491c3437a",
  "meta": {
    "versionId": "1"
  },
  "parameter": [
    {
      "name": "deceased",
      "valueString": "http://hl7.org/fhir/special-values|false"
    },
    {
      "name": "language",
      "valueString": "urn:ietf:bcp:47|en-US"
    },
    {
      "name": "race",
      "valueString": "2028-9"
    },
...
```
Une fois que vous voyez que votre paramètre de recherche s’affiche comme prévu, vous pouvez réindexer une ressource unique pour tester la recherche à l’aide de l’élément. Tout d’abord, vous allez réindexer une ressource unique :

```rest
POST https://{{FHIR_URL}/{{RESOURCE}}/{{RESOURCE_ID}}/$reindex
```

À l’exécution de ce paramètre, définit les index pour tous les paramètres de recherche pour la ressource spécifique que vous avez définie pour ce type de ressource. Cela effectue une mise à jour du serveur FHIR. Vous pouvez maintenant Rechercher et définir l’en-tête use partial indexes sur true, ce qui signifie qu’il retourne des résultats où l’une des ressources a le paramètre de recherche indexé, même si toutes les ressources de ce type n’ont pas été indexées. 

Poursuivons avec notre exemple ci-dessus, vous pouvez indexer un patient pour activer la course de base américaine `SearchParameter` :

```rest
POST https://{{FHIR_URL}/Patient/{{PATIENT_ID}}/$reindex
```

Puis recherchez les patients qui ont une course spécifique :

```rest
GET https://{{FHIR_URL}}/Patient?race=2028-9
x-ms-use-partial-indices: true
```

Après avoir testé et vérifié que votre paramètre de recherche fonctionne comme prévu, exécutez ou planifiez votre travail de réindexation afin que les paramètres de recherche puissent être utilisés dans le serveur FHIR pour les cas d’usage en production.

## <a name="update-a-search-parameter"></a>Mettre à jour un paramètre de recherche

Pour mettre à jour un paramètre de recherche, utilisez `PUT` pour créer une nouvelle version du paramètre de recherche. Vous devez inclure `SearchParameter ID` dans l' `id` élément du corps de la `PUT` demande et dans l' `PUT` appel.

> [!NOTE]
> Si vous ne connaissez pas l’ID de votre paramètre de recherche, vous pouvez le Rechercher. L’utilisation `GET {{FHIR_URL}}/SearchParameter` de retourne tous les paramètres de recherche personnalisés, et vous pouvez faire défiler le paramètre de recherche pour trouver le paramètre de recherche dont vous avez besoin. Vous pouvez également limiter la recherche par nom. Dans l’exemple ci-dessous, vous pouvez rechercher le nom à l’aide de `USCoreRace: GET {{FHIR_URL}}/SearchParameter?name=USCoreRace` .

```rest
PUT {{FHIR_ULR}}/SearchParameter/{SearchParameter ID}

{
  "resourceType" : "SearchParameter",
  "id" : "SearchParameter ID",
  "url" : "http://hl7.org/fhir/us/core/SearchParameter/us-core-race",
  "version" : "3.1.1",
  "name" : "USCoreRace",
  "status" : "active",
  "date" : "2019-05-21",
  "publisher" : "US Realm Steering Committee",
  "contact" : [
    {
      "telecom" : [
        {
          "system" : "other",
          "value" : "http://www.healthit.gov/"
        }
      ]
    }
  ],
  "description" : "New Description!",
  "jurisdiction" : [
    {
      "coding" : [
        {
          "system" : "urn:iso:std:iso:3166",
          "code" : "US",
          "display" : "United States of America"
        }
      ]
    }
  ],
  "code" : "race",
  "base" : [
    "Patient"
  ],
  "type" : "token",
  "expression" : "Patient.extension.where(url = 'http://hl7.org/fhir/us/core/StructureDefinition/us-core-race').extension.value.code"
}

```

Le résultat sera un mis à jour `SearchParameter` et la version sera incrémentée.

> [!Warning]
> Soyez prudent lors de la mise à jour de SearchParameters qui ont déjà été indexés dans votre base de données. La modification du comportement d’un SearchParameter existant peut avoir un impact sur le comportement attendu. Nous vous recommandons d’exécuter un travail de réindexation immédiatement.

## <a name="delete-a-search-parameter"></a>Supprimer un paramètre de recherche

Si vous devez supprimer un paramètre de recherche, utilisez ce qui suit :

```rest
Delete {{FHIR_URL}}/SearchParameter/{SearchParameter ID}
```

> [!Warning]
> Soyez prudent lorsque vous supprimez des SearchParameters qui ont déjà été indexés dans votre base de données. La modification du comportement d’un SearchParameter existant peut avoir un impact sur le comportement attendu. Nous vous recommandons d’exécuter un travail de réindexation immédiatement.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à créer un paramètre de recherche. Vous pouvez ensuite apprendre à réindexer votre serveur FHIR.

>[!div class="nextstepaction"]
>[Guide pratique pour exécuter un travail de réindexation](how-to-run-a-reindex.md)