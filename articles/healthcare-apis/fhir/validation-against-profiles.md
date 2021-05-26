---
title: $validate des ressources FHIR par rapport aux profils sur l’API Azure pour FHIR
description: $validate les ressources FHIR sur les profils
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 05/06/2021
ms.author: ginle
ms.openlocfilehash: 2c367dbed14e0dba9a8a95a3ce2709d2415c7cd6
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110466698"
---
# <a name="how-to-validate-fhir-resources-against-profiles"></a>Comment valider des ressources FHIR par rapport à des profils

HL7 FHIR définit une méthode standard et interopérable pour stocker et échanger des données de santé. Même dans la spécification FHIR de base, il peut être utile de définir des règles ou des extensions supplémentaires en fonction du contexte utilisé par FHIR. Pour de telles utilisations spécifiques au contexte de FHIR, les **profils FHIR** sont utilisés pour la couche supplémentaire de spécifications.

Le [Profil FHIR](https://www.hl7.org/fhir/profiling.html) décrit un contexte supplémentaire, tel que des contraintes ou des extensions, sur une ressource représentée sous la forme d’un `StructureDefinition` . La norme HL7 FHIR définit un ensemble de ressources de base, et ces ressources de base standard ont des définitions génériques. Le profil FHIR vous permet de limiter et de personnaliser les définitions de ressource à l’aide de contraintes et d’extensions.

L’API Azure pour FHIR permet de valider des ressources par rapport à des profils pour déterminer si les ressources sont conformes aux profils. Cet article décrit les principes fondamentaux du profil FHIR et explique comment utiliser `$validate` pour valider des ressources par rapport aux profils lors de la création et de la mise à jour des ressources.

## <a name="fhir-profile-the-basics"></a>Profil FHIR : notions de base

Un profil définit un contexte supplémentaire sur la ressource, généralement représenté sous la forme d’une `StructureDefinition` ressource. `StructureDefinition` définit un ensemble de règles sur le contenu d’une ressource ou d’un type de données, telles que les champs d’une ressource et les valeurs que ces champs peuvent prendre. Par exemple, les profils peuvent restreindre la cardinalité (par exemple, définir la cardinalité maximale sur 0 pour éliminer l’élément), restreindre le contenu d’un élément à une seule valeur fixe ou définir les extensions requises pour la ressource. Il peut également spécifier des contraintes supplémentaires sur un profil existant. Un `StructureDefinition` est identifié par son URL canonique :

```rest
http://hl7.org/fhir/StructureDefinition/{profile}
```

Dans le `{profile}` champ, spécifiez le nom du profil.

Par exemple :

- `http://hl7.org/fhir/StructureDefinition/patient-birthPlace` est un profil de base qui nécessite des informations sur l’adresse enregistrée de naissance du patient.
- `http://hl7.org/fhir/StructureDefinition/bmi` est un autre profil de base qui définit comment représenter les observations IMC (Body masse index).
- `http://hl7.org/fhir/us/core/StructureDefinition/us-core-allergyintolerance` est un profil de base US qui définit les attentes minimales pour `AllergyIntolerance` la ressource associée à un patient et identifie les champs obligatoires tels que les extensions et les jeux de valeurs.

### <a name="base-profile-and-custom-profile"></a>Profil de base et profil personnalisé

Il existe deux types de profils : le profil de base et le profil personnalisé. Un profil de base est une base à `StructureDefinition` laquelle une ressource doit se conformer, et a été défini par des ressources de base telles que `Patient` ou `Observation` . Par exemple, un profil IMC (corps de masse) `Observation` peut démarrer comme suit :

```json
{
  "resourceType" : "StructureDefinition",
  "id" : "bmi",
...
}
```

Un profil personnalisé est un ensemble de contraintes supplémentaires sur un profil de base, restreignant ou ajoutant des paramètres de ressources qui ne font pas partie de la spécification de base. Le profil personnalisé est utile, car vous pouvez personnaliser vos propres définitions de ressources en spécifiant les contraintes et les extensions sur la ressource de base existante. Par exemple, vous souhaiterez peut-être créer un profil qui affiche des `AllergyIntolerance` instances de ressource en fonction du `Patient` sexe, auquel cas vous créez un profil personnalisé sur un profil existant `Patient` avec un `AllergyIntolerance` profil.

> [!NOTE]
> Les profils personnalisés doivent être générés par-dessus la ressource de base et ne peuvent pas entrer en conflit avec la ressource de base. Par exemple, si un élément a une cardinalité de 1.. 1, le profil personnalisé ne peut pas le rendre facultatif.

Profils personnalisés également spécifiés par différents guides d’implémentation. Voici quelques guides d’implémentation courants :

|Nom |URL
|---- |----
Cœur américain |<https://www.hl7.org/fhir/us/core/>
Bouton bleu CARIN |<http://hl7.org/fhir/us/carin-bb/>
Échange de données du payeur da Vinci |<http://hl7.org/fhir/us/davinci-pdex/>
Argonaut |<http://www.fhir.org/guides/argonaut/pd/>

## <a name="accessing-profiles-and-storing-profiles"></a>Accès aux profils et stockage des profils

### <a name="storing-profiles"></a>Stockage des profils

Pour stocker des profils sur le serveur, vous pouvez effectuer une `POST` requête :

```rest
POST http://<your FHIR service base URL>/{Resource}
```

Dans laquelle le champ `{Resource}` sera remplacé par `StructureDefinition` , et vous auriez la `StructureDefinition` ressource `POST` Ed sur le serveur au `JSON` `XML` format ou. Par exemple, si vous souhaitez stocker le `us-core-allergyintolerance` profil, procédez comme suit :

```rest
POST http://my-fhir-server.azurewebsites.net/StructureDefinition?url=http://hl7.org/fhir/us/core/StructureDefinition/us-core-allergyintolerance
```

Emplacement de stockage et de récupération du profil d’intolérance aux allergies de base US :

```json
{
    "resourceType" : "StructureDefinition",
    "id" : "us-core-allergyintolerance",
    "text" : {
        "status" : "extensions"
    },
    "url" : "http://hl7.org/fhir/us/core/StructureDefinition/us-core-allergyintolerance",
    "version" : "3.1.1",
    "name" : "USCoreAllergyIntolerance",
    "title" : "US  Core AllergyIntolerance Profile",
    "status" : "active",
    "experimental" : false,
    "date" : "2020-06-29",
        "publisher" : "HL7 US Realm Steering Committee",
    "contact" : [
    {
      "telecom" : [
        {
          "system" : "url",
          "value" : "http://www.healthit.gov"
        }
      ]
    }
  ],
    "description" : "Defines constraints and extensions on the AllergyIntolerance resource for the minimal set of data to query and retrieve allergy information.",

...
```

La plupart des profils ont le type `StructureDefinition` de ressource, mais ils peuvent également être des types `ValueSet` et `CodeSystem` , qui sont des ressources [terminologiques](http://hl7.org/fhir/terminologies.html) . Par exemple, si vous avez `POST` un `ValueSet` Profil dans un formulaire JSON, le serveur retourne le profil stocké avec le affecté `id` pour le profil, comme c’est le cas avec `StructureDefinition` . Voici un exemple que vous pouvez obtenir lorsque vous chargez un profil de [gravité de condition](https://www.hl7.org/fhir/valueset-condition-severity.html) , qui spécifie les critères pour une gravité de condition/de diagnostic :

```json
{
    "resourceType": "ValueSet",
    "id": "35ab90e5-c75d-45ca-aa10-748fefaca7ee",
    "meta": {
        "versionId": "1",
        "lastUpdated": "2021-05-07T21:34:28.781+00:00",
        "profile": [
            "http://hl7.org/fhir/StructureDefinition/shareablevalueset"
        ]
    },
    "text": {
        "status": "generated"
    },
    "extension": [
        {
            "url": "http://hl7.org/fhir/StructureDefinition/structuredefinition-wg",
            "valueCode": "pc"
        }
    ],
    "url": "http://hl7.org/fhir/ValueSet/condition-severity",
    "identifier": [
        {
            "system": "urn:ietf:rfc:3986",
            "value": "urn:oid:2.16.840.1.113883.4.642.3.168"
        }
    ],
    "version": "4.0.1",
    "name": "Condition/DiagnosisSeverity",
    "title": "Condition/Diagnosis Severity",
    "status": "draft",
    "experimental": false,
    "date": "2019-11-01T09:29:23+11:00",
    "publisher": "FHIR Project team",
...
```

Vous pouvez voir que `resourceType` est un `ValueSet` et que le du `url` Profil spécifie également qu’il s’agit d’un type `ValueSet` : `"http://hl7.org/fhir/ValueSet/condition-severity"` .

### <a name="viewing-profiles"></a>Affichage des profils

Vous pouvez accéder à vos profils personnalisés existants sur le serveur à l’aide d’une `GET` demande. Tous les profils valides, tels que les profils avec des URL canoniques valides dans les guides d’implémentation, doivent être accessibles en interrogeant :

```rest
GET http://<your FHIR service base URL>/StructureDefinition?url={canonicalUrl} 
```

Où le champ `{canonicalUrl}` serait remplacé par l’URL canonique de votre profil.

Par exemple, si vous souhaitez afficher le profil de ressource de base US `Goal` :

```rest
GET http://my-fhir-server.azurewebsites.net/StructureDefinition?url=http://hl7.org/fhir/us/core/StructureDefinition/us-core-goal
```

Cette opération renvoie la `StructureDefinition` ressource pour le profil d’objectif principal des États-Unis, qui se présente comme suit :

```json
{
  "resourceType" : "StructureDefinition",
  "id" : "us-core-goal",
  "url" : "http://hl7.org/fhir/us/core/StructureDefinition/us-core-goal",
  "version" : "3.1.1",
  "name" : "USCoreGoalProfile",
  "title" : "US Core Goal Profile",
  "status" : "active",
  "experimental" : false,
  "date" : "2020-07-21",
  "publisher" : "HL7 US Realm Steering Committee",
  "contact" : [
    {
      "telecom" : [
        {
          "system" : "url",
          "value" : "http://www.healthit.gov"
        }
      ]
    }
  ],
  "description" : "Defines constraints and extensions on the Goal resource for the minimal set of data to query and retrieve a patient's goal(s).",
...
```

Notre serveur FHIR ne retourne pas `StructureDefinition` d’instances pour les profils de base, mais ils peuvent être facilement trouvés sur le site Web HL7, par exemple :

- `http://hl7.org/fhir/Observation.profile.json.html`
- `http://hl7.org/fhir/Patient.profile.json.html`


### <a name="profiles-in-the-capability-statement"></a>Profils dans l’instruction Capability

`Capability Statement`Répertorie tous les comportements possibles de votre serveur FHIR à utiliser comme une instruction des fonctionnalités du serveur, telles que les définitions de structure et les ensembles de valeurs. L’API Azure pour FHIR met à jour l’instruction de fonctionnalité avec des informations sur les profils téléchargés et stockés sous les formes suivantes :

- `CapabilityStatement.rest.resource.profile`
- `CapabilityStatement.rest.resource.supportedProfile`

Celles-ci affichent toutes les spécifications du profil qui décrivent la prise en charge globale de la ressource, y compris toutes les contraintes sur la cardinalité, les liaisons, les extensions ou d’autres restrictions. Par conséquent, lorsque vous disposez d’un `POST` profil sous la forme d’un `StructureDefinition` et des `GET` métadonnées de ressource pour afficher l’instruction de fonctionnalité complète, vous verrez en regard du `supportedProfiles` paramètre tous les détails sur le profil que vous avez chargé.

Par exemple, si vous êtes `POST` un profil patient de base US, qui commence comme suit :

```json
{
  "resourceType": "StructureDefinition",
  "id": "us-core-patient",
  "url": "http://hl7.org/fhir/us/core/StructureDefinition/us-core-patient",
  "version": "3.1.1",
  "name": "USCorePatientProfile",
  "title": "US Core Patient Profile",
  "status": "active",
  "experimental": false,
  "date": "2020-06-27",
  "publisher": "HL7 US Realm Steering Committee",
...
```

Et envoyez une `GET` demande pour votre `metadata` :

```rest
GET http://<your FHIR service base URL>/metadata
```

Vous êtes redirigé avec un `CapabilityStatement` qui contient les informations suivantes sur le profil patient de base US que vous avez chargé sur votre serveur FHIR :

```json
...
{
    "type": "Patient",
    "profile": "http://hl7.org/fhir/StructureDefinition/Patient",
    "supportedProfile":[
        "http://hl7.org/fhir/us/core/StructureDefinition/us-core-patient"
    ],
...
```

## <a name="validating-resources-against-the-profiles"></a>Validation des ressources par rapport aux profils

Les ressources FHIR, telles que `Patient` ou `Observation` , peuvent exprimer leur conformité à des profils spécifiques. Cela permet à notre serveur FHIR de **valider** les ressources spécifiées par rapport aux profils associés ou aux profils spécifiés. La validation d’une ressource par rapport aux profils consiste à vérifier si votre ressource est conforme aux profils, y compris les spécifications indiquées dans `Resource.meta.profile` ou dans un guide d’implémentation.

Il existe deux façons de valider votre ressource. Tout d’abord, vous pouvez utiliser `$validate` une opération sur une ressource qui se trouve déjà dans le serveur FHIR. Deuxièmement, vous pouvez `POST` le faire sur le serveur dans le cadre d’une ressource ou d’une `Update` `Create` opération. Dans les deux cas, vous pouvez choisir via la configuration de votre serveur FHIR la procédure à suivre lorsque la ressource n’est pas conforme au profil souhaité.

### <a name="using-validate"></a>Utilisation de $validate

L' `$validate` opération vérifie si le profil fourni est valide et si la ressource est conforme au profil spécifié. Comme mentionné dans les [spécifications HL7 FHIR](https://www.hl7.org/fhir/resource-operation-validate.html), vous pouvez également spécifier `mode` pour `$validate` , comme `create` et `update` :

- `create`: Le serveur vérifie que le contenu du profil est unique à partir des ressources existantes et qu’il est acceptable d’être créé en tant que nouvelle ressource.
- `update`: vérifie que le profil est une mise à jour par rapport à la ressource existante nommée (par exemple, qu’aucune modification n’est apportée aux champs immuables)

Le serveur renverra toujours un `OperationOutcome` comme résultat de la validation.

#### <a name="validating-an-existing-resource"></a>Validation d’une ressource existante

Pour valider une ressource existante, utilisez `$validate` dans une `GET` demande :

```rest
GET http://<your FHIR service base URL>/{resource}/{resource ID}/$validate
```

Par exemple :

```rest
GET http://my-fhir-server.azurewebsites.net/Patient/a6e11662-def8-4dde-9ebc-4429e68d130e/$validate
```

Dans l’exemple ci-dessus, vous validez la `Patient` ressource existante `a6e11662-def8-4dde-9ebc-4429e68d130e` . S’il est valide, vous obtiendrez un `OperationOutcome` tel que celui-ci :

```json
{
    "resourceType": "OperationOutcome",
    "issue": [
        {
            "severity": "information",
            "code": "informational",
            "diagnostics": "All OK"
        }
    ]
}
```

Si la ressource n’est pas valide, vous obtiendrez un code d’erreur et un message d’erreur avec des détails sur la raison pour laquelle la ressource n’est pas valide. Une `4xx` `5xx` erreur ou signifie que la validation elle-même n’a pas pu être effectuée et qu’elle est inconnue, que la ressource soit valide ou non. Un exemple `OperationOutcome` renvoyé avec des messages d’erreur peut se présenter comme suit :

```json
{
    "resourceType": "OperationOutcome",
    "issue": [
        {
            "severity": "error",
            "code": "invalid",
            "details": {
                "coding": [
                    {
                        "system": "http://hl7.org/fhir/dotnet-api-operation-outcome",
                        "code": "1028"
                    }
                ],
                "text": "Instance count for 'Patient.identifier.value' is 0, which is not within the specified cardinality of 1..1"
            },
            "location": [
                "Patient.identifier[1]"
            ]
        },
        {
            "severity": "error",
            "code": "invalid",
            "details": {
                "coding": [
                    {
                        "system": "http://hl7.org/fhir/dotnet-api-operation-outcome",
                        "code": "1028"
                    }
                ],
                "text": "Instance count for 'Patient.gender' is 0, which is not within the specified cardinality of 1..1"
            },
            "location": [
                "Patient"
            ]
        }
    ]
}
```

Dans cet exemple ci-dessus, la ressource n’était pas conforme au `Patient` Profil fourni qui nécessitait une valeur d’identificateur de patient et un sexe.

Si vous souhaitez spécifier un profil en tant que paramètre, vous pouvez spécifier l’URL canonique du profil à valider, comme dans l’exemple suivant avec le profil de base US `Patient` et un profil de base pour `heartrate` :

```rest
GET http://<your FHIR service base URL>/{Resource}/{Resource ID}/$validate?profile={canonicalUrl}
```

Par exemple :

```rest
GET http://my-fhir-server.azurewebsites.net/Patient/a6e11662-def8-4dde-9ebc-4429e68d130e/$validate?profile=http://hl7.org/fhir/us/core/StructureDefinition/us-core-patient
GET http://my-fhir-server.azurewebsites.net/Observation/12345678/$validate?profile=http://hl7.org/fhir/StructureDefinition/heartrate
```

#### <a name="validating-a-new-resource"></a>Validation d’une nouvelle ressource

Si vous souhaitez valider une nouvelle ressource que vous téléchargez sur le serveur, vous pouvez effectuer une `POST` requête :

```rest
POST http://<your FHIR service base URL>/{Resource}/$validate
```

Par exemple :

```rest
POST http://my-fhir-server.azurewebsites.net/Patient/$validate 
```

Cette demande crée la ressource que vous spécifiez dans la charge utile de la demande, qu’elle soit au format JSON ou XML, et valide la ressource téléchargée. Ensuite, il retourne un `OperationOutcome` résultat de la validation sur la nouvelle ressource.

### <a name="validate-on-resource-create-or-resource-update"></a>Valider lors de la création ou de la mise à jour des ressources

Vous pouvez choisir le moment auquel vous souhaitez valider votre ressource, par exemple lors de la création ou de la mise à jour d’une ressource. Vous pouvez le spécifier dans le paramètre de configuration du serveur, sous le `CoreFeatures` :

```json
{
   "FhirServer": {
      "CoreFeatures": {
            "ProfileValidationOnCreate": true,
            "ProfileValidationOnUpdate": false
        }
}
```

Si la ressource est conforme au fourni et que `Resource.meta.profile` le profil est présent dans le système, le serveur agira en conséquence en fonction du paramètre de configuration ci-dessus. Si le profil fourni n’est pas présent sur le serveur, la demande de validation est ignorée et conservée dans `Resource.meta.profile` .
La validation étant généralement une opération coûteuse, elle est généralement exécutée uniquement sur des serveurs de test ou sur un petit sous-ensemble de ressources, ce qui explique pourquoi il est important de disposer de ces moyens pour activer ou désactiver la validation côté serveur. Si la configuration du serveur spécifie de refuser la validation lors de la création/mise à jour d’une ressource, l’utilisateur peut remplacer le comportement en le spécifiant dans la `header` de la demande de création/mise à jour :

```rest
x-ms-profile-validation: true
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris les profils FHIR et comment valider des ressources par rapport à des profils à l’aide de $validate. Pour en savoir plus sur les autres fonctionnalités prises en charge de l’API Azure pour FHIR, consultez :

>[!div class="nextstepaction"]
>[Fonctionnalités prises en charge par FHIR](fhir-features-supported.md)
