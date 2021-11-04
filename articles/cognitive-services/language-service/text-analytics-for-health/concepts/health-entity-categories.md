---
title: Catégories d’entités reconnues par Analyse de texte pour la santé
titleSuffix: Azure Cognitive Services
description: Découvrir les catégories reconnues par Analyse de texte pour la santé
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: article
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-health, ignite-fall-2021
ms.openlocfilehash: e61589eaf90da3cb7e8310724a28bf993e8efb21
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096311"
---
# <a name="supported-text-analytics-for-health-entity-categories"></a>Catégories d’entités Analyse de texte pour la santé prises en charge

Analyse de texte pour les processus d’intégrité et extrait des informations à partir de données médicales non structurées. Le service détecte et détecte les concepts médicaux, assigne des assertions aux concepts, déduit les relations sémantiques entre les concepts et les lie à des ontologies médicaux courantes.

Analyse de texte pour la santé détecte les concepts médicaux dans les catégories suivantes.

## <a name="anatomy"></a>Anatomie

### <a name="entities"></a>Entités

**BODY_STRUCTURE** : systèmes corporels, emplacements ou régions anatomiques et sites du corps. Par exemple : arm, knee, abdomen, nose, liver, head, respiratory system, lymphocytes (bras, genou, abdomen, nez, foie, tête, système respiratoire, lymphocytes).

:::image type="content" source="../media/entities/anatomy-entities-body-structure.png" alt-text="Exemple d’entité de structure du corps." lightbox="../media/entities/anatomy-entities-body-structure.png":::

## <a name="demographics"></a>Démographie

### <a name="entities"></a>Entités

**ÂGE** : tous les termes et expressions relatifs à l’âge, y compris celui d’un patient, de membres de la famille et autres. Par exemple : 40-year-old, 51 yo, 3 months old, adult, infant, elderly, young, minor, middle-aged (40 ans, 51, 3 mois, adulte, nourrisson, personne âgée, jeune, mineur, âge moyen).

**GENDER** : termes qui révèlent le sexe du sujet. Par exemple : male, female, woman, gentleman, lady (homme, femme, féminin, masculin).

:::image type="content" source="../media/entities/age-entity.png" alt-text="Exemple d’entité d’âge." lightbox="../media/entities/age-entity.png":::

## <a name="examinations"></a>Examens

### <a name="entities"></a>Entités

**EXAMINATION_NAME** : procédures et tests de diagnostic, y compris les signes vitaux et les mesures de corps. Par exemple : MRI, ECG, HIV test, hemoglobin, platelets count, scale systems (*Bristol stool scale*) (IRM, ECG, test du VIH, hémoglobine, nombre de plaquettes, systèmes d’échelle comme l’Échelle de Bristol).

:::image type="content" source="../media/entities/exam-name-entities.png" alt-text="Exemple d’entité d’examen." lightbox="../media/entities/exam-name-entities.png":::

## <a name="external-influence"></a>Influence externe

### <a name="entities"></a>Entités

**ALLERGEN** : antigène déclenchant une réaction allergique. Par exemple : les chats, les arachides.

:::image type="content" source="../media/entities/external-influence-allergen.png" alt-text="Exemple d’une entité d’influence externe." lightbox="../media/entities/external-influence-allergen.png":::


## <a name="general-attributes"></a>Attributs généraux

### <a name="entities"></a>Entités

**COURSE** : description d’une modification d’une autre entité au fil du temps, comme la progression d’un état (p. ex. : amélioration, aggravation, résolution, rémission) ou une série de traitement ou prise de médicaments (p. ex. : augmentation de la posologie des médicaments). 

:::image type="content" source="../media/entities/course-entity.png" alt-text="Exemple d’une entité de traitement." lightbox="../media/entities/course-entity.png":::

**DATE** -date complète relative à une condition médicale, à un examen, à un traitement, à un médicament ou à un événement administratif.

:::image type="content" source="../media/entities/date-entity.png" alt-text="Exemple d’une entité de date." lightbox="../media/entities/date-entity.png":::

