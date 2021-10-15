---
title: Transformer des données XML dans des flux de travail d’intégration d’entreprise
description: Transformez des données XML à l’aide de mappages dans Azure Logic Apps avec Enterprise Integration Pack.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/15/2021
ms.openlocfilehash: 84d0e0b509505e6d56e39294b3e2819e4b03e024
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129350009"
---
# <a name="transform-xml-in-workflows-with-azure-logic-apps"></a>Transformer des données XML dans des flux de travail dans Azure Logic Apps

Dans des scénarios d’intégration d’entreprise interentreprises (B2B), vous devrez peut-être convertir des données XML entre des formats. Votre flux de travail d’application logique peut transformer du code XML à l’aide de l’action **Transformer XML** et d’une [*carte*](logic-apps-enterprise-integration-maps.md)prédéfinie. Par exemple, imaginons que vous receviez régulièrement des commandes ou des factures B2B de la part d’un client qui utilise le format de date YearMonthDay (AAAAMMJJ). Votre organisation, quant à elle, utilise le format de date MonthDayYear (MMJJAAAA). Vous pouvez créer et utiliser un mappage qui transforme le format de date YearMonthDay au format MonthDayYear avant d’enregistrer les détails de la commande ou de la facture dans votre base de données d’activité clients.

Si vous débutez avec les applications logiques, voir [Qu’est-ce qu’Azure Logic Apps](logic-apps-overview.md) ? Pour plus d’informations sur l’intégration d’entreprise B2B, consultez [Workflows d’intégration d’entreprise B2B avec Azure Logic Apps et Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Prérequis

* Un compte et un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [vous inscrire pour obtenir un compte Azure gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un workflow d’application logique qui démarre déjà avec un déclencheur afin que vous puissiez ajouter l’action **Transformer du XML** si nécessaire dans votre workflow.

* Une [ressource de compte d’intégration](logic-apps-enterprise-integration-create-integration-account.md) dans laquelle vous définissez et stockez les artefacts, comme les parties, les contrats, les certificats, etc., à utiliser dans vos flux de travail d’intégration d’entreprise et B2B. Cette ressource doit remplir les conditions suivantes :

  * Associé au même abonnement Azure que votre ressource d’application logique.

  * Existante dans le même emplacement ou la même région Azure que la ressource d’application logique dans laquelle vous envisagez d’utiliser l’action **Transformer au format XML**.

  * Si vous utilisez le type de ressource [Application logique (consommation) **, vous devez disposer d’un** compte d’intégration](logic-apps-overview.md#resource-type-and-host-environment-differences) qui répond aux exigences suivantes :

    * [Mappage](logic-apps-enterprise-integration-maps.md) à utiliser pour transformer le contenu XML.

    * Un [lien vers votre ressource d'application logique](logic-apps-enterprise-integration-create-integration-account.md#link-account).

  * Si vous utilisez le type de ressource [**Logic App (Standard)** ,](logic-apps-overview.md#resource-type-and-host-environment-differences) vous ne stockez pas de cartes dans votre compte d'intégration. Au lieu de cela, vous pouvez [ajouter directement des cartes à votre ressource d'application logique](logic-apps-enterprise-integration-maps.md) en utilisant soit le portail Azure, soit Visual Studio Code. Seul XSLT 1.0 est actuellement pris en charge. Vous pouvez ensuite utiliser ces mappages sur plusieurs workflows au sein de la *même ressource d’application logique*.

    Toutefois, vous avez toujours besoin de ce compte pour stocker les artefacts, tels que les partenaires, les contrats et les certificats, en plus d’utiliser les opérations [AS2](logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md) ou [EDIFACT](logic-apps-enterprise-integration-edifact.md). Toutefois, vous n’avez pas besoin de lier votre ressource d’application logique à votre compte d’intégration, donc la fonctionnalité de liaison n’existe pas. Votre compte d’intégration doit toujours répondre à d’autres exigences, comme l’utilisation du même abonnement Azure et la présence dans le même emplacement que votre ressource d’application logique.

    > [!NOTE]
    > Actuellement, seul le type de ressource **Application logique (Consommation)** prend en charge les opérations [RosettaNet](logic-apps-enterprise-integration-rosettanet.md). Le type de ressource **Application logique (Standard)** n’inclut pas les opérations [RosettaNet](logic-apps-enterprise-integration-rosettanet.md).

## <a name="add-transform-xml-action"></a>Ajouter une action Transformer du XML

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre application logique et votre workflow en mode concepteur.

1. Si vous avez une application logique vide qui n’a pas de déclencheur, ajoutez un déclencheur de votre choix. Cet exemple utilise le déclencheur de demande. Sinon, passez à l’étape suivante.

   Pour ajouter le déclencheur de requête, dans la zone de recherche du concepteur, entrez `HTTP request`, puis sélectionnez le déclencheur de requête nommé **Lors de la réception d’une requête HTTP**.

1. Dans l’étape de votre workflow où vous souhaitez ajouter l’action **Transformer du XML**, choisissez l’une des étapes suivantes :

   Pour une application logique basée sur un plan de consommation ou ISE, choisissez une étape :

   * Pour ajouter l’action **Transformer du XML** à la fin de votre workflow, sélectionnez **Nouvelle étape**.

   * Pour ajouter l’action **Transformer du XML** entre des étapes existantes, positionnez votre pointeur sur la flèche qui connecte ces étapes de sorte que le signe plus ( **+** ) apparaisse. Sélectionnez ce signe plus, puis sélectionnez **Ajouter une action**.

   Pour une application logique basée sur un plan standard, choisissez une étape :

   * Pour ajouter l’action **Transformer du XML** à la fin de votre workflow, sélectionnez le signe moins ( **+** ), puis **Ajouter une action**.

   * Pour ajouter l’action **Transformer du XML** entre des étapes existantes, sélectionnez le signe plus ( **+** ) entre celles-ci, puis sélectionnez **Ajouter une action**.

1. Sous **Choisir une opération**, sélectionnez **Intégré**. Dans la zone de recherche, entrez `transform xml`. Dans la liste des actions, sélectionnez **Transformer du XML**.

1. Pour spécifier le contenu XML pour la transformation, vous pouvez utiliser toutes les données XML que vous recevez dans la requête HTTP. Cliquez dans la zone **Contenu** pour afficher la liste du contenu dynamique.

   La liste de contenu dynamique affiche des jetons de propriété qui représentent les sorties des étapes précédentes du workflow. Si la liste n’affiche pas de propriété attendue, vérifiez le titre du déclencheur ou de l’action dans la liste, et si vous pouvez sélectionner **Afficher plus**.

   Pour une application logique basée sur un plan de consommation ou ISE, le concepteur ressemble à l’exemple suivant :

   ![Capture d’écran montrant un concepteur à plusieurs locataires avec le curseur dans la zone « Contenu » et une liste de contenu dynamique ouverte.](./media/logic-apps-enterprise-integration-transform/open-dynamic-content-list-multi-tenant.png)

   Pour une application logique basée sur un plan standard, le concepteur ressemble à l’exemple suivant :

   ![Capture d’écran montrant un concepteur à un seul locataire avec le curseur dans la zone « Contenu » et une liste de contenu dynamique ouverte.](./media/logic-apps-enterprise-integration-transform/open-dynamic-content-list-single-tenant.png)

1. Dans la liste de contenu dynamique, sélectionnez le jeton de propriété pour le contenu que vous souhaitez valider.

   Cet exemple sélectionne le jeton **Corps** du déclencheur.

   > [!NOTE]
   > Assurez-vous que le contenu que vous sélectionnez est bien du XML. Si le contenu n’est pas du XML ou s’il est codé en base 64, vous devez spécifier une expression qui traite le contenu. Par exemple, vous pouvez utiliser des [fonctions d’expression](workflow-definition-language-functions-reference.md), telles que `base64ToBinary()`, pour décoder le contenu ou `xml()` pour traiter le contenu comme du XML.

1. Pour spécifier le mappage à utiliser pour la transformation, ouvrez la liste **Mappage**, puis sélectionnez le mappage que vous avez ajouté précédemment.

1. Lorsque vous avez terminé, veillez à enregistrer votre workflow d’application logique.

   Vous avez maintenant terminé la configuration de votre action **Transformer du XML**. Dans une application réelle, vous souhaiterez peut-être stocker les données transformées dans une application métier (LOB) telle que Salesforce. Pour envoyer la sortie transformée à Salesforce, ajoutez une action Salesforce.

1. Pour tester votre action de transformation, déclenchez et exécutez votre workflow. Par exemple, pour le déclencheur de requête, envoyez une requête à l’URL de point de terminaison du déclencheur.

   L'action **Transformer XML** s'exécute après le déclenchement de votre workflow et lorsque le contenu XML est disponible pour la transformation.

## <a name="advanced-capabilities"></a>Fonctionnalités avancées

### <a name="reference-assembly-or-custom-code-from-maps"></a>Assembly de référence ou code personnalisé à partir de mappages

Dans les workflows **Application logique (consommation)** , l’action **Transformer du XML** prend en charge les mappages faisant référence à un assembly externe. Pour plus d’informations, consultez [Ajouter des mappages XSLT pour les workflows dans Azure Logic Apps](logic-apps-enterprise-integration-maps.md#add-assembly).

### <a name="byte-order-mark"></a>Marque d'ordre d'octet

Par défaut, la réponse de la transformation commence par la marque d’ordre d’octet. Vous n’avez accès à cette fonctionnalité que dans l’éditeur en mode Code. Pour désactiver cette fonctionnalité, définissez la propriété `transformOptions` sur `disableByteOrderMark` :

```json
"Transform_XML": {
    "inputs": {
        "content": "@{triggerBody()}",
        "integrationAccount": {
            "map": {
                "name": "TestMap"
            }
        },
        "transformOptions": "disableByteOrderMark"
    },
    "runAfter": {},
    "type": "Xslt"
}
```

## <a name="next-steps"></a>Étapes suivantes

* [Ajouter des mappages XSLT pour la transformation XML dans Azure Logic Apps](logic-apps-enterprise-integration-maps.md)
* [Valider des données XML pour des workflows dans Azure Logic Apps](logic-apps-enterprise-integration-xml-validation.md)
