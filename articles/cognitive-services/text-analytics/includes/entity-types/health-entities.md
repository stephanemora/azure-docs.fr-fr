---
title: Entités nommées pour la santé
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/28/2020
ms.author: aahi
ms.openlocfilehash: 4ac65e85e05f408b8d2f37a1d6845dc9e28e2bab
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87373066"
---
## <a name="health-entity-categories"></a>Catégories d’entités nommées :

Les catégories d’entités retournées par l’[Analyse de texte pour l’intégrité](../../how-tos/text-analytics-for-health.md) sont les suivantes.  Notez que seul le texte en anglais est pris en charge dans cette préversion de conteneur et qu’une seule version de modèle est fournie dans chaque image conteneur.

### <a name="named-entity-recognition"></a>Reconnaissance d’entité nommée

|Category  |Description   |
|----------|--------------|
| AGE | Ages. |
| BODY_STRUCTURE | Parties du corps humain, notamment les organes et autres structures. | 
| CONDITION_QUALIFIER | Niveau de pathologie. Exemples : *mild* (léger), *extended* (étendu) ou *diffuse* (diffus). | 
| DIAGNOSIS | États pathologiques. Exemple : *hypertension*. | 
| DIRECTION | Directions. Exemples : *left* (gauche) ou *anterior* (antérieur). | 
| DOSAGE | Taille ou quantité de médicament.  | 
| EXAMINATION_NAME | Méthode ou procédure d’examen. | 
| EXAMINATION_RELATION | Combinaison entre une unité de mesure et un examen.  | 
| EXAMINATION_UNIT | Unité de mesure pour un examen. | 
| EXAMINATION_VALUE | Valeur de l’unité de mesure de l’examen. | 
| FAMILY_RELATION | Relation familiale. Exemple : *sister* (sœur).  | 
| FRÉQUENCE | Fréquences.   | 
| GENDER | Sexe. | 
| GENE | Entité génétique. Exemple : *TP53*.   | 
| MEDICATION_CLASS | Classes de médicaments. Exemple : *antibiotics* (antibiotiques).  | 
| MEDICATION_NAME  | Médicaments génériques et princeps.| 
| ROUTE_OR_MODE  | Mode d’administration du médicament. | 
| SYMPTOM_OR_SIGN  | Symptômes de maladie. | 
| TEMPS  | Indications temporelles. Exemples : « 8 ans » ou « à 2 h 30 du matin ». |
| TREATMENT_NAME  | Noms de traitements. | 
| VARIANT  | Variante génétique de l’entité génétique. | 

### <a name="relation-extraction"></a>Extraction de relations

L’extraction de relations identifie les connexions pertinentes entre les concepts mentionnés dans le texte. Par exemple, une relation « heure de maladie » est trouvée en associant un nom de pathologie à une indication temporelle. L’Analyse de texte pour la santé peut identifier les relations suivantes :

* DIRECTION_OF_BODY_STRUCTURE  
* TIME_OF_CONDITION
* QUALIFIER_OF_CONDITION  
* DOSAGE_OF_MEDICATION 
* FORM_OF_MEDICATION  
* ROUTE_OR_MODE_OF_MEDICATION   
* STRENGTH_OF_MEDICATION 
* ADMINISTRATION_RATE_OF_MEDICATION   
* FREQUENCY_OF_MEDICATION 
* TIME_OF_MEDICATION 
* TIME_OF_TREATMENT 
* FREQUENCY_OF_TREATMENT  
* VALUE_OF_EXAMINATION
* UNIT_OF_EXAMINATION 
* RELATION_OF_EXAMINATION 
* TIME_OF_EXAMINATION  
* ABBREVIATION 
