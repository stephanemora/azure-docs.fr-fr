---
title: Comment effectuer une recherche personnalisée dans l’API Azure pour FHIR
description: Cet article explique comment vous pouvez définir vos propres paramètres de recherche personnalisés à utiliser dans la base de données.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 4/23/2021
ms.author: cavoeg
ms.openlocfilehash: 1482bd7f054c75c4f26b77907d50a6471c389d68
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108322535"
---
# <a name="defining-custom-search-parameters"></a>Définition des paramètres de recherche personnalisés

La spécification FHIR définit un ensemble de paramètres de recherche pour toutes les ressources et tous les paramètres de recherche spécifiques à une ou plusieurs ressources. Toutefois, il existe des scénarios dans lesquels vous pouvez effectuer une recherche dans un champ d’une ressource qui n’est pas défini par la spécification comme un paramètre de recherche standard. Cet article explique comment vous pouvez définir vos propres [paramètres de recherche](https://www.hl7.org/fhir/searchparameter.html) à utiliser dans le serveur FHIR.

> [!NOTE]
> Chaque fois que vous créez, mettez à jour ou supprimez un paramètre de recherche, vous devez exécuter un [travail de réindexation](how-to-run-a-reindex.md) pour activer la modification dans votre base de données.

> [!Warning]
> Si vous mettez à jour ou supprimez un paramètre de recherche, vérifiez que vous exécutez immédiatement un [travail de réindexation](how-to-run-a-reindex.md). Il est possible que votre base de données soit dans un état anormal avec des paramètres de recherche mis à jour ou supprimés qui apparaissent toujours actifs en raison d’un besoin d’indexation des modifications. 

## <a name="create-new-search-parameter"></a>Créer un nouveau paramètre de recherche

Pour créer un nouveau paramètre de recherche, vous devez disposer `POST` d’un nouveau paramètre de recherche dans la base de données. L’exemple de code ci-dessous montre comment ajouter le [paramètre de recherche course Core US](http://hl7.org/fhir/us/core/STU3.1.1/SearchParameter-us-core-race.html) à la ressource patient.

```json
POST {fhirurl}/SearchParameter
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
> Le nouveau paramètre de recherche s’affiche dans votre instruction de fonctionnalité une fois que vous l’avez publié sur le serveur FHIR et que vous avez réindexé votre base de données. Il ne sera pas utilisable tant que vous n’aurez pas exécuté un [travail de réindexation](how-to-run-a-reindex.md). C’est le seul moyen de savoir si un paramètre de recherche est pris en charge ou non dans votre base de données. Si vous pouvez trouver le paramètre de recherche en recherchant le paramètre de recherche, mais ne le voyez pas dans l’instruction de fonctionnalité, exécutez un travail de réindexation. Vous pouvez poster plusieurs paramètres de recherche avant de déclencher une opération de réindexation.

Descriptions des champs importants :

* **URL**: il s’agit d’une clé unique pour décrire le paramètre de recherche. De nombreuses organisations, telles que HL7, utilisent un format standard pour les URL qu’elles définissent, comme indiqué ci-dessus dans le paramètre de recherche course Core US.

* **code**: la valeur ici est celle que vous allez utiliser lors de la recherche. Dans l’exemple ci-dessus, vous devez effectuer une recherche dans `GET {FHIR URL}/Patient?race=2028-9` pour obtenir tous les patients asiatiques. Cette valeur doit être unique pour la ou les ressources auxquelles elle s’applique.

* **base**: décrit la ou les ressources auxquelles le paramètre de recherche s’applique. S’il s’applique à toutes les ressources, vous pouvez simplement utiliser la ressource. dans le cas contraire, vous pouvez répertorier toutes les ressources pertinentes.
 
* **type**: décrit le type de données pour le paramètre de recherche.

* **expression**: lors de la description d’un paramètre de recherche, vous devez inclure l’expression, même si elle n’est techniquement pas requise par la spécification. Cela est dû au fait que vous avez besoin de l’expression ou de la syntaxe XPath et de l’API Azure pour FHIR ignore la syntaxe XPath pour le moment. Cette rubrique décrit comment rechercher la valeur de la recherche. 

> [!NOTE]
> « Type » est limité par la prise en charge de l’API Azure pour FHIR. Cela signifie que vous ne pouvez pas définir un paramètre de recherche de type spécial ou définir un [paramètre de recherche composite](overview-of-search.md) , sauf s’il est d’un type que nous prenons en charge.

Une fois que vous avez ajouté vos paramètres de recherche, exécutez ou planifiez votre travail de réindexation afin que les paramètres de recherche puissent être utilisés dans le serveur FHIR.

## <a name="update-a-search-parameter"></a>Mettre à jour un paramètre de recherche

Pour mettre à jour un paramètre de recherche, utilisez `PUT` pour créer une nouvelle version du paramètre de recherche.

`PUT {fhirurl}/SearchParameter/{SearchParameter ID}`

Vous devez inclure `SearchParameter ID` dans le champ ID du corps de la `PUT` demande et dans l' `PUT` appel.

> [!NOTE]
> Si vous ne connaissez pas l’ID de votre paramètre de recherche, vous pouvez le Rechercher. L’utilisation `GET {fhirurl}/SearchParameter` de retourne tous les paramètres de recherche personnalisés, et vous pouvez faire défiler le paramètre de recherche pour trouver le paramètre de recherche dont vous avez besoin. Vous pouvez également limiter la recherche par nom. Dans l’exemple ci-dessous, vous pouvez rechercher le nom à l’aide de `USCoreRace: GET {fhirurl}/SearchParameter?name=USCoreRace` .

```json
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
> Soyez prudent lors de la mise à jour de SearchParameters qui ont déjà été indexés dans votre base de données. La modification du comportement d’un SearchParameter existant peut avoir un impact sur le comportement attendu. 

## <a name="delete-a-search-parameter"></a>Supprimer un paramètre de recherche

Si vous devez supprimer un paramètre de recherche, utilisez ce qui suit :

`Delete {fhirurl}/SearchParameter/{SearchParameter ID}`

> [!Warning]
> Soyez prudent lorsque vous supprimez des SearchParameters qui ont déjà été indexés dans votre base de données. La modification du comportement d’un SearchParameter existant peut avoir un impact sur le comportement attendu.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à créer un paramètre de recherche. Pour savoir comment réindexer un travail, consultez.

>[!div class="nextstepaction"]
>[Comment exécuter un travail de réindexation](how-to-run-a-reindex.md)