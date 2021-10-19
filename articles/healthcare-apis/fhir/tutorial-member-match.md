---
title: Didacticiel-opération de $member de correspondance
description: présente l’opération de correspondance de $member définie dans le cadre de l’enregistrement d’intégrité Da Vinci Exchange (HRex).
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 08/06/2021
ms.openlocfilehash: 2771458c6c9f23c9e87691e1a74d3e29986ad482
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778262"
---
# <a name="member-match-operation-in-fhir-service"></a>opération de correspondance de $member dans le service FHIR

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

[$member-match](http://hl7.org/fhir/us/davinci-hrex/2020Sep/OperationDefinition-member-match.html) est une opération qui est définie dans le cadre de l’enregistrement d’intégrité Da Vinci Exchange (HRex). Dans ce guide, nous allons vous guider tout au long de ce $member et comment l’utiliser.

## <a name="overview-of-member-match"></a>Vue d’ensemble de la correspondance $member

L’opération de correspondance de $member a été créée pour faciliter l’échange de données du payeur à payer, en permettant à un nouveau payeur d’obtenir un identificateur unique pour un patient du payeur précédent du patient. L’opération $member-match requiert la transmission de trois éléments d’information dans le corps de la demande :

* Données démographiques sur les patients
* Les anciennes informations de couverture
* Nouvelles informations de couverture (non requises en fonction de notre implémentation)

Une fois les données transmises, le service FHIR dans les API de santé Azure (par le biais du service FHIR) confirme qu’il peut trouver un patient qui correspond exactement aux données démographiques transmises avec les anciennes informations de couverture passées. Si un résultat est trouvé, la réponse est un bundle contenant les données d’origine du patient plus un nouvel identificateur ajouté à partir de l’ancien payeur et les anciennes informations de couverture.

> [!NOTE]
> La spécification décrit comment transmettre et sauvegarder les nouvelles informations de couverture. Nous avons décidé d’omettre ces données pour réduire les résultats.

## <a name="example-of-member-match"></a>Exemple de correspondance de $member

Pour utiliser $member-match, utilisez l’appel suivant :

`POST {{fhirurl}}/Patient/$member-match`

Vous devez inclure une ressource de paramètres dans le corps qui inclut le patient, l’ancienne couverture et la nouvelle couverture. Pour voir une représentation JSON, consultez [$member exemple de demande de correspondance](http://hl7.org/fhir/us/davinci-hrex/2020Sep/Parameters-member-match-in.json.html).

Si une correspondance unique est trouvée, vous recevrez une réponse 200 avec un autre identificateur ajouté :

:::image type="content" source="media/centers-medicare-services-tutorials/two-hundred-response.png" alt-text="200 100 code de réponse.":::

Si le $member-match ne peut pas trouver une correspondance unique, vous recevrez une réponse 422 avec un code d’erreur.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide, vous avez appris l’opération de correspondance de $member. ensuite, vous pouvez en savoir plus sur le test des données du payeur de vinci Da Exchange gi dans Touchstone, ce qui nécessite l’opération de correspondance $member.

>[!div class="nextstepaction"]
>[DaVinci PDex](davinci-pdex-tutorial.md)
