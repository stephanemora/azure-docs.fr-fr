---
title: Transformer des données XML pour l’intégration d’entreprise B2B
description: Transformez des données XML à l’aide de mappages dans Azure Logic Apps avec Enterprise Integration Pack.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 08/26/2021
ms.openlocfilehash: 30895da003122b760d6437b3cd14a270482f7a0a
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123105236"
---
# <a name="transform-xml-for-workflows-in-azure-logic-apps"></a>Transformer des données XML pour des workflows dans Azure Logic Apps

Dans des scénarios d’intégration d’entreprise interentreprises (B2B), vous devrez peut-être convertir des données XML entre des formats. Dans Azure Logic Apps, votre workflow d’application logique peut transformer des données XML à l’aide d’un mappage XLST (Extensible Stylesheet Language Transformation) et de l’action **Transformer du XML**. Un mappage est un document XML qui décrit comment convertir les données de XML dans un autre format. Ce document se compose d’un schéma XML source en entrée et d’un schéma XML cible en sortie.  Vous pouvez utiliser différentes fonctions intégrées pour aider à manipuler ou à contrôler les données, y compris les manipulations de chaînes, les affectations conditionnelles, les expressions arithmétiques, les formateurs d'heure et de date et même les constructions de bouclage.

Par exemple, imaginons que vous receviez régulièrement des commandes ou des factures B2B de la part d’un client qui utilise le format de date YearMonthDay (AAAAMMJJ). Votre organisation, quant à elle, utilise le format de date MonthDayYear (MMJJAAAA). Vous pouvez créer et utiliser un mappage qui transforme le format de date YearMonthDay au format MonthDayYear avant d’enregistrer les détails de la commande ou de la facture dans votre base de données d’activité clients.

Après avoir [créé un mappage](logic-apps-enterprise-integration-maps.md#create-maps) et vérifié que le mappage fonctionne, vous ajoutez ce mappage au compte d’intégration lié à votre application logique basée sur un plan de consommation mutualisée ou à l’application logique basée sur un plan ISE. Vous pouvez également ajouter ce mappage directement à votre application logique basée sur un plan standard à locataire unique. Pour plus d’informations, consultez [Ajouter des mappages XSLT pour la transformation XML dans Azure Logic Apps](logic-apps-enterprise-integration-maps.md). En supposant que votre workflow inclut l’action **Transformer du XML**, celle-ci s’exécute lorsque votre workflow est déclenché et lorsque du contenu XML est disponible pour la transformation.

Si vous débutez avec les applications logiques, consultez la documentation suivante :

* [En quoi consiste Azure Logic Apps - Type de ressource et environnements d’hôte ?](logic-apps-overview.md#resource-type-and-host-environment-differences)

* [Créer un workflow d’intégration avec Azure Logic Apps à locataire unique (Standard)](create-single-tenant-workflows-azure-portal.md)

* [Créer des workflows d’application logique à locataire unique](create-single-tenant-workflows-azure-portal.md)

* [Modèles de mesure de l’utilisation, de facturation et de tarification pour Azure Logic Apps](logic-apps-pricing.md)

## <a name="prerequisites"></a>Prérequis

* Un compte et un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [vous inscrire pour obtenir un compte Azure gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un workflow d’application logique qui démarre déjà avec un déclencheur afin que vous puissiez ajouter l’action **Transformer du XML** si nécessaire dans votre workflow.

* Si vous utilisez le type de ressource **Application logique (Standard)** , vous n’avez pas besoin de compte d’intégration. Au lieu de cela, vous pouvez ajouter des mappages directement à votre ressource d’application logique dans le portail Azure ou Visual Studio Code. Seul XSLT 1.0 est actuellement pris en charge. Vous pouvez ensuite utiliser ces mappages sur plusieurs workflows au sein de la *même ressource d’application logique*.

* Si vous utilisez le type de ressource **Application logique (Consommation)** , vous devez posséder une [ressource de compte d’intégration](logic-apps-enterprise-integration-create-integration-account.md) où vous pouvez stocker vos mappages et autres artefacts à utiliser dans les solutions d’intégration d’entreprise et interentreprises (B2B). Cette ressource doit remplir les conditions suivantes :

  * Associée au même abonnement Azure que votre ressource d’application logique.

  * Existante dans le même emplacement ou la même région Azure que votre ressource d’application logique dans laquelle vous envisagez d’utiliser l’action **Transformer du XML**.

  * Est [liée](logic-apps-enterprise-integration-create-integration-account.md#link-account) à votre ressource d’application logique dans laquelle vous souhaitez utiliser l’action **Transformer du XML**.

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