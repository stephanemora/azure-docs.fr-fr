---
title: Utilisation de patients-tout dans l’API Azure pour FHIR
description: Cet article explique comment utiliser l’opération patient-tout dans l’API Azure pour FHIR
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 06/04/2021
ms.author: cavoeg
ms.openlocfilehash: b02a2b582130c439298653d7c2a0512edbb8a23b
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111756362"
---
# <a name="patient-everything-in-fhir"></a>Patient-tout dans FHIR

L’opération [$patient-Everything](https://www.hl7.org/fhir/patient-operation-everything.html) a été créée pour fournir à un patient un accès à son enregistrement entier ou à un fournisseur ou à un autre utilisateur pour effectuer un téléchargement de données en bloc. Cette opération permet de renvoyer toutes les informations relatives à un ou plusieurs patients décrits dans la ressource ou le contexte sur lequel cette opération est appelée.  

## <a name="use-patient-everything"></a>Utiliser tout le patient
Pour appeler patient-tout, utilisez la commande suivante :

```json
GET {FHIRURL}/Patient/{ID}/$everything
```
L’API Azure pour FHIR valide qu’il peut trouver le patient correspondant à l’ID de patient fourni. Si un résultat est trouvé, la réponse est un bundle de type « searchset » avec les informations suivantes : 
* [Ressource patient](https://www.hl7.org/fhir/patient.html) 
*  Ressources directement référencées par la ressource patient (à l’exception du lien) 
*  Ressources dans le [compartiment](https://www.hl7.org/fhir/compartmentdefinition-patient.html) du patient
*  [Ressources](https://www.hl7.org/fhir/device.html) de l’appareil qui font référence à la ressource patient. Notez que cela est limité à 100 appareils. Si le patient a plus de 100 appareils qui y sont liés, seuls 100 sont retournés. 

 
> [!Note]
> $patient-tout est disponible dans le serveur FHIR Open source sauvegardé par Cosmos DB Now et sera disponible dans l’API Azure pour FHIR avant le 1er juillet. La prise en charge de l’instruction de capacité pour le serveur FHIR est manquante pour $patient tout, suivi ici : problème [1989](https://github.com/microsoft/fhir-server/issues/1989). 


## <a name="patient-everything-parameters"></a>Paramètres de tous les patients
L’API Azure pour FHIR prend en charge les paramètres de requête suivants. Tous ces paramètres sont facultatifs :

|Paramètre de requête.        |  Description|
|-----------------------|------------|
| \_type | Vous permet de spécifier les types de ressources qui seront inclus dans la réponse. Par exemple, \_ type = rencontrer retourne uniquement `Encounter` les ressources associées au patient. |
| \_since | Renverra uniquement les ressources qui ont été modifiées depuis l’heure indiquée. |
| start | La spécification de la date de début extraira les ressources dont la date de début est postérieure à la date de début spécifiée. Si aucune date de début n’est fournie, tous les enregistrements antérieurs à la date de fin se trouvent dans l’étendue. |
| end | La spécification de la date de fin entraîne l’extraction des ressources dans lesquelles la date clinique est antérieure à la date de fin spécifiée. Si aucune date de fin n’est fournie, tous les enregistrements postérieurs à la date de début se trouvent dans l’étendue. |

> [!Note]
> Vous devez spécifier un ID pour un patient spécifique. Si vous avez besoin de toutes les données pour tous les patients, consultez [$Export](export-data.md). 


## <a name="examples-of-patient-everything"></a>Exemples de $patient-tout 

Voici quelques exemples supplémentaires d’utilisation de l’opération $patient tout. 

Pour utiliser $patient tout pour interroger le « tout » d’un patient entre 2010 et 2020, utilisez l’appel suivant : 

```json
GET {FHIRURL}/Patient/{ID}/$everything?start=2010&end=2020
``` 

Pour utiliser $patient tout pour interroger l’observation et la rencontre d’un patient, utilisez l’appel suivant : 
```json
GET {FHIRURL}/Patient/{ID}/$everything_type=Observation,Encounter 
```

Pour utiliser $patient tout pour interroger le « tout » d’un patient depuis 2021-05-27T05:00:00Z, utilisez l’appel suivant : 

```json
GET {FHIRURL}/Patient/{ID}/$everything?_since=2021-05-27T05:00:00Z 
```

Si un patient est trouvé pour chacun de ces appels, vous obtenez une réponse 200 avec un bundle des ressources correspondantes.

## <a name="next-step"></a>Étape suivante
Maintenant que vous savez comment utiliser l’opération patient-Everything, vous pouvez découvrir d’autres options de recherche dans la vue d’ensemble du Guide de recherche.

>[!div class="nextstepaction"]
>[Vue d’ensemble de la recherche FHIR](overview-of-search.md)