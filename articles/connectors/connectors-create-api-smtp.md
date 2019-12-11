---
title: Se connecter à SMTP à partir d’Azure Logic Apps
description: Automatiser des tâches et des flux de travail qui envoient des e-mails via votre compte SMTP (Simple Mail Transfer Protocol) à l’aide d’Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: fb501a158c839e6d4d71fc2af5ae50e48c248466
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789152"
---
# <a name="send-email-from-your-smtp-account-with-azure-logic-apps"></a>Envoyer un e-mail à partir de votre compte SMTP avec Azure Logic Apps

Avec Azure Logic Apps et le connecteur SMTP (Simple Mail Transfer Protocol), vous pouvez créer des tâches et des flux de travail automatisés qui envoient des e-mails à partir de votre compte SMTP. Vous pouvez également faire en sorte que des actions utilisent la sortie d’actions SMTP. Par exemple, lorsque votre SMTP envoie un e-mail, vous pouvez informer votre équipe dans Slack avec le connecteur Slack. Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/). 

* Vos informations d’identification utilisateur et compte SMTP

  Vos informations d’identification autorisent votre application logique à créer une connexion et à accéder à votre compte SMTP.

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L’application logique à partir de laquelle vous souhaitez accéder à votre compte SMTP. Pour utiliser une action SMTP, démarrez votre application logique avec un déclencheur, tel qu’un déclencheur Salesforce, si vous avez un compte Salesforce.

  Par exemple, vous pouvez démarrer votre application logique avec le déclencheur Salesforce **Lorsqu'un enregistrement est créé**. 
  Ce déclencheur entre en jeu chaque fois qu’un enregistrement, tel qu’un prospect, est créé dans Salesforce. 
  Vous pouvez ensuite suivre ce déclencheur avec l’action SMTP **Envoyer un e-mail**. Ainsi, quand le nouvel enregistrement est créé, votre application logique envoie un e-mail à partir de votre compte SMTP concernant le nouvel enregistrement.

## <a name="connect-to-smtp"></a>Se connecter à SMTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Connectez-vous au [portail Azure](https://portal.azure.com) et ouvrez votre application logique dans le concepteur d’application logique, si elle n’est pas déjà ouverte.

1. Sous la dernière étape où vous souhaitez ajouter une action SMTP, choisissez **Nouvelle étape**. 

   Pour ajouter une action entre des étapes, placez votre pointeur au-dessus de la flèche qui les sépare. 
   Cliquez sur le signe plus ( **+** ) qui s’affiche, puis sélectionnez **Ajouter une action**.

1. Dans la zone de recherche, entrez « smtp » comme filtre. Sous la liste des actions, sélectionnez l’action souhaitée.

1. Lorsque vous y êtes invité, fournissez ces informations de connexion :

   | Propriété | Obligatoire | Description |
   |----------|----------|-------------|
   | **Nom de connexion** | OUI | Un nom pour la connexion à votre serveur SMTP | 
   | **Adresse du serveur SMTP** | OUI | L’adresse de votre serveur SMTP | 
   | **Nom d’utilisateur** | OUI | Votre nom d'utilisateur pour votre compte SMTP | 
   | **Mot de passe** | OUI | Votre mot de passe pour votre compte SMTP | 
   | **Port du serveur SMTP** | Non | Un port spécifique que vous souhaitez utiliser sur votre serveur SMTP | 
   | **Activer le protocole SSL ?** | Non | Activer ou désactiver le chiffrement SSL. | 
   |||| 

1. Fournissez les informations nécessaires pour l’action sélectionnée. 

1. Enregistrez votre application logique ou poursuivez la génération du flux de travail de votre application logique.

## <a name="connector-reference"></a>Référence de connecteur

Pour obtenir des détails techniques sur les déclencheurs, les actions et les limites, qui sont décrits par la description OpenAPI du connecteur (anciennement Swagger), consultez la [page de référence](/connectors/smtpconnector/) du connecteur.

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le [site de commentaires des utilisateurs Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)