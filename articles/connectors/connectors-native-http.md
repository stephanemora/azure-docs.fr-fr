---
title: Appeler des points de terminaison de service à l’aide de HTTP ou HTTPS
description: Envoyer des requêtes HTTP ou HTTPS sortantes aux points de terminaison de service à partir d’Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 06/09/2020
tags: connectors
ms.openlocfilehash: 8c7a0ddb80ba28548fc1821cc2063e500af0fa66
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87286629"
---
# <a name="call-service-endpoints-over-http-or-https-from-azure-logic-apps"></a>Appeler des points de terminaison HTTP ou HTTPS à partir d'Azure Logic Apps

Avec [Azure Logic Apps](../logic-apps/logic-apps-overview.md) et le déclencheur ou l’action HTTP intégrés, vous pouvez créer des tâches et des workflows automatisés qui envoient des requêtes à des points de terminaison de service via HTTP ou HTTPS. Par exemple, vous pouvez surveiller le point de terminaison de service pour votre site web en vérifiant ce point de terminaison selon une planification spécifique. Lorsque l'événement spécifié se produit au niveau de ce point de terminaison, comme une panne de votre site web, l’événement déclenche le flux de travail de votre application logique et exécute les actions dans ce workflow. Pour recevoir et répondre aux appels HTTP ou HTTPS entrants, utilisez le [déclencheur de requête ou l’action Réponse](../connectors/connectors-native-reqres.md) intégrés.

* Vous pouvez *ajouter le déclencheur HTTP* comme première étape de votre workflow pour vérifier ou [interroger](#http-trigger) un point de terminaison selon une planification récurrente. Chaque fois que le déclencheur vérifie le point de terminaison, il appelle ou envoie une *requête* au point de terminaison. La réponse du point de terminaison détermine si le flux de travail de votre application logique s’exécute. Le déclencheur transmet le contenu de la réponse du point de terminaison aux actions de votre application logique.

* Pour appeler un point de terminaison à partir de n’importe quel autre emplacement de votre workflow, [ajoutez l’action HTTP](#http-action). La réponse du point de terminaison détermine la façon dont s’exécutent les actions restantes de votre flux de travail.

Cet article explique comment ajouter un déclencheur ou une action HTTP au workflow de votre application logique.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/).

* L’URL du point de terminaison cible que vous souhaitez appeler

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md). Si vous débutez avec les applications logiques, voir [Qu’est-ce qu’Azure Logic Apps](../logic-apps/logic-apps-overview.md) ?

