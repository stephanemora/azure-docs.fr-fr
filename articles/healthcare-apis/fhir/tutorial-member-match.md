---
title: Didacticiel-opération $member-match-API Azure pour FHIR
description: Ce didacticiel présente l’opération de correspondance de $member qui est définie dans le cadre de l’échange de l’enregistrement d’intégrité da Vinci (HRex).
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 06/01/2021
ms.openlocfilehash: a6d24d0ebf91e2ae6992f6b101b8be0d4a0d4201
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2021
ms.locfileid: "111562666"
---
# <a name="member-match-operation"></a>opération de correspondance de $member

[$member-match](http://hl7.org/fhir/us/davinci-hrex/2020Sep/OperationDefinition-member-match.html) est une opération définie dans le cadre de l’échange de l’enregistrement d’intégrité da Vinci (HRex). Dans ce guide, nous allons vous guider tout au long de ce $member et comment l’utiliser.

## <a name="overview-of-member-match"></a>Vue d’ensemble de la correspondance $member

L’opération de correspondance de $member a été créée pour faciliter l’échange de données du payeur à payer, en permettant à un nouveau payeur d’obtenir un identificateur unique pour un patient du payeur précédent du patient. L’opération $member-match requiert la transmission de trois éléments d’information dans le corps de la demande :

* Données démographiques sur les patients

* Les anciennes informations de couverture

* Nouvelles informations de couverture (non requises en fonction de notre implémentation)

Une fois les données transmises, l’API Azure pour FHIR valide qu’il peut trouver un patient qui correspond exactement aux données démographiques transmises avec les anciennes informations de couverture passées. Si un résultat est trouvé, la réponse est un bundle contenant les données d’origine du patient plus un nouvel identificateur ajouté à partir de l’ancien payeur et les anciennes informations de couverture.

> [!NOTE]
> La spécification décrit comment transmettre et sauvegarder les nouvelles informations de couverture. Nous avons décidé d’omettre ces données pour réduire les résultats.

## <a name="example-of-member-match"></a>Exemple de correspondance de $member

Pour utiliser $member-match, utilisez l’appel suivant :

`POST {{fhirurl}}/Patient/$member-match`

Vous devez inclure une ressource de paramètres dans le corps qui inclut le patient, l’ancienne couverture et la nouvelle couverture. Pour voir une représentation JSON, consultez [$member exemple de demande de correspondance](http://hl7.org/fhir/us/davinci-hrex/2020Sep/Parameters-member-match-in.json.html).

Si une correspondance unique est trouvée, vous recevrez une réponse 200 avec un autre identificateur ajouté :

:::image type="content" source="media/cms-tutorials/two-hundred-response.png" alt-text="200 100 code de réponse.":::

Si le $member-match ne peut pas trouver une correspondance unique, vous recevrez une réponse 422 avec un code d’erreur.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide, vous avez appris l’opération de correspondance de $member. Ensuite, vous pouvez en savoir plus sur le test de la mise à l’épreuve de l’échange de données de da Vinci dans Touchstone, ce qui nécessite l’opération de correspondance de $member.

>[!div class="nextstepaction"]
>[DaVinci PDex](davinci-pdex-tutorial.md)