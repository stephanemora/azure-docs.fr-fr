---
title: Créer des paramètres pour les workflows dans Azure Logic Apps monolocataire
description: Définissez des paramètres pour les valeurs qui diffèrent dans les workflows entre les environnements de déploiement dans Azure Logic Apps monolocataire.
services: logic-apps
ms.suite: integration
ms.reviewer: azla
ms.topic: how-to
ms.date: 06/08/2021
ms.openlocfilehash: 7de89605f86e47b3062ec07160288ab14584f42e
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111747416"
---
# <a name="create-parameters-for-values-that-change-in-workflows-across-environments-for-single-tenant-azure-logic-apps"></a>Créer des paramètres pour les valeurs qui changent dans les workflows entre les environnements pour Azure Logic Apps monolocataire

Dans Azure Logic Apps, vous pouvez utiliser des paramètres pour extraire les valeurs qui peuvent changer d’un environnement à l’autre. En définissant des paramètres à utiliser dans vos workflows, vous pouvez d’abord vous concentrer sur la conception de vos workflows, puis insérer ultérieurement vos variables propres à l’environnement.

Dans Azure Logic Apps *multilocataire*, vous pouvez créer et référencer des paramètres dans le concepteur de workflow, puis définir les variables dans vos fichiers de paramètres et modèle ARM (Azure Resource Manager). Les paramètres sont définis et activés au moment du déploiement. Ainsi, même si vous ne devez changer qu’une seule variable, vous devez redéployer le modèle ARM de votre application logique.

Dans Azure Logic Apps *monolocataire*, vous pouvez utiliser des variables d’environnement au moment de l’exécution et du déploiement avec des paramètres et des paramètres d’application. Cet article explique comment modifier, appeler et référencer des variables d’environnement avec la nouvelle expérience de paramètres monolocataires.

## <a name="prerequisites"></a>Prérequis