* L’application logique à partir de laquelle vous souhaitez appeler le point de terminaison cible. Pour démarrer avec un déclencheur HTTP, [créez une application logique vide](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pour utiliser l’action HTTP, démarrez votre application logique avec le déclencheur de votre choix. Cet exemple utilise le déclencheur HTTP en tant que première étape.

<a name="http-trigger"></a>

## <a name="add-an-http-trigger"></a>Ajouter un déclencheur HTTP

Ce déclencheur intégré effectue un appel HTTP vers l’URL spécifiée d’un point de terminaison et renvoie une réponse.

1. Connectez-vous au [portail Azure](https://portal.azure.com). Ouvrez votre application logique vide dans le Concepteur d’application logique.

1. Dans la zone de recherche du concepteur, sélectionnez **Intégré**. Dans la zone de recherche, entrez `http` en guise de filtre. Dans la liste **Déclencheurs**, sélectionnez le déclencheur **HTTP**.

   ![Sélectionner le déclencheur HTTP](./media/connectors-native-http/select-http-trigger.png)

   Cet exemple renomme le déclencheur « Déclencheur HTTP » afin que l’étape ait un nom plus descriptif. En outre, il ajoute ultérieurement une action HTTP, et les deux noms doivent être uniques.

1. Indiquez les valeurs des [paramètres du déclencheur HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger) à inclure dans l’appel au point de terminaison cible. Configurez la fréquence à laquelle le déclencheur doit vérifier le point de terminaison cible.

   ![Entrer les paramètres du déclencheur HTTP](./media/connectors-native-http/http-trigger-parameters.png)

   Si vous sélectionnez un type d’authentification autre que **Aucun**, les paramètres d’authentification varient en fonction de votre sélection. Pour plus d’informations sur les types d’authentification disponibles pour HTTP, consultez ces rubriques :

   * [Ajouter l’authentification aux appels sortants](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Authentifier l’accès aux ressources avec des identités managées](../logic-apps/create-managed-service-identity.md)

1. Pour ajouter d’autres paramètres disponibles, ouvrez la liste **Ajouter un nouveau paramètre**, puis sélectionnez les paramètres de votre choix.

1. Continuez à générer le flux de travail de votre application logique avec des actions qui s’exécutent quand le déclencheur se déclenche.

1. Lorsque vous avez terminé, pensez à enregistrer votre application logique. Dans la barre d’outils du Concepteur, sélectionnez **Enregistrer**.

<a name="http-action"></a>

## <a name="add-an-http-action"></a>Ajouter une action HTTP

Cette action intégrée effectue un appel HTTP à l’URL spécifiée d’un point de terminaison et renvoie une réponse.

1. Connectez-vous au [portail Azure](https://portal.azure.com). Ouvrez votre application logique dans le Concepteur d’applications logiques.

   Cet exemple utilise le déclencheur HTTP en tant que première étape.

1. Sous l’étape à laquelle vous souhaitez ajouter l’action HTTP, choisissez **Nouvelle étape**.

   Pour ajouter une action entre des étapes, placez votre pointeur au-dessus de la flèche qui les sépare. Cliquez sur le signe ( **+** ) qui s’affiche, puis sélectionnez **Ajouter une action**.

1. Sous **Choisir une action**, sélectionnez **Intégré**. Dans la zone de recherche, entrez `http` en guise de filtre. Dans la liste **Actions**, sélectionnez **HTTP**.

   ![Sélection de l’action HTTP](./media/connectors-native-http/select-http-action.png)

   Cet exemple renomme l’action « Action HTTP » afin qu’elle ait un nom plus descriptif.

1. Indiquez les valeurs des [paramètres d’actions HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) à inclure dans l’appel au point de terminaison cible.

   ![Entrer les paramètres de l’action HTTP](./media/connectors-native-http/http-action-parameters.png)

   Si vous sélectionnez un type d’authentification autre que **Aucun**, les paramètres d’authentification varient en fonction de votre sélection. Pour plus d’informations sur les types d’authentification disponibles pour HTTP, consultez ces rubriques :

   * [Ajouter l’authentification aux appels sortants](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Authentifier l’accès aux ressources avec des identités managées](../logic-apps/create-managed-service-identity.md)

1. Pour ajouter d’autres paramètres disponibles, ouvrez la liste **Ajouter un nouveau paramètre**, puis sélectionnez les paramètres de votre choix.

1. Lorsque vous avez terminé, pensez à enregistrer votre application logique. Dans la barre d’outils du Concepteur, sélectionnez **Enregistrer**.

<a name="tls-support"></a>

## <a name="transport-layer-security-tls"></a>TLS (Transport Layer Security)

En fonction de la capacité du point de terminaison cible, les appels sortants prennent en charge le protocole TLS (anciennement protocole SSL) versions 1.0, 1.1 et 1.2. Logic Apps négocie avec le point de terminaison en utilisant la version prise en charge la plus récente possible.

Par exemple, si le point de terminaison prend en charge la version 1.2, le connecteur HTTP utilise d’abord cette version. Sinon, le connecteur utilise la version prise en charge juste inférieure.

<a name="self-signed"></a>

## <a name="self-signed-certificates"></a>Certificats auto-signés

* Pour les applications logiques situées dans l’environnement Azure multilocataire global, le connecteur HTTP n’autorise pas les certificats TLS/SSL auto-signés. Si votre application logique émet un appel HTTP vers un serveur et présente un certificat auto-signé TLS/SSL, l’appel HTTP échouera avec une erreur `TrustFailure`.

* Pour les applications logiques situées dans un [environnement ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), le connecteur HTTP autorise les certificats auto-signés pour les négociations TLS/SSL. Toutefois, vous devez d’abord [activer la prise en charge des certificats auto-signés](../logic-apps/create-integration-service-environment-rest-api.md#request-body) pour un environnement ISE existant ou un nouvel ISE à l’aide de l’API REST Logic Apps, puis installer le certificat public à l’emplacement `TrustedRoot`.

## <a name="content-with-multipartform-data-type"></a>Contenu avec type multipart/form-data

Pour gérer le contenu qui a le type `multipart/form-data` dans les requêtes HTTP, vous pouvez ajouter un objet JSON qui comprend les attributs `$content-type` et `$multipart` dans le corps de la requête HTTP à l’aide de ce format.

```json
"body": {
   "$content-type": "multipart/form-data",
   "$multipart": [
      {
         "body": "<output-from-trigger-or-previous-action>",
         "headers": {
            "Content-Disposition": "form-data; name=file; filename=<file-name>"
         }
      }
   ]
}
```

Par exemple, supposons que vous ayez une application logique qui envoie une requête HTTP POST pour un fichier Excel sur un site web à l’aide de l’API de ce site, qui prend en charge le type `multipart/form-data`. Voici comment cette action peut se présenter :

![Données de formulaire en plusieurs parties](./media/connectors-native-http/http-action-multipart.png)

Voici le même exemple qui montre la définition JSON de l’action HTTP dans la définition de flux de travail sous-jacente :

```json
"HTTP_action": {
   "inputs": {
      "body": {
         "$content-type": "multipart/form-data",
         "$multipart": [
            {
               "body": "@trigger()",
               "headers": {
                  "Content-Disposition": "form-data; name=file; filename=myExcelFile.xlsx"
               }
            }
         ]
      },
      "method": "POST",
      "uri": "https://finance.contoso.com"
   },
   "runAfter": {},
   "type": "Http"
}
```

<a name="asynchronous-pattern"></a>

## <a name="asynchronous-request-response-behavior"></a>Comportement de type requête-réponse asynchrone

Par défaut, dans Azure Logic Apps, toutes les actions HTTP suivent le [modèle d’opération asynchrone standard](/azure/architecture/patterns/async-request-reply). Ce modèle spécifie qu’après l’appel d’une action HTTP ou l’envoi d’une requête à un point de terminaison, un service, un système ou une API, le récepteur retourne immédiatement la réponse [« 202 ACCEPTED »](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.3). Ce code confirme que le récepteur a accepté la requête, mais indique qu’il n’a pas terminé le traitement. La réponse peut inclure un en-tête `location` qui spécifie l’URL et un ID d’actualisation que l’appelant peut utiliser pour interroger ou vérifier l’état de la requête asynchrone jusqu’à ce que le récepteur arrête le traitement et retourne la réponse de réussite [« 200 OK »](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.1) ou une réponse autre qu’une réponse 202. Toutefois, l’appelant n’a pas besoin d’attendre la fin du traitement de la requête et peut exécuter l’action suivante. Pour plus d’informations, consultez [L’intégration asynchrone des microservices permet l’autonomie des microservices](/azure/architecture/microservices/design/interservice-communication#synchronous-versus-asynchronous-messaging).

* Dans le concepteur d’applications logiques, c’est l’action HTTP (et non le déclencheur) qui comporte un paramètre **Modèle asynchrone** qui est activé par défaut. Ce paramètre spécifie que l’appelant n’a pas à attendre la fin du traitement et peut passer à l’action suivante, tout en continuant de vérifier l’état jusqu’à ce que le traitement s’arrête. S’il est désactivé, ce paramètre spécifie que l’appelant doit attendre la fin du traitement avant de passer à l’action suivante.

  Pour trouver ce paramètre, effectuez les étapes suivantes :

  1. Dans la barre de titre de l’action HTTP, sélectionnez les points de suspension ( **...** ) qui permettent d’ouvrir les paramètres de l’action.

  1. Recherchez le paramètre **Modèle asynchrone**.

     ![Paramètre « Modèle asynchrone »](./media/connectors-native-http/asynchronous-pattern-setting.png)

* La définition JSON sous-jacente de l’action HTTP suit implicitement le modèle d’opération asynchrone.

<a name="disable-asynchronous-operations"></a>

## <a name="disable-asynchronous-operations"></a>Désactiver les opérations asynchrones

Dans certains scénarios, vous aurez besoin du comportement asynchrone de l’action HTTP, par exemple lorsque vous souhaitez :

* [Éviter les expirations de délai HTTP pour les tâches de longue durée](#avoid-http-timeouts)
* [Désactiver la vérification des en-têtes d’emplacement](#disable-location-header-check)

<a name="turn-off-asynchronous-pattern-setting"></a>

### <a name="turn-off-asynchronous-pattern-setting"></a>Désactiver le paramètre **Modèle asynchrone**

1. Dans le concepteur d’applications logiques, dans la barre de titre de l’action HTTP, sélectionnez les points de suspension ( **...** ) qui permettent d’ouvrir les paramètres de l’action.

1. Recherchez le paramètre **Modèle asynchrone**, configurez-le sur **Désactivé** s’il est activé, puis sélectionnez **Terminé**.

   ![Désactiver le paramètre « Modèle asynchrone »](./media/connectors-native-http/disable-asynchronous-pattern-setting.png)

<a name="add-disable-async-pattern-option"></a>

### <a name="disable-asynchronous-pattern-in-actions-json-definition"></a>Désactiver le modèle asynchrone dans la définition JSON de l’action

Dans la définition JSON sous-jacente de l’action HTTP, [ajoutez l’option d’opération `"DisableAsyncPattern"`](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options) à la définition de l’action afin que celle-ci suive le modèle d’opération synchrone. Pour plus d’informations, consultez aussi [Exécuter des actions de manière synchrone](../logic-apps/logic-apps-workflow-actions-triggers.md#disable-asynchronous-pattern).

<a name="avoid-http-timeouts"></a>

## <a name="avoid-http-timeouts-for-long-running-tasks"></a>Éviter les expirations de délai HTTP pour les tâches de longue durée

Les requêtes HTTP ont un [délai d’expiration](../logic-apps/logic-apps-limits-and-config.md#http-limits). Si vous avez une action HTTP de longue durée qui expire en raison de ce délai, vous disposez des options suivantes :

* [Désactivez le modèle d’opération asynchrone de l’action HTTP](#disable-asynchronous-operations) afin que l’action n’interroge pas ou ne vérifie pas continuellement l’état de la requête. Au lieu d’un tel comportement, l’action attend que le récepteur lui réponde en indiquant l’état et les résultats à la fin du traitement de la requête.

* Remplacez l’action HTTP par l’[action webhook HTTP](../connectors/connectors-native-webhook.md), qui attend que le récepteur réponde en indiquant l’état et les résultats à la fin du traitement de la requête.

<a name="disable-location-header-check"></a>

## <a name="disable-checking-location-headers"></a>Désactiver la vérification des en-têtes d’emplacement

Certains points de terminaison, services, systèmes ou API retournent une réponse « 202 ACCEPTED » qui ne comporte pas d’en-tête `location`. Pour éviter qu’une action HTTP vérifie continuellement l’état de la requête en l’absence d’un en-tête `location`, vous pouvez utiliser les options suivantes :

* [Désactivez le modèle d’opération asynchrone de l’action HTTP](#disable-asynchronous-operations) afin que l’action n’interroge pas ou ne vérifie pas continuellement l’état de la requête. Au lieu d’un tel comportement, l’action attend que le récepteur lui réponde en indiquant l’état et les résultats à la fin du traitement de la requête.

* Remplacez l’action HTTP par l’[action webhook HTTP](../connectors/connectors-native-webhook.md), qui attend que le récepteur réponde en indiquant l’état et les résultats à la fin du traitement de la requête.

## <a name="known-issues"></a>Problèmes connus

<a name="omitted-headers"></a>

### <a name="omitted-http-headers"></a>En-têtes HTTP omis

Si un déclencheur HTTP ou une action contient les en-têtes ci-dessous, Logic Apps les supprime du message de requête généré sans afficher d’avertissement ou d’erreur :

* `Accept-*`
* `Allow`
* `Content-*` à ces exceptions près : `Content-Disposition`, `Content-Encoding` et `Content-Type`
* `Cookie`
* `Expires`
* `Host`
* `Last-Modified`
* `Origin`
* `Set-Cookie`
* `Transfer-Encoding`

Si Logic Apps ne vous empêche pas d’enregistrer des applications logiques ayant un déclencheur ou une action HTTP contenant ces en-têtes, le service les ignore.

## <a name="connector-reference"></a>Référence de connecteur

Pour en savoir plus sur les paramètres des déclencheurs et des actions, consultez les sections suivantes :

* [Paramètres de déclencheurs HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)
* [Paramètres d’actions HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)

### <a name="output-details"></a>Détails des résultats

Voici d’autres informations sur les sorties d’un déclencheur ou d’une action HTTP qui renvoient les données suivantes :

| Propriété | Type | Description |
|----------|------|-------------|
| `headers` | Objet JSON | En-têtes de la requête |
| `body` | Objet JSON | Objet avec le contenu du corps de la requête |
| `status code` | Integer | Code d’état de la requête |
|||

| Code d’état | Description |
|-------------|-------------|
| 200 | OK |
| 202 | Acceptée |
| 400 | Demande incorrecte |
| 401 | Non autorisé |
| 403 | Interdit |
| 404 | Introuvable |
| 500 | Erreur interne du serveur. Une erreur inconnue s’est produite. |
|||

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)

