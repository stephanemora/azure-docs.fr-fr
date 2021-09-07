---
title: Valider des messages XML pour l’intégration d’entreprise B2B
description: Validez des documents XML à l’aide de schémas dans Azure Logic Apps avec Enterprise Integration Pack.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 08/25/2021
ms.openlocfilehash: 87650a1ab950f8e88fe08a1c4555c98652776730
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123099281"
---
# <a name="validate-xml-for-workflows-in-azure-logic-apps"></a>Valider du code XML pour des flux de travail dans Azure Logic Apps

Souvent, dans des scénarios B2B d’intégration d’entreprise, les partenaires commerciaux parties à un contrat doivent s’assurer que les messages qu’ils échangent sont valides avant de commencer tout traitement de données. Vous pouvez valider des documents par rapport à un schéma prédéfini à l’aide de l’action **Validation XML** dans Azure Logic Apps.

## <a name="prerequisites"></a>Prérequis

* Un compte et un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [vous inscrire pour obtenir un compte Azure gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un flux de travail d’application logique, vide ou existant, dans lequel vous souhaitez utiliser l’action **Validation XML**.

  Si vous avez un flux de travail vide, utilisez un déclencheur de votre choix. Cet exemple utilise le déclencheur de demande.

  Si vous débutez avec les applications logiques, consultez la documentation suivante :

  * [Qu’est-ce qu’Azure Logic Apps](logic-apps-overview.md)

  * [Démarrage rapide : Créer votre premier workflow d’application logique](quickstart-create-first-logic-app-workflow.md)

  * [Créer des flux de travail d’application logique à un seul locataire](create-single-tenant-workflows-azure-portal.md)

  * [Modèles de mesure de l’utilisation, de facturation et de tarification pour Azure Logic Apps](logic-apps-pricing.md)

* Si vous utilisez le type de ressource **Application logique (consommation)** , vous devez disposer d’un [compte d’intégration](logic-apps-enterprise-integration-create-integration-account.md) qui répond aux exigences suivantes :

  * Associé au même abonnement Azure que votre ressource d’application logique.

  * Existant dans le même emplacement ou la même région Azure que la ressource d’application logique dans laquelle vous envisagez d’utiliser l’action **Validation XML**.

  * [Lié](logic-apps-enterprise-integration-create-integration-account.md#link-account) à la ressource d’application logique.

  * Contenant le [schéma](logic-apps-enterprise-integration-schemas.md) à utiliser pour valider le contenu XML.

  Si vous utilisez le type de ressource **Application logique (standard)** , vous n’avez pas besoin de lier un compte d’intégration. Toutefois, vous devez toujours ajouter le [schéma](logic-apps-enterprise-integration-schemas.md) à utiliser pour valider le contenu XML dans votre ressource d’application logique. Vous pouvez accomplir cette tâche dans le menu de votre ressource d’application logique, sous **Paramètres**, dans la section **Schémas**.

## <a name="add-xml-validation-action"></a>Ajouter l’action de validation XML

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre application logique et votre flux de travail en mode concepteur.

1. Si vous avez une application logique vide qui n’a pas de déclencheur, ajoutez un déclencheur de votre choix. Cet exemple utilise le déclencheur de demande. Sinon, passez à l’étape suivante.

   Pour ajouter le déclencheur de demande, dans la zone de recherche du concepteur, entrez `HTTP request`, puis sélectionnez le déclencheur de demande nommé **When an HTTP request is received** (Lors de la réception d’une requête HTTP).

1. Dans la phase de votre flux de travail où vous souhaitez ajouter l’action **Validation XML**, choisissez l’une des étapes suivantes :

   Pour une application logique basée sur un plan de consommation ou ISE, choisissez une étape :

   * Pour ajouter l’action **Validation XML** à la fin de votre flux de travail, sélectionnez **Nouvelle étape**.

   * Pour ajouter l’action **Validation XML** entre des étapes existantes, positionnez votre pointeur sur la flèche qui connecte ces étapes de façon à afficher le signe plus ( **+** ). Sélectionnez ce signe plus, puis sélectionnez **Ajouter une action**.

   Pour une application logique basée sur un plan standard, choisissez une étape :

   * Pour ajouter l’action **Validation XML** à la fin de votre flux de travail, sélectionnez le signe moins ( **+** ), puis **Ajouter une action**.

   * Pour ajouter l’action **Validation XML** entre des étapes existantes, sélectionnez le signe plus ( **+** ) entre celles-ci, puis sélectionnez **Ajouter une action**.

1. Sous **Choisir une opération**, sélectionnez **Intégré**. Dans la zone de recherche, entrez `xml validation`. Dans la liste des actions, sélectionnez **Validation XML**.

1. Pour spécifier le contenu XML à valider, cliquez dans la zone **Contenu** afin d’afficher la liste de contenu dynamique.

   La liste de contenu dynamique affiche des jetons de propriété qui représentent les sorties des étapes précédentes du workflow. Si la liste n’affiche pas de propriété attendue, vérifiez le titre du déclencheur ou de l’action dans la liste, et si vous pouvez sélectionner **Afficher plus**.

   Pour une application logique basée sur un plan de consommation ou ISE, le concepteur ressemble à l’exemple suivant :

   ![Capture d’écran montrant un concepteur à plusieurs locataires avec le curseur dans la zone « Contenu » et une liste de contenu dynamique ouverte.](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list-multi-tenant.png)

   Pour une application logique basée sur un plan standard, le concepteur ressemble à l’exemple suivant :

   ![Capture d’écran montrant un concepteur à un seul locataire avec le curseur dans la zone « Contenu » et une liste de contenu dynamique ouverte.](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list-single-tenant.png)

1. Dans la liste de contenu dynamique, sélectionnez le jeton de propriété pour le contenu que vous souhaitez valider.

   Cet exemple sélectionne le jeton **Corps** du déclencheur.

1. Pour spécifier le schéma à utiliser pour la validation, ouvrez la liste **Nom du schéma**, puis sélectionnez le schéma que vous avez ajouté précédemment.

1. Quand vous avez terminé, veillez à enregistrer votre flux de travail d’application logique.

   Vous avez maintenant terminé la configuration de votre action **Validation XML**. Dans une application réelle, vous souhaiterez peut-être stocker les données validées dans une application métier telle que Salesforce. Pour envoyer la sortie validée à Salesforce, ajoutez une action Salesforce.

1. Pour tester votre action de validation, déclenchez et exécutez votre flux de travail. Par exemple, pour le déclencheur de demande, envoyez une demande à l’URL de point de terminaison du déclencheur.

## <a name="next-steps"></a>Étapes suivantes

* [Ajouter des schémas pour la validation de code XML dans Azure Logic Apps](logic-apps-enterprise-integration-schemas.md)
* [Transformer du code XML pour des flux de travail dans Azure Logic Apps](logic-apps-enterprise-integration-transform.md)