**DIRECTION** : conditions directionnelles qui peuvent être liées à une structure corporelle, à une condition médicale, à un examen ou à un traitement, par exemple : gauche, latéral, supérieur, postérieur.

:::image type="content" source="../media/entities/direction-entity.png" alt-text="Exemple d’une entité de direction." lightbox="../media/entities/direction-entity.png":::

**FRÉQUENCE** : décrit la fréquence à laquelle une condition, un examen, un traitement ou une médication médicale s’est produit, ou doit se produire.

:::image type="content" source="../media/entities/medication-form.png" alt-text="Exemple d’attribut de fréquence de médicament." lightbox="../media/entities/medication-form.png":::


**TIME** : Termes temporels relatifs au début et/ou à la longueur (durée) d'un état pathologique, d'un examen, d'un traitement, d'un médicament ou d'un événement administratif. 

**MEASUREMENT_UNIT** : unité de mesure liée à un examen ou à une mesure médicale.

**MEASUREMENT_VALUE** : valeur associée à un examen ou une mesure de condition médicale.

:::image type="content" source="../media/entities/measurement-value-entity.png" alt-text="Exemple d’une entité de valeur de mesure." lightbox="../media/entities/measurement-value-entity.png":::

**RELATIONAL_OPERATOR** : expressions qui expriment la relation entre une entité et des informations supplémentaires.

:::image type="content" source="../media/entities/measurement-unit.png" alt-text="Exemple d’une entité d’unité de mesure." lightbox="../media/entities/measurement-unit.png"::: 

## <a name="genomics"></a>Genomics

### <a name="entities"></a>Entités

**VARIANT** : toutes les mentions de variations et de mutations génétiques. Par exemple, `c.524C>T`,`(MTRR):r.1462_1557del96`
  
**GENE_OR_PROTEIN** : toutes les mentions de noms et de symboles des gènes humains, ainsi que les chromosomes et parties des chromosomes et des protéines. Par exemple : MTRR, F2.

**MUTATION_TYPE** : description de la mutation, y compris son type, son effet et sa localisation. Par exemple, trisomie, mutation germinale, perte de fonction.

:::image type="content" source="../media/entities/genomics-entities.png" alt-text="Exemple d’une entité de gène." lightbox="../media/entities/genomics-entities.png":::

**EXPRESSION** : niveau d’expression des gènes. Par exemple, positif, négatif, surexprimé, détecté à des niveaux élevés/faibles, élevé.

:::image type="content" source="../media/entities/expression.png" alt-text="Exemple d’une entité d’expression génique." lightbox="../media/entities/expression.png":::


## <a name="healthcare"></a>Santé

### <a name="entities"></a>Entités
  
**ADMINISTRATIVE_EVENT** : événements liés au système de santé, mais d’une nature administrative/semi-administrative. Par exemple : registration, admission, trial, study entry, transfer, discharge, hospitalization, hospital stay (enregistrement, admission, essai, entrée d’étude, transfert, décharge, hospitalisation, séjour hospitalier). 

**CARE_ENVIRONMENT** : environnement ou emplacement où les patients reçoivent des soins. Par exemple : emergency room, physician’s office, cardio unit, hospice, hospital (salle des urgences, bureau du médecin, unité de cardiologie, hospice, hôpital).

:::image type="content" source="../media/entities/healthcare-event-entity.png" alt-text="Exemple d’entité d’événement de santé." lightbox="../media/entities/healthcare-event-entity.png" :::

**HEALTHCARE_PROFESSION** : praticien de la santé avec ou sans licence. Par exemple : dentist, pathologist, neurologist, radiologist, pharmacist, nutritionist, physical therapist, chiropractor (dentiste, pathologiste, neurologue, radiologue, pharmacien, nutritionniste, physiothérapeute, chiropracteur).

:::image type="content" source="../media/entities/healthcare-profession-entity-2.png" alt-text="Autre exemple d’entité d’environnement de santé." lightbox="../media/entities/healthcare-profession-entity-2.png":::

