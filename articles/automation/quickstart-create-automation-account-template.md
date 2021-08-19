---
title: 'Démarrage rapide : Créer un compte Automation – Modèle Azure'
titleSuffix: Azure Automation
description: Ce guide de démarrage rapide montre comment créer un compte Automation en utilisant le modèle Azure Resource Manager.
services: automation
ms.author: magoedte
ms.date: 07/20/2021
ms.topic: quickstart
ms.workload: infrastructure-services
ms.custom:
- mvc
- subject-armqs
- mode-arm
ms.openlocfilehash: 17183bffcfe9607a774d1205180c284d49cd7503
ms.sourcegitcommit: 6f21017b63520da0c9d67ca90896b8a84217d3d3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2021
ms.locfileid: "114652007"
---
# <a name="quickstart-create-an-automation-account-by-using-arm-template"></a>Démarrage rapide : Créer un compte Automation à l’aide d’un modèle ARM

Azure Automation offre un service d’automatisation et de configuration cloud prenant en charge une gestion cohérente de vos environnements Azure et non-Azure. Ce guide de démarrage rapide vous montre comment déployer un modèle Azure Resource Manager (modèle ARM) qui crée un compte Automation. L’utilisation d’un modèle ARM comprend moins d’étapes que les autres méthodes de déploiement.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Si votre environnement remplit les prérequis et que vous êtes déjà familiarisé avec l’utilisation des modèles ARM, sélectionnez le bouton **Déployer sur Azure**. Le modèle s’ouvre dans le portail Azure.

