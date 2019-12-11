---
title: Intégrer et gérer des opérations de sécurité et Microsoft Graph Security
description: Améliorer la protection de votre application contre les menaces et la façon dont elle détecte les menaces et y répond avec Microsoft Graph Security et Azure Logic Apps
services: logic-apps
ms.suite: integration
author: preetikr
ms.author: preetikr
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 01/30/2019
tags: connectors
ms.openlocfilehash: 7e9cc2d8d38af7e5e6cf26ccc3659ee58ef17e59
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789050"
---
# <a name="improve-threat-protection-by-integrating-security-operations-with-microsoft-graph-security--azure-logic-apps"></a>Améliorer la protection contre les menaces en intégrant des opérations de sécurité avec Microsoft Graph Security et Azure Logic Apps

Avec [Azure Logic Apps](../logic-apps/logic-apps-overview.md) et le connecteur [Microsoft Graph Security](https://docs.microsoft.com/graph/security-concept-overview), vous pouvez améliorer la façon dont votre application détecte les menaces, y répond et vous protège contre celles-ci en créant des workflows automatisés pour intégrer des partenaires, des services et des produits de sécurité Microsoft. Par exemple, vous pouvez créer des [playbooks Azure Security Center](../security-center/security-center-playbooks.md) qui supervisent et gèrent des entités Microsoft Graph Security, telles que des alertes. Voici quelques scénarios pris en charge par le connecteur Microsoft Graph Security :

* Obtenir des alertes basées sur des requêtes ou par ID d’alerte. Par exemple, vous pouvez obtenir une liste qui inclut des alertes d’un niveau de gravité élevé.
* Mettre à jour des alertes. Par exemple, vous pouvez mettre à jour des affectations d’alerte, ajouter des commentaires aux alertes ou étiqueter des alertes.
* Superviser quand les alertes sont générées ou modifiées en créant des [abonnements d’alerte (webhooks)](https://docs.microsoft.com/graph/api/resources/webhooks).
* Gérer vos abonnements d’alerte. Par exemple, vous pouvez obtenir des abonnements actifs, étendre le délai d’expiration pour un abonnement ou supprimer des abonnements.

Le workflow de votre application logique peut utiliser des actions visant à obtenir des réponses du connecteur Microsoft Graph Security et à rendre cette sortie disponible pour d’autres actions de votre workflow. Il est également possible que d’autres actions de votre workflow utilisent la sortie des actions du connecteur Microsoft Graph Security. Par exemple, si vous obtenez des alertes d’un niveau de gravité élevé par le biais du connecteur Microsoft Graph Security, vous pouvez envoyer ces alertes dans un e-mail à l’aide du connecteur Outlook. 

Pour en savoir plus sur Microsoft Graph Security, consultez la [vue d’ensemble de l’API Microsoft Graph Security](https://aka.ms/graphsecuritydocs). Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md). Si vous recherchez Microsoft Flow ou PowerApps, consultez [Qu’est-ce que Flow ?](https://flow.microsoft.com/) ou [Qu’est-ce que PowerApps ?](https://powerapps.microsoft.com/)

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/). 

* Pour utiliser le connecteur Microsoft Graph Security, vous devez avoir *donné explicitement* votre consentement à l’administrateur du locataire Azure Active Directory (AD), ce qui fait partie des [exigences d’authentification Microsoft Graph Security](https://aka.ms/graphsecurityauth). Ce consentement nécessite le nom et l’ID d’application du connecteur Microsoft Graph Security, que vous pouvez également trouver dans le [portail Azure](https://portal.azure.com) :

   | Propriété | Valeur |
   |----------|-------|
   | **Nom d’application** | `MicrosoftGraphSecurityConnector` |
   | **ID d’application** | `c4829704-0edc-4c3d-a347-7c4a67586f3c` |
   |||

   Afin de donner le consentement pour le connecteur, l’administrateur de locataire Azure AD peut suivre l’une de ces procédures :

   * [Accorder à l’administrateur de locataire son consentement pour les applications Azure AD](../active-directory/develop/v2-permissions-and-consent.md).

   * Au cours de la première exécution de l’application logique, l’application peut demander le consentement de l’administrateur de locataire Azure AD via l’[expérience du consentement d’application](../active-directory/develop/application-consent-experience.md).
   
* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Application logique où vous souhaitez accéder aux entités Microsoft Graph Security, telles que des alertes. Pour l’instant, ce connecteur ne comporte aucun déclencheur. Par conséquent, pour utiliser une action Microsoft Graph Security, démarrez votre application logique avec un déclencheur, par exemple le déclencheur **Périodicité**.

## <a name="connect-to-microsoft-graph-security"></a>Se connecter à Microsoft Graph Security 

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Connectez-vous au [portail Azure](https://portal.azure.com/) et ouvrez votre application logique dans le concepteur d’application logique, si elle n’est pas déjà ouverte.

1. Pour les applications logiques vides, ajoutez le déclencheur et toute autre action souhaitée avant d’ajouter une action Microsoft Graph Security.

   -ou-

   Pour les applications logiques existantes, sous la dernière étape où vous souhaitez ajouter une action Microsoft Graph Security, choisissez **Nouvelle étape**.

   -ou-

   Pour ajouter une action entre des étapes, placez votre pointeur au-dessus de la flèche qui les sépare. 
   Choisissez le signe plus (+) qui s’affiche, puis sélectionnez **Ajouter une action**.

1. Dans la zone de recherche, entrez « microsoft graph security » comme filtre. Dans la liste des actions, sélectionnez l’action souhaitée.

1. Connectez-vous avec vos informations d’identification Microsoft Graph Security.

1. Fournissez les informations nécessaires pour votre action sélectionné et continuez à générer le flux de travail de votre application logique.

## <a name="add-actions"></a>Ajouter des actions

Voici des détails plus spécifiques sur l’utilisation des différentes actions disponibles avec le connecteur Microsoft Graph Security.

### <a name="manage-alerts"></a>Gérer les alertes

Pour filtrer, trier ou obtenir les résultats les plus récents, fournissez *uniquement* les [paramètres de requête ODATA pris en charge par Microsoft Graph](https://docs.microsoft.com/graph/query-parameters). *Ne spécifiez pas* l’URL de base complète ni l’action HTTP, par exemple `https://graph.microsoft.com/v1.0/security/alerts`, ni l’opération `GET` ou `PATCH`. Voici un exemple spécifique qui montre les paramètres pour une action **Obtenir des alertes** quand vous voulez une liste avec des alertes d’un niveau de gravité élevé :

`Filter alerts value as Severity eq 'high'`

Pour plus d’informations sur les requêtes que vous pouvez utiliser avec ce connecteur, consultez la [documentation de référence des alertes Microsoft Graph Security](https://docs.microsoft.com/graph/api/alert-list). Pour générer des expériences améliorées avec ce connecteur, découvrez plus en détail les [alertes des propriétés de schéma](https://docs.microsoft.com/graph/api/resources/alert) prises en charge par le connecteur.

| Action | Description |
|--------|-------------|
| **Obtenir des alertes** | Obtenez des alertes filtrées basées sur une ou plusieurs [propriétés d’alerte](https://docs.microsoft.com/graph/api/resources/alert), par exemple : <p>`Provider eq 'Azure Security Center' or 'Palo Alto Networks'` | 
| **Obtenir une alerte par ID** | Obtenez une alerte spécifique basée sur l’ID d’alerte. | 
| **Mettre à jour une alerte** | Mettez à jour une alerte spécifique basée sur l’ID d’alerte. <p>Pour vous assurer que vous passez les propriétés obligatoires et modifiables dans votre demande, consultez les [propriétés modifiables pour les alertes](https://docs.microsoft.com/graph/api/alert-update). Par exemple, pour affecter une alerte à un analyste de la sécurité afin qu’il puisse enquêter, vous pouvez mettre à jour la propriété **Affecté à** de l’alerte. |
|||

### <a name="manage-alert-subscriptions"></a>Gérer les abonnements d’alerte

Microsoft Graph prend en charge les [*abonnements*](https://docs.microsoft.com/graph/api/resources/subscription), ou [*webhooks*](https://docs.microsoft.com/graph/api/resources/webhooks). Pour obtenir, mettre à jour ou supprimer des abonnements, fournissez les [paramètres de requête ODATA pris en charge par Microsoft Graph](https://docs.microsoft.com/graph/query-parameters) à la construction d’entité Microsoft Graph et ajoutez `security/alerts`, puis la requête ODATA. 
*N’incluez pas* l’URL de base, par exemple `https://graph.microsoft.com/v1.0`. Au lieu de cela, utilisez le format dans cet exemple :

`security/alerts?$filter=status eq 'New'`

| Action | Description |
|--------|-------------|
| **Création d’abonnements** | [Créez un abonnement](https://docs.microsoft.com/graph/api/subscription-post-subscriptions) qui vous informe de toutes les modifications. Vous pouvez filtrer cet abonnement selon les types d’alertes spécifiques souhaités. Par exemple, vous pouvez créer un abonnement qui vous informe des alertes d’un niveau de gravité élevé. |
| **Obtenir des abonnements actifs** | [Obtenez les abonnements non expirés](https://docs.microsoft.com/graph/api/subscription-list). | 
| **Mettre à jour un abonnement** | [Mettez à jour un abonnement](https://docs.microsoft.com/graph/api/subscription-update) en indiquant l’ID d’abonnement. Par exemple, pour étendre votre abonnement, vous pouvez mettre à jour la propriété `expirationDateTime` de l’abonnement. | 
| **Supprimer un abonnement** | [Supprimez un abonnement](https://docs.microsoft.com/graph/api/subscription-delete) en indiquant l’ID d’abonnement. | 
||| 

## <a name="connector-reference"></a>Référence de connecteur

Pour obtenir des détails techniques sur les déclencheurs, les actions et les limites, qui sont décrits par la description OpenAPI du connecteur (anciennement Swagger), consultez la [page de référence](https://aka.ms/graphsecurityconnectorreference) du connecteur.

## <a name="get-support"></a>Obtenir de l’aide

Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le [site de commentaires des utilisateurs Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)
