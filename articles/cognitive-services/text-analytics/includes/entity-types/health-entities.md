---
title: Entités nommées pour la santé
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/11/2021
ms.author: aahi
ms.openlocfilehash: 805c726d33f2050f6f2797c0689069aa5ec4ee71
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599321"
---
[Analyse de texte pour les processus d’intégrité](../../how-tos/text-analytics-for-health.md) et extrait des informations à partir de données médicales non structurées. Le service détecte et détecte les concepts médicaux, assigne des assertions aux concepts, déduit les relations sémantiques entre les concepts et les lie à des ontologies médicaux courantes.

Analyse de texte pour la santé détecte les concepts médicaux dans les catégories suivantes. Seul le texte anglais est pris en charge dans cet aperçu et une seule version du modèle est disponible.

| Catégorie  | Description  |
|---------|---------|
| [ANATOMY](#anatomy) | concepts qui capturent des informations sur le corps et les systèmes anatomiques, les sites, les emplacements ou les régions. |
 | [DEMOGRAPHICS](#demographics) | concepts qui capturent des informations sur le sexe et l’âge. |
 | [EXAMINATION](#examinations) | concepts qui capturent des informations sur les procédures de diagnostic et les tests. |
 | [ATTRIBUTS GÉNÉRAUX](#general-attributes) | concepts qui fournissent plus d’informations sur les autres concepts des catégories ci-dessus. |
 | [GENOMICS](#genomics) | concepts qui capturent des informations sur les gènes et les variantes. |
 | [HEALTHCARE](#healthcare) | concepts qui capturent des informations sur les événements administratifs, les environnements de soins et les professions de santé. |
 | [ÉTAT PATHOLOGIQUE](#medical-condition) | concepts qui capturent des informations sur les diagnostics, les symptômes ou les signes. |
 | [MÉDICAMENT](#medication) | concepts qui capturent des informations sur les médicaments, y compris les noms des médicaments, les classes, la posologie et la voie d’administration. |
 | [SOCIAL](#social) | concepts qui capturent des informations sur les aspects sociaux pertinents d’un point de vue médical, comme la famille. |
 | [TREATMENT](#treatment) | concepts qui capturent des informations sur les procédures thérapeutiques. |

Vous trouverez plus d’informations et d’exemples ci-dessous.

## <a name="anatomy"></a>Anatomie

### <a name="entities"></a>Entités

**BODY_STRUCTURE** : systèmes corporels, emplacements ou régions anatomiques et sites du corps. Par exemple : arm, knee, abdomen, nose, liver, head, respiratory system, lymphocytes (bras, genou, abdomen, nez, foie, tête, système respiratoire, lymphocytes).

:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure.png" alt-text="Exemple d’entité de structure du corps.":::


:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure-2.png" alt-text="Exemple étendu d’entité de structure du corps.":::

## <a name="demographics"></a>Démographie

### <a name="entities"></a>Entités

**ÂGE** : tous les termes et expressions relatifs à l’âge, y compris celui d’un patient, de membres de la famille et autres. Par exemple : 40-year-old, 51 yo, 3 months old, adult, infant, elderly, young, minor, middle-aged (40 ans, 51, 3 mois, adulte, nourrisson, personne âgée, jeune, mineur, âge moyen).

:::image type="content" source="../../media/ta-for-health/age-entity.png" alt-text="Exemple d’entité d’âge.":::

:::image type="content" source="../../media/ta-for-health/age-entity-2.png" alt-text="Autre exemple d’entité d’âge.":::


**GENDER** : termes qui révèlent le sexe du sujet. Par exemple : male, female, woman, gentleman, lady (homme, femme, féminin, masculin).

:::image type="content" source="../../media/ta-for-health/gender-entity.png" alt-text="Exemple d’une entité de sexe.":::

## <a name="examinations"></a>Examens

### <a name="entities"></a>Entités

**EXAMINATION_NAME** : procédures et tests de diagnostic, y compris les signes vitaux et les mesures de corps. Par exemple : MRI, ECG, HIV test, hemoglobin, platelets count, scale systems (*Bristol stool scale*) (IRM, ECG, test du VIH, hémoglobine, nombre de plaquettes, systèmes d’échelle comme l’Échelle de Bristol).

:::image type="content" source="../../media/ta-for-health/exam-name-entities.png" alt-text="Exemple d’entité d’examen.":::

:::image type="content" source="../../media/ta-for-health/exam-name-entities-2.png" alt-text="Autre exemple d’entité de nom d’examen.":::

## <a name="general-attributes"></a>Attributs généraux

### <a name="entities"></a>Entités

**DATE** -date complète relative à une condition médicale, à un examen, à un traitement, à un médicament ou à un événement administratif.

**DIRECTION** : conditions directionnelles qui peuvent être liées à une structure corporelle, à une condition médicale, à un examen ou à un traitement, par exemple : gauche, latéral, supérieur, postérieur.

**FRÉQUENCE** : décrit la fréquence à laquelle une condition, un examen, un traitement ou une médication médicale s’est produit, ou doit se produire.

**MEASUREMENT_VALUE** : valeur associée à un examen ou une mesure de condition médicale.

**MEASUREMENT_UNIT** : unité de mesure liée à un examen ou à une mesure médicale.

**RELATIONAL_OPERATOR** : expressions qui expriment la relation entre une entité et des informations supplémentaires.

**TIME** : Termes temporels relatifs au début et/ou à la longueur (durée) d'un état pathologique, d'un examen, d'un traitement, d'un médicament ou d'un événement administratif. 

## <a name="genomics"></a>Genomics

### <a name="entities"></a>Entités

**GENE_OR_PROTEIN** : toutes les mentions de noms et de symboles des gènes humains, ainsi que les chromosomes et parties des chromosomes et des protéines. Par exemple : MTRR, F2.

:::image type="content" source="../../media/ta-for-health/genomics-entities.png" alt-text="Exemple d’une entité de gène.":::

**VARIANT** : toutes les mentions des variantes d’un gène. Par exemple, `c.524C>T`,`(MTRR):r.1462_1557del96`
  
## <a name="healthcare"></a>Santé

### <a name="entities"></a>Entités
  
**ADMINISTRATIVE_EVENT** : événements liés au système de santé, mais d’une nature administrative/semi-administrative. Par exemple : registration, admission, trial, study entry, transfer, discharge, hospitalization, hospital stay (enregistrement, admission, essai, entrée d’étude, transfert, décharge, hospitalisation, séjour hospitalier). 

:::image type="content" source="../../media/ta-for-health/healthcare-event-entity.png" alt-text="Exemple d’entité d’événement de santé.":::

**CARE_ENVIRONMENT** : environnement ou emplacement où les patients reçoivent des soins. Par exemple : emergency room, physician’s office, cardio unit, hospice, hospital (salle des urgences, bureau du médecin, unité de cardiologie, hospice, hôpital).

:::image type="content" source="../../media/ta-for-health/healthcare-environment-entity.png" alt-text="Cette capture d’écran montre un exemple d’entité d’environnement médical.":::

**HEALTHCARE_PROFESSION** : praticien de la santé avec ou sans licence. Par exemple : dentist, pathologist, neurologist, radiologist, pharmacist, nutritionist, physical therapist, chiropractor (dentiste, pathologiste, neurologue, radiologue, pharmacien, nutritionniste, physiothérapeute, chiropracteur).

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity.png" alt-text="Cette capture d’écran montre un autre exemple d’entité d’environnement médical.":::

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity-2.png" alt-text="Autre exemple d’entité d’environnement de santé.":::

## <a name="medical-condition"></a>État pathologique

### <a name="entities"></a>Entités

**DIAGNOSIS** : maladie, syndrome, empoisonnement. Par exemple : breast cancer, Alzheimer’s, HTN, CHF, spinal cord injury (cancer du sein, Alzheimer, HTN, CHF, lésion médullaire).

:::image type="content" source="../../media/ta-for-health/medical-condition-entity.png" alt-text="Exemple d’entité d’état pathologique.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-entity-2.png" alt-text="Autre exemple d’entité d’état pathologique.":::

**SYMPTOM_OR_SIGN** : preuve subjective ou objective de la maladie ou d’autres diagnostics. Par exemple : chest pain, headache, dizziness, rash, SOB, abdomen was soft, good bowel sounds, well nourished (douleur de poitrine, mal de tête, vertiges, éruption, SOB, abdomen mou, sons des intestins appropriés, bien nourri).

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity.png" alt-text="Exemple d’entité de signe ou de symptôme d’état pathologique.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity-2.png" alt-text="Autre exemple d’entité de signe ou de symptôme d’état pathologique.":::

**CONDITION_QUALIFIER** : termes qualitatifs utilisés pour décrire un état pathologique. Toutes les sous-catégories suivantes sont considérées comme des qualificateurs :

1.  Expressions liées au temps : il s’agit de termes qui décrivent la dimension du temps de manière qualitative. Par exemple : sudden, acute, chronic, longstanding (soudain, aigu, chronique, longue durée). 
2.  Expressions de qualité :  Il s’agit de termes qui décrivent la « nature » de l’état pathologique. Par exemple : burning, sharp (brûlure, aigu).
3.  Expressions de gravité : severe, mild, a bit, uncontrolled (grave, léger, un peu, non contrôlé).
4.  Expressions d’étendue : local, focal, diffuse (local, concentré, diffus).
5.  Expressions de rayonnement : radiates, radiation (irradie, rayonnement).
6.  Échelle de condition Dans certains cas, une condition est caractérisée par une échelle, qui est une liste triée finie de valeurs. Par exemple : Patients with stage III pancreatic cancer (patients avec cancer pancréatique en la phase III).
7.  Cours de condition : Terme relatif au cours ou à la progression d’une condition. Par exemple : improvement, worsening, resolution, remission (amélioration, détérioration, guérison et rémission). 

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis.png" alt-text="Exemple d’attribut de qualificateur de condition et d’entité de diagnostic.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-2.png" alt-text="Autre exemple d’attribut de qualificateur de condition et d’entité de diagnostic.":::

:::image type="content" source="../../media/ta-for-health/conditional-qualifier-symptom-medication.png" alt-text="Exemple d’attribut de qualificateur de condition avec entités de symptôme et de médicament.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-3.png" alt-text="Cette capture d’écran montre un autre exemple d’attribut de qualificateur de condition avec une entité de diagnostic.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-symptom.png" alt-text="Cette capture d’écran montre un autre exemple d’attribut de qualificateur de condition avec une entité de diagnostic.":::

## <a name="medication"></a>Médicament

### <a name="entities"></a>Entités

**MEDICATION_CLASS** : ensemble de médicaments avec un mécanisme d’action similaire, un mode d’action associé, une structure chimique similaire et/ou qui sont utilisés pour traiter la même maladie. Par exemple : ACE inhibitor, opioid, antibiotics, pain relievers (inhibiteur ACE, opioïdes, antibiotiques, anti-douleur).

:::image type="content" source="../../media/ta-for-health/medication-entities-class.png" alt-text="Exemple d’entité de classe de médicament.":::

**MEDICATION_NAME** : mentions médicamenteuses, y compris les noms de marques et autres noms. Par exemple, l’ibuprofène.

:::image type="content" source="../../media/ta-for-health/medication-entities-name.png" alt-text="Exemple d’entité de nom de médicament.":::

**DOSAGE** : quantité de médicament demandée. Par exemple : Infuse Sodium Chloride solution *1000 mL* (Infuser solution de chlorure de sodium, 1 000 ml).

:::image type="content" source="../../media/ta-for-health/medication-dosage.png" alt-text="Exemple d’attribut de posologie de médicament.":::

**MEDICATION_FORM** : forme du médicament. Par exemple : solution, pill, capsule, tablet, patch, gel, paste, foam, spray, drops, cream, syrup (solution, pilule, capsule, tablette, patch, gel, pâte, mousse, spray, goutte, crème, sirop).

:::image type="content" source="../../media/ta-for-health/medication-form.png" alt-text="Exemple d’attribut de forme de médicament.":::

**MEDICATION_ROUTE** : méthode d’administration de médicament. Par exemple : oral, vaginal, IV, epidural, topical, inhaled (oral, vaginal, intraveineuse, épidurale, topique, inhalé).

:::image type="content" source="../../media/ta-for-health/medication-route.png" alt-text="Exemple d’attribut d’administration de médicament.":::

## <a name="social"></a>Réseaux sociaux

### <a name="entities"></a>Entités

**FAMILY_RELATION** : mentions relatives à la famille du sujet. Par exemple : father, daughter, siblings, parents (père, fille, frères et sœurs, parents).

:::image type="content" source="../../media/ta-for-health/family-relation.png" alt-text="Cette capture d’écran montre un autre exemple d’attribut de temps de traitement.":::

## <a name="treatment"></a>Traitement

### <a name="entities"></a>Entités

**TREATMENT_NAME** : procédures thérapeutiques. Par exemple : knee replacement surgery, bone marrow transplant, TAVI, diet (chirurgie de remplacement du genou, transplantation de moelle osseuse, TAVI, régime).

:::image type="content" source="../../media/ta-for-health/treatment-entities-name.png" alt-text="Exemple d’entité de nom de traitement.":::
