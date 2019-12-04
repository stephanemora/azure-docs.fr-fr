---
title: Tutoriel - Créer et déployer un modèle
description: Créez votre premier modèle Azure Resource Manager. Dans ce tutoriel, vous découvrirez la syntaxe des fichiers de modèle et comment déployer un compte de stockage.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 9650b8c67e3fd5c786b066c53e78b106935e11e1
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406039"
---
# <a name="tutorial-create-and-deploy-your-first-azure-resource-manager-template"></a>Didacticiel : Créer et déployer votre premier modèle Azure Resource Manager

Ce tutoriel vous présente les modèles Azure Resource Manager. Il vous montre comment créer un modèle de démarrage et le déployer sur Azure. Vous allez découvrir la structure du modèle et les outils dont vous avez besoin pour utiliser les modèles. Il faut environ **12 minutes** pour suivre ce tutoriel, mais le temps réel varie en fonction du nombre d’outils que vous devez installer.

Ce tutoriel est le premier d’une série. Au fur et à mesure que vous progressez dans la série, vous modifiez petit à petit le modèle de démarrage, jusqu’à ce que vous ayez exploré toutes les parties vitales qui composent un modèle Resource Manager. Il s’agit des éléments constitutifs de modèles bien plus complexes. Nous espérons qu’à la fin de la série, vous vous lancerez dans la création de vos propres modèles et que vous serez prêt à automatiser vos déploiements à l’aide de modèles.

Si vous souhaitez découvrir les avantages qu’offre l’utilisation de modèles et les raisons pour lesquelles vous devriez automatiser le déploiement avec des modèles, consultez [Modèles Azure Resource Manager](template-deployment-overview.md).

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="get-tools"></a>Obtenir les outils

Commençons par nous assurer que vous disposez des outils indispensables pour créer et déployer des modèles.

### <a name="editor"></a>Éditeur

Les modèles sont des fichiers JSON. Pour créer des modèles, vous avez besoin d’un bon éditeur JSON. Nous conseillons Visual Studio Code avec l’extension Outils Resource Manager. Si vous devez installer ces outils, consultez [Utiliser Visual Studio Code pour créer des modèles Azure Resource Manager](./resource-manager-tools-vs-code.md).

### <a name="command-line-deployment"></a>Déploiement en ligne de commande

Vous avez également besoin d’Azure PowerShell ou d’Azure CLI pour déployer le modèle. Pour les instructions d’installation, consultez :

