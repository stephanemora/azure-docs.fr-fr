---
title: Didacticiel sur la formule de la drogue da Vinci
description: Ce didacticiel vous guide dans la configuration du service FHIR pour passer les tests Touchstone au Guide de mise en œuvre de la formule du médicament DaVinci Drug.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: modillon
ms.date: 08/06/2021
ms.openlocfilehash: 56cf21f960cadd54340be48deeb44b1e37470f69
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778811"
---
# <a name="tutorial-for-da-vinci-drug-formulary"></a>Didacticiel pour la formule de la drogue da Vinci

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

dans ce didacticiel, nous allons passer en revue la configuration du service FHIR dans les api de santé Azure (par le biais du service FHIR) pour transmettre les tests [Touchstone](https://touchstone.aegis.net/touchstone/) pour le Guide d’implémentation des données de la formule de la [drogue Da Vinci Exchange](http://hl7.org/fhir/us/Davinci-drug-formulary/).

## <a name="touchstone-capability-statement"></a>Instruction de fonctionnalité Touchstone

Le premier test sur lequel nous allons nous concentrer consiste à tester le service FHIR à l’aide de l’instruction de fonctionnalité de la formule de la [drogue da Vinci](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/Formulary/00-Capability&activeOnly=false&contentEntry=TEST_SCRIPTS). Si vous exécutez ce test sans aucune mise à jour, le test échoue en raison de paramètres de recherche manquants et de profils manquants.

### <a name="define-search-parameters"></a>Définir les paramètres de recherche

Dans le cadre de la définition de médicament da Vinci, vous devez définir trois [nouveaux paramètres de recherche](how-to-do-custom-search.md) pour la ressource FormularyDrug. Les trois sont testés dans l’instruction de fonctionnalité.

* [DrugTier](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/SearchParameter-DrugTier.json.html)
* [DrugPlan](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/SearchParameter-DrugPlan.json.html)
* [DrugName](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/SearchParameter-DrugName.json.html)

Le reste des paramètres de recherche nécessaires pour la spécification de la spécification de la drogue da Vinci est défini par la spécification de base et est déjà disponible dans le service FHIR sans aucune mise à jour supplémentaire.

### <a name="store-profiles"></a>Stocker les profils

En dehors de la définition des paramètres de recherche, la seule autre mise à jour que vous devez effectuer pour réussir ce test consiste à charger les [profils requis](validation-against-profiles.md). Deux profils sont utilisés dans le cadre du IG de la drogue da Vinci.

* [Médicament de la formule](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/StructureDefinition-usdf-FormularyDrug.html)
* [Plan de couverture de la formule](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/StructureDefinition-usdf-CoveragePlan.html)

### <a name="sample-rest-file"></a>Exemple de fichier Rest

Pour faciliter la création de ces paramètres de recherche et de ces profils, nous disposons de l’exemple de fichier HTTP [da Vinci](https://github.com/microsoft/fhir-server/blob/main/docs/rest/DaVinciFormulary/DaVinciFormulary.http) sur le site Open source, qui comprend toutes les étapes décrites ci-dessus dans un fichier unique. Une fois que vous avez téléchargé tous les profils et paramètres de recherche nécessaires, vous pouvez exécuter le test d’instruction de fonctionnalité dans Touchstone. Vous devez obtenir une exécution réussie :

:::image type="content" source="media/centers-medicare-services-tutorials/davinci-test-script-execution.png" alt-text="Exécution du script de test da Vinci.":::

## <a name="touchstone-query-test"></a>Test de requête Touchstone

Le deuxième test est celui des [fonctionnalités de requête](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/Formulary/01-Query&activeOnly=false&contentEntry=TEST_SCRIPTS). Ce test permet de vérifier que vous pouvez rechercher un plan de couverture spécifique et des ressources médicamenteuses à l’aide de différents paramètres. Le meilleur moyen consiste à effectuer des tests sur les ressources que vous avez déjà dans votre base de données, mais le fichier http [Da VinciFormulary_Sample_Resources](https://github.com/microsoft/fhir-server/blob/main/docs/rest/DaVinciFormulary/DaVinciFormulary_Sample_Resources.http) est également disponible avec les exemples de ressources tirés des exemples de la IG que vous pouvez utiliser pour créer les ressources et les tests.

:::image type="content" source="media/centers-medicare-services-tutorials/davinci-test-execution-results.png" alt-text="Résultats de l’exécution du test da Vinci.":::

## <a name="next-steps"></a>Étapes suivantes

dans ce didacticiel, nous avons passé en revue la façon de transmettre les données du payeur de vinci Da Exchange de la formule de médicaments aux états-unis dans Touchstone. Ensuite, vous pouvez apprendre à tester le Guide d’implémentation de da Vinci PDex dans Touchstone.

>[!div class="nextstepaction"]
>[Da Vinci PDex](davinci-pdex-tutorial.md)