---
title: Sécuriser l’accès à Azure Logic Apps | Microsoft Docs
description: Ajouter la sécurité pour Azure Logic Apps, notamment les déclencheurs, les entrées et les sorties, les paramètres et les autres services
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: 9fab1050-cfbc-4a8b-b1b3-5531bee92856
ms.topic: article
ms.date: 02/05/2019
ms.openlocfilehash: 31b18e2dfbad1f572b5c9b0ae59f5e076db7cde2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60995662"
---
# <a name="secure-access-in-azure-logic-apps"></a>Accès sécurisé dans Azure Logic Apps

Voici les éléments dans votre application logique dans lequel vous pouvez sécuriser l’accès :

* [Déclencheurs de requête ou webhook](#secure-triggers)
* [Opérations telles que la gestion, la modification ou l’affichage](#secure-operations) de votre application logique
* [Entrées et sorties](#secure-run-history) issues de l’historique des exécutions de votre application logique
* [Entrées et paramètres d’action](#secure-action-parameters)
* [Services permettant d’obtenir des requêtes](#secure-requests) depuis votre application logique

<a name="secure-triggers"></a>

## <a name="secure-access-to-request-triggers"></a>Sécuriser l’accès aux déclencheurs de requête

Lorsque votre application logique utilise un déclencheur basé sur une requête HTTP, tel que [Requête](../connectors/connectors-native-reqres.md) ou [Webhook](../connectors/connectors-native-webhook.md), vous pouvez restreindre l’accès de sorte que seuls les clients autorisés puissent démarrer votre application logique. Toutes les requêtes reçues par une application logique sont chiffrées et sécurisées avec le protocole SSL (Secure Sockets Layer). Voici les différentes façons dont vous pouvez sécuriser l’accès à ce type de déclencheur :

* [Générer des signatures d’accès partagé](#sas)
* [Limiter les adresses IP entrantes](#restrict-incoming-ip-addresses)
* [Ajouter Azure Active Directory, OAuth ou une autre sécurité](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures"></a>Générer des signatures d’accès partagé

Chaque point de terminaison de requête pour une application logique inclut une partie [Signature d’accès partagé](../storage/common/storage-dotnet-shared-access-signature-part-1.md) (SAP) dans l’URL du point de terminaison. Chaque URL contient un paramètre de requête `sp`, `sv` et `sig` :

* `sp` spécifie les autorisations qui correspondent aux méthodes HTTP autorisées à utiliser.
* `sv` spécifie la version utilisée pour générer la signature.
* `sig` est utilisé pour authentifier l’accès au déclencheur.

La signature est générée à l’aide de l’algorithme SHA256 avec une clé d’accès secrète sur tous les chemins d’accès à l’URL et les propriétés. La clé secrète n’est jamais exposée ou publiée, et est chiffrée et stockée avec l’application logique. Votre application logique autorise uniquement les déclencheurs contenant une signature valide créée avec la clé secrète. 

Voici plus d’informations sur la sécurisation de l’accès grâce à la Signature d'accès partagé :

* [Régénération des clés d’accès](#access-keys)
* [Création d’URL de rappel qui arrivent à expiration](#expiring-urls)
* [Création d’URL avec une clé primaire ou une clé secondaire](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>Régénération de clés d'accès

Pour régénérer une nouvelle clé d’accès sécurisé à tout moment, utilisez l’API REST Azure ou le portail Azure. Toutes les URL générées précédemment et utilisant l’ancienne clé sont invalidées et ne sont plus autorisées à déclencher l’application logique. Les URL que vous récupérez après la régénération sont signées avec la nouvelle clé d’accès.

1. Dans le portail Azure, ouvrez l’application logique possédant la clé que vous souhaitez régénérer.

1. Dans le menu de l’application logique, sous **Paramètres**, sélectionnez **Clés d’accès**.

1. Sélectionnez la clé que vous souhaitez régénérer et terminez le processus.

<a name="expiring-urls"></a>

#### <a name="create-callback-urls-with-expiration-dates"></a>Créer des URL de rappel avec des dates d’expiration

Si vous partagez une URL d’un point de terminaison de déclencheur basé sur des requêtes avec d’autres parties, vous pouvez générer des URL de rappel avec des clés et des dates d’expiration en fonction des besoins. Vous pouvez ensuite en toute transparence restaurer des clés ou restreindre l’accès pour déclencher votre application logique à un intervalle de temps spécifique. Vous pouvez spécifier une date d’expiration pour une URL à l’aide de l’[API REST de Logic Apps](https://docs.microsoft.com/rest/api/logic/workflowtriggers), par exemple :

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

Dans le corps, incluez la propriété `NotAfter` utilisant une chaîne de date JSON. Cette propriété retourne une URL de rappel qui est valide uniquement jusqu’à la date et l’heure `NotAfter`.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>Créer des URL avec une clé de secret principale ou secondaire

Lorsque vous générez ou répertoriez des URL de rappel pour des déclencheurs basés sur une requête, vous pouvez également spécifier la clé à utiliser pour signer l’URL. Vous pouvez générer une URL qui est signée par une clé spécifique à l’aide de l’[API REST de Logic Apps](https://docs.microsoft.com/rest/api/logic/workflowtriggers), par exemple :

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

Dans le corps, incluez la propriété `KeyType` en tant que `Primary` ou `Secondary`. Cette propriété retourne une URL qui est signée par la clé sécurisée spécifiée.

<a name="restrict-incoming-ip"></a>

### <a name="restrict-incoming-ip-addresses"></a>Limiter les adresses IP entrantes

Avec Signature d’accès partagé, vous pouvez limiter le nombre de clients spécifiques qui peuvent appeler votre application logique.  
Par exemple, si vous gérez votre point de terminaison de requête via Gestion des API Azure, vous pouvez limiter l’application logique à n’accepter la requête que lorsqu’elle provient de l’adresse IP de l’instance de gestion des API. 

#### <a name="set-ip-ranges---azure-portal"></a>Définir des plage d’adresses IP - Portail Azure

Pour configurer cette restriction dans le portail Azure, accédez aux paramètres de votre application logique : 

1. Dans le portail Azure, ouvrez votre application logique dans le Concepteur d’applications logiques. 

1. Dans le menu de votre application logique, sous **Paramètres**, sélectionnez **Paramètres de flux de travail**.

1. Sous **Configuration du contrôle d'accès** > 
**Adresses IP entrantes autorisées**, sélectionnez **Plages d’adresses IP spécifiques**.

1. Sous **Plages d'adresses IP pour les déclencheurs**, spécifiez les plages d’adresses IP acceptées par le déclencheur. Une plage d’adresses IP valide utilise ces formats : *x.x.x.x/x* ou *x.x.x.x-x.x.x.x* 

Si vous souhaitez que votre application logique se déclenche uniquement comme une application logique imbriquée, dans la liste **Adresses IP entrantes autorisées**, sélectionnez **Uniquement les autres Logic Apps**. Cette option écrit un tableau vide dans votre ressource d’application logique : seuls les appels du service Logic Apps (applications logiques parentes) peuvent déclencher l’application logique imbriquée.

> [!NOTE]
> Indépendamment de l’adresse IP, vous pouvez toujours exécuter une application logique qui a un déclencheur basé sur une requête en utilisant `/triggers/{triggerName}/run` via l’API REST Azure ou via Gestion des API. Cependant, ce scénario nécessite encore une authentification auprès de l’API REST Azure, et tous les événements apparaissent dans le journal d’audit Azure. Assurez-vous de définir les stratégies de contrôle d’accès en conséquence.

#### <a name="set-ip-ranges---logic-app-deployment-template"></a>Définir des plages d’adresses IP - modèle de déploiement d’applications logiques

Si vous automatisez les déploiements d’applications logiques en utilisant un [modèle de déploiement Azure Resource Manager](../logic-apps/logic-apps-create-deploy-template.md), vous pouvez définir les plages d’adresses IP dans ce modèle, par exemple :

``` json
{
   "properties": {
      "definition": {},
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
   },
   "type": "Microsoft.Logic/workflows",
}
```

<a name="add-authentication"></a>

### <a name="add-azure-active-directory-oauth-or-other-security"></a>Ajouter Azure Active Directory, OAuth ou une autre sécurité

Pour ajouter des protocoles d’autorisation supplémentaires à votre application logique, pensez à utiliser [Gestion des API Azure](https://azure.microsoft.com/services/api-management/). Ce service fournit une surveillance avancée, une sécurité, une stratégie et une documentation sur n’importe quel point de terminaison et vous permet d’exposer votre application logique sous la forme d’une API. La Gestion des API peut exposer un point de terminaison public ou privé de votre application logique, permettant ainsi d’utiliser Azure Active Directory, OAuth, un certificat ou d’autres normes de sécurité. Lorsque Gestion des API reçoit une requête, le service envoie celle-ci à votre application logique, en effectuant également toutes les transformations ou restrictions nécessaires tout au long du processus. Pour que seule la fonctionnalité Gestion des API déclenche votre application logique, vous pouvez utiliser les paramètres de plage d’adresses IP entrantes de votre application logique. 

<a name="secure-operations"></a>

## <a name="secure-access-to-logic-app-operations"></a>Sécuriser l’accès aux opérations d’application logique

Pour limiter l’exécution de votre application logique à des utilisateurs ou des groupes spécifiques, vous pouvez restreindre l’accès à des tâches telles que la gestion, l’édition et l’affichage. Logic Apps prend en charge la fonctionnalité Azure, [Contrôle d'accès en fonction du rôle](../role-based-access-control/role-assignments-portal.md), que vous pouvez personnaliser ou utiliser pour attribuer des rôles intégrés aux membres dans votre abonnement, par exemple :

* [Contributeur d’application logique](../role-based-access-control/built-in-roles.md#logic-app-contributor) : Les utilisateurs peuvent afficher, modifier et mettre à jour votre application logique. 

* [Opérateur d’application logique](../role-based-access-control/built-in-roles.md#logic-app-operator) : Les utilisateurs peuvent afficher votre application logique et l’historique des exécutions, et activer ou désactiver votre application logique. Ce rôle ne peut pas modifier ou mettre à jour de votre application logique.

Pour empêcher la modification ou la suppression de votre application logique, vous pouvez utiliser le [Verrouillage de la ressource Azure](../azure-resource-manager/resource-group-lock-resources.md). Grâce à cette fonctionnalité, vous pouvez empêcher d’autres utilisateurs de modifier ou de supprimer des ressources de production.

<a name="secure-run-history"></a>

## <a name="secure-access-to-logic-app-run-history"></a>Sécuriser l’accès à l’historique des exécutions d’une application logique

Pour protéger le contenu transmis en tant qu’entrées ou sorties à partir de précédentes exécutions de l’application logique, vous pouvez restreindre l’accès à des plages d’adresses IP spécifiques. Cette fonctionnalité offre un contrôle d’accès plus précis. Toutes les données traitées pendant l’exécution d’une application logique sont chiffrées au repos et lors de leur transit. Lorsque vous demandez l’historique des exécutions d’une application logique, Logic Apps authentifie cette requête et fournit des liens vers les entrées et sorties des requêtes et des réponses dans le flux de travail de votre application logique. Vous pouvez protéger ces liens pour que seules les requêtes provenant d’une adresse IP spécifique renvoient ce contenu. Par exemple, vous pouvez même spécifier une adresse IP telle que `0.0.0.0-0.0.0.0` pour que personne ne puisse accéder aux entrées et sorties. Seul un utilisateur disposant d’autorisations d’administrateur peut supprimer cette restriction, permettant ainsi d’obtenir un accès « juste-à-temps » au contenu de votre application logique.

### <a name="set-ip-ranges---azure-portal"></a>Définir des plage d’adresses IP - Portail Azure

Pour configurer cette restriction dans le portail Azure, accédez aux paramètres de votre application logique :

1. Dans le portail Azure, ouvrez votre application logique dans le Concepteur d’applications logiques. 

1. Dans le menu de votre application logique, sous **Paramètres**, sélectionnez **Paramètres de flux de travail**.

1. Sous **Configuration du contrôle d’accès** > 
   **Adresses IP entrantes autorisées**, sélectionnez **Plages d’adresses IP spécifiques**.

1. Sous **Plages d’adresses IP pour le contenu**, spécifiez les plages d’adresses IP qui peuvent accéder au contenu issu des entrées et sorties. 
   Une plage d’adresses IP valide utilise ces formats : *x.x.x.x/x* ou *x.x.x.x-x.x.x.x* 

### <a name="set-ip-ranges---logic-app-deployment-template"></a>Définir des plages d’adresses IP - modèle de déploiement d’applications logiques

Si vous automatisez les déploiements d’applications logiques en utilisant un [modèle de déploiement Azure Resource Manager](../logic-apps/logic-apps-create-deploy-template.md), vous pouvez définir les plages d’adresses IP dans ce modèle, par exemple :

``` json
{
   "properties": {
      "definition": {},
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
   },
   "type": "Microsoft.Logic/workflows",
}
```

<a name="secure-action-parameters"></a>

## <a name="secure-action-parameters-and-inputs"></a>Sécuriser des entrées et paramètres d’action

Lors d’un déploiement dans différents environnements, vous pouvez paramétrer des éléments spécifiques dans la définition du flux de travail de votre application logique. De cette façon, vous pouvez fournir des entrées basées sur les environnements que vous utilisez, et protéger les informations sensibles. Par exemple, si vous authentifiez des actions HTTP avec [Azure Active Directory](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication), définissez et sécurisez les paramètres qui acceptent l’ID client et la clé secrète client utilisée pour l’authentification. Pour ces paramètres, votre définition d’application logique a sa propre section `parameters`.
Pour accéder aux valeurs de paramètre en cours d’exécution, vous pouvez utiliser l’expression `@parameters('parameterName')` fournie par le [Langage de définition du flux de travail](https://aka.ms/logicappsdocs). 

Pour protéger les paramètres et valeurs que vous ne voulez pas présenter lors de la modification de votre application logique ou de l’affichage de l’historique d’exécution, vous pouvez définir des paramètres avec le type `securestring` et utiliser un encodage si nécessaire. Les paramètres de ce type ne sont pas retournés avec la définition de la ressource, et ne sont pas accessibles lors de l’affichage de la ressource après déploiement.

> [!NOTE]
> Lorsque vous utilisez un paramètre dans les en-têtes ou le corps d’une requête, celui-ci pourrait être visible lors de l’accès à l’historique d’exécution de votre application logique et à la requête HTTP sortante. Veillez à définir également les stratégies d’accès à votre contenu en conséquence.
> Les en-têtes d’autorisation ne sont jamais visibles par le biais d’entrées ou de sorties. Si un secret est utilisé ici, il n’est pas récupérable.

Pour plus d’informations sur la sécurisation de paramètres dans des définitions d’application logique, voir [Sécuriser des paramètres dans des définitions d’application logique](#secure-parameters-workflow) plus loin dans cette page.

Si vous automatisez des déploiements avec des [modèles de déploiement Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md#parameters), vous pouvez également utiliser des paramètres sécurisés dans ces modèles. Par exemple, vous pouvez utiliser des paramètres pour obtenir des secrets de KeyVault lors de la création de votre application logique. Votre définition de modèle de déploiement a sa propre section `parameters`, distincte de la section `parameters` de votre application logique. Pour plus d’informations sur la sécurisation des paramètres dans les modèles de déploiement, voir [Sécuriser des paramètres dans des modèles de déploiement](#secure-parameters-deployment-template) plus loin dans cette page.

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-logic-app-definitions"></a>Sécuriser des paramètres dans des définitions d’application logique

Pour protéger des informations sensibles dans la définition de flux de travail de votre application logique, utilisez des paramètres sécurisés afin que ces informations ne soient pas visibles après l’enregistrement de votre application logique. Par exemple, supposons que vous utilisez une authentification `Basic` dans votre définition d’action HTTP. Cet exemple inclut une section `parameters` qui définit les paramètres pour la définition de l’action, plus une section `authentication` qui accepte les valeurs de paramètre `username` et `password`. Pour fournir des valeurs pour ces paramètres, vous pouvez utiliser un fichier de paramètres séparé, par exemple :

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
               "username": "@parameters('usernameParam')",
               "password": "@parameters('passwordParam')"
            }
         },
         "runAfter": {}
      }
   },
   "parameters": {
      "passwordParam": {
         "type": "securestring"
      },
      "userNameParam": {
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

Si vous utilisez des secrets, vous pouvez les obtenir au moment du déploiement en utilisant un [KeyVault Azure Resource Manager](../azure-resource-manager/resource-manager-keyvault-parameter.md).

<a name="secure-parameters-deployment-template"></a>

### <a name="secure-parameters-in-azure-resource-manager-deployment-templates"></a>Sécuriser des paramètres dans des modèles de déploiement Azure Resource Manager

Cet exemple présente un modèle de déploiement Azure Resource Manager qui utilise plusieurs paramètres de runtime avec le type `securestring` :

* `armTemplatePasswordParam`, qui est entré pour le paramètre `logicAppWfParam` de la définition de l’application logique

* `logicAppWfParam`, qui est entré pour l’action HTTP dans le cadre de l’authentification de base

Cet exemple inclut une section `parameters` interne qui appartient à la définition de flux de travail de votre application logique, et une section `parameters` externe qui appartient à votre modèle de déploiement. Pour spécifier les valeurs d’environnement pour les paramètres, vous pouvez utiliser un fichier de paramètres séparé. 

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "logicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "metadata": {
            "description": "Name of the Logic App."
         }
      },
      "armTemplatePasswordParam": {
         "type": "securestring"
      },
      "logicAppLocation": {
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
         "name": "[parameters('logicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('logicAppLocation')]",
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
                           "username": "@parameters('usernameParam')",
                           "password": "@parameters('logicAppWfParam')"
                        }
                     },
                  "runAfter": {}
                  }
               },
               "parameters": {
                  "logicAppWfParam": {
                     "type": "securestring"
                  },
                  "userNameParam": {
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
               "logicAppWfParam": {
                  "value": "[parameters('armTemplatePasswordParam')]"
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

Si vous utilisez des secrets, vous pouvez les obtenir au moment du déploiement en utilisant un [KeyVault Azure Resource Manager](../azure-resource-manager/resource-manager-keyvault-parameter.md).

<a name="secure-requests"></a>

## <a name="secure-access-to-services-receiving-requests"></a>Sécuriser l’accès aux services recevant des requêtes

Voici quelques façons de sécuriser n’importe quel point d’accès où votre application logique a besoin d’accès et envoie des requêtes.

### <a name="add-authentication-on-outbound-requests"></a>Ajouter l’authentification sur les requêtes sortantes

Lorsque vous utilisez une action HTTP, HTTP + Swagger (API ouverte) ou Webhook, vous pouvez ajouter l’authentification à la requête envoyée par votre application logique. Par exemple, vous pouvez inclure l’authentification de base, l’authentification par certificat ou l’authentification Azure Active Directory. Pour plus d’informations, voir [Authentifier des déclencheurs ou des actions](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

### <a name="restrict-access-to-logic-app-ip-addresses"></a>Limiter l’accès aux adresses IP d’application logique

Tous les appels d’applications logiques proviennent d’adresses IP désignées par région. Vous pouvez ajouter le filtrage afin d’accepter les requêtes provenant uniquement de ces adresses IP. Pour obtenir ces adresses IP, consultez [Limites et configuration d’Azure Logic Apps](logic-apps-limits-and-config.md#configuration).

### <a name="secure-on-premises-connectivity"></a>Sécuriser la connectivité locale

Azure Logic Apps permet l’intégration avec ces services afin de fournir une communication locale sécurisée et fiable.

#### <a name="on-premises-data-gateway"></a>Passerelle de données locale

De nombreux connecteurs gérés d’Azure Logic Apps fournissent des connexions sécurisées aux systèmes locaux, telles que le système de fichiers, SQL, SharePoint, DB2 et d’autres encore. La passerelle envoie les données des sources locales sur des canaux chiffrés via Azure Service Bus. Tout le trafic est initialisé en tant que trafic sortant de l’agent de passerelle sécurisé. Découvrez comment [la passerelle de données sur site fonctionne](logic-apps-gateway-install.md#gateway-cloud-service).

#### <a name="azure-api-management"></a>Gestion des API Azure

[Gestion des API Azure](https://azure.microsoft.com/services/api-management/) fournit des options de connexion locale, telles que l’intégration de réseaux privés virtuels de site à site et ExpressRoute pour un proxy et une communication sécurisés vers les systèmes locaux. Dans le concepteur d’application logique, vous pouvez sélectionner rapidement une API exposée par Gestion des API depuis le flux de travail de votre application logique, offrant ainsi un accès rapide aux systèmes locaux.

## <a name="next-steps"></a>Étapes suivantes

* [Créer un modèle de déploiement](logic-apps-create-deploy-template.md)  
* [Gestion des exceptions](logic-apps-exception-handling.md)  
* [Analyser vos applications logiques](logic-apps-monitor-your-logic-apps.md)  
* [Diagnostiquer des échecs et problèmes d’applications logiques](logic-apps-diagnosing-failures.md)  
