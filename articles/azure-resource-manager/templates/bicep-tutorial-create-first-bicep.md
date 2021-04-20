---
title: 'Tutoriel : Créer et déployer des fichiers Bicep Azure Resource Manager'
description: Créez votre premier fichier Bicep pour le déploiement de ressources Azure. Dans ce tutoriel, vous découvrirez la syntaxe des fichiers Bicep et la façon de déployer un compte de stockage.
author: mumian
ms.date: 04/12/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 0ffd8a97d797144d458e7ec5836042cc6d8d8193
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306674"
---
# <a name="tutorial-create-and-deploy-first-azure-resource-manager-bicep-file"></a>Tutoriel : Créer et déployer votre premier fichier Bicep Azure Resource Manager

Ce tutoriel vous présente le langage [Bicep](./bicep-overview.md). Il vous montre comment créer un fichier Bicep de base et le déployer sur Azure. Vous allez découvrir la structure du fichier Bicep et les outils dont vous aurez besoin pour utiliser les fichiers Bicep. Il faut environ **12 minutes** pour suivre ce tutoriel, mais le temps réel varie en fonction du nombre d’outils que vous devez installer.

Ce tutoriel est le premier d’une série. Au fur et à mesure que vous progressez dans la série, vous modifiez petit à petit le fichier Bicep de départ jusqu’à ce que vous ayez exploré toutes les parties vitales qui composent un fichier Bicep. Il s’agit des éléments constitutifs de fichiers Bicep bien plus complexes. Nous espérons qu’à la fin de la série, vous vous lancerez dans la création de vos propres fichiers Bicep et que vous serez prêt à automatiser vos déploiements à l’aide de fichiers Bicep.

Si vous souhaitez découvrir les avantages qu’offre l’utilisation du langage Bicep et les raisons pour lesquelles vous devriez automatiser le déploiement avec des fichiers Bicep, consultez [Bicep](./bicep-overview.md).

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="get-tools"></a>Obtenir les outils

Commençons par nous assurer que vous disposez des outils indispensables pour créer et déployer des fichiers Bicep. Installez ces outils sur votre machine locale.

### <a name="editor"></a>Éditeur