## <a name="medical-condition"></a>État pathologique

### <a name="entities"></a>Entités

**DIAGNOSIS** : maladie, syndrome, empoisonnement. Par exemple : breast cancer, Alzheimer’s, HTN, CHF, spinal cord injury (cancer du sein, Alzheimer, HTN, CHF, lésion médullaire).

**SYMPTOM_OR_SIGN** : preuve subjective ou objective de la maladie ou d’autres diagnostics. Par exemple : chest pain, headache, dizziness, rash, SOB, abdomen was soft, good bowel sounds, well nourished (douleur de poitrine, mal de tête, vertiges, éruption, SOB, abdomen mou, sons des intestins appropriés, bien nourri).

:::image type="content" source="../media/entities/medical-condition-entity.png" alt-text="Exemple d’entité d’état pathologique." lightbox="../media/entities/medical-condition-entity.png":::

**CONDITION_QUALIFIER** : termes qualitatifs utilisés pour décrire un état pathologique. Toutes les sous-catégories suivantes sont considérées comme des qualificateurs :

* Expressions liées au temps : il s’agit de termes qui décrivent la dimension du temps de manière qualitative. Par exemple : sudden, acute, chronic, longstanding (soudain, aigu, chronique, longue durée). 
* Expressions de qualité :  Il s’agit de termes qui décrivent la « nature » de l’état pathologique. Par exemple : burning, sharp (brûlure, aigu).
* Expressions de gravité : severe, mild, a bit, uncontrolled (grave, léger, un peu, non contrôlé).
* Expressions d’étendue : local, focal, diffuse (local, concentré, diffus).

:::image type="content" source="../media/entities/condition-qualifier-diagnosis-3.png" alt-text="Cette capture d’écran montre un autre exemple d’attribut de qualificateur de condition avec une entité de diagnostic." lightbox="../media/entities/condition-qualifier-diagnosis-3.png" :::

**CONDITION_SCALE** : termes qualitatifs qui caractérisent la condition par une échelle, qui est une liste ordonnée finie de valeurs.

:::image type="content" source="../media/entities/condition-scale-example.png" alt-text="Autre exemple d’attribut de qualificateur de condition et d’entité de diagnostic." lightbox="../media/entities/condition-scale-example.png":::

## <a name="medication"></a>Médicament

### <a name="entities"></a>Entités

**MEDICATION_CLASS** : ensemble de médicaments avec un mécanisme d’action similaire, un mode d’action associé, une structure chimique similaire et/ou qui sont utilisés pour traiter la même maladie. Par exemple : ACE inhibitor, opioid, antibiotics, pain relievers (inhibiteur ACE, opioïdes, antibiotiques, anti-douleur).

:::image type="content" source="../media/entities/medication-entities-class.png" alt-text="Exemple d’entité de classe de médicament." lightbox="../media/entities/medication-entities-class.png":::

**MEDICATION_NAME** : mentions médicamenteuses, y compris les noms de marques et autres noms. Par exemple, l’ibuprofène.

**DOSAGE** : quantité de médicament demandée. Par exemple : Infuse Sodium Chloride solution *1000 mL* (Infuser solution de chlorure de sodium, 1 000 ml).

**MEDICATION_FORM** : forme du médicament. Par exemple : solution, pill, capsule, tablet, patch, gel, paste, foam, spray, drops, cream, syrup (solution, pilule, capsule, tablette, patch, gel, pâte, mousse, spray, goutte, crème, sirop).

:::image type="content" source="../media/entities/medication-dosage.png" alt-text="Exemple d’attribut de posologie de médicament." lightbox="../media/entities/medication-dosage.png":::

**MEDICATION_ROUTE** : méthode d’administration de médicament. Par exemple, par voie orale, application locale ou par inhalation.

:::image type="content" source="../media/entities/medication-form.png" alt-text="Exemple d’attribut de forme de médicament." lightbox="../media/entities/medication-form.png"::: 