- Un compte et un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [vous inscrire pour obtenir un compte Azure gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Un [workflow d’application logique hébergé dans Azure Logic Apps monlocataire](single-tenant-overview-compare.md).

  Si vous n’avez pas d’application logique, [créez votre application logique (Standard) dans le portail Azure](create-single-tenant-workflows-azure-portal.md) ou [dans Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md).

## <a name="parameters-versus-app-settings"></a>Paramètres et paramètres d’application

Avant de décider où stocker vos variables d’environnement, passez en revue les informations suivantes.

Si vous utilisez déjà Azure Functions ou Azure Web Apps, vous connaissez peut-être les paramètres d’application. Dans Azure Logic Apps, les paramètres d’application s’intègrent à Azure Key Vault. Vous pouvez [référencer directement des chaînes sécurisées](../app-service/app-service-key-vault-references.md), telles que des chaînes de connexion et des clés. Comme pour les modèles ARM, où vous pouvez définir des variables d’environnement au moment du déploiement, vous pouvez définir des paramètres d’application dans la [définition de workflow de votre application logique](/azure/templates/microsoft.logic/workflows). Vous pouvez ensuite capturer des valeurs d’infrastructure générées dynamiquement, comme des points de terminaison de connexion, des chaînes de stockage, etc. Toutefois, les paramètres d’application ont des limitations de taille et ne peuvent pas être référencés à partir de certaines zones dans Azure Logic Apps.

Si vous êtes familiarisé avec les workflows dans Azure Logic Apps multilocataire, vous connaissez peut-être également les paramètres. Vous pouvez utiliser des paramètres dans un plus grand nombre de cas d’usage que les paramètres d’application, tels que la prise en charge des objets complexes et des valeurs de grandes tailles. Si vous utilisez Visual Studio Code comme outil de développement local, vous pouvez également référencer des paramètres dans les fichiers **workflow.json** et **connections.json** de votre projet d’application logique. Si vous souhaitez utiliser les deux options dans votre solution, vous pouvez également référencer des paramètres d’application en utilisant des paramètres.

> [!NOTE]
> Pour le développement, si vous paramétrisez le fichier **connections.json**, l’expérience du concepteur devient restreinte, à la fois localement et dans le portail Azure. Si vous devez utiliser le concepteur pour le développement, utilisez un fichier **connections.json** non paramétrisé. Ensuite, dans vos pipelines de déploiement, remplacez-le par le fichier paramétrisé. Le runtime fonctionne toujours avec la paramétrisation. Les améliorations du concepteur sont en cours de développement.

Envisagez la recommandation d’utiliser des paramètres comme mécanisme par défaut pour la paramétrisation. Ainsi, quand vous devez stocker des clés ou des chaînes sécurisées, vous pouvez suivre la recommandation pour référencer les paramètres de l’application à partir de vos paramètres.

## <a name="what-is-parameterization"></a>Qu’est-ce que la paramétrisation ?

Si vous utilisez Visual Studio Code, dans votre projet d’application logique, vous pouvez définir des paramètres dans le fichier **parameters.json**. Vous pouvez référencer n’importe quel paramètre dans le fichier **parameters.json** à partir d’un objet de workflow ou de connexion dans votre application logique. La paramétrisation de vos entrées de workflow dans Azure Logic Apps monolocataire fonctionne de la même façon que dans Azure Logic Apps multilocataire.

Pour référencer des paramètres dans vos entrées de déclencheur ou d’action, utilisez l’expression `@parameters('<parameter-name>')`.

> [!IMPORTANT]
> Veillez à inclure également tous les paramètres que vous référencez dans votre fichier **parameters.json**.

Dans Azure Logic Apps *monolocataire*, vous pouvez paramétriser différentes parties de votre fichier **connections.json**. Vous pouvez ensuite vérifier votre fichier **connections.json** dans le contrôle de code source, puis gérer les connexions par le biais de votre fichier **parameters.json**. Pour paramétriser votre fichier **connections.json**, remplacez les valeurs des littéraux, tels que `ConnectionRuntimeUrl`, par une expression unique `parameters()`, par exemple, `@parameters('api-runtimeUrl')`.

Vous pouvez également paramétriser des objets complexes, tels que l’objet JSON `authentication`. Par exemple, remplacez la valeur de l’objet `authentication` par une chaîne qui contient une expression de paramètres unique, telle que `@parameters('api-auth')`. 

> [!NOTE]
> Les seuls types d’expressions valides dans le fichier **connections.json** sont `@parameters` et `@appsetting`.

## <a name="define-parameters"></a>Définir les paramètres

Dans les workflows monolocataires, vous devez placer toutes les valeurs de paramètre dans un fichier JSON de niveau racine nommé **parameters.json**. Ce fichier contient un objet qui renferme des paires clé-valeur. Les clés sont les noms de chaque paramètre, tandis que les valeurs sont les structures pour chaque paramètre. Chaque structure doit inclure à la fois une déclaration `type` et `value`.

> [!NOTE]
> Le seul type d’expression valide dans le fichier **parameters.json** est `@appsetting`.

L’exemple suivant montre un fichier de paramètres de base :

```json
{ 
    "responseString": { 
        "type": "string", 
        "value": "hello" 
    }, 
    "functionAuth": { 
        "type": "object", 
        "value": { 
            "type": "QueryString", 
            "name": "Code", 
            "value": "@appsetting('<AzureFunctionsOperation-FunctionAppKey')" 
        } 
    } 
} 
```

En règle générale, vous devez gérer plusieurs versions de fichiers de paramètres. Vous pouvez avoir des valeurs ciblées pour différents environnements de déploiement, tels que les environnements de développement, de test et de production. La gestion de ces fichiers de paramètres fonctionne souvent comme la gestion des fichiers de paramètres de modèle ARM. Quand vous effectuez un déploiement sur un environnement spécifique, vous promouvez le fichier de paramètres correspondant, généralement par le biais d’un pipeline pour DevOps.

Pour remplacer dynamiquement les fichiers de paramètres en utilisant Azure CLI, exécutez la commande suivante :

```azurecli
az functionapp deploy --resource-group MyResourceGroup --name MyLogicApp --src-path C:\parameters.json --type static --target-path parameters.json
```

Si vous avez un projet d’application logique NuGet, vous devez mettre à jour votre fichier projet ( **&lt;logic-app-name&gt;.csproj**) pour inclure le fichier de paramètres dans la sortie de la génération, par exemple :
  
```csproj
<ItemGroup>
  <None Update="parameters.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
  </None>
</ItemGroup>
```

> [!NOTE]
> La possibilité de remplacer dynamiquement les fichiers de paramètres n’est pas encore disponible dans le portail Azure ou dans le concepteur de workflow.

Pour plus d’informations sur la configuration de vos applications logiques pour les déploiements DevOps, consultez la documentation suivante :

- [Présentation du déploiement DevOps pour les applications logiques monolocataires](devops-deployment-single-tenant-azure-logic-apps.md)
- [Configurer le déploiement DevOps pour les applications logiques monolocataires](set-up-devops-deployment-single-tenant-azure-logic-apps.md)

## <a name="manage-app-settings"></a>Gérer les paramètres d’application

Dans Azure Logic Apps monolocataire, les paramètres d’application contiennent des options de configuration globale pour *tous les workflows* dans la même application logique. Quand vous exécutez des workflows localement dans Visual Studio Code, ces paramètres sont accessibles en tant que variables d’environnement local dans le fichier **local.settings.json**. Vous pouvez ensuite référencer ces paramètres d’application dans vos paramètres.

Pour ajouter, mettre à jour ou supprimer des paramètres d’application, sélectionnez et consultez les sections suivantes selon que vous utilisez Visual Studio Code, le portail Azure, Azure CLI ou un modèle ARM (Bicep).

### <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

Pour passer en revue les paramètres d’application de votre application logique dans le portail Azure, procédez comme suit :

1. Dans la zone de recherche du [portail Azure](https://portal.azure.com/), recherchez et ouvrez votre application logique monolocataire.
1. Dans le menu de votre application logique, sous **Paramètres**, sélectionnez **Configuration**.
1. Dans la page **Configuration**, sous l’onglet **Paramètres de l’application**, examinez les paramètres de l’application pour votre application logique.
1. Pour voir toutes les valeurs, sélectionnez **Afficher les valeurs**. Ou, pour voir une valeur unique, sélectionnez cette valeur.

Pour ajouter un nouveau paramètre, effectuez les étapes suivantes :

1. Sous l’onglet **Paramètres de l’application**, sous **Paramètres de l’application**, sélectionnez **Nouveau paramètre d’application**.
1. Pour **Nom**, entrez la *clé* ou le nom de votre nouveau paramètre.
1. Pour **Valeur**, entrez la valeur de votre nouveau paramètre.
1. Quand vous êtes prêt à créer votre paire *clé-valeur*, sélectionnez **OK**.

:::image type="content" source="./media/parameterize-workflow-app/portal-app-settings-values.png" alt-text="Capture d’écran montrant le portail Azure et le volet de configuration avec les paramètres et les valeurs d’application pour une application logique monolocataire." lightbox="./media/parameterize-workflow-app/portal-app-settings-values.png":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour passer en revue les paramètres actuels de votre application avec Azure CLI, exécutez la commande `az logicapp config appsettings list`. Assurez-vous que votre commande comprend les paramètres `--name -n` et `--resource-group -g`, par exemple :

```azurecli
az logicapp config appsettings list --name MyLogicApp --resource-group MyResourceGroup
```

Pour ajouter ou mettre à jour un paramètre d’application avec Azure CLI, exécutez la commande `az logicapp config appsettings set`. Assurez-vous que votre commande comprend les paramètres `--name n` et `--resource-group -g`. Par exemple, la commande suivante crée un paramètre avec une clé nommée `CUSTOM_LOGIC_APP_SETTING` et la valeur `12345` :

```azurecli
az logicapp config appsettings set --name MyLogicApp --resource-group MyResourceGroup --settings CUSTOM_LOGIC_APP_SETTING=12345 
```

### <a name="resource-manager-or-bicep-template"></a>[Modèle Resource Manager ou Bicep](#tab/azure-resource-manager)

Pour examiner et définir vos paramètres d’application dans un modèle ARM ou un modèle Bicep, recherchez la définition des ressources de votre application logique et mettez à jour l’objet JSON `appSettings`. Pour obtenir la définition complète des ressources, consultez les [informations de référence sur le modèle ARM](/azure/templates/microsoft.web/sites).

Cet exemple montre les paramètres de fichier pour les modèles ARM ou Bicep :

```json
"appSettings": [
    {
        "name": "string",
        "value": "string"
    },
    {
        "name": "string",
        "value": "string"
    },
    <...>
], 
```

---

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Architecture monolocataire ou multilocataire et environnement de service d’intégration pour Azure Logic Apps](single-tenant-overview-compare.md)
