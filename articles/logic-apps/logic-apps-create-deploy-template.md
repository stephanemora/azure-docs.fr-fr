---
title: Création de développement de modèles pour Azure Logic Apps | Microsoft Docs
description: Créer des modèles Azure Resource Manager pour déployer des applications logiques
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.assetid: 85928ec6-d7cb-488e-926e-2e5db89508ee
ms.date: 10/18/2016
ms.openlocfilehash: 624539557b0bf57e9d919a3a46337f1cf93a4f07
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "62128598"
---
# <a name="create-azure-resource-manager-templates-for-deploying-logic-apps"></a>Créer des modèles Azure Resource Manager pour déployer des applications logiques

Quand vous créez une application logique, vous pouvez étendre la définition de l’application logique dans un [modèle Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Avec ce modèle, vous pouvez ensuite automatiser les déploiements en définissant les ressources et paramètres à utiliser pour un déploiement et en spécifiant les valeurs des paramètres dans un [fichier de paramètres](../azure-resource-manager/resource-group-template-deploy.md#parameter-files).
De cette façon, vous déployez vos applications logiques plus facilement, dans l’environnement ou le groupe de ressources Azure de votre choix. 

Azure Logic Apps fournit un [modèle Azure Resource Manager prédéfini pour les applications logiques](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json). À l’aide de ce modèle, vous pouvez créer des applications logiques, mais aussi définir les ressources et les paramètres à utiliser pour les déployer. Vous pouvez utiliser ce modèle pour vos propres scénarios professionnels, ou le personnaliser selon vos besoins. Découvrez-en plus sur la [structure et syntaxe des modèles Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). Pour plus d’informations sur la syntaxe JSON et les propriétés, consultez [Types de ressources Microsoft.Logic](/azure/templates/microsoft.logic/allversions).

Pour plus d’informations sur les modèles Azure Resource Manager, consultez ces articles :

* [Création de modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)
* [Développer des modèles Azure Resource Manager pour la cohérence du cloud](../azure-resource-manager/templates-cloud-consistency.md)

## <a name="logic-app-structure"></a>Structure d’une application logique

La définition d’une application logique comporte ces sections de base, que vous pouvez afficher en passant du « mode Concepteur » au « mode Code » ou en utilisant un outil comme [Azure Resource Explorer](http://resources.azure.com). Les définitions d’application logique utilisent le format JSON (JavaScript Object Notation). Pour plus d’informations sur la syntaxe et les propriétés JSON, consultez les [types de ressources Microsoft.Logic](/azure/templates/microsoft.logic/allversions).

* **Ressource d’application logique** : spécifie plusieurs informations, telles que l’emplacement ou la région, le plan tarifaire et la définition de workflow de l’application logique.

* **Définition de workflow** : spécifie les déclencheurs et les actions de l’application logique, et la façon dont le service Azure Logic Apps exécute le workflow. En mode Code, la définition de workflow se trouve dans la section `definition`.

* **Connexions** : si vous utilisez des connecteurs managés dans votre application logique, la section `$connections` référence d’autres ressources qui stockent de manière sécurisée les métadonnées concernant les connexions entre l’application logique et d’autres systèmes ou services, comme les chaînes de connexion et les jetons d’accès. Dans la définition de votre application logique, les références à ces connexions se trouvent dans la section `parameters`.

Pour créer un modèle d’application logique utilisable avec des déploiements de groupes de ressources Azure, vous devez définir et paramétrer les ressources comme il convient. Par exemple, si vous effectuez un déploiement dans un environnement de développement, de test et de production, vous préférez sans doute utiliser différentes chaînes de connexion à une base de données SQL dans chaque environnement.
Ou bien, vous pourrez procéder au déploiement dans différents abonnements ou groupes de ressources.

## <a name="create-logic-app-deployment-templates"></a>Créer des modèles de déploiement d’application logique

Le moyen le plus simple de créer un modèle de déploiement d’application logique valide est d’utiliser Visual Studio et l’extension Outils Azure Logic Apps pour Visual Studio. Après avoir téléchargé votre application logique à partir du portail Azure dans Visual Studio, vous disposez d’un modèle de déploiement valide pour tous les abonnements et emplacements Azure. De plus, le téléchargement de l’application logique paramètre automatiquement la définition d’application logique qui est incorporée dans le modèle.
Pour plus d’informations sur la création et la gestion d’applications logiques dans Visual Studio, consultez [Créer des applications logiques avec Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) et [Gérer des applications logiques avec Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

En plus des deux méthodes décrites dans cette rubrique pour créer votre modèle et les paramètres nécessaires, avec Visual Studio ou manuellement, vous pouvez aussi utiliser le [module PowerShell pour créer des modèles d’application logique](https://github.com/jeffhollan/LogicAppTemplateCreator). Ce module open source évalue d’abord votre application logique et toutes les connexions que celle-ci utilise. Il génère ensuite les ressources de modèle avec les paramètres nécessaires pour le déploiement. Par exemple, supposons que votre application logique reçoive un message d’une file d’attente Azure Service Bus et qu’elle ajoute des données à une base de données SQL Azure. L’outil de module conserve toute la logique d’orchestration et paramètre les chaînes de connexion SQL et Service Bus afin que vous puissiez définir ces valeurs au moment du déploiement.

> [!IMPORTANT]
> Les connexions doivent exister dans le même groupe de ressources Azure que l’application logique.
> Pour que le module PowerShell fonctionne avec n’importe quel jeton d’accès locataire et abonnement, utilisez le module avec l’[outil client Azure Resource Manager](https://github.com/projectkudu/ARMClient). Pour plus d’informations, consultez cet [article sur l’outil client Azure Resource Manager](https://blog.davidebbo.com/2015/01/azure-resource-manager-client.html), qui présente ARMClient plus en détail.

### <a name="install-powershell-module-for-logic-app-templates"></a>Installer le module PowerShell pour les modèles d’application logique

La méthode la plus simple pour installer le module à partir de [PowerShell Gallery](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1) est d’utiliser cette commande .

`Install-Module -Name LogicAppTemplate`

Vous pouvez aussi installer manuellement le module PowerShell :

1. Téléchargez la dernière version de [Logic App Template Creator](https://github.com/jeffhollan/LogicAppTemplateCreator/releases).

1. Décompressez le dossier dans le dossier de votre module PowerShell, qui est généralement `%UserProfile%\Documents\WindowsPowerShell\Modules`.

### <a name="generate-logic-app-template---powershell"></a>Générer le modèle d’application logique - PowerShell

Une fois PowerShell installé, vous pouvez générer un modèle à l’aide de la commande suivante :

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId` est l’ID d’abonnement Azure. Cette ligne obtient d’abord un jeton d’accès via ARMClient, puis le dirige vers le script PowerShell et enfin crée le modèle dans un fichier JSON.

## <a name="parameters-in-logic-app-templates"></a>Paramètres dans les modèles d’application logique

Après avoir créé votre modèle d’application logique, vous pouvez ajouter et modifier tous les paramètres nécessaires. Votre modèle a plusieurs sections `parameters`. Par exemple : 

* La définition de workflow de votre application logique a sa propre [section `parameters`](../logic-apps/logic-apps-workflow-definition-language.md#parameters), dans laquelle vous pouvez définir tous les paramètres utilisés par l’application logique pour accepter les entrées au moment du déploiement.

* Votre modèle Resource Manager a sa propre [section `parameters`](../azure-resource-manager/resource-group-authoring-templates.md#parameters), distincte de la section `parameters` de votre application logique. Par exemple :

  [!INCLUDE [logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

Par exemple, supposons que votre définition d’application logique référence un ID de ressource qui représente une fonction Azure ou un workflow d’application logique imbriqué. Vous souhaitez déployer cet ID de ressource avec votre application logique dans un seul et même déploiement. Vous pouvez ajouter cet ID en tant que ressource dans votre modèle et ensuite le paramétrer. La même approche est possible pour les références à des API personnalisées ou à des points de terminaison OpenAPI (auparavant appelés « Swagger ») que vous souhaitez déployer avec chaque groupe de ressources Azure.

Si vous utilisez des paramètres dans votre modèle de déploiement, suivez ces conseils pour que les paramètres s’affichent correctement dans le Concepteur d’applications logiques :

* Utilisez des paramètres uniquement dans les déclencheurs et actions suivants :

  * Application Azure Functions
  * Workflow d’application logique imbriqué ou enfant
  * Appel à Gestion des API
  * URL d’exécution de connexion d’API
  * Chemin de connexion d’API

* Quand vous définissez des paramètres, veillez à spécifier les valeurs par défaut à l’aide de la valeur de propriété `defaultValue`. Par exemple :

  ```json
  "parameters": {
     "IntegrationAccount": {
        "type":"string",
        "minLength": 1,
        "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource=group-name>/providers/Microsoft.Logic/integrationAccounts/<integration-account-name>"
     }
  },
  ```

* Pour sécuriser ou masquer les informations sensibles dans les modèles, sécurisez vos paramètres. Découvrez-en plus sur l’[utilisation des paramètres sécurisés](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-workflow).

L’exemple ci-dessous montre comment paramétrer l’action « Envoyer un message » d’Azure Service Bus :

```json
"Send_message": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['servicebus']['connectionId']"
         },
         // If the `host.runtimeUrl` property appears in your template, 
         // you can remove this property, which is optional, for example:
         "runtimeUrl": {}
      },
      "method": "POST",
      "path": "[concat('/@{encodeURIComponent(''', parameters('<Azure-Service-Bus-queue-name>'), ''')}/messages')]",
      "body": {
         "ContentData": "@{base64(triggerBody())}"
      },
      "queries": {
         "systemProperties": "None"
      }
   },
   "runAfter": {}
},
```

### <a name="reference-dependent-resources"></a>Référencer les ressources dépendantes

Si votre application logique doit référencer des ressources dépendantes, vous pouvez utiliser les [fonctions de modèle Azure Resource Manager](../azure-resource-manager/resource-group-template-functions.md) dans le modèle de déploiement de l’application logique. Par exemple, supposons que votre application logique doive référencer une fonction Azure ou un compte d’intégration avec les définitions pour des partenaires, des contrats et d’autres artefacts à déployer en même temps que votre application logique.
Vous pouvez alors utiliser des fonctions de modèle Resource Manager, comme `parameters`, `variables`, `resourceId`, `concat`, etc.

L’exemple ci-dessous montre comment remplacer l’ID de ressource pour une fonction Azure en définissant ces paramètres :

``` json
"parameters": {
   "<Azure-function-name>": {
      "type": "string",
      "minLength": 1,
      "defaultValue": "<Azure-function-name>"
   }
},
```

Voici comment utiliser ces paramètres pour référencer la fonction Azure :

```json
"MyFunction": {
   "type": "Function",
   "inputs": {
      "body": {},
      "function": {
         "id":"[resourceid('Microsoft.Web/sites/functions','<Azure-Functions-app-name>', parameters('<Azure-function-name>'))]"
      }
   },
   "runAfter": {}
},
```

## <a name="add-logic-app-to-resource-group-project"></a>Ajouter l’application logique à un projet de groupe de ressources

Si vous avez déjà un projet de groupe de ressources Azure, vous pouvez y ajouter votre application logique à partir de la fenêtre Structure JSON. Vous pouvez également ajouter une autre application logique en même temps que l’application que vous avez créée précédemment.

1. Dans l’Explorateur de solutions, ouvrez le fichier `<template>.json`.

2. Dans le menu **Affichage**, sélectionnez **Autres fenêtres** > **Structure JSON**.

3. Pour ajouter une ressource au fichier de modèle, choisissez **Ajouter une ressource** en haut de la fenêtre Structure JSON. Ou, dans la fenêtre Structure JSON, cliquez avec le bouton droit sur **Ressources**, puis sélectionnez **Ajouter une nouvelle ressource**.

   ![Fenêtre Structure JSON](./media/logic-apps-create-deploy-template/jsonoutline.png)

4. Dans la boîte de dialogue **Ajouter une ressource**, recherchez et sélectionnez **Application logique**. Donnez un nom à votre application logique, puis choisissez **Ajouter**.

   ![Ajouter une ressource](./media/logic-apps-create-deploy-template/addresource.png)

## <a name="get-support"></a>Obtenir de l’aide

Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Déployer des modèles d’application logique](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md)