## <a name="social"></a>Réseaux sociaux

### <a name="entities"></a>Entités

**FAMILY_RELATION** : mentions relatives à la famille du sujet. Par exemple : father, daughter, siblings, parents (père, fille, frères et sœurs, parents).

:::image type="content" source="../media/entities/family-relation.png" alt-text="Exemple d’une entité de relation familiale." lightbox="../media/entities/family-relation.png":::

## <a name="treatment"></a>Traitement

### <a name="entities"></a>Entités

**TREATMENT_NAME** : procédures thérapeutiques. Par exemple : knee replacement surgery, bone marrow transplant, TAVI, diet (chirurgie de remplacement du genou, transplantation de moelle osseuse, TAVI, régime).

:::image type="content" source="../media/entities/treatment-entities-name.png" alt-text="Exemple d’entité de nom de traitement." lightbox="../media/entities/treatment-entities-name.png":::


## <a name="supported-assertions"></a>Assertions prises en charge

Les modificateurs d’assertion sont divisés en trois catégories, chacune se concentrant sur un aspect différent.
Chaque catégorie contient un ensemble de valeurs s’excluant mutuellement. Une seule valeur par catégorie est attribuée à chaque entité. La valeur par défaut est la valeur la plus courante pour chaque catégorie. La réponse de sortie du service contient uniquement des modificateurs d’assertion qui sont différents de la valeur par défaut.

### <a name="certainty"></a>Certainty  

fournit des informations sur la présence (présent ou absent) du concept et du niveau de certitude du texte quant à sa présence (certain ou possible).

**Positive** (par défaut) : le concept existe ou est survenu.

**Negative** : le concept n’existe pas maintenant ou n’a jamais eu lieu.

:::image type="content" source="../media/entities/negative-entity.png" alt-text="Exemple d’une entité négative." lightbox="../media/entities/negative-entity.png":::

**Positive_Possible** : le concept existe probablement, mais il y a une certaine incertitude.

:::image type="content" source="../media/entities/positive-possible-entity.png" alt-text="Exemple d’une entité positive possible." lightbox="../media/entities/positive-possible-entity.png" :::

**Negative_Possible** : l’existence du concept est peu probable, mais il y a une certaine incertitude.

:::image type="content" source="../media/entities/negative-possible-entity.png" alt-text="Exemple d’une entité négative possible." lightbox="../media/entities/negative-possible-entity.png" :::

**Neutral_Possible** : le concept peut exister ou non sans une tendance claire dans l’une ou l’autre direction.

:::image type="content" source="../media/entities/neutral-possible-entity.png" alt-text="Exemple d’une entité neutre possible." lightbox="../media/entities/neutral-possible-entity.png":::

### <a name="conditionality"></a>Conditionality

fournit des informations indiquant si l’existence d’un concept dépend de certaines conditions. 

**None** (par défaut) : le concept est factuel et non hypothétique, et il ne dépend pas de certaines conditions.

**Hypothetical** : le concept peut se développer ou se produire à l’avenir.

:::image type="content" source="../media/entities/hypothetical-entity.png" alt-text="Exemple d’une entité hypothétique." lightbox="../media/entities/hypothetical-entity.png":::

**Conditional** : le concept existe ou se produit uniquement sous certaines conditions.

:::image type="content" source="../media/entities/conditional-entity.png" alt-text="Exemple d’entité conditionnelle." lightbox="../media/entities/conditional-entity.png":::

### <a name="association"></a>Association

indique si le concept est associé à l’objet du texte ou à une autre personne.

**Subject** (par défaut) : le concept est associé à l’objet du texte, généralement le patient.

**Someone_Else**: le concept est associé à une personne qui n’est pas l’objet du texte.

:::image type="content" source="../media/entities/association-entity.png" alt-text="Exemple d’une entité d’association." lightbox="../media/entities/association-entity.png":::



## <a name="next-steps"></a>Étapes suivantes

* [Présentation de NER](../../named-entity-recognition/overview.md)