[![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.automation%2F101-automation%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="review-the-template"></a>Vérifier le modèle

Cet exemple de modèle effectue les opérations suivantes :

* Automatise la création d’un espace de travail Azure Monitor Log Analytics.
* Automatise la création d’un compte Azure Automation.
* Lie le compte Automation à l’espace de travail Log Analytics.
* Ajoute des exemples de runbooks Automation au compte.

>[!NOTE]
>La création du compte d’identification Automation n’est pas prise en charge quand vous utilisez un modèle ARM. Pour créer manuellement un compte d’identification à partir du portail ou à l’aide de PowerShell, consultez [Créer un compte d’identification](create-run-as-account.md).

Une fois ces étapes accomplies, vous devez [configurer des paramètres de diagnostic](automation-manage-send-joblogs-log-analytics.md) pour votre compte Automation, afin d’envoyer l’état de la tâche du Runbook et les flux de tâches à l’espace de travail Log Analytics lié.

Le modèle utilisé dans ce démarrage rapide est tiré des [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/101-automation/).

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.automation/101-automation/azuredeploy.json":::

### <a name="api-versions"></a>Versions d’API

La table suivante répertorie la version d’API pour les ressources utilisées dans cet exemple.

| Ressource | Type de ressource | Version de l'API |
|:---|:---|:---|
| [Espace de travail](/azure/templates/microsoft.operationalinsights/workspaces) | workspaces | 2020-03-01-preview |
| [Compte Automation](/azure/templates/microsoft.automation/automationaccounts) | automation | 2020-01-13-preview |
| [Services liés à l’espace de travail](/azure/templates/microsoft.operationalinsights/workspaces/linkedservices) | workspaces | 2020-03-01-preview |

### <a name="before-you-use-the-template"></a>Avant d’utiliser le modèle

Le modèle de paramètres JSON est configuré pour que vous spécifiiez les éléments suivants :

* Nom de l’espace de travail.
* Région dans laquelle créer l’espace de travail.
* Nom du compte Automation.
* Région dans laquelle créer le compte Automation.

Les paramètres suivants dans le modèle sont définis avec une valeur par défaut pour l’espace de travail Log Analytics :

* La valeur par défaut de *sku* est le niveau de tarification par Go publié dans le modèle de tarification d’avril 2018.
* La valeur par défaut de *dataRetention* est 30 jours.

>[!WARNING]
>Si vous voulez créer ou configurer un espace de travail Log Analytics dans un abonnement pour lequel le modèle de tarification d’avril 2018 a été choisi, le seul niveau tarifaire Log Analytics valide est *PerGB2018*.
>

Le modèle JSON spécifie une valeur par défaut pour les autres paramètres susceptibles d’être utilisés comme configuration standard dans votre environnement. Vous pouvez stocker le modèle dans un compte de stockage Azure pour mettre en place un accès partagé dans votre organisation. Pour plus d’informations sur l’utilisation des modèles, consultez [Déployer des ressources à l’aide de modèles ARM et d’Azure CLI](../azure-resource-manager/templates/deploy-cli.md).

Si vous ne connaissez pas encore Azure Automation et Azure vous devez bien comprendre les détails de configuration suivants. Ils peuvent vous aider à éviter des erreurs quand vous essayez de créer, de configurer et d’utiliser un espace de travail Log Analytics lié à votre nouveau compte Automation.

* Examinez les [détails supplémentaires](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace) pour comprendre pleinement les options de configuration de l’espace de travail, telles que le mode de contrôle d’accès, le niveau tarifaire, la conservation des données et le niveau de réservation de capacité.

* Passez en revue les [mappages d’espace de travail](how-to/region-mappings.md) pour spécifier les régions prises en charge incluses ou figurant dans un fichier de paramètres. Seules certaines régions sont prises en charge pour la liaison d’un espace de travail Log Analytics et d’un compte Automation dans votre abonnement.

* Si vous ne connaissez pas les journaux Azure Monitor et n’avez pas encore déployé un espace de travail, vous devez consulter les [conseils pour la conception de l’espace de travail](../azure-monitor/logs/design-logs-deployment.md). Ils vous aideront à vous familiariser avec le contrôle d’accès et à comprendre les stratégies d’implémentation de conception que nous recommandons pour votre organisation.

## <a name="deploy-the-template"></a>Déployer le modèle

1. Cliquez sur l’image ci-après pour vous connecter à Azure et ouvrir un modèle. Le modèle crée un compte Azure Automation, un espace de travail Log Analytics et lie le compte Automation à cet espace de travail.

    [![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.automation%2F101-automation%2Fazuredeploy.json)

2. Entrez les valeurs.

    Lorsque vous tentez d’exécuter le modèle ARM à partir de PowerShell, de l’interface CLI ou de la fonction Modèles du portail, si le paramètre `_artifactsLocation` n’est pas correctement défini, vous recevez un message d’erreur similaire au suivant :

    `"message": "Deployment template validation failed: 'The template resource '_artifactsLocation' at line '96' and column '31' is not valid: The language expression property 'templateLink' doesn't exist, available properties are 'template, templateHash, parameters, mode, debugSetting, provisioningState'.. Please see https://aka.ms/arm-template-expressions for usage details.'."`

    Pour éviter cela, lors de l’exécution à partir de la fonction Modèles du portail, indiquez ce qui suit pour le paramètre `_artifactsLocation` : `https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.automation/101-automation/azuredeploy.json`.

    Quand vous exécutez à partir de PowerShell, incluez le paramètre et sa valeur `-TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.automation/101-automation/azuredeploy.json`.

    Quand vous exécutez à partir d’Azure CLI, incluez le paramètre et sa valeur `--template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.automation/101-automation/azuredeploy.json`.

    Pour des références sur PowerShell/CLI, consultez [Créer un compte Azure Automation (microsoft.com)](https://azure.microsoft.com/resources/templates/101-automation/) sous la section **Utiliser le modèle**.

3. Le déploiement peut prendre plusieurs minutes. Une fois l’opération terminée, le résultat ressemble à ce qui suit :

    ![Exemple de résultat lorsque le déploiement est achevé](media/quickstart-create-automation-account-template/template-output.png)

## <a name="review-deployed-resources"></a>Vérifier les ressources déployées

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans le portail Azure, ouvrez le compte Automation que vous venez de créer.

3. Dans le volet gauche, sélectionnez **Runbooks**. Dans la page **Runbooks**, trois runbooks de tutoriel créés avec le compte Automation sont répertoriés.

    ![Runbooks de tutoriel créés avec le compte Automation](./media/quickstart-create-automation-account-template/automation-sample-runbooks.png)

4. Dans le volet gauche, sélectionnez **Espace de travail lié**. La page **Espace de travail lié** affiche l’espace de travail Log Analytics que vous avez spécifié précédemment, lié à votre compte Automation.

    ![Compte Automation lié à l’espace de travail Log Analytics](./media/quickstart-create-automation-account-template/automation-account-linked-workspace.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’en avez plus besoin, dissociez le compte Automation de l’espace de travail Log Analytics, puis supprimez le compte Automation et l’espace de travail.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un compte Automation et un espace de travail Log Analytics, et vous les avez liés ensemble.

Pour en savoir plus, passez aux tutoriels concernant Azure Automation.

> [!div class="nextstepaction"]
> [Tutoriels Azure Automation](learn/automation-tutorial-runbook-graphical.md)
