---
title: Créez et déployez des workflows sur Logic Apps avec Azure Arc
description: Créez et déployez des workflows d’application logique monolocataires qui s’exécutent partout où Kubernetes peut s’exécuter.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, ladolan, reylons, archidda, sopai, azla
ms.topic: how-to
ms.date: 05/25/2021
ms.openlocfilehash: 46b5f12b005eaea36c96f183857311614ddba5bc
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110385756"
---
# <a name="create-and-deploy-single-tenant-based-logic-app-workflows-with-azure-arc-enabled-logic-apps-preview"></a>Créez et déployez des workflows d’application logique monolocataire avec Logic Apps avec Azure Arc (préversion)

> [!NOTE]
> Cette fonctionnalité est en préversion et est soumise aux [conditions d’utilisation supplémentaires](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) des préversions de Microsoft Azure.

Avec Logic Apps avec Azure Arc et le portail Azure, vous pouvez créer et déployer des workflows d’application logique monolocataire dans une infrastructure Kubernetes que vous exploitez et gérez. Vos applications logiques s’exécutent dans un *emplacement personnalisé* qui est mappé à un cluster Kubernetes avec Azure Arc sur lequel vous avez installé et activé le groupe d’extensions de plateforme Azure App Service.

Par exemple, ce cluster peut être un service Azure Kubernetes, un Kubernetes nu ou une autre configuration. Le pack d’extensions vous permet d’exécuter des services de plateforme tels qu’Azure Logic Apps, Azure App Service et Azure Functions sur votre cluster Kubernetes. 

Pour plus d’informations, consultez la documentation suivante :

- [Qu’est-ce que Logic Apps avec Azure Arc ?](azure-arc-enabled-logic-apps-overview.md)
- [Architecture monolocataire ou multilocataire et environnement de service d’intégration](../logic-apps/single-tenant-overview-compare.md)
- [Vue d’ensemble d’Azure Arc](../azure-arc/overview.md)
- [Présentation d’Azure Kubernetes Service](../aks/intro-kubernetes.md)
- [Qu’est-ce que Kubernetes avec Azure Arc ?](../azure-arc/kubernetes/overview.md)
- [Emplacements personnalisés sur Kubernetes avec Azure Arc](../azure-arc/kubernetes/conceptual-custom-locations.md)
- [App Service, Functions et Logic Apps sur Azure Arc (préversion)](../app-service/overview-arc-integration.md)
- [Configurer un cluster Kubernetes avec Azure Arc pour exécuter App Service, Functions et Logic Apps (préversion)](../app-service/manage-create-arc-environment.md)

## <a name="prerequisites"></a>Prérequis

Cette section décrit les conditions préalables courantes pour toutes les approches et tous les outils que vous pouvez utiliser pour créer et déployer vos workflows d’application logique. Les conditions préalables spécifiques à l’outil s’affichent en même temps que les étapes correspondantes.

