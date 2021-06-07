---
title: Didacticiel-DaVinci PDex-API Azure pour FHIR
description: Ce didacticiel vous guide dans la configuration de l’API Azure pour FHIR afin de passer des tests pour le Guide d’implémentation de l’échange de données d’échange de données da Vinci.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 06/02/2021
ms.openlocfilehash: 0e6ee8ba2288ae61eb1fe0d5e6b76ebc63a64897
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2021
ms.locfileid: "111562709"
---
# <a name="davinci-pdex"></a>DaVinci PDex

Dans ce didacticiel, nous allons passer en revue la configuration de l’API Azure pour FHIR afin de transmettre les tests [Touchstone](https://touchstone.aegis.net/touchstone/) pour le Guide d’implémentation d’échange de données (PDex GI) du [payeur de Vinci](http://hl7.org/fhir/us/davinci-pdex/toc.html) .

> [!NOTE]
> Pour tous ces tests, nous allons les exécuter sur les tests JSON. L’API Azure pour FHIR prend en charge JSON et XML, mais elle n’a pas de points de terminaison distincts pour accéder à JSON ou XML. Pour cette raison, tous les tests XML échouent. Si vous souhaitez afficher l’instruction de fonctionnalité dans XML, vous devez simplement passer le \_ paramètre de format : \` obtenir {fhirurl}/Metadata ? \_ format = XML\`

## <a name="touchstone-capability-statement"></a>Instruction de fonctionnalité Touchstone

Le premier ensemble de tests sur lequel nous allons nous concentrer consiste à tester l’API Azure pour FHIR à l’aide de l’instruction de fonctionnalité PDex GI. Ces tests ont trois processus de validation :

* Le premier test valide simplement l’instruction de la fonctionnalité de base par rapport aux exigences de la GI et passera sans aucune mise à jour.

* Le deuxième test valide tous les profils qui ont été ajoutés pour le Centre des États-Unis. Ce test passera sans mises à jour, mais inclura un ensemble d’avertissements. Pour supprimer ces avertissements, vous devez [charger les profils de base US](validation-against-profiles.md). Nous avons créé un [exemple de fichier http](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PayerDataExchange/USCore.http) qui vous guide dans la création de tous les profils. Vous pouvez également obtenir les [profils](http://hl7.org/fhir/us/core/STU3.1.1/profiles.html#profiles) directement à partir du site HL7, qui disposera des versions les plus récentes.

* Le troisième test vérifie que l’opération patient-Everything est prise en charge. Pour le moment, ce test échouera. L’opération sera disponible en mi-juin 2021 dans l’API Azure pour FHIR et est disponible maintenant dans le serveur FHIR Open source sur Cosmos DB. Toutefois, il ne figure pas dans l’instruction de fonctionnalité. ce test échouera jusqu’à ce que nous puissions publier un correctif sur le bogue [1989](https://github.com/microsoft/fhir-server/issues/1989). 

 
:::image type="content" source="media/cms-tutorials/davinci-pdex-test-script-failed.png" alt-text="Échec de l’exécution de DaVinci PDex.":::

## <a name="touchstone-member-match-test"></a>Touchstone $member-test de correspondance

Le [deuxième test](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PayerExchange/01-Member-Match&activeOnly=false&contentEntry=TEST_SCRIPTS) de la section échange de données payeurs teste l’existence de l' [opération de correspondance de $Member](http://hl7.org/fhir/us/davinci-hrex/2020Sep/OperationDefinition-member-match.html). Pour plus d’informations sur l’opération de correspondance de $member, consultez la [vue d’ensemble de l’opération $Member-match](tutorial-member-match.md).

Dans ce test, vous devrez charger des exemples de données pour que le test réussisse. Nous [disposons d’un fichier Rest](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PayerDataExchange/membermatch.http) avec le patient et la couverture dont vous aurez besoin pour le test. Une fois ces données chargées, vous serez en mesure de réussir ce test. Si les données ne sont pas chargées, vous recevrez une réponse 422 en raison de la non-correspondance exacte.

:::image type="content" source="media/cms-tutorials/davinci-pdex-test-script-passed.png" alt-text="Le script de test DaVinci PDex a réussi.":::

## <a name="touchstone-patient-by-reference"></a>Touchstone patient par référence

Les tests suivants que nous allons examiner sont le [patient par des tests de référence](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PayerExchange/02-PatientByReference&activeOnly=false&contentEntry=TEST_SCRIPTS) . Cet ensemble de tests vous permet de vérifier que vous pouvez trouver un patient en fonction de différents critères de recherche. La meilleure façon de tester le patient par référence consiste à tester vos propres données, mais nous avons chargé un exemple de fichier de [ressources](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PayerDataExchange/PDex_Sample_Data.http) que vous pouvez charger pour utiliser également.

:::image type="content" source="media/cms-tutorials/davinci-pdex-test-execution-passed.png" alt-text="Exécution de DaVinci PDex réussie.":::

## <a name="touchstone-patienteverything-test"></a>Test TouchStone patient/$everything

Le test final que nous allons examiner est le test de tous les patients. Pour ce test, vous devez charger un patient, puis utiliser l’ID de ce patient pour vérifier que vous pouvez utiliser l’opération de $everything pour extraire toutes les données relatives au patient.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, nous avons parcouru la manière de passer les tests d’échange du payeur dans Touchstone. Ensuite, vous pouvez en savoir plus sur toutes les fonctionnalités de l’API Azure pour FHIR.

>[!div class="nextstepaction"]
>[Fonctionnalités prises en charge](fhir-features-supported.md)  