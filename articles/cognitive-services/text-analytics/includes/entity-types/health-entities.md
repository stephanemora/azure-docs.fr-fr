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
ms.openlocfilehash: 6880391fb54791fe5f597de2305d24f8c0e47ec6
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88122262"
---
## <a name="health-entity-categories"></a>Catégories d’entités nommées :

Les catégories d’entités retournées par l’[Analyse de texte pour l’intégrité](../../how-tos/text-analytics-for-health.md) sont les suivantes.  Notez que seul le texte en anglais est pris en charge dans cette préversion de conteneur et qu’une seule version de modèle est fournie dans chaque image conteneur.

### <a name="named-entity-recognition"></a>Reconnaissance d’entité nommée

|Category  |Description   |
|----------|--------------|
| Age | Ages. Exemple : *30 ans* |
| AdministrativeEvent | Un événement administratif. |
| BodyStructure | Parties du corps humain, notamment les organes et autres structures. Exemples : *bras* ou *cœur* | 
| CareEnvironment | Environnement dans lequel les soins ou le traitement sont administrés. Exemple : *salle d’urgence* | 
| ConditionQualifier | Niveaux de condition. Exemples : *léger*, *étendu* ou *diffuse* | 
| Diagnostic | États pathologiques. Exemple : *hypertension* | 
| Sens | Côtés. Exemples : *gauche* ou *antérieur* | 
| Dosage | Taille ou quantité de médicament. Exemple : *25 mg*  | 
| ExaminationName | Méthode ou procédure d’examen. Exemple : *rayon X* | 
| RelationalOperator | Opérateur qui définit une relation entre deux entités. Exemples : *moins de* ou `>=`  | 
| MeasurementUnit | Unité de mesure (comme un pourcentage). | 
| MeasurementValue | Valeur numérique d’une unité de mesure. | 
| FamilyRelation | Un lien de parenté. Exemple : *sœur*  | 
| Fréquence | Fréquences.   | 
| Sexe | Sexe. | 
| Gène | Entité génétique. Exemple : *TP53*.   | 
| HealthcareProfession | Mode d’administration du médicament. Exemple : *administration orale* | 
| MedicationClass | Classes de médicaments. Exemple : *antibiotics* (antibiotiques).  | 
| MedicationForm | Forme de médication. Exemple : *gélule* | 
| MedicationName  | Médicaments génériques et princeps. Par exemple, *Ibuprofène*. | 
| MedicationRoute | Mode d’administration du médicament. Exemple : *administration orale* | 
| SymptomOrSign  | Symptômes de maladie. Par exemple, *mal de gorge*. | 
| Temps | Indications temporelles. Exemples : *8 ans* ou *2h30 ce matin* |
| TreatmentName  | Noms de traitements. Exemple : *thérapie* | 
| Variant | Variante génétique de l’entité génétique. | 

### <a name="relation-extraction"></a>Extraction de relations

L’extraction de relations identifie les connexions pertinentes entre les concepts mentionnés dans le texte. Par exemple, une relation « heure de maladie » est trouvée en associant un nom de pathologie à une indication temporelle. L’Analyse de texte pour la santé peut identifier les relations suivantes :

|Category  |Description   |
|----------|--------------|
| DirectionOfBodyStructure | Côté d’une structure du corps. |
| DirectionOfCondition | Direction d’un état. |
| DirectionOfExamination | Direction d’un examen. |
| DirectionOfTreatment | Direction d’un traitement. |
| TimeOfCondition | Heure associée à la survenance d’un état. |
| QualifierOfCondition | Qualificateur associé à un état. |
| DosageOfMedication | Dosage du médicament. |
| FormOfMedication | Forme de médication. |
| FormOfMedication | Voie ou mode de consommation d’un médicament. Example : *orale*. |
| FrequencyOfMedication | Fréquence à laquelle un médicament est consommée. | 
| ValueOfCondition | Valeur numérique associée à un état. |
| UnitOfCondition | Unité (comme l’heure) associée à un état. |
| TimeOfMedication | Heure à laquelle un médicament a été consommé. |
| TimeOfTreatment | Heure à laquelle un traitement a été administré. | 
| FrequencyOfTreatment | Fréquence à laquelle un traitement est administré. |
| ValueOfExamination | Valeur numérique associée à un examen. | 
| ValueOfExamination | Unité (comme un pourcentage) associée à un examen. |
| RelationOfExamination | Relation entre une entité et un examen. | 
| RelationOfExamination | Heure associée à un examen. |
| Abréviation | Une abréviation.  | 
