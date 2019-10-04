---
title: Accès et données sécurisés - Azure Logic Apps
description: Protégez les entrées de paramètres, les déclencheurs de requêtes HTTP, l’historique des exécutions, les opérations d’application logique et les connexions vers d’autres services dans Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 06/28/2019
ms.openlocfilehash: be10d144fadb21a695c5573c82681a26136e71d4
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/15/2019
ms.locfileid: "71004091"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Accès et données sécurisés dans Azure Logic Apps

Pour contrôler l’accès et protéger les données dans Azure Logic Apps, vous pouvez configurer des paramètres de sécurité au niveau des accès suivants :

* [Accès aux déclencheurs de requêtes HTTP](#secure-triggers)
* [Accès aux opérations d’une application logique](#secure-operations)
* [Accès aux entrées et aux sorties de l’historique des exécutions](#secure-run-history)
* [Accès aux entrées de paramètres](#secure-action-parameters)
* [Accès aux services et aux systèmes appelés par les applications logiques](#secure-requests)

<a name="secure-triggers"></a>

## <a name="access-to-http-request-triggers"></a>Accès aux déclencheurs de requêtes HTTP

Lorsque votre application logique utilise un déclencheur basé sur une requête HTTP, comme le déclencheur [Requête](../connectors/connectors-native-reqres.md) ou [Webhook](../connectors/connectors-native-webhook.md), vous pouvez restreindre l’accès de sorte que seuls les clients autorisés puissent démarrer votre application logique. Toutes les requêtes reçues par une application logique sont chiffrées et sécurisées avec le protocole SSL (Secure Sockets Layer). 

Voici les différentes méthodes qui sont disponibles pour sécuriser l’accès à ce type de déclencheur :

* [Générer des signatures d’accès partagé](#sas)
* [Limiter les adresses IP entrantes](#restrict-incoming-ip-addresses)
* [Ajouter Azure Active Directory, OAuth ou une autre sécurité](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>Générer des signatures d’accès partagé (SAP)

Dans une application logique, l’URL de chaque point de terminaison de requête comprend une [signature d’accès partagé](https://docs.microsoft.com/rest/api/storageservices/constructing-a-service-sas) (SAP). L’URL est au format suivant :

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

Chaque URL contient un paramètre de requête `sp`, `sv` et `sig`, comme vous pouvez le voir dans le tableau suivant :

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

Pour regénérer une nouvelle clé d’accès sécurisé à tout moment, utilisez l’API REST Azure ou le portail Azure. Toutes les URL précédemment générées qui utilisent l’ancienne clé sont invalidées et ne sont plus autorisées à déclencher l’application logique. Les URL que vous récupérez après la regénération sont signées avec la nouvelle clé d’accès.

1. Dans le portail Azure, ouvrez l’application logique possédant la clé que vous souhaitez régénérer.

1. Dans le menu de l’application logique, sous **Paramètres**, sélectionnez **Clés d’accès**.

1. Sélectionnez la clé que vous souhaitez regénérer et terminez le processus.

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>Créer des URL de rappel qui expirent

Si vous décidez de partager l’URL d’un point de terminaison pour un déclencheur basé sur des requêtes HTTP, vous pouvez générer des URL de rappel qui utilisent des clés et pour lesquelles des dates d’expiration ont été configurées. De cette façon, vous pouvez facilement restaurer les clés ou restreindre l’accès au déclenchement de votre application logique selon une période donnée. Si vous souhaitez spécifier une date d’expiration pour une URL, utilisez l’[API REST Logic Apps](https://docs.microsoft.com/rest/api/logic/workflowtriggers), par exemple :

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

Dans le corps, ajoutez la propriété `NotAfter` en utilisant une chaîne de date JSON. Cette propriété retourne une URL de rappel qui est valide uniquement jusqu’à la date et l’heure `NotAfter`.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>Créer des URL avec une clé de secret principale ou secondaire

Lorsque vous générez ou listez des URL de rappel pour des déclencheurs basés sur une requête HTTP, vous pouvez également spécifier la clé à utiliser pour signer l’URL. Pour générer une URL signée par une clé spécifique, utilisez l’[API REST Logic Apps](https://docs.microsoft.com/rest/api/logic/workflowtriggers), par exemple :

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

Dans le corps, incluez la propriété `KeyType` en tant que `Primary` ou `Secondary`. Cette propriété retourne une URL qui est signée par la clé sécurisée spécifiée.

<a name="restrict-incoming-ip"></a>

### <a name="restrict-incoming-ip-addresses"></a>Limiter les adresses IP entrantes

En plus de la signature d’accès partagé, vous pouvez aussi restreindre les clients qui sont autorisés à appeler votre application logique. Par exemple, si vous gérez votre point de terminaison de requête via la gestion des API Azure, vous pouvez restreindre votre application logique, de sorte qu’elle n’accepte que les requêtes qui proviennent de l’adresse IP de l’instance Gestion des API.

#### <a name="restrict-incoming-ip-ranges-in-azure-portal"></a>Restreindre les plages d’adresses IP entrantes dans le portail Azure

1. Dans le portail Azure, ouvrez votre application logique dans le Concepteur d’applications logiques.

1. Dans le menu de votre application logique, sous **Paramètres**, sélectionnez **Paramètres de flux de travail**.

1. Sous **Configuration du contrôle d'accès** > **Adresses IP entrantes autorisées**, sélectionnez **Plages d’adresses IP spécifiques**.

1. Sous **Plages d'adresses IP pour les déclencheurs**, spécifiez les plages d’adresses IP acceptées par le déclencheur.

   Une plage d’adresses IP valide utilise ces formats : *x.x.x.x/x* ou *x.x.x.x-x.x.x.x*

Si vous souhaitez que votre application logique se déclenche uniquement comme une application logique imbriquée, dans la liste **Adresses IP entrantes autorisées**, sélectionnez **Uniquement les autres Logic Apps**. Cette option crée un tableau vide dans votre ressource d’application logique. De cette façon, seuls les appels émanant du service Logic Apps (applications logiques parentes) peuvent déclencher l’application logique imbriquée.

> [!NOTE]
> Indépendamment de l’adresse IP, vous pouvez toujours exécuter une application logique qui a un déclencheur basé sur une requête HTTP en utilisant `/triggers/<trigger-name>/run` via l’API REST Azure ou via la Gestion des API. Cependant, ce scénario nécessite encore une authentification auprès de l’API REST Azure. Tous les événements s’affichent dans le journal d’audit Azure. Veillez à définir les stratégies de contrôle d’accès en conséquence.

#### <a name="restrict-incoming-ip-ranges-in-azure-resource-manager-template"></a>Restreindre les plages d’adresses IP entrantes dans un modèle Azure Resource Manager

Si vous automatisez le déploiement des applications logiques à l’aide d’un [modèle Azure Resource Manager](../logic-apps/logic-apps-create-deploy-template.md), vous pouvez spécifier les plages d’adresses IP à l’aide de la section `accessControl` avec la section `triggers` de la définition de ressource de votre application logique. Par exemple :

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

### <a name="add-azure-active-directory-oauth-or-other-security"></a>Ajouter Azure Active Directory, OAuth ou une autre sécurité

Pour ajouter des protocoles d’autorisation supplémentaires à votre application logique, pensez à utiliser [Gestion des API Azure](../api-management/api-management-key-concepts.md). Ce service vous permet d’exposer votre application logique sous la forme d’une API. En outre, il fournit des fonctionnalités avancées de supervision, de sécurité et d’application des stratégies, ainsi qu’une documentation riche sur tous les points de terminaison. La Gestion des API peut exposer un point de terminaison public ou privé de votre application logique, permettant ainsi d’utiliser Azure Active Directory, OAuth, un certificat ou d’autres normes de sécurité. Lorsque Gestion des API reçoit une requête, le service envoie celle-ci à votre application logique, en effectuant également toutes les transformations ou restrictions nécessaires tout au long du processus. Pour que seule la fonctionnalité Gestion des API déclenche votre application logique, vous pouvez utiliser les paramètres de plage d’adresses IP entrantes de votre application logique.

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>Accès aux opérations d’une application logique

Vous pouvez autoriser seulement certains utilisateurs ou groupes à exécuter des opérations, telles que la gestion, la modification ou l’affichage des applications logiques. Pour contrôler leurs autorisations, utilisez le [Contrôle d’accès en fonction du rôle (RBAC) Azure](../role-based-access-control/role-assignments-portal.md) afin d’attribuer des rôles personnalisés ou intégrés aux membres de votre abonnement Azure :

* [Contributeur d’application logique](../role-based-access-control/built-in-roles.md#logic-app-contributor) : Permet de gérer des applications logiques, mais pas d’en modifier l’accès.

* [Opérateur d’application logique](../role-based-access-control/built-in-roles.md#logic-app-operator) : Permet de lire, d’activer et de désactiver des applications logiques, mais pas de les modifier ni de les mettre à jour.

Pour empêcher que votre application logique ne soit modifiée ou supprimée par d’autres utilisateurs, vous pouvez utiliser le [verrouillage des ressources Azure](../azure-resource-manager/resource-group-lock-resources.md), qui empêche les autres utilisateurs de modifier ou de supprimer des ressources de production.

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>Accès à l’historique des exécutions

Pendant l’exécution d’une application logique, toutes les données sont chiffrées pendant le transit à l’aide de [Transit Layer Security (TLS)](https://azure.microsoft.com/updates/app-service-and-functions-hosted-apps-can-now-update-tls-versions/) et au [repos](../security/fundamentals/encryption-atrest.md). À la fin de l’exécution de votre application logique, vous pouvez afficher l’historique de l’exécution. Celui-ci comprend notamment les étapes exécutées, ainsi que l’état, la durée, les entrées et les sorties associés à chaque action. Ces informations détaillées fournissent des insights sur la façon dont votre application logique s’est exécutée et vous permettent de résoudre les problèmes qui peuvent survenir.

Lorsque vous accédez à l’historique des exécutions de votre application logique, Logic Apps authentifie votre accès, et fournit des liens vers les entrées et les sorties des requêtes et des réponses de l’exécution de votre application logique. Toutefois, pour les actions qui gèrent des mots de passe, des secrets, des clés ou d’autres informations sensibles, vous devez empêcher les autres utilisateurs d’accéder à ces données. Par exemple, si votre application logique obtient un secret dans [Azure Key Vault](../key-vault/key-vault-overview.md) en vue de l’utiliser lors de l’authentification d’une action HTTP, vous devez empêcher l’affichage de ce secret.

Pour contrôler l’accès aux entrées et aux sorties dans l’historique des exécutions de votre application logique, vous disposez des options suivantes :

* [Restreindre l’accès en fonction de la plage d’adresses IP](#restrict-ip).

  Cette option vous permet de sécuriser l’accès à l’historique des exécutions en spécifiant une plage d’adresses IP autorisée.

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

Si vous automatisez le déploiement des applications logiques à l’aide d’un [modèle Azure Resource Manager](../logic-apps/logic-apps-create-deploy-template.md), vous pouvez spécifier les plages d’adresses IP à l’aide de la section `accessControl` avec la section `contents` de la définition de ressource de votre application logique. Par exemple :

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

De nombreux déclencheurs et actions disposent de paramètres permettant masquer les entrées et/ou sorties dans l’historique d’exécution d’une application logique. Voici quelques [aspects à prendre en compte](#obfuscation-considerations) lors de l’utilisation de ces paramètres pour sécuriser ces données.

#### <a name="secure-inputs-and-outputs-in-the-designer"></a>Sécuriser les entrées et sorties dans le concepteur

1. Si votre application logique n’est pas encore ouverte dans le [portail Azure](https://portal.azure.com), ouvrez-la dans le Concepteur d’application logique.

   ![Ouvrir un exemple d’application logique](media/logic-apps-securing-a-logic-app/sample-logic-app.png)

1. Au niveau du déclencheur ou de l’action où vous souhaitez sécuriser les données, sélectionnez les points de suspension ( **...** ), puis sélectionnez **Paramètres**.

   ![Ouvrir les paramètres](media/logic-apps-securing-a-logic-app/open-settings.png)

1. Activez **Entrées sécurisées**, **Sorties sécurisées**, ou les deux. Quand vous avez terminé, cliquez sur **Terminé**.

   ![Activer les entrées ou les sorties sécurisées](media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   L’action ou le déclencheur affiche désormais une icône de verrou dans la barre de titre.

   ![Icône de verrou dans la barre de titre](media/logic-apps-securing-a-logic-app/title-bar-lock-icon.png)

   Les jetons qui représentent les sorties sécurisées d’actions précédentes affichent également un verrou. Par exemple, lorsque vous sélectionnez une sortie de ce type dans la liste de contenu dynamique en vue de l’utiliser dans une action, ce jeton affiche une icône de verrou.

   ![Sélectionner une sortie](media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. Après l’exécution de l’application logique, vous pouvez voir l’historique de cette exécution.

   1. Dans le volet **Vue d’ensemble** de l’application logique, sélectionnez l’exécution que vous souhaitez voir.

   1. Dans le volet **Exécution d’application logique**, développez les actions que vous souhaitez examiner.

      Si vous avez choisi de sécuriser à la fois les entrées et les sorties, ces valeurs seront masquées.

      ![Données masquées dans l’historique des exécutions](media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="secure-inputs-and-outputs-in-code-view"></a>Sécuriser les entrées et sorties en mode Code

Dans la définition de déclencheur ou d’action sous-jacente, ajoutez ou mettez à jour le tableau `runtimeConfiguration.secureData.properties` avec une des deux valeurs ou les deux :

* `"inputs"`: Sécurise les entrées dans l’historique des exécutions.
* `"outputs"`: Sécurise les sorties dans l’historique des exécutions.

Voici quelques [aspects à prendre en compte](#obfuscation-considerations) lors de l’utilisation de ces paramètres pour sécuriser ces données.

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

* Lorsque vous sécurisez les entrées ou les sorties d’un déclencheur ou d’une action, Logic Apps n’envoie pas les données sécurisées à Azure Log Analytics. De plus, vous ne pouvez pas ajouter de [propriétés suivies](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details) à ce déclencheur ou à cette action à des fins de supervision.

* L’[API Logic Apps qui permet de gérer l’historique des workflows](https://docs.microsoft.com/rest/api/logic/) ne retourne pas de sorties sécurisées.

* Si une action qui a des entrées sécurisées, ou qui utilise explicitement des sorties sécurisées, retourne une réponse avec des sorties qui incluent ces données sécurisées, vous devrez activer manuellement les **sorties sécurisées** dans cette action afin de les sécuriser.

* Veillez à activer les **entrées sécurisées** ou les **sorties sécurisées** dans des actions en aval où l’historique des exécutions doit sécuriser ces données.

  **Paramètre Sorties sécurisées**

  Lorsque vous activez manuellement les **sorties sécurisées** dans un déclencheur ou dans une action, Logic Apps sécurise ces sorties dans l’historique des exécutions. Si une action en aval utilise explicitement ces sorties sécurisées comme des entrées, Logic Apps masque les entrées de cette action dans l’historique des exécutions, mais *il n’active pas* le paramètre **Entrées sécurisées** de l’action.

  ![Sorties sécurisées en tant qu’entrées et impact en aval sur la plupart des actions](media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  Les actions Composer, Analyser JSON et Réponse comportent uniquement le paramètre **Entrées sécurisées**. Lorsqu’il est activé, ce paramètre masque également les sorties de ces actions. Si ces actions utilisent explicitement les sorties sécurisées en amont comme des entrées, Logic Apps masque les entrées et les sorties de ces actions, mais *il n’active pas* le paramètre **Entrées sécurisées** de ces actions. Si une action en aval utilise explicitement les sorties masquées des actions Composer, Analyser JSON ou Réponse comme des entrées, Logic Apps *ne masque pas les entrées ou les sorties de cette action en aval*.

  ![Sorties sécurisées en tant qu’entrées et impact en aval sur certaines actions](media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **Paramètre Entrées sécurisées**

  Lorsque vous activez manuellement les **entrées sécurisées** dans un déclencheur ou dans une action, Logic Apps sécurise ces entrées dans l’historique des exécutions. Si une action en aval utilise explicitement les sorties visibles de ce déclencheur ou de cette action comme des entrées, Logic Apps masque les entrées de cette action en aval dans l’historique des exécutions, mais *il n’active pas* les **entrées sécurisées** de cette action, et ne masque pas les sorties de cette action.

  ![Entrées sécurisées et impact en aval sur la plupart des actions](media/logic-apps-securing-a-logic-app/secure-inputs-flow.png)

  Si les actions Composer, Analyser JSON et Réponse utilisent explicitement les sorties visibles du déclencheur ou de l’action qui a des entrées sécurisées, Logic Apps masque les entrées et les sorties de ces actions, mais *il n’active pas* le paramètre **Entrées sécurisées** de ces actions. Si une action en aval utilise explicitement les sorties masquées des actions Composer, Analyser JSON ou Réponse comme des entrées, Logic Apps *ne masque pas les entrées ou les sorties de cette action en aval*.

  ![Entrées sécurisées et impact en aval sur certaines actions](media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>Accès aux entrées de paramètres

Si vous effectuez des déploiements dans différents environnements, vous pouvez paramétrer les valeurs de votre définition de workflow qui varient selon l’environnement. De cette façon, vous pouvez utiliser un [modèle Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md#parameters) pour déployer votre application logique, protéger les informations sensibles en configurant des paramètres sécurisés et fournir ces entrées de paramètres séparément par le biais des paramètres du modèle, à l’aide d’un [fichier de paramètres](../azure-resource-manager/resource-group-template-deploy.md#pass-parameter-values).

Par exemple, si vous authentifiez des actions HTTP avec [Azure Active Directory](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication), vous pouvez configurer et sécuriser les paramètres qui acceptent l’ID client et le secret client utilisés pour l’authentification. Si vous souhaitez configurer ces paramètres pour votre application logique, utilisez la section `parameters` située dans la définition de workflow de votre application logique. Pour protéger les valeurs de paramètre que vous ne voulez pas présenter lors de la modification de votre application logique ou de la consultation de l’historique des exécutions, configurez les paramètres avec le type `securestring` ou `secureobject`, et utilisez un encodage si nécessaire. Les paramètres de ce type ne sont pas retournés avec la définition de la ressource, et ne sont pas accessibles lors de l’affichage de la ressource après le déploiement. Pour accéder à ces valeurs de paramètre pendant l’exécution, utilisez l’expression `@parameters('<parameter-name>')` située dans la définition de votre workflow. Cette expression est évaluée uniquement au moment de l’exécution et est décrite par le [langage de définition du workflow](../logic-apps/logic-apps-workflow-definition-language.md).

> [!NOTE]
> Si vous utilisez un paramètre dans l’en-tête ou le corps d’une requête HTTP, il peut être visible lorsque vous consultez l’historique des exécutions de l’application logique et la requête HTTP sortante. Veillez également à définir vos stratégies d’accès au contenu en conséquence. Les en-têtes d’autorisation ne sont jamais visibles par le biais d’entrées ou de sorties. Si un secret est utilisé ici, il n’est pas récupérable.

Pour plus d’informations, consultez [Sécuriser des paramètres dans des définitions d’application logique](#secure-parameters-workflow) plus loin dans cette rubrique.

Lorsque vous automatisez des déploiements à l’aide de [modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md#parameters), vous pouvez définir des paramètres de modèles sécurisés, qui sont évalués au moment du déploiement, à l’aide des types `securestring` et `secureobject`. Pour définir des paramètres de modèle, utilisez la section `parameters` de niveau supérieur de votre modèle, qui est différente de la section `parameters` de votre définition de workflow. Pour fournir les valeurs des paramètres de modèle, utilisez un autre [fichier de paramètres](../azure-resource-manager/resource-group-template-deploy.md#pass-parameter-values).

Par exemple, si vous utilisez des secrets, vous pouvez définir et utiliser des paramètres de modèles sécurisés qui récupèrent ces secrets à partir d’[Azure Key Vault](../key-vault/key-vault-overview.md) lors du déploiement. Vous pouvez ensuite référencer le coffre de clés et le secret dans votre fichier de paramètres. Pour plus d’informations, consultez les rubriques suivantes :

* [Utiliser Azure Key Vault pour transmettre une valeur de paramètre sécurisée pendant le déploiement](../azure-resource-manager/resource-manager-keyvault-parameter.md)
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

Le modèle Resource Manager d’une application logique comporte plusieurs sections `parameters`. Pour protéger les mots de passe, les clés, les secrets et d’autres informations sensibles, configurez des paramètres sécurisés au niveau du modèle et au niveau de la définition de workflow à l’aide du type `securestring` ou `secureobject`. Vous pouvez ensuite stocker ces valeurs dans [Azure Key Vault](../key-vault/key-vault-overview.md) et utiliser le [fichier de paramètres](../azure-resource-manager/resource-group-template-deploy.md#pass-parameter-values) pour référencer le coffre de clés et le secret. Votre modèle récupère ensuite ces informations au moment du déploiement. Pour plus d’informations, consultez [Utiliser Azure Key Vault pour transmettre une valeur de paramètre sécurisée pendant le déploiement](../azure-resource-manager/resource-manager-keyvault-parameter.md).

Voici plus d’informations sur les sections `parameters` :

* En haut du modèle, une section `parameters` définit les paramètres à l’aide des valeurs utilisées par le modèle au moment du *déploiement*. Par exemple, ces valeurs peuvent inclure des chaînes de connexion pour un environnement de déploiement spécifique. Vous pouvez ensuite stocker ces valeurs dans un autre [fichier de paramètres](../azure-resource-manager/resource-group-template-deploy.md#pass-parameter-values), ce qui facilite la modification de ces valeurs.

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

Voici quelques méthodes permettant de sécuriser tous les points de terminaison auxquels votre application logique doit accéder pour envoyer des requêtes :

* Ajoutez l’authentification pour les requêtes sortantes.

  Lorsque vous utilisez une action HTTP, HTTP + Swagger (API ouverte) ou Webhook, vous pouvez ajouter l’authentification à la requête qui est envoyée par votre application logique. Par exemple, vous pouvez inclure l’authentification de base, l’authentification par certificat ou l’authentification Azure Active Directory. Pour plus d’informations, voir [Authentifier des déclencheurs ou des actions](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

* Restreignez l’accès des adresses IP d’application logique.

  Tous les appels de points de terminaison émis par des applications logiques proviennent d’adresses IP désignées en fonction des régions de vos applications logiques. Vous pouvez ajouter le filtrage afin d’accepter les requêtes provenant uniquement de ces adresses IP. Pour obtenir ces adresses IP, consultez [Limites et configuration d’Azure Logic Apps](logic-apps-limits-and-config.md#configuration).

* Authentifiez-vous puis accédez aux ressources comme à des identités managées.

  Pour accéder aux ressources des autres locataires d’Azure Active Directory (Azure AD), votre application logique peut authentifier votre identité sans nécessiter de connexion, en utilisant une identité managée (anciennement appelée MSI ou Managed Service Identity), plutôt que des informations d’identification ou des secrets. Azure gère cette identité pour vous et vous aide à sécuriser vos informations d’identification, car vous n’êtes pas obligé de fournir ni de faire pivoter des secrets. Pour plus d’informations sur la configuration et l’utilisation d’une identité managée affectée par le système pour votre application logique, consultez [S’authentifier et accéder aux ressources avec des identités managées dans Azure Logic Apps](../logic-apps/create-managed-service-identity.md).

* Sécurisez les connexions aux systèmes locaux.

  Azure Logic Apps permet l’intégration avec ces services afin de fournir une communication locale sécurisée et fiable.

  * Passerelle de données locale

    De nombreux connecteurs managés Azure Logic Apps fournissent des connexions sécurisées aux systèmes locaux, comme File System, SQL, SharePoint ou DB2. La passerelle envoie les données des sources locales sur des canaux chiffrés via Azure Service Bus. Tout le trafic est initialisé en tant que trafic sortant de l’agent de passerelle sécurisé. Découvrez comment [la passerelle de données sur site fonctionne](logic-apps-gateway-install.md#gateway-cloud-service).

  * Connectez-vous via la Gestion des API Azure.

    [Gestion des API Azure](../api-management/api-management-key-concepts.md) fournit des options de connexion locale, telles que l’intégration de réseaux privés virtuels de site à site et ExpressRoute pour un proxy et une communication sécurisés vers les systèmes locaux. Dans le concepteur d’application logique, à partir du workflow de votre application logique, vous pouvez sélectionner rapidement une API exposée par la Gestion des API, et ainsi, accéder rapidement aux systèmes locaux.

## <a name="next-steps"></a>Étapes suivantes

* [Créer des modèles de déploiement](logic-apps-create-deploy-template.md)  
* [Analyser vos applications logiques](logic-apps-monitor-your-logic-apps.md)  
* [Diagnostiquer des échecs et problèmes d’applications logiques](logic-apps-diagnosing-failures.md)  
* [Automatiser le déploiement d’application logique](logic-apps-azure-resource-manager-templates-overview.md)
