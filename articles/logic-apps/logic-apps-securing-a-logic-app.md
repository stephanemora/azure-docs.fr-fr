---
title: Sécuriser l’accès et les données
description: Sécuriser l’accès aux entrées, aux sorties, aux déclencheurs basés sur des requêtes, à l’historique des exécutions, aux tâches de gestion ainsi qu’à d’autres ressources dans Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/04/2020
ms.openlocfilehash: 4fc4960eb3af8a3d3c9902c9b24505bb5610b709
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657176"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Accès et données sécurisés dans Azure Logic Apps

Pour contrôler l’accès et protéger les données dans Azure Logic Apps, vous pouvez configurer des paramètres de sécurité au niveau des accès suivants :

* [Accès aux déclencheurs basés sur des requêtes](#secure-triggers)
* [Accès aux opérations d’une application logique](#secure-operations)
* [Accès aux entrées et aux sorties de l’historique des exécutions](#secure-run-history)
* [Accès aux entrées de paramètres](#secure-action-parameters)
* [Accès aux services et aux systèmes appelés par les applications logiques](#secure-requests)

<a name="secure-triggers"></a>

## <a name="access-to-request-based-triggers"></a>Accès aux déclencheurs basés sur des requêtes

Si votre application logique utilise un déclencheur basé sur une requête, qui reçoit des requêtes ou appels entrants, comme le déclencheur [Requête](../connectors/connectors-native-reqres.md) ou [Webhook](../connectors/connectors-native-webhook.md), vous pouvez restreindre l’accès de sorte que seuls les clients autorisés puissent démarrer votre application logique. Toutes les requêtes reçues par une application logique sont chiffrées et sécurisées à l’aide du protocole Transport Layer Security (TLS), précédemment connu sous le nom Secure Sockets Layer (SSL).

Voici quelques méthodes qui peuvent vous aider à sécuriser l’accès à ce type de déclencheur :

* [Générer des signatures d’accès partagé](#sas)
* [Limiter les adresses IP entrantes](#restrict-inbound-ip-addresses)
* [Ajouter OAuth Azure Active Directory ou une autre sécurité](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>Générer des signatures d’accès partagé (SAP)

Dans une application logique, l’URL de chaque point de terminaison de requête comprend une [signature d’accès partagé](https://docs.microsoft.com/rest/api/storageservices/constructing-a-service-sas) (SAP). L’URL est au format suivant :

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

Chaque URL contient le paramètre de requête `sp`, `sv` et `sig`, comme vous pouvez le voir dans le tableau suivant :

| Paramètre de requête. | Description |
|-----------------|-------------|
| `sp` | Spécifie des autorisations pour les méthodes HTTP autorisées à utiliser. |
| `sv` | Spécifie la version SAP à utiliser pour générer la signature. |
| `sig` | Spécifie la signature à utiliser pour authentifier l’accès au déclencheur. La signature est générée à l’aide de l’algorithme SHA256 avec une clé d’accès secrète pour l’ensemble des chemins et des propriétés de l’URL. Cette clé n’est jamais exposée ni publiée. Elle est chiffrée et stockée avec l’application logique. Votre application logique autorise uniquement les déclencheurs contenant une signature valide créée avec la clé secrète. |
|||

Pour plus d’informations sur la sécurisation de l’accès avec la signature d’accès partagé, consultez les sections suivantes de cette rubrique :

* [Régénération des clés d’accès](#access-keys)
* [Création d’URL de rappel qui arrivent à expiration](#expiring-urls)
* [Création d’URL avec une clé primaire ou une clé secondaire](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>Régénération de clés d'accès

Pour générer une nouvelle clé d’accès de sécurité à tout moment, utilisez l’API REST Azure ou le portail Azure. Toutes les URL précédemment générées qui utilisent l’ancienne clé sont invalidées et ne sont plus autorisées à déclencher l’application logique. Les URL que vous récupérez après la regénération sont signées avec la nouvelle clé d’accès.

1. Dans le [portail Azure](https://portal.azure.com), ouvrez l’application logique possédant la clé que vous souhaitez régénérer.

1. Dans le menu de l’application logique, sous **Paramètres**, sélectionnez **Clés d’accès**.

1. Sélectionnez la clé que vous souhaitez regénérer et terminez le processus.

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>Créer des URL de rappel qui expirent

Si vous décidez de partager l’URL d’un point de terminaison pour un déclencheur basé sur des requêtes, vous pouvez générer des URL de rappel qui utilisent des clés et pour lesquelles des dates d’expiration ont été configurées. De cette façon, vous pouvez facilement restaurer les clés ou restreindre l’accès au déclenchement de votre application logique selon une période donnée. Si vous souhaitez spécifier une date d’expiration pour une URL, utilisez l’[API REST Logic Apps](https://docs.microsoft.com/rest/api/logic/workflowtriggers), par exemple :

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

Dans le corps, ajoutez la propriété `NotAfter` en utilisant une chaîne de date JSON. Cette propriété retourne une URL de rappel qui est valide uniquement jusqu’à la date et l’heure `NotAfter`.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>Créer des URL avec une clé de secret principale ou secondaire

Lorsque vous générez ou listez des URL de rappel pour des déclencheurs basés sur une requête, vous pouvez également spécifier la clé à utiliser pour signer l’URL. Pour générer une URL signée par une clé spécifique, utilisez l’[API REST Logic Apps](https://docs.microsoft.com/rest/api/logic/workflowtriggers), par exemple :

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

Dans le corps, incluez la propriété `KeyType` en tant que `Primary` ou `Secondary`. Cette propriété retourne une URL signée par la clé de sécurité spécifiée.

<a name="restrict-inbound-ip"></a>

### <a name="restrict-inbound-ip-addresses"></a>Limiter les adresses IP entrantes

En plus de la signature d’accès partagé (SAP), vous pouvez aussi restreindre spécifiquement les clients qui sont autorisés à appeler votre application logique. Par exemple, si vous gérez votre point de terminaison de requête via la gestion des API Azure, vous pouvez restreindre votre application logique, de sorte qu’elle n’accepte que les requêtes qui proviennent de l’adresse IP de l’instance Gestion des API.

#### <a name="restrict-inbound-ip-ranges-in-azure-portal"></a>Restreindre les plages d’adresses IP entrantes dans le Portail Azure

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre application logique dans le Concepteur d’applications logiques.

1. Dans le menu de votre application logique, sous **Paramètres**, sélectionnez **Paramètres de flux de travail**.

1. Sous **Configuration du contrôle d’accès** > **Adresses IP entrantes autorisées**, sélectionnez **Plages d’adresses IP spécifiques**.

1. Sous **Plages d'adresses IP pour les déclencheurs**, spécifiez les plages d’adresses IP acceptées par le déclencheur.

   Une plage d’adresses IP valide utilise ces formats : *x.x.x.x/x* ou *x.x.x.x-x.x.x.x*

Si vous souhaitez que votre application logique se déclenche uniquement comme une application logique imbriquée, dans la liste **Adresses IP entrantes autorisées**, sélectionnez **Uniquement les autres Logic Apps**. Cette option crée un tableau vide dans votre ressource d’application logique. De cette façon, seuls les appels émanant du service Logic Apps (applications logiques parentes) peuvent déclencher l’application logique imbriquée.

> [!NOTE]
> Indépendamment de l’adresse IP, vous pouvez toujours exécuter une application logique qui a un déclencheur basé sur une requête en utilisant `/triggers/<trigger-name>/run` via l’API REST Azure ou via Gestion des API. Cependant, ce scénario nécessite encore une authentification auprès de l’API REST Azure. Tous les événements s’affichent dans le journal d’audit Azure. Veillez à définir les stratégies de contrôle d’accès en conséquence.

#### <a name="restrict-inbound-ip-ranges-in-azure-resource-manager-template"></a>Restreindre les plages d’adresses IP entrantes dans un modèle Azure Resource Manager

Si vous [automatisez le déploiement des applications logiques à l’aide de modèles Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), vous pouvez spécifier les plages d’adresses IP à l’aide de la section `accessControl` avec la section `triggers` de la définition de ressource de votre application logique. Par exemple :

``` json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {<workflow-definition>},
            "parameters": {},
            "accessControl": {
               "triggers": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     },
                     {
                        "addressRange": "2001:0db8::/64"
                     }
                  ]
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="add-authentication"></a>

### <a name="add-azure-active-directory-oauth-or-other-security"></a>Ajouter OAuth Azure Active Directory ou une autre sécurité

Pour ajouter des protocoles d’autorisation supplémentaires à votre application logique, pensez à utiliser le service [Gestion des API Azure](../api-management/api-management-key-concepts.md). Ce service vous permet d’exposer votre application logique sous la forme d’une API. En outre, il fournit des fonctionnalités avancées de supervision, de sécurité et d’application des stratégies, ainsi qu’une documentation riche sur tous les points de terminaison. Le service Gestion des API peut exposer un point de terminaison public ou privé pour votre application logique. Pour autoriser l’accès à ce point de terminaison, vous pouvez utiliser [Azure Active Directory OAuth](#azure-active-directory-oauth-authentication), un [certificat client](#client-certificate-authentication) ou d’autres normes de sécurité. Lorsque Gestion des API reçoit une requête, le service envoie celle-ci à votre application logique, en effectuant également toutes les transformations ou restrictions nécessaires tout au long du processus. Pour que seule la fonctionnalité Gestion des API déclenche votre application logique, vous pouvez utiliser les paramètres de plage d’adresses IP entrantes de votre application logique.

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>Accès aux opérations d’une application logique

Vous pouvez autoriser seulement certains utilisateurs ou groupes à exécuter des tâches, telles que la gestion, la modification ou l’affichage des applications logiques. Pour contrôler leurs autorisations, utilisez le [Contrôle d’accès en fonction du rôle (RBAC) Azure](../role-based-access-control/role-assignments-portal.md) afin d’attribuer des rôles personnalisés ou intégrés aux membres de votre abonnement Azure :

* [Contributeur d’application logique](../role-based-access-control/built-in-roles.md#logic-app-contributor) : Permet de gérer des applications logiques, mais pas d’en modifier l’accès.

* [Opérateur d’application logique](../role-based-access-control/built-in-roles.md#logic-app-operator) : Permet de lire, d’activer et de désactiver des applications logiques, mais pas de les modifier ni de les mettre à jour.

Pour empêcher la modification ou la suppression de votre application logique, vous pouvez utiliser le [Verrouillage de la ressource Azure](../azure-resource-manager/management/lock-resources.md). Grâce à cette fonctionnalité, vous pouvez empêcher d’autres utilisateurs de modifier ou de supprimer des ressources de production.

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>Accès à l’historique des exécutions

Pendant l’exécution d’une application logique, toutes les données sont [chiffrées pendant le transit](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) à l’aide de Transport Layer Security (TLS) et [au repos](../security/fundamentals/encryption-atrest.md). À la fin de l’exécution de votre application logique, vous pouvez afficher l’historique de l’exécution. Celui-ci comprend notamment les étapes exécutées, ainsi que l’état, la durée, les entrées et les sorties associés à chaque action. Ces informations détaillées fournissent des insights sur la façon dont votre application logique s’est exécutée et vous permettent de résoudre les problèmes qui peuvent survenir.

Lorsque vous affichez l’historique des exécutions de votre application logique, Logic Apps authentifie votre accès, et fournit des liens vers les entrées et les sorties des requêtes et des réponses pour chaque exécution. Toutefois, pour les actions qui gèrent des mots de passe, des secrets, des clés ou d’autres informations sensibles, vous devez empêcher les autres utilisateurs d’accéder à ces données. Par exemple, si votre application logique obtient un secret dans [Azure Key Vault](../key-vault/key-vault-overview.md) en vue de l’utiliser lors de l’authentification d’une action HTTP, vous devez empêcher l’affichage de ce secret.

Pour contrôler l’accès aux entrées et aux sorties dans l’historique des exécutions de votre application logique, vous disposez des options suivantes :

* [Restreindre l’accès en fonction de la plage d’adresses IP](#restrict-ip).

  Cette option vous aide à sécuriser l’accès à l’historique des exécutions en fonction des requêtes provenant d’une plage d’adresses IP spécifique.

* [Masquez les données de l’historique d’exécution à l’aide d’une obfuscation](#obfuscate).

  Vous pouvez masquer les entrées et/ou sorties de nombreux déclencheurs et actions dans l’historique d’exécution d’une application logique.

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>Restreindre l’accès en fonction de la plage d’adresses IP

Vous pouvez restreindre l’accès aux entrées et aux sorties de l’historique des exécutions de votre application logique afin que seules les requêtes provenant de certaines plages d’adresses IP soient autorisées à y accéder. Par exemple, pour empêcher quiconque d’accéder aux entrées et aux sorties, spécifiez une plage d’adresses IP comme celle-ci : `0.0.0.0-0.0.0.0`. Seul un utilisateur disposant d’autorisations d’administrateur peut supprimer cette restriction, permettant ainsi d’obtenir un accès « juste-à-temps » aux données de votre application logique. Vous pouvez spécifier les plages d’adresses IP non autorisées à l’aide du portail Azure ou d’un modèle Azure Resource Manager que vous utilisez pour le déploiement des applications logiques.

#### <a name="restrict-ip-ranges-in-azure-portal"></a>Restreindre les plages d’adresses IP dans le portail Azure

1. Dans le portail Azure, ouvrez votre application logique dans le Concepteur d’applications logiques.

1. Dans le menu de votre application logique, sous **Paramètres**, sélectionnez **Paramètres de flux de travail**.

1. Sous **Configuration du contrôle d’accès** > **Adresses IP entrantes autorisées**, sélectionnez **Plages d’adresses IP spécifiques**.

1. Sous **Plages d’adresses IP pour le contenu**, spécifiez les plages d’adresses IP qui peuvent accéder au contenu issu des entrées et sorties. 

   Une plage d’adresses IP valide utilise ces formats : *x.x.x.x/x* ou *x.x.x.x-x.x.x.x*

#### <a name="restrict-ip-ranges-in-azure-resource-manager-template"></a>Restreindre les plages d’adresses IP dans un modèle Azure Resource Manager

Si vous [automatisez le déploiement des applications logiques à l’aide de modèles Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), vous pouvez spécifier les plages d’adresses IP à l’aide de la section `accessControl` avec la section `contents` de la définition de ressource de votre application logique. Par exemple :

``` json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {<workflow-definition>},
            "parameters": {},
            "accessControl": {
               "contents": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     },
                     {
                        "addressRange": "2001:0db8::/64"
                     }
                  ]
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="obfuscate"></a>

### <a name="hide-data-from-run-history-by-using-obfuscation"></a>Masquer les données de l’historique d’exécution à l’aide d’une obfuscation

De nombreux déclencheurs et actions disposent de paramètres permettant masquer les entrées et/ou sorties dans l’historique d’exécution d’une application logique. Voici certains [aspects à prendre en considération](#obfuscation-considerations) quand vous utilisez ces paramètres pour sécuriser ces données.

#### <a name="hide-inputs-and-outputs-in-the-designer"></a>Masquer des entrées et des sorties dans le concepteur

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre application logique dans le Concepteur d’applications logiques.

   ![Ouvrir une application logique dans le concepteur d’application logique](./media/logic-apps-securing-a-logic-app/open-sample-logic-app-in-designer.png)

1. Pour le déclencheur ou l’action dont vous souhaitez masquer les données sensibles, sélectionnez le bouton de sélection ( **...** ), puis sélectionnez **Paramètres**.

   ![Ouvrir les paramètres d’action ou de déclencheur](./media/logic-apps-securing-a-logic-app/open-action-trigger-settings.png)

1. Activez **Entrées sécurisées**, **Sorties sécurisées**, ou les deux. Quand vous avez terminé, cliquez sur **Terminé**.

   ![Activer « Entrées sécurisées » ou « Sorties sécurisées »](./media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   L’action ou le déclencheur affiche désormais une icône de verrou dans la barre de titre.

   ![La barre de titre de l’action ou du déclencheur affiche l’icône de verrou](./media/logic-apps-securing-a-logic-app/lock-icon-action-trigger-title-bar.png)

   Les jetons qui représentent les sorties sécurisées d’actions précédentes affichent également un verrou. Par exemple, lorsque vous sélectionnez une sortie de ce type dans la liste de contenu dynamique en vue de l’utiliser dans une action, ce jeton affiche une icône de verrou.

   ![Sélectionner un jeton pour une sortie sécurisée](./media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. Après l’exécution de l’application logique, vous pouvez voir l’historique de cette exécution.

   1. Dans le volet **Vue d’ensemble** de l’application logique, sélectionnez l’exécution que vous souhaitez voir.

   1. Dans le volet **Exécution d’application logique**, développez les actions que vous souhaitez examiner.

      Si vous avez choisi de rendre les entrées et les sorties secrètes, ces valeurs sont désormais masquées.

      ![Entrées et sorties masquées dans l’historique des exécutions](./media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="hide-inputs-and-outputs-in-code-view"></a>Masquer les entrées et les sorties en mode Code

Dans la définition de déclencheur ou d’action sous-jacente, ajoutez ou mettez à jour le tableau `runtimeConfiguration.secureData.properties` avec une des deux valeurs ou les deux :

* `"inputs"`: Sécurise les entrées dans l’historique des exécutions.
* `"outputs"`: Sécurise les sorties dans l’historique des exécutions.

Voici certains [aspects à prendre en considération](#obfuscation-considerations) quand vous utilisez ces paramètres pour sécuriser ces données.

```json
"<trigger-or-action-name>": {
   "type": "<trigger-or-action-type>",
   "inputs": {
      <trigger-or-action-inputs>
   },
   "runtimeConfiguration": {
      "secureData": {
         "properties": [
            "inputs",
            "outputs"
         ]
      }
   },
   <other-attributes>
}
```

<a name="obfuscation-considerations"></a>

#### <a name="considerations-when-hiding-inputs-and-outputs"></a>Considérations relatives au masquage d’entrées et sorties

* Quand vous rendez secrètes les entrées ou les sorties d’un déclencheur ou d’une action, Logic Apps n’envoie pas les données sécurisées à Azure Log Analytics. De plus, vous ne pouvez pas ajouter de [propriétés suivies](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data) à ce déclencheur ou à cette action à des fins de supervision.

* L’[API Logic Apps qui permet de gérer l’historique des workflows](https://docs.microsoft.com/rest/api/logic/) ne retourne pas de sorties sécurisées.

* Pour masquer les sorties d’une action qui rend les entrées secrètes ou qui rend explicitement les sorties secrètes, activez manuellement **Sorties sécurisées** dans cette action.

* Veillez à activer **Entrées sécurisées** ou **Sorties sécurisées** dans les actions en aval, où l’historique des exécutions doit rendre ces données secrètes.

  **Paramètre Sorties sécurisées**

  Lorsque vous activez manuellement les **sorties sécurisées** dans un déclencheur ou dans une action, Logic Apps sécurise ces sorties dans l’historique des exécutions. Si une action en aval utilise explicitement ces sorties sécurisées comme des entrées, Logic Apps masque les entrées de cette action dans l’historique des exécutions, mais *il n’active pas* le paramètre **Entrées sécurisées** de l’action.

  ![Sorties sécurisées en tant qu’entrées et impact en aval sur la plupart des actions](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  Les actions Composer, Analyser JSON et Réponse comportent uniquement le paramètre **Entrées sécurisées**. Lorsqu’il est activé, ce paramètre masque également les sorties de ces actions. Si ces actions utilisent explicitement les sorties sécurisées en amont comme des entrées, Logic Apps masque les entrées et les sorties de ces actions, mais *il n’active pas* le paramètre **Entrées sécurisées** de ces actions. Si une action en aval utilise explicitement les sorties masquées des actions Composer, Analyser JSON ou Réponse comme des entrées, Logic Apps *ne masque pas les entrées ou les sorties de cette action en aval*.

  ![Sorties sécurisées en tant qu’entrées et impact en aval sur certaines actions](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **Paramètre Entrées sécurisées**

  Lorsque vous activez manuellement les **entrées sécurisées** dans un déclencheur ou dans une action, Logic Apps sécurise ces entrées dans l’historique des exécutions. Si une action en aval utilise explicitement les sorties visibles de ce déclencheur ou de cette action comme entrées, Logic Apps masque les entrées de cette action en aval dans l’historique des exécutions, mais *il n’active pas* les **entrées sécurisées** de cette action, et ne masque pas les sorties de cette action.

  ![Entrées sécurisées et impact en aval sur la plupart des actions](./media/logic-apps-securing-a-logic-app/secure-inputs-impact-on-downstream.png)

  Si les actions Composer, Analyser JSON et Réponse utilisent explicitement les sorties visibles du déclencheur ou de l’action qui a des entrées sécurisées, Logic Apps masque les entrées et les sorties de ces actions, mais *il n’active pas* le paramètre **Entrées sécurisées** de ces actions. Si une action en aval utilise explicitement les sorties masquées des actions Composer, Analyser JSON ou Réponse comme des entrées, Logic Apps *ne masque pas les entrées ou les sorties de cette action en aval*.

  ![Entrées sécurisées et impact en aval sur certaines actions](./media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>Accès aux entrées de paramètres

Si vous effectuez des déploiements dans différents environnements, vous pouvez paramétrer les valeurs de votre définition de workflow qui varient selon l’environnement. De cette façon, vous pouvez éviter les données codées en dur en utilisant un [modèle Azure Resource Manager](../azure-resource-manager/templates/overview.md) pour déployer votre application logique, protéger les données sensibles en définissant des paramètres sécurisés et transmettre ces données sous forme d’entrées distinctes par le biais des [paramètres du modèle](../azure-resource-manager/templates/template-parameters.md) à l’aide d’un [fichier de paramètres](../azure-resource-manager/templates/parameter-files.md).

Par exemple, si vous authentifiez des actions HTTP avec [OAuth Azure Active Directory](#azure-active-directory-oauth-authentication), vous pouvez définir et rendre secrets les paramètres qui acceptent l’ID client et le secret client utilisés pour l’authentification. Si vous souhaitez configurer ces paramètres dans votre application logique, utilisez la section `parameters` située dans la définition de workflow de votre application logique et le modèle Resource Manager pour le déploiement. Pour masquer les valeurs de paramètre que vous ne voulez pas présenter lors de la modification de votre application logique ou de la consultation de l’historique des exécutions, configurez les paramètres avec le type `securestring` ou `secureobject`, et utilisez un encodage si nécessaire. Les paramètres de ce type ne sont pas retournés avec la définition de la ressource, et ne sont pas accessibles lors de l’affichage de la ressource après le déploiement. Pour accéder à ces valeurs de paramètre pendant l’exécution, utilisez l’expression `@parameters('<parameter-name>')` située dans la définition de votre workflow. Cette expression est évaluée uniquement au moment de l’exécution et est décrite par le [langage de définition du workflow](../logic-apps/logic-apps-workflow-definition-language.md).

> [!NOTE]
> Si vous utilisez un paramètre dans l’en-tête ou le corps d’une requête, il peut être visible lorsque vous consultez l’historique des exécutions de l’application logique et la requête HTTP sortante. Veillez également à définir vos stratégies d’accès au contenu en conséquence. Vous pouvez également utiliser l’[obfuscation](#obfuscate) pour masquer les entrées et les sorties dans votre historique des exécutions. Les en-têtes d’autorisation ne sont jamais visibles par le biais d’entrées ou de sorties. Si un secret est utilisé ici, il n’est pas récupérable.

Pour en savoir plus, consultez les sections suivantes de la présente rubrique :

* [Sécuriser des paramètres dans les définitions de workflow](#secure-parameters-workflow)
* [Masquer les données de l’historique des exécutions à l’aide d’une obfuscation](#obfuscate)

Si vous [automatisez le déploiement d’applications logiques à l’aide de modèles Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), vous pouvez définir des [paramètres de modèles](../azure-resource-manager/templates/template-parameters.md) sécurisés, qui sont évalués au moment du déploiement, à l’aide des types `securestring` et `secureobject`. Pour définir des paramètres de modèle, utilisez la section `parameters` de niveau supérieur de votre modèle, qui est différente de la section `parameters` de votre définition de workflow. Pour fournir les valeurs des paramètres de modèle, utilisez un autre [fichier de paramètres](../azure-resource-manager/templates/parameter-files.md).

Par exemple, si vous utilisez des secrets, vous pouvez définir et utiliser des paramètres de modèles sécurisés qui récupèrent ces secrets à partir d’[Azure Key Vault](../key-vault/key-vault-overview.md) lors du déploiement. Vous pouvez ensuite référencer le coffre de clés et le secret dans votre fichier de paramètres. Pour plus d’informations, consultez les rubriques suivantes :

* [Transmettre des valeurs sensibles au déploiement à l’aide d’Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)
* [Sécuriser des paramètres dans des modèles Azure Resource Manager](#secure-parameters-deployment-template) plus loin dans cette rubrique

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>Sécuriser des paramètres dans les définitions de workflow

Pour protéger des informations sensibles dans la définition de workflow de votre application logique, utilisez des paramètres sécurisés afin que ces informations ne soient pas visibles après l’enregistrement de votre application logique. Par exemple, supposons que vous avez une action HTTP qui nécessite une authentification de base, c’est-à-dire, un nom d’utilisateur et un mot de passe. Dans la définition de workflow, la section `parameters` définit les paramètres `basicAuthPasswordParam` et `basicAuthUsernameParam` à l’aide du type `securestring`. La définition de l’action référence ensuite ces paramètres dans la section `authentication`.

```json
"definition": {
   "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
   "actions": {
      "HTTP": {
         "type": "Http",
         "inputs": {
            "method": "GET",
            "uri": "https://www.microsoft.com",
            "authentication": {
               "type": "Basic",
               "username": "@parameters('basicAuthUsernameParam')",
               "password": "@parameters('basicAuthPasswordParam')"
            }
         },
         "runAfter": {}
      }
   },
   "parameters": {
      "basicAuthPasswordParam": {
         "type": "securestring"
      },
      "basicAuthUsernameParam": {
         "type": "securestring"
      }
   },
   "triggers": {
      "manual": {
         "type": "Request",
         "kind": "Http",
         "inputs": {
            "schema": {}
         }
      }
   },
   "contentVersion": "1.0.0.0",
   "outputs": {}
}
```

<a name="secure-parameters-deployment-template"></a>

### <a name="secure-parameters-in-azure-resource-manager-templates"></a>Sécuriser des paramètres dans des modèles Azure Resource Manager

Un [modèle Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) d’une application logique comporte plusieurs sections `parameters`. Pour protéger les mots de passe, les clés, les secrets et d’autres informations sensibles, configurez des paramètres sécurisés au niveau du modèle et au niveau de la définition de workflow à l’aide du type `securestring` ou `secureobject`. Vous pouvez ensuite stocker ces valeurs dans [Azure Key Vault](../key-vault/key-vault-overview.md) et utiliser le [fichier de paramètres](../azure-resource-manager/templates/parameter-files.md) pour référencer le coffre de clés et le secret. Votre modèle récupère ensuite ces informations au moment du déploiement. Pour plus d’informations, consultez [Transmettre des valeurs sensibles au déploiement à l’aide d’Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md).

Voici plus d’informations sur les sections `parameters` :

* En haut du modèle, une section `parameters` définit les paramètres à l’aide des valeurs utilisées par le modèle au moment du *déploiement*. Par exemple, ces valeurs peuvent inclure des chaînes de connexion pour un environnement de déploiement spécifique. Vous pouvez ensuite stocker ces valeurs dans un autre [fichier de paramètres](../azure-resource-manager/templates/parameter-files.md), ce qui facilite la modification de ces valeurs.

* Dans la définition de ressource de votre application logique, mais en dehors de la définition de workflow, une section `parameters` spécifie les valeurs des paramètres de votre définition de workflow. Dans cette section, vous pouvez affecter ces valeurs à l’aide d’expressions de modèle qui référencent les paramètres de votre modèle. Ces expressions sont évaluées au moment du déploiement.

* Dans votre définition de workflow, une section `parameters` définit les paramètres utilisés par votre application logique au moment de l’exécution. Vous pouvez ensuite référencer ces paramètres dans le workflow de votre application logique à l’aide d’expressions de définition de workflow, qui sont évaluées au moment de l’exécution.

Cet exemple de modèle avec plusieurs définitions de paramètres sécurisés qui utilisent le type `securestring` :

| Nom du paramètre | Description |
|----------------|-------------|
| `TemplatePasswordParam` | Paramètre de modèle acceptant un mot de passe qui est ensuite passé au paramètre `basicAuthPasswordParam` de la définition de workflow. |
| `TemplateUsernameParam` | Paramètre de modèle acceptant un nom d’utilisateur qui est ensuite passé au paramètre `basicAuthUserNameParam` de la définition de workflow. |
| `basicAuthPasswordParam` | Paramètre de définition de workflow qui accepte le mot de passe pour l’authentification de base dans une action HTTP |
| `basicAuthUserNameParam` | Paramètre de définition de workflow qui accepte le nom d’utilisateur pour l’authentification de base dans une action HTTP |
|||

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "metadata": {
            "description": "Name of the Logic App."
         }
      },
      "TemplatePasswordParam": {
         "type": "securestring"
      },
      "TemplateUsernameParam": {
         "type": "securestring"
      },
      "LogicAppLocation": {
         "type": "string",
         "defaultValue": "[resourceGroup().location]",
         "allowedValues": [
            "[resourceGroup().location]",
            "eastasia",
            "southeastasia",
            "centralus",
            "eastus",
            "eastus2",
            "westus",
            "northcentralus",
            "southcentralus",
            "northeurope",
            "westeurope",
            "japanwest",
            "japaneast",
            "brazilsouth",
            "australiaeast",
            "australiasoutheast",
            "southindia",
            "centralindia",
            "westindia",
            "canadacentral",
            "canadaeast",
            "uksouth",
            "ukwest",
            "westcentralus",
            "westus2"
         ],
         "metadata": {
            "description": "Location of the Logic App."
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-0601/workflowdefinition.json#",
               "actions": {
                  "HTTP": {
                     "type": "Http",
                     "inputs": {
                        "method": "GET",
                        "uri": "https://www.microsoft.com",
                        "authentication": {
                           "type": "Basic",
                           "username": "@parameters('basicAuthUsernameParam')",
                           "password": "@parameters('basicAuthPasswordParam')"
                        }
                     },
                  "runAfter": {}
                  }
               },
               "parameters": {
                  "basicAuthPasswordParam": {
                     "type": "securestring"
                  },
                  "basicAuthUsernameParam": {
                     "type": "securestring"
                  }
               },
               "triggers": {
                  "manual": {
                     "type": "Request",
                     "kind": "Http",
                     "inputs": {
                        "schema": {}
                     }
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "basicAuthPasswordParam": {
                  "value": "[parameters('TemplatePasswordParam')]"
               },
               "basicAuthUsernameParam": {
                  "value": "[parameters('TemplateUsernameParam')]"
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-requests"></a>

## <a name="access-to-services-and-systems-called-from-logic-apps"></a>Accès aux services et aux systèmes appelés par les applications logiques

Voici quelques méthodes qui permettent de sécuriser les points de terminaison recevant des appels ou des requêtes de votre application logique :

* Ajoutez l’authentification aux requêtes sortantes.

  Lorsque utilisez un déclencheur ou une action basé(e) sur HTTP qui effectue des appels sortants, tels que HTTP, HTTP + Swagger ou Webhook, vous pouvez ajouter l’authentification à la demande envoyée par votre application logique. Par exemple, vous pouvez utiliser les types d’authentification suivants :

  * [Authentification de base](#basic-authentication)

  * [Authentification par certificat client](#client-certificate-authentication)

  * [Authentification OAuth Active Directory](#azure-active-directory-oauth-authentication)

  * [Authentification d’une identité managée](#managed-identity-authentication)
  
  Pour plus d’informations, consultez la section [Ajouter l’authentification aux appels sortants](#add-authentication-outbound) plus loin dans cette rubrique.

* Restreignez l’accès des adresses IP d’application logique.

  Tous les appels de points de terminaison émis par des applications logiques proviennent d’adresses IP désignées en fonction des régions de vos applications logiques. Vous pouvez ajouter le filtrage afin d’accepter les requêtes provenant uniquement de ces adresses IP. Pour obtenir ces adresses IP, consultez [Limites et configuration d’Azure Logic Apps](logic-apps-limits-and-config.md#configuration).

* Améliorez la sécurité des connexions aux systèmes locaux.

  Azure Logic Apps permet l’intégration avec ces services pour fournir une communication locale plus sécurisée et plus fiable.

  * Passerelle de données locale

    De nombreux connecteurs managés dans Azure Logic Apps facilitent les connexions sécurisées aux systèmes locaux, par exemple File System, SQL, SharePoint et DB2. La passerelle envoie les données des sources locales sur des canaux chiffrés via Azure Service Bus. L’ensemble du trafic est généré sous forme de trafic sortant sécurisé en provenance de l’agent de passerelle. Découvrez comment [la passerelle de données sur site fonctionne](logic-apps-gateway-install.md#gateway-cloud-service).

  * Connectez-vous via la Gestion des API Azure.

    [Gestion des API Azure](../api-management/api-management-key-concepts.md) fournit des options de connexion locale, telles que l’intégration de réseaux privés virtuels de site à site et ExpressRoute pour un proxy et une communication sécurisés vers les systèmes locaux. Dans le concepteur d’application logique, à partir du workflow de votre application logique, vous pouvez sélectionner rapidement une API exposée par la Gestion des API, et ainsi, accéder rapidement aux systèmes locaux.

<a name="add-authentication-outbound"></a>

## <a name="add-authentication-to-outbound-calls"></a>Ajouter l’authentification aux appels sortants

Les points de terminaison HTTP et HTTPS prennent en charge différents types d’authentification. En fonction du déclencheur ou de l’action que vous utilisez pour effectuer des appels sortants ou des demandes qui accèdent à ces points de terminaison, vous pouvez choisir parmi différentes plages de types d’authentification. Pour garantir la protection des informations sensibles gérées par votre application logique, utilisez des paramètres sécurisés et encodez les données si nécessaire. Pour plus d’informations sur l’utilisation et la sécurisation des paramètres, consultez [Accès aux entrées de paramètres](#secure-action-parameters).

> [!NOTE]
> Dans le concepteur d’applications logiques, la propriété **Authentication** peut être masquée sur certains déclencheurs et actions où vous pouvez spécifier le type d’authentification. Pour que la propriété apparaisse dans ces cas-là, dans le déclencheur ou l’action, ouvrez la liste **Ajouter un nouveau paramètre**, puis sélectionnez **Authentification**. Pour plus d’informations, consultez [Authentifier l’accès avec une identité managée](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

| Type d'authentification | Pris en charge par |
|---------------------|--------------|
| [De base](#basic-authentication) | Gestion des API Azure, Azure App Service, HTTP, HTTP + Swagger, Webhook HTTP |
| [Certificat client](#client-certificate-authentication) | Gestion des API Azure, Azure App Service, HTTP, HTTP + Swagger, Webhook HTTP |
| [OAuth Active Directory](#azure-active-directory-oauth-authentication) | Gestion des API Azure, Azure App Service, Azure Functions, HTTP, HTTP + Swagger, Webhook HTTP |
| [Brut](#raw-authentication) | Gestion des API Azure, Azure App Service, Azure Functions, HTTP, HTTP + Swagger, Webhook HTTP |
| [Identité gérée](#managed-identity-authentication) | Gestion des API Azure, Azure App Service, Azure Functions, HTTP, HTTP + Swagger, Webhook HTTP |
|||

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>Authentification de base

Si l’option [De base](../active-directory-b2c/secure-rest-api.md) est disponible, spécifiez les valeurs de propriété suivantes :

| Propriété (concepteur) | Propriété (JSON) | Obligatoire | Valeur | Description |
|---------------------|-----------------|----------|-------|-------------|
| **Authentification** | `type` | Oui | De base | Type d’authentification à utiliser |
| **Nom d’utilisateur** | `username` | Oui | <*user-name*>| Nom d’utilisateur permettant d’authentifier l’accès au point de terminaison de service cible |
| **Mot de passe** | `password` | Oui | <*password*> | Mot de passe permettant d’authentifier l’accès au point de terminaison de service cible |
||||||

Lorsque vous utilisez des [paramètres sécurisés](#secure-action-parameters) pour gérer et protéger des informations sensibles, par exemple dans un [modèle Azure Resource Manager pour l’automatisation du déploiement](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), vous pouvez utiliser des expressions pour accéder à ces valeurs de paramètre au moment de l’exécution. Cet exemple de définition d’action HTTP spécifie l’authentification `type` en tant que `Basic` et utilise la fonction [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) pour récupérer les valeurs des paramètres :

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Basic",
         "username": "@parameters('userNameParam')",
         "password": "@parameters('passwordParam')"
      }
  },
  "runAfter": {}
}
```

<a name="client-certificate-authentication"></a>

### <a name="client-certificate-authentication"></a>Authentification par certificat client

Si l’option [Certificat client](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) est disponible, spécifiez ces valeurs de propriété :

| Propriété (concepteur) | Propriété (JSON) | Obligatoire | Valeur | Description |
|---------------------|-----------------|----------|-------|-------------|
| **Authentification** | `type` | Oui | **Certificat client** <br>or <br>`ClientCertificate` | Type d’authentification à utiliser pour les certificats clients TLS/SSL. Bien que les certificats autosignés sont pris en charge, les certificats autosignés pour TLS/SSL ne sont pas pris en charge. |
| **Pfx** | `pfx` | Oui | <*encoded-pfx-file-content*> | Contenu encodé en base64 à partir d’un fichier Personal Information Exchange (PFX) <p><p>Pour convertir le fichier PFX au format encodé en base64, vous pouvez utiliser PowerShell en procédant comme suit : <p>1. Enregistrez le contenu du certificat dans une variable : <p>   `$pfx_cert = get-content 'c:\certificate.pfx' -Encoding Byte` <p>2. Convertissez le contenu du certificat à l’aide de la fonction `ToBase64String()` et enregistrez ce contenu dans un fichier texte : <p>   `[System.Convert]::ToBase64String($pfx_cert) | Out-File 'pfx-encoded-bytes.txt'` |
| **Mot de passe** | `password`| Non | <*password-for-pfx-file*> | Mot de passe pour accéder au fichier PFX |
|||||

Lorsque vous utilisez des [paramètres sécurisés](#secure-action-parameters) pour gérer et protéger des informations sensibles, par exemple dans un [modèle Azure Resource Manager pour l’automatisation du déploiement](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), vous pouvez utiliser des expressions pour accéder à ces valeurs de paramètre au moment de l’exécution. Cet exemple de définition d’action HTTP spécifie l’authentification `type` en tant que `ClientCertificate` et utilise la fonction [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) pour récupérer les valeurs des paramètres :

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ClientCertificate",
         "pfx": "@parameters('pfxParam')",
         "password": "@parameters('passwordParam')"
      }
   },
   "runAfter": {}
}
```

Pour plus d’informations sur la sécurisation des services à l’aide de l’authentification par certificat client, consultez les rubriques suivantes :

* [Améliorer la sécurité des API à l’aide de l’authentification par certificat client dans Gestion des API Azure](../api-management/api-management-howto-mutual-certificates-for-clients.md)
* [Améliorer la sécurité des services back-end à l’aide de l’authentification par certificat client dans Gestion des API Azure](../api-management/api-management-howto-mutual-certificates.md)
* [Améliorer la sécurité de votre service RESTful à l’aide de certificats clients](../active-directory-b2c/secure-rest-api.md)
* [Informations d’identification de certificat pour l’authentification d’application](../active-directory/develop/active-directory-certificate-credentials.md)
* [Utiliser un certificat TLS/SSL dans votre code dans Azure App Service](../app-service/configure-ssl-certificate-in-code.md)

<a name="azure-active-directory-oauth-authentication"></a>

### <a name="azure-active-directory-oauth-authentication"></a>Authentification OAuth Azure Active Directory

Si l’option [OAuth Active Directory](../active-directory/develop/about-microsoft-identity-platform.md) est disponible, spécifiez ces valeurs de propriété :

| Propriété (concepteur) | Propriété (JSON) | Obligatoire | Valeur | Description |
|---------------------|-----------------|----------|-------|-------------|
| **Authentification** | `type` | Oui | **OAuth Active Directory** <br>or <br>`ActiveDirectoryOAuth` | Type d’authentification à utiliser. Logic Apps suit actuellement le [protocole OAuth 2.0](../active-directory/develop/v2-overview.md). |
| **Authority** | `authority` | Non | <*URL de l’autorité émettrice du jeton*> | URL de l’autorité qui fournit le jeton d’authentification. Par défaut, cette valeur est définie sur `https://login.windows.net`. |
| **Locataire** | `tenant` | Oui | <*ID de locataire*> | Identificateur du locataire Azure AD |
| **Public ciblé** | `audience` | Oui | <*ressource à autoriser*> | Ressource à utiliser pour l’autorisation, par exemple, `https://management.core.windows.net/` |
| **ID client** | `clientId` | Oui | <*ID client*> | ID client pour l’application demandant l’autorisation |
| **Type d’informations d’identification** | `credentialType` | Oui | Certificat <br>or <br>Secret | Type d’informations d’identification que le client utilise pour la demande d’autorisation. Ces propriété et valeur n’apparaissent pas dans la définition sous-jacente de votre application logique, mais elles déterminent les propriétés affichées pour le type d’informations d’identification sélectionné. |
| **Secret** | `secret` | Oui, uniquement pour le type d’informations d’identification « Secret » | <*client-secret*> | Clé secrète client permettant de demander une autorisation |
| **Pfx** | `pfx` | Oui, mais uniquement pour le type d’informations d’identification « Certificat » | <*encoded-pfx-file-content*> | Contenu encodé en base64 à partir d’un fichier Personal Information Exchange (PFX) |
| **Mot de passe** | `password` | Oui, mais uniquement pour le type d’informations d’identification « Certificat » | <*password-for-pfx-file*> | Mot de passe pour accéder au fichier PFX |
|||||

Lorsque vous utilisez des [paramètres sécurisés](#secure-action-parameters) pour gérer et protéger des informations sensibles, par exemple dans un [modèle Azure Resource Manager pour l’automatisation du déploiement](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), vous pouvez utiliser des expressions pour accéder à ces valeurs de paramètre au moment de l’exécution. Cet exemple de définition d’action HTTP spécifie l’authentification `type` en tant que `ActiveDirectoryOAuth`, le type d’informations d’identification en tant que `Secret` et utilise la [fonction Parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) pour récupérer les valeurs des paramètres :

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ActiveDirectoryOAuth",
         "tenant": "@parameters('tenantIdParam')",
         "audience": "https://management.core.windows.net/",
         "clientId": "@parameters('clientIdParam')",
         "credentialType": "Secret",
         "secret": "@parameters('secretParam')"
     }
   },
   "runAfter": {}
}
```

<a name="raw-authentication"></a>

### <a name="raw-authentication"></a>Authentification brute

Si l’option **Brut** est disponible, vous pouvez utiliser ce type d’authentification lorsque vous devez utiliser des [schémas d’authentification](https://iana.org/assignments/http-authschemes/http-authschemes.xhtml) qui ne suivent pas le [protocole OAuth 2.0](https://oauth.net/2/). Avec ce type, vous créez manuellement la valeur d’en-tête d’autorisation que vous envoyez avec la demande sortante, puis vous spécifiez cette valeur d’en-tête dans votre déclencheur ou action.

Par exemple, voici un exemple d’en-tête pour une requête HTTPS qui suit le protocole [OAuth 1.0](https://tools.ietf.org/html/rfc5849) :

```text
Authorization: OAuth realm="Photos",
   oauth_consumer_key="dpf43f3p2l4k3l03",
   oauth_signature_method="HMAC-SHA1",
   oauth_timestamp="137131200",
   oauth_nonce="wIjqoS",
   oauth_callback="http%3A%2F%2Fprinter.example.com%2Fready",
   oauth_signature="74KNZJeDHnMBp0EMJ9ZHt%2FXKycU%3D"
```

Dans le déclencheur ou l’action qui prend en charge l’authentification brute, spécifiez les valeurs de propriétés suivantes :

| Propriété (concepteur) | Propriété (JSON) | Obligatoire | Valeur | Description |
|---------------------|-----------------|----------|-------|-------------|
| **Authentification** | `type` | Oui | Brut | Type d’authentification à utiliser |
| **Valeur** | `value` | Oui | <*authorization-header-value*> | Valeur d’en-tête d’autorisation à utiliser pour l’authentification |
||||||

Lorsque vous utilisez des [paramètres sécurisés](#secure-action-parameters) pour gérer et protéger des informations sensibles, par exemple dans un [modèle Azure Resource Manager pour l’automatisation du déploiement](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), vous pouvez utiliser des expressions pour accéder à ces valeurs de paramètre au moment de l’exécution. Cet exemple de définition d’action HTTP spécifie l’authentification `type` en tant que `Raw` et utilise la fonction [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) pour récupérer les valeurs des paramètres :

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Raw",
         "value": "@parameters('authHeaderParam')"
      }
   },
   "runAfter": {}
}
```

<a name="managed-identity-authentication"></a>

### <a name="managed-identity-authentication"></a>Authentification d’une identité managée

Si l’option [Identité managée](../active-directory/managed-identities-azure-resources/overview.md) est disponible, votre application logique peut utiliser l’identité affectée par le système ou une *seule* identité affectée par l’utilisateur créée manuellement pour authentifier l’accès aux ressources dans d’autres locataires Azure AD (Azure Active Directory) sans se connecter. Azure gère cette identité pour vous et vous aide à sécuriser vos informations d’identification, car vous n’êtes pas obligé de fournir ni de faire pivoter des secrets. En savoir plus sur les [services Azure qui prennent en charge les identités managées pour l’authentification Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

1. Pour que votre application logique puisse utiliser une identité managée, suivez les étapes décrites dans [Authentifier l’accès aux ressources Azure à l’aide des identités managées dans Azure Logic Apps](../logic-apps/create-managed-service-identity.md). Ces étapes activent l’identité managée sur votre application logique et configurent l’accès de cette identité à la ressource Azure cible.

1. Pour qu’une fonction Azure puisse utiliser une identité managée, vous devez d’abord [activer l’authentification des fonctions Azure](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions).

1. Dans le déclencheur ou l’action où vous souhaitez utiliser l’identité managée, spécifiez les valeurs de propriété suivantes :

   | Propriété (concepteur) | Propriété (JSON) | Obligatoire | Valeur | Description |
   |---------------------|-----------------|----------|-------|-------------|
   | **Authentification** | `type` | Oui | **Identité gérée** <br>or <br>`ManagedServiceIdentity` | Type d’authentification à utiliser |
   | **Identité gérée** | `identity` | Oui | * **Identité managée affectée par le système** <br>or <br>`SystemAssigned` <p><p>* <*nom-identité-affectée-par-utilisateur*> | Identité managée à utiliser |
   | **Public ciblé** | `audience` | Oui | <*target-resource-ID*> | ID de la ressource cible à laquelle vous souhaitez accéder. <p>Par exemple, `https://storage.azure.com/` rend les jetons d’accès pour l’authentification valides pour tous les comptes de stockage. Toutefois, vous pouvez également spécifier une URL de service racine, par exemple `https://fabrikamstorageaccount.blob.core.windows.net` pour un compte de stockage spécifique. <p>**Remarque** : La propriété **Audience** peut être masquée dans certains déclencheurs ou certaines actions. Pour que la propriété apparaisse, dans le déclencheur ou l’action, ouvrez la liste **Ajouter un nouveau paramètre**, puis sélectionnez **Audience**. <p><p>**Important !** Vérifiez que cet ID de ressource cible *correspond exactement* à ce qu’attend Azure AD, notamment les barres obliques de fin obligatoires. Ainsi, l’ID de ressource `https://storage.azure.com/` pour tous les comptes Stockage blob Azure requiert une barre oblique finale. Toutefois, l’ID de ressource pour un compte de stockage spécifique ne requiert pas de barre oblique finale. Pour rechercher ces ID de ressource, consultez les [services Azure qui prennent en charge Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). |
   |||||

   Lorsque vous utilisez des [paramètres sécurisés](#secure-action-parameters) pour gérer et protéger des informations sensibles, par exemple dans un [modèle Azure Resource Manager pour l’automatisation du déploiement](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), vous pouvez utiliser des expressions pour accéder à ces valeurs de paramètre au moment de l’exécution. Cet exemple de définition d’action HTTP spécifie l’authentification `type` en tant que `ManagedServiceIdentity` et utilise la fonction [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) pour récupérer les valeurs des paramètres :

   ```json
   "HTTP": {
      "type": "Http",
      "inputs": {
         "method": "GET",
         "uri": "@parameters('endpointUrlParam')",
         "authentication": {
            "type": "ManagedServiceIdentity",
            "identity": "SystemAssigned",
            "audience": "https://management.azure.com/"
         },
      },
      "runAfter": {}
   }
   ```

## <a name="next-steps"></a>Étapes suivantes

* [Automatiser le déploiement pour le service Azure Logic Apps](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)  
* [Surveiller les applications logiques](../logic-apps/monitor-logic-apps-log-analytics.md)  
* [Diagnostiquer des échecs et problèmes d’applications logiques](../logic-apps/logic-apps-diagnosing-failures.md)  
* [Automatiser le déploiement d’application logique](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