- [Installation d'Azure PowerShell](/powershell/azure/install-az-ps)
- [Installer Azure CLI sur Windows](/cli/azure/install-azure-cli-windows)
- [Installer Azure CLI sur Linux](/cli/azure/install-azure-cli-linux)

Après avoir installé Azure PowerShell ou Azure CLI, prenez soin de vous connecter une première fois. Pour obtenir de l’aide, consultez [Se connecter - PowerShell](/powershell/azure/install-az-ps#sign-in) ou [Se connecter - Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in).

Bien, vous êtes prêt à découvrir les modèles.

## <a name="create-your-first-template"></a>Créer votre premier modèle

1. Ouvrez Visual Studio Code avec l’extension Outils Resource Manager installée.
1. À partir du menu **Fichier**, sélectionnez **Nouveau fichier** pour créer un fichier.
1. Dans le menu **Fichier**, sélectionnez **Enregistrer sous**.
1. Nommez le fichier **azuredeploy** et sélectionnez l’extension de fichier **JSON**. Le nom complet du fichier est **azuredeploy.json**.
1. Enregistrez le fichier sur votre station de travail. Sélectionnez un chemin facile à mémoriser, car vous devrez fournir ce chemin plus tard, lors du déploiement du modèle.
1. Copiez et collez le code JSON suivant dans le fichier :

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": []
    }
    ```

    Voici à quoi ressemble votre environnement VS Code :

    ![Premier modèle Visual Studio Code avec modèle Resource Manager](./media/template-tutorial-create-first-template/resource-manager-visual-studio-code-first-template.png)

    Ce modèle ne déploie aucune ressource. Nous commençons avec un modèle vide, ce qui vous permet de vous familiariser avec les étapes de déploiement d’un modèle tout en réduisant au minimum le risque de problème.

    Le fichier JSON contient les éléments suivants :

    - **$schema** : précise l’emplacement du fichier de schéma JSON. Le fichier de schéma décrit les propriétés qui sont disponibles dans un modèle. Par exemple, le schéma définit **resources** comme faisant partie des propriétés valides d’un modèle. Ne vous inquiétez pas de voir 2015-01-01 comme date du schéma. Cette version de schéma est à jour et comprend toutes les fonctionnalités les plus récentes. La date du schéma n’a pas été modifiée, car il n’y a eu aucun changement cassant depuis sa création.
    - **contentVersion** : spécifie la version du modèle (par exemple, 1.0.0.0). Vous pouvez fournir n’importe quelle valeur pour cet élément. Utilisez cette valeur pour documenter les modifications importantes dans votre modèle. Quand vous déployez des ressources à l'aide du modèle, cette valeur permet de vous assurer que le bon modèle est utilisé.
    - **Ressources** : contient les ressources que vous souhaitez déployer ou mettre à jour. Actuellement, il est vide, mais vous ajouterez des ressources ultérieurement.

1. Enregistrez le fichier .

Félicitations, vous avez créé votre premier modèle.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Pour commencer à utiliser Azure PowerShell/Azure CLI, connectez-vous à l’aide de vos informations d’identification Azure.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-clitabazure-cli"></a>[Interface de ligne de commande Azure](#tab/azure-cli)

```azurecli
az login
```

---
## <a name="create-resource-group"></a>Créer un groupe de ressources

Lorsque vous déployez un modèle, vous indiquez un groupe de ressources pour contenir les ressources. Avant d’exécuter la commande de déploiement, créez le groupe de ressources avec Azure CLI ou Azure PowerShell. Sélectionnez les onglets dans la section de code suivante pour choisir entre Azure PowerShell et Azure CLI. Les exemples CLI de cet article sont écrits pour l’interpréteur de commandes Bash.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name myResourceGroup `
  -Location "Central US"
```

# <a name="azure-clitabazure-cli"></a>[Interface de ligne de commande Azure](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroup \
  --location "Central US"
```

---

## <a name="deploy-template"></a>Déployer un modèle

Pour déployer le modèle, utilisez au choix Azure CLI ou Azure PowerShell. Utilisez le groupe de ressources que vous avez créé. Donnez un nom au déploiement pour pouvoir l’identifier facilement dans l’historique des déploiements. Par souci pratique, créez également une variable qui stocke le chemin du fichier de modèle. Cette variable vous permet d’exécuter plus facilement les commandes de déploiement, car vous n’êtes pas obligé de retaper le chemin chaque fois que vous déployez.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{provide-the-path-to-the-template-file}"
New-AzResourceGroupDeployment `
  -Name blanktemplate `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-clitabazure-cli"></a>[Interface de ligne de commande Azure](#tab/azure-cli)

```azurecli
templateFile="{provide-the-path-to-the-template-file}"
az group deployment create \
  --name blanktemplate \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

La commande de déploiement retourne des résultats. Recherchez `ProvisioningState` pour voir si le déploiement s’est déroulé correctement.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

![État de provisionnement du déploiement PowerShell](./media/template-tutorial-create-first-template/resource-manager-deployment-provisioningstate.png)

# <a name="azure-clitabazure-cli"></a>[Interface de ligne de commande Azure](#tab/azure-cli)

![État de provisionnement du déploiement Azure CLI](./media/template-tutorial-create-first-template/azure-cli-provisioning-state.png)

---

## <a name="verify-deployment"></a>Vérifier le déploiement

Vous pouvez vérifier le déploiement en explorant le groupe de ressources à partir du portail Azure.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

1. Dans le menu de gauche, sélectionnez **Groupes de ressources**.

1. Sélectionnez le groupe de ressources déployé dans la dernière procédure. Le nom par défaut est **myResourceGroup**. Vous ne voyez aucune ressource déployée dans le groupe de ressources.

1. Remarquez l’état du déploiement affiché en haut à droite dans la vue d’ensemble. Sélectionnez **1 Réussite**.

   ![Affichage de l’état du déploiement](./media/template-tutorial-create-first-template/deployment-status.png)

1. Vous voyez un historique du déploiement du groupe de ressources. Sélectionnez **blanktemplate**.

   ![Sélectionner un déploiement](./media/template-tutorial-create-first-template/select-from-deployment-history.png)

1. Vous affichez un résumé du déploiement. Dans ce cas, il n’y a pas grand-chose à voir parce qu’aucune ressource n’a été déployée. Plus loin dans cette série, vous trouverez peut-être utile de consulter le résumé de l’historique de déploiement. Notez à gauche que vous pouvez voir les entrées, les sorties et le modèle utilisé pendant le déploiement.

   ![Afficher le résumé du déploiement](./media/template-tutorial-create-first-template/view-deployment-summary.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous passez au tutoriel suivant, vous n’avez pas besoin de supprimer le groupe de ressources.

Si vous arrêtez maintenant, vous pouvez supprimer le groupe de ressources.

1. Dans le portail Azure, sélectionnez **Groupe de ressources** dans le menu de gauche.
2. Entrez le nom du groupe de ressources dans le champ **Filtrer par nom**.
3. Sélectionnez le nom du groupe de ressources.
4. Sélectionnez **Supprimer le groupe de ressources** dans le menu supérieur.

## <a name="next-steps"></a>Étapes suivantes

Vous avez créé un modèle simple à déployer sur Azure. Dans le tutoriel suivant, vous allez ajouter un compte de stockage au modèle et le déployer dans votre groupe de ressources.

> [!div class="nextstepaction"]
> [Ajouter une ressource](template-tutorial-add-resource.md)