- Compte Azure avec un abonnement actif. Si vous n’avez pas d’abonnement Azure, [créez un compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Un environnement Kubernetes avec un cluster Kubernetes avec Azure Arc et un *emplacement personnalisé* où vous pouvez héberger et exécuter Azure Logic Apps, Azure App Service et Azure Functions. Assurez-vous que vous utilisez le même emplacement pour votre environnement Kubernetes, l’emplacement personnalisé et la ressource d’application logique.

  Par exemple, pour déployer et exécuter en Europe de l’Ouest, utilisez Europe Ouest comme emplacement pour les trois ressources.

  En outre, lorsque vous créez l’extension de pack App Service sur votre cluster Kubernetes, vous pouvez [modifier le comportement de mise à l’échelle par défaut](#change-scaling) pour l’exécution de vos workflows d’application logique. Lorsque vous créez l’extension à l’aide de la commande Azure CLI [ **`az k8s-extension create`**](/cli/azure/k8s-extension), veillez à inclure le paramètre de configuration `keda.enabled=true` :

  `az k8s-extension create {other-command-options} --configuration-settings "keda.enabled=true"`

  Pour plus d’informations, consultez la documentation suivante :

  * [App Service, Functions et Logic Apps sur Azure Arc (préversion)](../app-service/overview-arc-integration.md)
  * [Extensions de cluster sur Kubernetes avec Azure Arc](../azure-arc/kubernetes/conceptual-extensions.md)
  * [Configurer un cluster Kubernetes avec Azure Arc pour exécuter App Service, Functions et Logic Apps (préversion)](../app-service/manage-create-arc-environment.md)
  * [Modifier le comportement de mise à l’échelle par défaut](#change-scaling)

- Votre propre identité Azure Active Directory (Azure AD)

  Si vos workflows doivent utiliser des connexions hébergées sur Azure, comme Office 365 Outlook ou Stockage Azure, votre application logique doit utiliser une identité Azure AD pour l’authentification. Logic Apps avec Azure Arc peut s’exécuter sur n’importe quelle infrastructure, mais nécessite une identité qui a l’autorisation d’utiliser des connexions hébergées sur Azure. Pour configurer cette identité, créez une inscription d’application dans Azure AD que votre application logique utilise comme identité requise.

  > [!NOTE]
  > La prise en charge des identités managées n’est pas disponible actuellement pour Logic Apps avec Azure Arc.

  Pour créer une inscription d’application Azure Active Directory (Azure AD) à l’aide d’Azure CLI, procédez comme suit :
    1. Créez une inscription d’application à l’aide de la commande [`az ad sp create`](/cli/azure/ad/sp#az_ad_sp_create).
    1. Pour passer en revue tous les détails, exécutez la commande [`az ad sp show`](/cli/azure/ad/sp#az_ad_sp_show).
    1. À partir de la sortie des deux commandes, recherchez et enregistrez l’ID client, l’ID d’objet, l’ID de locataire et les valeurs de clé secrète client que vous devez conserver pour une utilisation ultérieure.

  Pour créer une inscription d’application Azure Active Directory (Azure AD) à l’aide du portail Azure, procédez comme suit :
    1. Créez une nouvelle inscription d’application Azure AD à l’aide du [portail Azure](../active-directory/develop/quickstart-register-app.md).
    1. Une fois la création terminée, recherchez la nouvelle inscription d’application dans le portail.
    1. Dans le menu d’inscription, sélectionnez **Vue d’ensemble**, puis enregistrez l’ID client, l’ID de locataire et les valeurs de clé secrète client.
    1. Pour trouver l’ID d’objet, en regard du champ **Application gérée dans l’annuaire local**, sélectionnez le nom de l’inscription de votre application. Dans la vue Propriétés, copiez l’ID de l’objet.

## <a name="create-and-deploy-logic-apps"></a>Créer et déployer des applications logiques

Selon que vous souhaitez utiliser Azure CLI, Visual Studio Code ou le portail Azure, sélectionnez l’onglet correspondant pour passer en revue les conditions préalables et les étapes spécifiques.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

#### <a name="prerequisites"></a>Prérequis

- [Azure CLI](/cli/azure/install-azure-cli) installé sur votre ordinateur local.
- [Groupe de ressources Azure](#create-resource-group) où créer votre application logique.

Vérifiez votre environnement avant de commencer :

1. Connectez-vous au portail Azure. Vérifiez que votre abonnement est actif en exécutant la commande suivante :

   ```azurecli-interactive
   az login
   ```

1. Vérifiez votre version d’Azure CLI dans un terminal ou une fenêtre de commande en exécutant la commande suivante :

   ```azurecli-interactive
   az --version
   ```

   Pour obtenir la version la plus récente, consultez les [notes de publication les plus récentes](/cli/azure/release-notes-azure-cli?tabs=azure-cli).

1. Si vous ne disposez pas de la dernière version, mettez à jour votre installation en suivant le [guide d’installation pour votre système d’exploitation ou votre plateforme](/cli/azure/install-azure-cli).

#### <a name="install-logic-apps-extension"></a>Installer l’extension Logic Apps

Installez la préversion de l’extension Logic Apps pour Azure CLI :

```azurecli
az extension add --yes --source "https://aka.ms/logicapp-latest-py2.py3-none-any.whl"
```

#### <a name="create-resource-group"></a>Créer un groupe de ressources

Si ne disposez pas encore d’un groupe de ressources pour votre application logique, créez-en un avec la commande `az group create`. Veillez à utiliser le paramètre `--subscription` avec le nom ou l’identificateur de votre abonnement. Par exemple, la commande suivante crée un groupe de ressources nommé `MyResourceGroupName` à l’emplacement `eastus` :

```azurecli
az group create --name MyResourceGroupName --location eastus --subscription MySubscription
```

> [!TIP]
> Vous n’êtes pas obligé d’utiliser le paramètre `--subscription` si vous avez défini un abonnement par défaut pour votre compte Azure.
> Pour définir un abonnement par défaut, exécutez la commande suivante et remplacez `MySubscription` par le nom ou l’identificateur de votre abonnement.
> `az account set --subscription MySubscription`

Une fois votre groupe de ressources créé, la sortie affiche l’état `provisioningState` `Succeeded` :

```output
<...>
   "name": "testResourceGroup",
   "properties": {
      "provisioningState": "Succeeded"
    },
<...>
```

#### <a name="create-logic-app"></a>Créer une application logique

Pour créer une application logique avec Azure Arc à l’aide d’Azure CLI, exécutez la commande `az logicapp create` comme suit :

```azurecli
az logicapp create --resource-group MyResourceGroupName --name MyLogicAppName 
   --storage-account MyStorageAccount --custom-location MyCustomLocation 
   --subscription MySubscription
```

> [!IMPORTANT]
> Veillez à utiliser le même emplacement de ressources (région Azure) que votre emplacement personnalisé et votre environnement Kubernetes. Les emplacements de ressources doivent tous être identiques pour votre application logique, votre emplacement personnalisé et votre environnement Kubernetes. Cette valeur n’est *pas la même* que le *nom* de votre emplacement personnalisé.

Veillez à renseigner les paramètres obligatoires suivants dans votre commande :

| Paramètres | Description |
|------------|-------------|
| `--name -n` | Nom unique pour votre application logique |
| `--resource-group -g` | Nom du [groupe de ressources](../azure-resource-manager/management/manage-resource-groups-cli.md) dans lequel vous souhaitez créer votre application logique. Si vous n’en avez pas, [créez un groupe de ressources](#create-resource-group). |
| `--storage-account -s` | [Compte de stockage](/cli/azure/storage/account) que vous souhaitez utiliser avec votre application logique. Pour les comptes de stockage dans le même groupe de ressources, utilisez une valeur de chaîne. Pour les comptes de stockage dans un groupe de ressources différent, utilisez un ID de ressource. |
|||

Pour créer une application logique dans Azure Arc à l’aide d’une image Azure Container Registry privée, exécutez `az logicapp create` comme suit :

```azurecli
az logicapp create --resource-group MyResourceGroupName --name MyLogicAppName 
   --storage-account MyStorageAccount --subscription MySubscription
   --custom-location MyCustomLocation 
   --deployment-container-image-name myacr.azurecr.io/myimage:tag
   --docker-registry-server-password passw0rd 
   --docker-registry-server-user MyUser
```

#### <a name="show-logic-app-details"></a>Afficher les détails de l’application logique

Pour afficher les détails de votre application logique avec Azure Arc, exécutez la commande `az logicapp show` comme suit :

```azurecli
az logicapp show --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription
```

#### <a name="deploy-logic-app"></a>Déployer l’application logique

Pour déployer votre application logique à l’aide du déploiement zip de Kudu, exécutez la commande `az logicapp deployment source config-zip`. Par exemple :

```azurecli
az logicapp deployment source config-zip --name MyLogicAppName 
   --resource-group MyResourceGroupName 
   --src C:\uploads\v22.zip 
   --subscription MySubscription
```

#### <a name="start-logic-app"></a>Démarrer l’application logique

Pour démarrer votre application logique avec Azure Arc, exécutez la commande `az logicapp start` avec les paramètres requis suivants :

```azurecli
az logicapp start --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription
```

#### <a name="stop-logic-app"></a>Arrêter l’application logique

Pour arrêter votre application logique avec Azure Arc, exécutez la commande `az logicapp stop` avec les paramètres requis suivants :

```azurecli
az logicapp stop --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription
```

#### <a name="restart-logic-app"></a>Redémarrer l’application logique

Pour redémarrer votre application logique avec Azure Arc, exécutez la commande `az logicapp restart` avec les paramètres requis suivants :

```azurecli
az logicapp restart --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription
```

#### <a name="delete-logic-app"></a>Supprimer l’application logique

Pour supprimer votre application logique avec Azure Arc, exécutez la commande `az logicapp delete` avec les paramètres requis suivants :

Par exemple : 

```azurecli
az logicapp delete --name MyLogicAppName --resource-group MyResourceGroupName --subscription MySubscription
```

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Vous pouvez créer, déployer et surveiller vos workflows d’application logique de bout en bout dans Visual Studio Code. Il n’y a aucune modification ou différence dans l’expérience du concepteur entre le développement de workflows d’application logique qui s’exécutent dans Azure Logic Apps monolocataire par rapport à Logic Apps avec Azure Arc.

#### <a name="create-and-deploy-logic-app-workflows"></a>Créer et déployer des workflows d’application logique

1. Pour créer un projet d’application logique, suivez les conditions préalables et les étapes de la documentation [Créer des workflows d’intégration dans Azure Logic Apps monolocataire avec Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md).

1. Lorsque vous êtes prêt à effectuer le déploiement sur Azure, utilisez l’expérience **Déployer dans une application logique** pour créer votre ressource d’application logique dans votre emplacement personnalisé précédemment créé et déployer vos workflows dans le même emplacement.

   1. Dans une zone vide de la fenêtre de votre projet d’application logique, ouvrez le menu contextuel, puis sélectionnez **Déployer dans une application logique**.

   1. Sélectionnez l’abonnement Azure associé à votre emplacement personnalisé.

   1. Pour créer une ressource Logic Apps avec Azure Arc, sélectionnez **Créer une nouvelle application logique dans Azure (avancé)** . Ou vous pouvez sélectionner une ressource d’application logique existante dans la liste et ignorer les étapes suivantes.

   1. Fournissez un nom globalement unique pour votre application logique.

   1. Sélectionnez l’emplacement personnalisé de l’environnement Kubernetes avec Azure Arc avec lequel vous souhaitez effectuer le déploiement. Si vous sélectionnez une région Azure générique à la place, vous créez une ressource d’application logique non Azure Arc qui s’exécute dans Azure Logic Apps monolocataire.

   1. Sélectionnez ou créez un nouveau groupe de ressources à l’emplacement où vous souhaitez déployer l’application logique.

   1. Sélectionnez ou créez un nouveau compte de stockage pour enregistrer l’état et les métadonnées de votre application logique.

   1. Sélectionnez ou créez une ressource Application Insights pour le stockage des journaux des applications pour votre application logique.

   1. Si vous ne l’avez pas fait, configurez votre identité Azure Active Directory (Azure AD) afin que votre application logique puisse authentifier les connexions d’API gérées. Pour plus d’informations, consultez les [prérequis](#prerequisites) de haut niveau.

   1. Entrez l’ID client, l’ID de locataire, l’ID d’objet et la clé secrète client pour votre identité d’Azure AD.

      > [!NOTE]
      > Vous ne devez effectuer cette étape qu’une seule fois. Visual Studio Code met à jour les stratégies d’accès du fichier connections.json de votre projet et vos connexions d’API gérées pour vous.

1. Lorsque vous avez terminé, votre application logique est en ligne et s’exécute dans votre cluster Kubernetes avec Azure Arc, prête à être testée.

### <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

#### <a name="create-and-deploy-logic-app-workflows"></a>Créer et déployer des workflows d’application logique

La fonctionnalité de modification du concepteur basée sur le portail est en cours de développement pour Logic Apps avec Azure Arc. Vous pouvez créer, déployer et afficher vos applications logiques à l’aide du concepteur basé sur le portail, mais vous ne pouvez pas les modifier dans le portail après le déploiement. Pour le moment, vous pouvez créer et modifier un projet d’application logique localement dans Visual Studio Code, puis déployer à l’aide de Visual Studio Code, d’Azure CLI ou de déploiements automatisés.

1. [Dans le portail, créez une ressource **Application logique (Standard)**](create-single-tenant-workflows-azure-portal.md), mais veillez à utiliser l’emplacement personnalisé que vous avez créé précédemment comme emplacement de votre application.

   > [!IMPORTANT]
   > Les emplacements de votre ressource d’application logique, de votre emplacement personnalisé et de votre environnement Kubernetes doivent tous être identiques.

   Par défaut, la ressource **Application logique (Standard)** s’exécute dans Azure Logic Apps monolocataire. Toutefois, pour Logic Apps avec Azure Arc, votre ressource d’application logique s’exécute à l’emplacement personnalisé que vous avez créé pour votre environnement Kubernetes. En outre, vous n’avez pas besoin de créer un plan App Service, qui est créé pour vous.

1. [Modifiez et déployez l’application logique à l’aide de Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md).

1. Une fois que vous avez créé et déployé votre application logique, vous pouvez surveiller et afficher vos workflows comme d’habitude à l’aide du portail ou d’Application Insights.

   L’expérience du portail pour les applications logiques déployées est actuellement disponible en mode lecture seule, ce qui signifie que vous ne pouvez pas modifier vos workflows ou paramètres d’application. Toutefois, vous pouvez toujours afficher l’historique des exécutions, l’historique des déclencheurs et d’autres informations sur vos applications. Pour le moment, pour mettre à jour vos applications logiques, vous pouvez utiliser Azure CLI, Visual Studio Code ou des déploiements automatisés.

---

## <a name="set-up-connection-authentication"></a>Configurer l’authentification de connexion

Actuellement, les clusters Kubernetes avec Azure Arc ne prennent pas en charge l’utilisation de l’identité managée d’une application logique pour authentifier les connexions d’API gérées. Vous créez ces connexions gérées et hébergées par Azure quand vous utilisez des connecteurs managés dans vos workflows.

Au lieu de cela, vous devez créer votre propre inscription d’application dans Azure Active Directory (Azure AD). Vous pouvez ensuite utiliser cette inscription d’application comme identité pour les applications logiques déployées et exécutées dans Logic Apps avec Azure Arc. Pour plus d’informations, consultez les [prérequis de haut niveau](#prerequisites).

À partir de l’inscription de votre application, vous avez besoin de l’ID client, de l’ID d’objet, de l’ID de locataire et de la clé secrète client. Si vous utilisez Visual Studio Code pour déployer, vous disposez d’une expérience intégrée pour configurer votre application logique avec une identité Azure AD. Pour plus d’informations, consultez [Créer et déployer des workflows d’application logique - Visual Studio Code](#create-and-deploy-logic-apps).

Toutefois, si vous utilisez Visual Studio Code pour le développement, mais que vous utilisez des pipelines Azure CLI ou automatisés pour le déploiement, procédez comme suit.

### <a name="configure-connection-and-app-settings-in-your-project"></a>Configurer les paramètres de connexion et d’application dans votre projet

1. Dans le fichier **connections.js** de votre projet d’application logique, recherchez l’objet `authentication` de la connexion managée. Remplacez le contenu de cet objet par les informations d’inscription de votre application, que vous avez générées précédemment dans les [prérequis de haut niveau](#prerequisites) :

   ```json
   "authentication": {
      "type": "ActiveDirectoryOAuth",
      "audience": "https://management.core.windows.net/",
      "credentialType": "Secret",
      "clientId": "@appsetting('WORKFLOWAPP_AAD_CLIENTID')",
      "tenant": "@appsetting('WORKFLOWAPP_AAD_TENANTID')",
      "secret": "@appsetting('WORKFLOWAPP_AAD_CLIENTSECRET')"
   } 
   ```

1. Dans le fichier **local.settings.json** de votre projet d’application logique, ajoutez l’ID client, l’ID d’objet, l’ID de locataire et la clé secrète client. Après le déploiement, ces paramètres deviennent les paramètres de votre application logique.

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         <...>
         "WORKFLOWAPP_AAD_CLIENTID":"<my-client-ID>",
         "WORKFLOWAPP_AAD_OBJECTID":"<my-object-ID",
         "WORKFLOWAPP_AAD_TENANTID":"<my-tenant-ID>",
         "WORKFLOWAPP_AAD_CLIENTSECRET":"<my-client-secret>"
      }
   }
   ```

> [!IMPORTANT]
> Pour les scénarios ou environnements de production, veillez à protéger et à sécuriser ces secrets et informations sensibles, par exemple en utilisant un [coffre de clés](../app-service/app-service-key-vault-references.md).

### <a name="add-access-policies"></a>Ajouter des stratégies d’accès

Dans Azure Logic Apps monolocataire, chaque application logique a une identité à laquelle les stratégies d’accès accordent des autorisations pour utiliser les connexions managées et hébergées par Azure. Vous pouvez configurer ces stratégies d’accès à l’aide des déploiements d’infrastructure ou du portail Azure.

#### <a name="arm-template"></a>Modèle ARM

Dans votre modèle Azure Resource Manager (ARM), incluez la définition de ressource suivante pour *chaque* connexion d’API managée et fournissez les informations suivantes :

| Paramètre | Description |
|-----------|-------------|
| <*connection-name*> | Nom de votre connexion d’API managée, par exemple `office365` |
| <*object-ID*> | L’ID d’objet de votre identité Azure AD, précédemment enregistré à partir de l’inscription de votre application |
| <*ID de locataire*> | L’ID de locataire de votre identité Azure AD, précédemment enregistré à partir de l’inscription de votre application |
|||

```json
{
   "type": "Microsoft.Web/connections/accessPolicies",
   "apiVersion": "2016-06-01",
   "name": "[concat('<connection-name>'),'/','<object-ID>')]",
   "location": "<location>",
   "dependsOn": [
      "[resourceId('Microsoft.Web/connections', parameters('connection_name'))]"
   ],
   "properties": {
      "principal": {
         "type": "ActiveDirectory",
         "identity": {
            "objectId": "<object-ID>",
            "tenantId": "<tenant-ID>"
         }
      }
   }
}
```

Pour plus d’informations, consultez la documentation [Microsoft.Web/connections/accesspolicies (modèle ARM)](/templates/microsoft.web/connections?tabs=json). 

#### <a name="azure-portal"></a>Portail Azure

Pour cette tâche, utilisez votre ID client précédemment enregistré en tant qu’*ID d’application*.

1. Dans le portail Azure, recherchez et sélectionnez votre application logique. Dans le menu de votre application logique, sous **Workflows**, sélectionnez **Connexions**, ce qui répertorie toutes les connexions dans les workflows de votre application logique.

1. Sous **Connexions d’API**, sélectionnez une connexion, `office365` dans cet exemple.

1. Dans le menu de la connexion, sous **Paramètres**, sélectionnez **Stratégies d’accès** > **Ajouter**.
 
1. Dans le volet **Ajouter une stratégie d’accès**, dans la zone de recherche, recherchez et sélectionnez votre ID client précédemment enregistré.

1. Une fois que vous avez terminé, sélectionnez **Ajouter**.

1. Répétez ces étapes pour chaque connexion Azure hébergée dans votre application logique.

## <a name="automate-devops-deployment"></a>Automatiser le déploiement de DevOps

Pour générer et déployer vos applications logiques avec Azure Arc, vous pouvez utiliser les mêmes pipelines et processus que pour les [applications logiques monolocataires](single-tenant-overview-compare.md). Pour automatiser les déploiements d’infrastructure à l’aide de pipelines pour DevOps, apportez les modifications suivantes au niveau de l’infrastructure pour les déploiements de conteneur et non conteneur.

### <a name="standard-deployment-non-container"></a>Déploiement standard (non conteneur)

Si vous utilisez zip deploy pour le déploiement d’applications logiques, vous n’avez pas besoin de configurer un registre Docker pour héberger des images de conteneur. Bien que Logic Apps sur Kubernetes s’exécute techniquement sur des conteneurs, Logic Apps avec Azure Arc gère ces conteneurs pour vous. Pour ce scénario, effectuez les tâches suivantes lors de la configuration de votre infrastructure :

- Informez le fournisseur de ressources que vous créez une application logique sur Kubernetes.
- Incluez un plan App Service avec votre déploiement. Pour plus d’informations, consultez [Inclure un plan App Service avec le déploiement](#include-app-service-plan).

Dans votre [modèle Azure Resource Manager (ARM)](../azure-resource-manager/templates/overview.md), incluez les valeurs suivantes :

| Élément | Propriété JSON | Description |
|------|---------------|-------------|
| Emplacement | `location` | Veillez à utiliser le même emplacement de ressources (région Azure) que votre emplacement personnalisé et votre environnement Kubernetes. Les emplacements de votre ressource d’application logique, de votre emplacement personnalisé et de votre environnement Kubernetes doivent tous être identiques. <p><p>**Remarque** : Cette valeur n’est pas la même que le *nom* de votre emplacement personnalisé. |
| Genre d’application | `kind` | Le type d’application que vous déployez afin que la plateforme Azure puisse identifier votre application. Pour Azure Logic Apps, ces informations ressemblent à l’exemple suivant : `kubernetes,functionapp,logicapp,linux` |
| Emplacement étendu | `extendedLocation` | Cet objet nécessite le `"name"` de votre *emplacement personnalisé* pour votre environnement Kubernetes et `"type"` doit être défini sur `"CustomLocation"`. |
| ID de ressource du plan d’hébergement | `serverFarmId` | L’ID de ressource du plan App Service associé, mis en forme comme suit : <p><p>`"/subscriptions/{subscriptionID}/resourceGroups/{groupName}/providers/Microsoft.Web/serverfarms/{appServicePlanName}"` |
| Chaîne de connexion de stockage | `AzureWebJobsStorage` | La chaîne de connexion de votre compte de stockage <p><p>**Important** : Vous devez fournir la chaîne de connexion pour votre compte de stockage dans votre modèle ARM. Pour les scénarios ou environnements de production, veillez à protéger et à sécuriser ces secrets et informations sensibles, par exemple en utilisant un coffre de clés. |
||||

#### <a name="arm-template"></a>Modèle ARM

L’exemple suivant décrit un exemple de définition de ressource Logic Apps avec Azure Arc que vous pouvez utiliser dans votre modèle ARM. Pour plus d’informations, consultez la documentation [Microsoft.Web/sites template format (JSON)](/templates/microsoft.web/sites?tabs=json).

```json
{
   "type": "Microsoft.Web/sites",
   "apiVersion": "2020-12-01",
   "name": "[parameters('name')]",
   "location": "[parameters('location')]",
   "kind": "kubernetes,functionapp,logicapp,linux",
   "extendedLocation": {
      "name": "[parameters('customLocationId')]",
      "type": "CustomLocation"
    },
   "properties": {
      "clientAffinityEnabled": false,
      "name": "[parameters('name')]",
      "serverFarmId": "<hosting-plan-ID>",
      "siteConfig": {
         "appSettings": [
            {
               "name": "FUNCTIONS_EXTENSION_VERSION",
               "value": "~3"
            },
            {
               "name": "FUNCTIONS_WORKER_RUNTIME",
               "value": "node"
            },
            {
               "name": "AzureWebJobsStorage",
               "value": "<storage-connection-string>"
            },
            {
               "name": "AzureFunctionsJobHost__extensionBundle__id",
               "value": "Microsoft.Azure.Functions.ExtensionBundle.Workflows"
            },
            {
               "name": "AzureFunctionsJobHost__extensionBundle__version",
               "value": "[1.*, 2.0.0)"
            },
            {
               "name": "APP_KIND",
               "value": "logicapp"
            }
         ],
         "use32BitWorkerProcess": "[parameters('use32BitWorkerProcess')]",
         "linuxFxVersion": "Node|12"
      }
   }
}
```

### <a name="container-deployment"></a>Déploiement de conteneur

Si vous préférez utiliser les outils de conteneur et les processus de déploiement, vous pouvez mettre en conteneur vos applications logiques et les déployer sur Logic Apps avec Azure Arc. Pour ce scénario, effectuez les tâches de haut niveau suivantes lors de la configuration de votre infrastructure :

- Configurez un registre Docker pour héberger vos images de conteneur.
- Informez le fournisseur de ressources que vous créez une application logique sur Kubernetes.
- Dans votre modèle de déploiement, pointez sur le registre Docker et l’image de conteneur dans laquelle vous envisagez de déployer. Azure Logic Apps monolocataire utilise ces informations pour récupérer l’image de conteneur à partir de votre registre Docker.
- Incluez un plan App Service avec votre déploiement. Pour plus d’informations, consultez [Inclure un plan App Service avec le déploiement](#include-app-service-plan).

Dans votre [modèle Azure Resource Manager (ARM)](../azure-resource-manager/templates/overview.md), incluez les valeurs suivantes :

| Élément | Propriété JSON | Description |
|------|---------------|-------------|
| Emplacement | `location` | Veillez à utiliser le même emplacement de ressources (région Azure) que votre emplacement personnalisé et votre environnement Kubernetes. Les emplacements de ressources doivent tous être identiques pour votre application logique, votre emplacement personnalisé et votre environnement Kubernetes. <p><p>**Remarque** : Cette valeur n’est *pas la même* que le *nom* de votre emplacement personnalisé. |
| Genre d’application | `kind` | Le type d’application que vous déployez afin que la plateforme Azure puisse identifier votre application. Pour Azure Logic Apps, ces informations ressemblent à l’exemple suivant : `kubernetes,functionapp,logicapp,container` |
| Emplacement étendu | `extendedLocation` | Cet objet nécessite le `"name"` de votre *emplacement personnalisé* pour votre environnement Kubernetes et `"type"` doit être défini sur `"CustomLocation"`. |
| Nom du conteneur | `linuxFxVersion` | Nom de votre conteneur, mis en forme comme suit : `DOCKER\|<container-name>` |
| ID de ressource du plan d’hébergement | `serverFarmId` | L’ID de ressource du plan App Service associé, mis en forme comme suit : <p><p>`"/subscriptions/{subscriptionID}/resourceGroups/{groupName}/providers/Microsoft.Web/serverfarms/{appServicePlanName}"` |
| Chaîne de connexion de stockage | `AzureWebJobsStorage` | La chaîne de connexion de votre compte de stockage <p><p>**Important** : Quand vous effectuez un déploiement sur un conteneur Docker, vous devez fournir la chaîne de connexion de votre compte de stockage dans votre modèle ARM. Pour les scénarios ou environnements de production, veillez à protéger et à sécuriser ces secrets et informations sensibles, par exemple en utilisant un coffre de clés. |
||||

Pour référencer votre registre Docker et votre image de conteneur, incluez ces valeurs dans votre modèle :

| Élément | Propriété JSON | Description |
|------|---------------|-------------|
| URL du serveur de registre Docker | `DOCKER_REGISTRY_SERVER_URL` | URL du serveur de registre Docker |
| Serveur de registre Docker | `DOCKER_REGISTRY_SERVER_USERNAME` | Nom d’utilisateur pour accéder au serveur de registre Docker |
| Mot de passe du serveur de registre Docker | `DOCKER_REGISTRY_SERVER_PASSWORD` | Mot de passe pour accéder au serveur de registre Docker |
||||

#### <a name="arm-template"></a>Modèle ARM

L’exemple suivant décrit un exemple de définition de ressource Logic Apps avec Azure Arc que vous pouvez utiliser dans votre modèle ARM. Pour plus d’informations, consultez la documentation [Microsoft.Web/sites template format (modèle ARM)](/templates/microsoft.web/sites?tabs=json).

```json
{
   "type": "Microsoft.Web/sites",
   "apiVersion": "2020-12-01",
   "name": "[parameters('name')]",
   "location": "[parameters('location')]",
   "kind": " kubernetes,functionapp,logicapp,container",
   "extendedLocation": {
      "name": "[parameters('customLocationId')]",
      "type": "CustomLocation"
    },
   "properties": {
      "name": "[parameters('name')]",
      "clientAffinityEnabled": false,
      "serverFarmId": "<hosting-plan-ID>",
      "siteConfig": {
         "appSettings": [
            {
               "name": "FUNCTIONS_EXTENSION_VERSION",
               "value": "~3"
            },
            {
               "name": "FUNCTIONS_WORKER_RUNTIME",
               "value": "node"
            },
            {
               "name": "AzureWebJobsStorage",
               "value": "<storage-connection-string>"
            },
            {
               "name": "AzureFunctionsJobHost__extensionBundle__id",
               "value": "Microsoft.Azure.Functions.ExtensionBundle.Workflows"
            },
            {
               "name": "AzureFunctionsJobHost__extensionBundle__version",
               "value": "[1.*, 2.0.0)"
            },
            {
               "name": "APP_KIND",
               "value": "logicapp"
            }, 
            {
               "name": "DOCKER_REGISTRY_SERVER_URL",
               "value": "<docker-registry-server-URL>"
            },
            { 
               "name": "DOCKER_REGISTRY_SERVER_USERNAME",
               "value": "<docker-registry-server-username>"
            },
            {
               "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
               "value": "<docker-registry-server-password>"
            }
         ],
         "use32BitWorkerProcess": "[parameters('use32BitWorkerProcess')]",
         "linuxFxVersion": "DOCKER|<container-name>"
      }
   }
}
```

<a name="include-app-service-plan"></a>

### <a name="include-app-service-plan-with-deployment"></a>Inclure un plan App Service avec le déploiement

Que vous disposiez d’un déploiement standard ou de conteneur, vous devez inclure un plan App Service avec votre déploiement. Bien que ce plan devienne moins pertinent dans un environnement Kubernetes, les déploiements standard et de conteneur requièrent toujours un plan App Service.

Bien que les autres options de création gèrent habituellement la mise en service de la ressource Azure pour ce plan, si vos déploiements utilisent des modèles d’« infrastructure en tant que code », vous devez créer explicitement la ressource Azure pour le plan. La ressource du plan d’hébergement ne change pas, uniquement les informations `sku`.

Dans votre [modèle Azure Resource Manager (ARM)](../azure-resource-manager/templates/overview.md), incluez les valeurs suivantes :

| Élément | Propriété JSON | Description |
|------|---------------|-------------|
| Emplacement | `location` | Veillez à utiliser le même emplacement de ressources (région Azure) que votre emplacement personnalisé et votre environnement Kubernetes. Les emplacements de votre ressource d’application logique, de votre emplacement personnalisé et de votre environnement Kubernetes doivent tous être identiques. <p><p>**Remarque** : Cette valeur n’est pas la même que le *nom* de votre emplacement personnalisé. |
| Type | `kind` | Type de plan app service déployé qui doit être `kubernetes,linux` |
| Emplacement étendu | `extendedLocation` | Cet objet nécessite le `"name"` de votre *emplacement personnalisé* pour votre environnement Kubernetes et `"type"` doit être défini sur `"CustomLocation"`. |
| Nom du plan d’hébergement | `name` | Le nom du plan App Service |
| Niveau de plan | `sku: tier` | Le niveau de plan App Service, qui est `K1` |
| Nom du plan | `sku: name` | Nom du plan App Service, qui est `Kubernetes` |
||||

#### <a name="arm-template"></a>Modèle ARM

L’exemple suivant décrit une définition de ressource de plan App Service que vous pouvez utiliser avec le déploiement de votre application. Pour plus d’informations, consultez la documentation [Microsoft.Web/serverfarms template format (modèle ARM)](/templates/microsoft.web/serverfarms?tabs=json).

```json
{
   "type": "Microsoft.Web/serverfarms",
   "apiVersion": "2020-12-01",
   "location": "<location>",
   "name": "<hosting-plan-name>",
   "kind": "kubernetes,linux",
   "extendedLocation": {
      "name": "[parameters('customLocationId')]",
      "type": "CustomLocation"
   },
   "sku": {
      "tier": "K1",
      "name": "Kubernetes", 
      "capacity": 1
   },
   "properties": {
      "kubeEnvironmentProfile": {
         "id": "[parameters('kubeEnvironmentId')]"
      }
   }
}
```

<a name="change-scaling"></a>

## <a name="change-default-scaling-behavior"></a>Modifier le comportement de mise à l’échelle par défaut

Logic Apps avec Azure Arc gère automatiquement la mise à l’échelle de vos applications logiques en fonction du nombre de *travaux* dans la file d’attente du stockage principal. Toutefois, vous pouvez modifier le comportement de mise à l’échelle par défaut.

Dans une application logique, la définition de workflow spécifie la séquence d’actions à exécuter. Chaque fois qu’une exécution de workflow est déclenchée, le runtime Azure Logic Apps crée un *travail* pour chaque type d’action dans la définition de workflow. Le runtime organise ensuite ces travaux dans un *séquenceur de travaux*. Ce séquenceur orchestre l’exécution des tâches pour la définition de workflow, mais le moteur d’orchestration de travail Azure Logic Apps sous-jacent exécute chaque travail.

Pour les workflows avec état, le moteur d’orchestration de travail utilise des messages de file d’attente de stockage pour planifier des travaux dans les séquenceurs de travaux. En arrière-plan, les *répartiteurs de tâches* (ou *instances de travail de répartiteur*) surveillent ces files d’attente de travaux. Le moteur d’orchestration utilise un nombre minimal et maximal d’instances de travail par défaut pour surveiller les files d’attente de travaux. Pour les workflows sans état, le moteur d’orchestration conserve complètement les états d’action en mémoire.

Pour modifier le comportement de mise à l’échelle par défaut, vous spécifiez des nombres minimum et maximum d’instances de travail qui analysent les files d’attente de travaux.

### <a name="prerequisites-to-change-scaling"></a>Conditions préalables à la modification de la mise à l’échelle

Sur votre cluster Kubernetes avec Arc, votre extension de pack App Service créée précédemment doit avoir la propriété `keda.enabled` définie sur `true`. Pour plus d’informations, consultez les [prérequis de haut niveau](#prerequisites).

### <a name="change-scaling-threshold"></a>Modifier le seuil de mise à l’échelle

Dans Logic Apps avec Azure Arc, la longueur de la file d’attente de travaux déclenche un événement de mise à l’échelle et définit un seuil pour la fréquence des mises à l’échelle pour votre application logique. Vous pouvez modifier la longueur de la file d’attente, avec la valeur par défaut définie sur `20` travaux. Pour une mise à l’échelle moins fréquente, augmentez la longueur de la file d’attente. Pour une mise à l’échelle plus fréquente, diminuez la longueur de la file d’attente. Ce processus peut nécessiter des expérimentations de votre part.

Pour modifier la longueur de la file d’attente, dans le fichier **host.json** au niveau de la racine du projet d’application logique, définissez la propriété `Runtime.ScaleMonitor.KEDA.TargetQueueLength`, par exemple :

```json
"extensions": {
   "workflow": {
      "settings": {
         "Runtime.ScaleMonitor.KEDA.TargetQueueLength": "10"
      }
   }
}
```

### <a name="change-throughput-maximum"></a>Modifier le débit maximal

Sur une ressource d’application logique existante, vous pouvez modifier le nombre maximal d’instances de travail, dont la valeur par défaut est `2`. Cette valeur détermine la limite supérieure du nombre d’instances de worker pouvant surveiller les files d’attente de travaux.

Pour modifier cette valeur maximale, utilisez Azure CLI (création d’application logique uniquement) et le portail Azure.

#### <a name="azure-cli"></a>Azure CLI

Pour une nouvelle application logique, exécutez la commande Azure CLI `az logicapp create`, par exemple :

```azurecli
az logicapp create --resource-group MyResourceGroupName 
   --name MyLogicAppName --storage-account MyStorageAccount 
   --custom-location --subscription MySubscription  MyCustomLocation 
   [--plan MyHostingPlan] [--min-worker-count 1] [--max-worker-count 4]
```

Pour configurer le nombre maximal d’instances, utilisez le paramètre `--settings` :

```azurecli
az logicapp config appsettings set --name MyLogicAppName 
   --resource-group MyResourceGroupName 
   --settings "K8SE_APP_MAX_INSTANCE_COUNT=10" 
   --subscription MySubscription
```

#### <a name="azure-portal"></a>Portail Azure

Dans les paramètres de votre application logique monolocataire, ajoutez ou modifiez la valeur du paramètre `K8SE_APP_MAX_INSTANCE_COUNT` en procédant comme suit :

1. Dans le portail Azure, recherchez et ouvrez votre application logique monolocataire.
1. Dans le menu de l’application logique, sous **Paramètres**, sélectionnez **Configuration**.
1. Dans le volet **Configuration**, sous **Paramètres de l’application**, ajoutez un nouveau paramètre d’application ou modifiez la valeur existante, si elle est déjà ajoutée.

   1. Sélectionnez **Nouveau paramètre d’application**, puis ajoutez le paramètre `K8SE_APP_MAX_INSTANCE_COUNT` avec la valeur maximale que vous souhaitez.

   1. Modifiez la valeur existante pour le paramètre `K8SE_APP_MAX_INSTANCE_COUNT`.

1. Lorsque vous avez terminé, enregistrez les modifications.

### <a name="change-throughput-minimum"></a>Modifier le débit minimal

Sur une ressource d’application logique existante, vous pouvez modifier le nombre minimal d’instances de travail, dont la valeur par défaut est `1`. Cette valeur détermine la limite inférieure du nombre d’instances de worker pouvant surveiller les files d’attente de travaux. Pour une haute disponibilité ou des performances élevées, augmentez cette valeur.

Pour modifier cette valeur minimale, utilisez Azure CLI ou le portail Azure.

#### <a name="azure-cli"></a>Azure CLI

Pour une ressource d’application logique existante, exécutez la commande Azure CLI `az logicapp scale`, par exemple :

```azurecli
az logicapp scale --name MyLogicAppName --resource-group MyResourceGroupName 
   --instance-count 5 --subscription MySubscription
```

Pour une nouvelle application logique, exécutez la commande Azure CLI `az logicapp create`, par exemple :

```azurecli
az logicapp create --resource-group MyResourceGroupName --name MyLogicAppName 
   --storage-account MyStorageAccount --custom-location 
   --subscription MySubscription MyCustomLocation 
   [--plan MyHostingPlan] [--min-worker-count 2] [--max-worker-count 4]
```

#### <a name="azure-portal"></a>Portail Azure

Dans les paramètres de votre application logique monolocataire, modifiez la valeur de la propriété **Scale out** en procédant comme suit :

1. Dans le portail Azure, recherchez et ouvrez votre application logique monolocataire.
1. Dans le menu de l’application logique, sous **Paramètres**, sélectionnez **Scale out**.
1. Dans le volet **Scale out**, faites glisser le curseur Nombre minimal d’instances vers la valeur de votre choix.
1. Lorsque vous avez terminé, enregistrez les modifications.

## <a name="troubleshoot-problems"></a>Résoudre les problèmes

Pour obtenir plus d’informations sur les applications logiques déployées, essayez les options suivantes :

### <a name="access-app-settings-and-configuration"></a>Accéder aux paramètres et à la configuration de l’application

Pour accéder aux paramètres de votre application, exécutez la commande Azure CLI suivante :

```azurecli
az logicapp config appsettings list --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription
```

Pour configurer un paramètre d’application, exécutez la commande `az logicapp config appsettings set` comme suit. Veillez à utiliser le paramètre `--settings` avec le nom et la valeur de votre paramètre.

```azurecli
az logicapp config appsettings set --name MyLogicAppName 
   --resource-group MyResourceGroupName 
   --settings "MySetting=1" 
   --subscription MySubscription
```

Pour supprimer un paramètre d’application, exécutez la commande `az logicapp config appsettings delete` comme suit. Veillez à utiliser le paramètre `--setting-names` avec le nom du paramètre que vous souhaitez supprimer.

```azurecli
az logicapp config appsettings delete --name MyLogicAppName 
   --resource-group MyResourceGroupName 
   --setting-names MySetting 
   --subscription MySubscription
```

### <a name="view-logic-app-properties"></a>Afficher les propriétés de l’application logique

Pour afficher les informations et les propriétés de votre application, exécutez la commande Azure CLI suivante : 

```azurecli
az logicapp show --name MyLogicAppName --resource-group MyResourceGroupName --subscription MySubscription
```

### <a name="monitor-workflow-activity"></a>Surveiller l’activité d’un workflow

Pour afficher l’activité d’un workflow dans votre application logique, procédez comme suit : 

1. Dans le portail Azure, recherchez et ouvrez votre application logique déployée.

1. Dans le menu de l’application logique, sélectionnez  **Workflows**, puis votre workflow. 

1. Dans le menu du workflow, sélectionnez  **Surveiller**.

### <a name="collect-logs"></a>Collecter les journaux d’activité

Pour obtenir des données journalisées sur votre application logique, activez Application Insights sur votre application logique si ce n’est pas déjà le cas.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur [Logic Apps avec Azure Arc](azure-arc-enabled-logic-apps-overview.md)
