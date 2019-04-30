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
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128598"
---
# <a name="create-azure-resource-manager-templates-for-deploying-logic-apps"></a>Créer des modèles Azure Resource Manager pour déployer des applications logiques

Lorsque vous créez une application logique, vous pouvez développer la définition de votre application logique dans un [modèle Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Vous pouvez ensuite utiliser ce modèle pour l’automatisation des déploiements en définissant les ressources et paramètres que vous voulez utiliser pour le déploiement et de fournir les valeurs de paramètre par le biais une [fichier de paramètres](../azure-resource-manager/resource-group-template-deploy.md#parameter-files).
De cette façon, vous pouvez déployer des applications logiques plus facilement et à n’importe quel environnement ou d’un groupe de ressources Azure que vous souhaitez. 

Azure Logic Apps fournit un [modèle Azure Resource Manager d’applications logiques prédéfinies](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) que vous pouvez réutiliser, non seulement pour la création d’applications logiques, mais également pour définir les ressources et les paramètres à utiliser pour le déploiement. Vous pouvez utiliser ce modèle pour vos propres scénarios professionnels, ou le personnaliser selon vos besoins. En savoir plus sur [structure de modèle Resource Manager et la syntaxe](../azure-resource-manager/resource-group-authoring-templates.md). Pour plus d’informations sur la syntaxe JSON et les propriétés, consultez [Types de ressources Microsoft.Logic](/azure/templates/microsoft.logic/allversions).

Pour plus d’informations sur les modèles Azure Resource Manager, consultez ces articles :

* [Création de modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)
* [Développer des modèles Azure Resource Manager pour la cohérence de cloud](../azure-resource-manager/templates-cloud-consistency.md)

## <a name="logic-app-structure"></a>Structure d’application logique

Votre définition d’application logique a ces sections de base, vous pouvez afficher en basculant à partir de la « vue concepteur » à « view code » ou en utilisant un outil tel que [Azure Resource Explorer](http://resources.azure.com). Définitions d’application logique utiliser Javascript Objet Notation (JSON), par conséquent, pour plus d’informations sur la syntaxe JSON et les propriétés, consultez [les types de ressources Microsoft.Logic](/azure/templates/microsoft.logic/allversions).

* **Ressource d’application logique** : Décrit les informations telles que l’emplacement de votre application logique ou une région, plan de tarification et définition de workflow.

* **Définition de workflow** : Décrit la logique et votre application déclencheurs et actions comment le service Azure Logic Apps s’exécute le flux de travail. En mode code, vous trouverez la définition de flux de travail dans le `definition` section.

* **Connexions** : Si vous utilisez des connecteurs gérés dans votre application logique, le `$connections` section référence des autres ressources qui stockent les métadonnées relatives à ces connexions entre votre application logique et d’autres systèmes ou services, tels que les chaînes de connexion et les jetons d’accès en toute sécurité. À l’intérieur de votre définition d’application logique, les références à ces connexions s’affichent à l’intérieur de votre définition d’application logique `parameters` section.

Pour créer un modèle d’application logique que vous pouvez utiliser avec les déploiements de groupe de ressources Azure, vous devez définir les ressources et paramétrer en fonction des besoins. Par exemple, si vous effectuez un déploiement dans un environnement de développement, de test et de production, vous préférez sans doute utiliser différentes chaînes de connexion à une base de données SQL dans chaque environnement.
Ou bien, vous pourrez procéder au déploiement dans différents abonnements ou groupes de ressources.

## <a name="create-logic-app-deployment-templates"></a>Créer des modèles de déploiement d’application logique

Pour obtenir le moyen le plus simple créer un modèle de déploiement d’application logique valide, utilisez Visual Studio et les outils Azure Logic Apps pour l’extension de Visual Studio. En téléchargeant votre application logique à partir du portail Azure dans Visual Studio, vous obtenez un modèle de déploiement valide que vous pouvez utiliser avec n’importe quel abonnement Azure et l’emplacement. Téléchargement de votre application logique automatiquement ajuste également, la définition d’application logique qui est incorporée dans le modèle.
Pour plus d’informations sur la création et la gestion des applications logiques dans Visual Studio, consultez [créer des applications logiques avec Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) et [gérer des applications logiques avec Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

Autres que Visual Studio ou manuellement en créant votre modèle et les paramètres nécessaires en suivant les instructions dans cette rubrique, vous pouvez également utiliser le [module PowerShell pour créer des modèles d’application logique](https://github.com/jeffhollan/LogicAppTemplateCreator). Ce module open source évalue d’abord votre application logique et toutes les connexions qui utilise l’application logique. Le module génère ensuite les ressources de modèle avec les paramètres nécessaires pour le déploiement. Par exemple, supposons que vous avez une application logique qui reçoit un message d’une file d’attente Azure Service Bus et ajoute des données à une base de données SQL Azure. L’outil de module conserve toute la logique d’orchestration et paramètre les chaînes de connexion SQL et Service Bus afin que vous pouvez définir ces valeurs au moment du déploiement.

> [!IMPORTANT]
> Connexions doivent exister dans le même groupe de ressources Azure que l’application logique.
> Pour le module PowerShell travailler avec n’importe quel Azure client et abonnement accès jeton, utilisez le module avec le [outil client de Azure Resource Manager](https://github.com/projectkudu/ARMClient). Pour plus d’informations, consultez ce [article sur l’outil client Azure Resource Manager](https://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) présente ARMClient de façon plus détaillée.

### <a name="install-powershell-module-for-logic-app-templates"></a>Installer le module PowerShell pour les modèles d’application logique

Pour le moyen le plus simple installer le module à partir de la [PowerShell Gallery](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1), utilisez la commande suivante :

`Install-Module -Name LogicAppTemplate`

Vous pouvez également installer manuellement le module PowerShell :

1. Téléchargez la dernière version [Logic App Template Creator](https://github.com/jeffhollan/LogicAppTemplateCreator/releases).

1. Extrayez le dossier dans votre dossier de module PowerShell, qui est généralement `%UserProfile%\Documents\WindowsPowerShell\Modules`.

### <a name="generate-logic-app-template---powershell"></a>Générer le modèle d’application logique - PowerShell

Une fois PowerShell installé, vous pouvez générer un modèle à l’aide de la commande suivante :

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId` est l’ID d’abonnement Azure. Cette ligne obtient d’abord un jeton d’accès via ARMClient, puis le dirige vers le script PowerShell et enfin crée le modèle dans un fichier JSON.

## <a name="parameters-in-logic-app-templates"></a>Paramètres dans les modèles d’application logique

Après avoir créé votre modèle d’application logique, vous pouvez ajouter et modifier tous les paramètres nécessaires. Votre modèle comporte plusieurs `parameters` section, par exemple : 

* Définition de flux de travail de votre application logique a son propre [ `parameters` section](../logic-apps/logic-apps-workflow-definition-language.md#parameters) dans laquelle vous pouvez définir tous les paramètres de votre application logique utilise pour accepter les entrées au moment du déploiement.

* Votre modèle Resource Manager possède son propre [ `parameters` section](../azure-resource-manager/resource-group-authoring-templates.md#parameters), indépendamment de votre application logique `parameters` section. Par exemple : 

  [!INCLUDE [logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

Par exemple, supposons que votre définition d’application logique fait référence à un ID de ressource qui représente une fonction Azure ou dans un workflow d’application logique imbriquée, et que vous souhaitez déployer cet ID de ressource, ainsi que votre application logique comme un seul déploiement. Vous pouvez ajouter cet ID en tant que ressource dans votre modèle et paramétrer cet ID. Vous pouvez utiliser cette même approche pour les références à des points de terminaison d’API ou OpenAPI personnalisés (anciennement « Swagger ») que vous souhaitez déployer avec chaque groupe de ressources Azure.

Lorsque vous utilisez des paramètres dans votre modèle de déploiement, suivez ces instructions pour le concepteur Logic Apps peut afficher ces paramètres correctement :

* Utiliser des paramètres uniquement dans ces déclencheurs et les actions :

  * Application Azure Functions
  * Imbriqué ou workflow d’application logique enfant
  * Appel de la gestion des API
  * URL d’exécution de connexion d’API
  * Chemin de connexion d’API

* Lorsque vous définissez les paramètres, assurez-vous que vous fournissez des valeurs par défaut à l’aide de la `defaultValue` valeur de propriété, par exemple :

  ```json
  "parameters": {
     "IntegrationAccount": {
        "type":"string",
        "minLength": 1,
        "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource=group-name>/providers/Microsoft.Logic/integrationAccounts/<integration-account-name>"
     }
  },
  ```

* Pour sécuriser ou masquer les informations sensibles dans les modèles, sécuriser vos paramètres. En savoir plus sur [comment utiliser les paramètres sécurisés](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-workflow).

Voici un exemple qui montre comment vous pouvez paramétrer l’action « Envoyer un message » Azure Service Bus :

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

### <a name="reference-dependent-resources"></a>Ressources dépendantes de référence

Si votre application logique nécessite des références aux ressources dépendantes, vous pouvez utiliser [les fonctions de modèle Azure Resource Manager](../azure-resource-manager/resource-group-template-functions.md) dans le modèle de déploiement de votre application logique. Par exemple, supposons que vous souhaitez que votre application logique référence une fonction Azure ou un compte d’intégration avec des définitions pour les partenaires, contrats et autres artefacts déployés en même temps que votre application logique.
Vous pouvez utiliser les fonctions de modèle Resource Manager, tels que `parameters`, `variables`, `resourceId`, `concat`, et ainsi de suite.

Voici un exemple qui montre comment vous pouvez remplacer l’ID de ressource pour une fonction Azure en définissant ces paramètres :

``` json
"parameters": {
   "<Azure-function-name>": {
      "type": "string",
      "minLength": 1,
      "defaultValue": "<Azure-function-name>"
   }
},
```

Voici comment vous utilisez ces paramètres lorsque vous référencez la fonction Azure :

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

Si vous avez un projet de groupe de ressources Azure existant, vous pouvez ajouter votre application logique à ce projet à l’aide de la fenêtre Structure JSON. Vous pouvez également ajouter une autre application logique en même temps que l’application que vous avez créée précédemment.

1. Dans l’Explorateur de solutions, ouvrez le fichier `<template>.json`.

2. À partir de la **vue** menu, sélectionnez **Windows autres** > **structure JSON**.

3. Pour ajouter une ressource dans le fichier de modèle, choisissez **ajouter une ressource** en haut de la fenêtre Structure JSON. Ou, dans la fenêtre Structure JSON, cliquez avec le bouton droit sur **Ressources**, puis sélectionnez **Ajouter une nouvelle ressource**.

   ![Fenêtre Structure JSON](./media/logic-apps-create-deploy-template/jsonoutline.png)

4. Dans la boîte de dialogue **Ajouter une ressource**, recherchez et sélectionnez **Application logique**. Donnez un nom à votre application logique, puis choisissez **Ajouter**.

   ![Ajouter une ressource](./media/logic-apps-create-deploy-template/addresource.png)

## <a name="get-support"></a>Obtenir de l’aide

Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Déployer des modèles d’application logique](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md)