Pour créer des fichiers Bicep, vous avez besoin d’un bon éditeur. Nous vous recommandons Visual Studio Code avec l’extension Bicep. Si vous devez installer ces outils, consultez [Configurer l’environnement de développement Bicep](./bicep-install.md#development-environment).

### <a name="command-line-deployment"></a>Déploiement en ligne de commande

Vous pouvez déployer des fichiers Bicep en utilisant Azure CLI ou Azure PowerShell. Pour Azure CLI, vous avez besoin de la version 2.20.0 ou ultérieure ; pour Azure PowerShell, vous avez besoin de la version 5.6.0 ou ultérieure. Pour les instructions d’installation, consultez :

- [Installation d'Azure PowerShell](/powershell/azure/install-az-ps)
- [Installer Azure CLI sur Windows](/cli/azure/install-azure-cli-windows)
- [Installer Azure CLI sur Linux](/cli/azure/install-azure-cli-linux)
- [Installer Azure CLI sur macOS](/cli/azure/install-azure-cli-macos)

Après avoir installé Azure PowerShell ou Azure CLI, prenez soin de vous connecter une première fois. Pour obtenir de l’aide, consultez [Se connecter - PowerShell](/powershell/azure/install-az-ps#sign-in) ou [Se connecter - Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in).

Bien, vous êtes prêt à découvrir le langage Bicep.

## <a name="create-your-first-bicep-file"></a>Créer votre premier fichier Bicep

1. Ouvrez Visual Studio Code avec l’extension Bicep installée.
1. À partir du menu **Fichier**, sélectionnez **Nouveau fichier** pour créer un fichier.
1. Dans le menu **Fichier**, sélectionnez **Enregistrer sous**.
1. Nommez le fichier _azuredeploy_ et sélectionnez l’extension de fichier _bicep_. Le nom complet du fichier est _azuredeploy.bicep_.
1. Enregistrez le fichier sur votre station de travail. Sélectionnez un chemin facile à mémoriser, car vous devrez fournir celui-ci plus tard lors du déploiement du fichier Bicep.
1. Copiez et collez le contenu suivant dans le fichier :

    ```bicep
    resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
      name: '{provide-unique-name}'  // must be globally unique
      location: 'eastus'
      sku: {
        name: 'Standard_LRS'
      }
      kind: 'StorageV2'
      properties: {
        supportsHttpsTrafficOnly: true
      }
    }
    ```

    Voici à quoi ressemble votre environnement Visual Studio Code :

    ![Visual Studio Code, premier fichier Bicep](./media/Bicep-tutorial-create-first-bicep/resource-manager-visual-studio-code-first-bicep.png)

    La déclaration de ressource se compose de quatre éléments :

    - **ressource** : mot clé.
    - **nom symbolique** (stg) : un nom symbolique est un identificateur permettant de référencer la ressource dans l’ensemble de votre fichier Bicep. Il ne s’agit pas du nom de la ressource lorsqu’elle sera déployée. Le nom de la ressource est défini par la propriété **name**.  Consultez le quatrième élément de cette liste. Pour faciliter le suivi des tutoriels, **stg** est utilisé comme nom symbolique pour la ressource de compte de stockage dans cette série de tutoriels. Pour savoir comment utiliser le nom symbolique afin d’obtenir la liste complète des propriétés de l’objet, consultez [Ajouter des sorties](./bicep-tutorial-add-outputs.md).
    - **type de ressource** (Microsoft.Storage/storageAccounts@2019-06-01) : il se compose du fournisseur de ressources (Microsoft.Storage), du type de ressource (storageAccounts) et de l’élément apiVersion (01-06-2019). Chaque fournisseur de ressources publiant ses propres versions d’API, cette valeur est donc propre au type. Vous trouverez d’autres types et éléments apiVersion pour différentes ressources Azure dans la section [Informations de référence sur les modèles ARM](/azure/templates/).
    - **propriétés** (tout ce qui se trouve entre accolades = {…}) : il s’agit des propriétés spécifiques que vous souhaitez spécifier pour le type de ressource donné. Ce sont exactement les mêmes propriétés que celles disponibles dans un modèle ARM. Chaque ressource comporte une propriété `name`. La plupart des ressources possèdent également une propriété `location`, qui définit la région dans laquelle la ressource est déployée. Les autres propriétés varient selon le type de la ressource et la version de l’API. Il est important de comprendre le lien existant entre la version de l’API et les propriétés disponibles. Voyons donc cela d’un peu plus près.

        Pour ce compte de stockage, vous pouvez voir que la version de l’API est [storageAccounts 2019-06-01](/azure/templates/microsoft.storage/2019-06-01/storageaccounts). Notez que vous n’avez pas ajouté toutes les propriétés à votre fichier Bicep. Beaucoup de ces propriétés sont facultatives. Le fournisseur de ressources `Microsoft.Storage` peut publier une nouvelle version de l’API, pour autant la version que vous déployez ne doit pas nécessairement être changée. Vous pouvez continuer à utiliser cette version tout en sachant que les résultats de votre déploiement seront cohérents.

        Si vous voyez une version plus ancienne de l’API, par exemple [storageAccounts 2016-05-01](/azure/templates/microsoft.storage/2016-05-01/storageaccounts), vous remarquerez qu’un ensemble de propriétés plus restreint est disponible.

        Si vous décidez de modifier la version de l’API pour une ressource, veillez à évaluer les propriétés de cette version et à ajuster votre fichier Bicep en conséquence.

    Pour plus d’informations, consultez [Structure de Bicep](./bicep-file.md).

    Il y a un commentaire pour la propriété name.  Utilisez `//` pour les commentaires d’une seule ligne ou `/* ... */` pour les commentaires de plusieurs lignes.

1. Remplacez `{provide-unique-name}`, accolades `{}` incluses, par un nom de compte de stockage unique.

    > [!IMPORTANT]
    > Le nom du compte de stockage doit être unique dans Azure. Le nom ne doit contenir que des lettres minuscules ou des chiffres. Il ne doit pas compter plus de 24 caractères. Vous pouvez essayer un modèle de nommage, par exemple en utilisant **store1** comme préfixe et le faisant suivre de vos initiales et de la date du jour. Ainsi, le nom que vous utilisez peut ressembler à **store1abc09092019**.

    Trouver un nom unique pour un compte de stockage n’est pas facile, et ne fonctionne pas bien dans l’automatisation des déploiements à grande échelle. Plus loin dans cette série de tutoriels, vous utiliserez des fonctionnalités de Bicep qui facilitent la création d’un nom unique.

1. Enregistrez le fichier .

Félicitations, vous avez créé votre premier fichier Bicep.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Pour commencer à utiliser Azure PowerShell/Azure CLI, connectez-vous à l’aide de vos informations d’identification Azure.

Sélectionnez les onglets dans les sections de code suivantes pour choisir entre Azure PowerShell et Azure CLI. Les exemples CLI de cet article sont écrits pour l’interpréteur de commandes Bash.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az login
```

---

Si vous avez plusieurs abonnements Azure, sélectionnez celui que vous souhaitez utiliser. Remplacez `[SubscriptionID/SubscriptionName]` et les crochets `[]` par vos informations d’abonnement :

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzContext [SubscriptionID/SubscriptionName]
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az account set --subscription [SubscriptionID/SubscriptionName]
```

---

## <a name="create-resource-group"></a>Créer un groupe de ressources

Lorsque vous déployez un fichier Bicep, vous indiquez un groupe de ressources pour contenir les ressources. Avant d’exécuter la commande de déploiement, créez le groupe de ressources avec Azure CLI ou Azure PowerShell.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name myResourceGroup `
  -Location "Central US"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroup \
  --location "Central US"
```

---

## <a name="deploy-bicep-file"></a>Déployer le fichier Bicep

Bicep est une abstraction transparente sur des modèles Azure Resource Manager (modèles ARM). Chaque fichier Bicep est compilé en un modèle ARM standard avant d’être déployé. Vous pouvez compiler votre fichier Bicep en modèle ARM avant de le déployer ou déployer votre fichier Bicep directement. Pour déployer le fichier Bicep, utilisez Azure CLI ou Azure PowerShell. Utilisez le groupe de ressources que vous avez créé. Donnez un nom au déploiement pour pouvoir l’identifier facilement dans l’historique des déploiements. Par souci pratique, créez également une variable qui stocke le chemin du fichier Bicep. Cette variable vous permet d’exécuter plus facilement les commandes de déploiement car vous n’êtes pas obligé de retaper le chemin d’accès chaque fois que vous déployez. Remplacez `{provide-the-path-to-the-bicep-file}`, accolades `{}` incluses, par le chemin d’accès à votre fichier Bicep avec le nom d’extension de fichier _.bicep_.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour exécuter cette cmdlet de déploiement, vous devez disposer de la [version la plus récente](/powershell/azure/install-az-ps) d’Azure PowerShell.

```azurepowershell
$bicepFile = "{provide-the-path-to-the-bicep-file}"
New-AzResourceGroupDeployment `
  -Name firstbicep `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour exécuter cette commande de déploiement, vous devez disposer de la [dernière version](/cli/azure/install-azure-cli) d’Azure CLI.

```azurecli
bicepFile="{provide-the-path-to-the-bicep-file}"
az deployment group create \
  --name firstbicep \
  --resource-group myResourceGroup \
  --template-file $bicepFile
```

---

La commande de déploiement retourne des résultats. Recherchez `ProvisioningState` pour voir si le déploiement s’est déroulé correctement.

> [!NOTE]
> Si le déploiement a échoué, utilisez le commutateur `verbose` pour obtenir des informations sur les ressources en cours de création. Utilisez le commutateur `debug` pour obtenir des informations supplémentaires sur le débogage.

## <a name="verify-deployment"></a>Vérifier le déploiement

Vous pouvez vérifier le déploiement en explorant le groupe de ressources à partir du portail Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Dans le menu de gauche, sélectionnez **Groupes de ressources**.

1. Sélectionnez le groupe de ressources déployé dans la dernière procédure. Le nom par défaut est **myResourceGroup**. Vous ne voyez aucune ressource déployée dans le groupe de ressources.

1. Remarquez l’état du déploiement affiché en haut à droite dans la vue d’ensemble. Sélectionnez **1 Réussite**.

   ![Affichage de l’état du déploiement](./media/bicep-tutorial-create-first-bicep/deployment-status.png)

1. Vous voyez un historique du déploiement du groupe de ressources. Sélectionnez **firstbicep**.

   ![Sélectionner un déploiement](./media/bicep-tutorial-create-first-bicep/select-from-deployment-history.png)

1. Vous affichez un résumé du déploiement. Un compte de stockage est déployé. Notez à gauche que vous pouvez voir les entrées, les sorties et le modèle utilisé pendant le déploiement.

   ![Afficher le résumé du déploiement](./media/bicep-tutorial-create-first-bicep/view-deployment-summary.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous passez au tutoriel suivant, vous n’avez pas besoin de supprimer le groupe de ressources.

Si vous arrêtez maintenant, vous pouvez supprimer le groupe de ressources.

1. Dans le portail Azure, sélectionnez **Groupe de ressources** dans le menu de gauche.
2. Entrez le nom du groupe de ressources dans le champ **Filtrer par nom**.
3. Sélectionnez le nom du groupe de ressources.
4. Sélectionnez **Supprimer le groupe de ressources** dans le menu supérieur.

## <a name="next-steps"></a>Étapes suivantes

Vous avez créé un fichier Bicep simple à déployer sur Azure.  Dans les tutoriels suivants, vous allez découvrir comment ajouter des paramètres, des variables, des sorties et des modules à un fichier Bicep. Ces fonctionnalités constituent les composants élémentaires de fichiers Bicep bien plus complexes.

> [!div class="nextstepaction"]
> [Ajouter des paramètres](bicep-tutorial-add-parameters.md)
