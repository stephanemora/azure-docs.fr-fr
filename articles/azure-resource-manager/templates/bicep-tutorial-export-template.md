---
title: 'Tutoriel : Exporter un modèle JSON à partir du portail Azure pour le développement de code Bicep'
description: Découvrez comment utiliser un modèle JSON exporté pour procéder au développement de votre code Bicep.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 3bc7ed4ada4f7810e9864778c7f76a0573c9dc89
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102632547"
---
# <a name="tutorial-use-exported-json-template-from-the-azure-portal"></a>Tutoriel : Utiliser un modèle JSON exporté à partir du portail Azure

Dans cette série de tutoriels, vous avez créé un fichier Bicep pour déployer un compte de stockage Azure. Dans les deux tutoriels suivants, vous allez ajouter un *plan App Service* et un *site web*. Au lieu de créer des modèles à partir de zéro, vous apprenez à exporter des modèles JSON depuis le portail Azure et à utiliser des exemples de modèles à partir des [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/). Vous personnalisez ces modèles par rapport à votre utilisation. Ce tutoriel met l’accent sur l’exportation de modèles et sur la personnalisation du résultat pour votre fichier Bicep. Il faut compter environ **14 minutes** pour effectuer ce tutoriel.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Prérequis

Nous vous recommandons de suivre le [tutoriel sur les sorties](bicep-tutorial-add-outputs.md), mais il n’est pas obligatoire.

Vous devez disposer de Visual Studio Code avec l’extension Bicep et, au choix, d’Azure PowerShell ou d’Azure CLI. Pour plus d’informations, consultez [Outils Bicep](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Vérifier le fichier Bicep

À la fin du précédent tutoriel, votre fichier Bicep présentait le contenu suivant :

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.bicep":::

Ce fichier Bicep fonctionne bien pour le déploiement des comptes de stockage, mais vous souhaitez peut-être y ajouter d’autres ressources. Vous pouvez exporter le modèle JSON d’une ressource existante pour obtenir rapidement le code JSON de cette ressource. Convertissez ensuite le fichier JSON en Bicep avant de pouvoir l’ajouter à votre fichier Bicep.

## <a name="create-app-service-plan"></a>Créer un plan App Service

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez **Créer une ressource**.
1. Dans **Rechercher sur la Place de marché**, entrez **Plan App Service**, puis sélectionnez **Plan App Service**.  Ne sélectionnez pas **Plan App Service (classique)** .
1. Sélectionnez **Create** (Créer).
1. Entrez :

    - **Abonnement** : sélectionnez votre abonnement Azure.
    - **Groupe de ressources** : Sélectionnez **Créer**, puis précisez un nom. Indiquez un autre nom de groupe de ressources que celui que vous avez utilisé dans cette série de tutoriels.
    - **Nom** : entrez un nom pour le plan App Service.
    - **Système d’exploitation** : sélectionnez **Linux**.
    - **Région** : sélectionnez une zone géographique Azure. Par exemple, **USA Centre**.
    - **Niveau tarifaire** : afin de réduire les coûts, changez la référence (SKU) pour **De base B1** (sous Dev/Test).

    ![Modèle Resource Manager - Exporter le modèle du portail](./media/bicep-tutorial-export-template/resource-manager-template-export.png)
1. Sélectionnez **Examiner et créer**.
1. Sélectionnez **Create** (Créer). Quelques instants sont nécessaires pour créer la ressource.

## <a name="export-template"></a>Exportation du modèle

Actuellement, le portail Azure prend uniquement en charge l’exportation de modèles JSON. Il existe des outils que vous pouvez utiliser pour décompiler des modèles JSON en fichiers Bicep.

1. Sélectionnez **Accéder à la ressource**.

    ![Accéder à la ressource](./media/bicep-tutorial-export-template/resource-manager-template-export-go-to-resource.png)

1. Sélectionnez **Exporter le modèle**.

    ![Modèle Resource Manager - Exporter le modèle](./media/bicep-tutorial-export-template/resource-manager-template-export-template.png)

   La fonction d’exportation de modèle prend l’état actuel d’une ressource et génère un modèle pour le déployer. L’exportation d’un modèle peut être un moyen pratique d’obtenir rapidement le code JSON dont vous avez besoin pour déployer une ressource.

1. La définition de `Microsoft.Web/serverfarms` et la définition de paramètre sont les parties dont vous avez besoin.

    ![Modèle Resource Manager - Exporter le modèle - Modèle exporté](./media/bicep-tutorial-export-template/resource-manager-template-exported-template.png)

    > [!IMPORTANT]
    > En règle générale, le modèle exporté est plus détaillé que ce que vous pourriez souhaiter lors de la création d’un modèle. Par exemple, l’objet SKU du modèle exporté possède cinq propriétés. Ce modèle fonctionne, mais vous pouvez simplement utiliser la propriété `name`. Vous pouvez commencer avec le modèle exporté, puis le modifier à votre convenance pour répondre à vos besoins.

1. Sélectionnez **Télécharger**.  Le fichier zip téléchargé contient un **template.json** et un **parameters.json**. Décompressez les fichiers
1. Accédez à **https://bicepdemo.z22.web.core.windows.net/** , sélectionnez **Decompile** (Décompiler), puis ouvrez **template.json**. Vous obtenez le modèle en Bicep.

## <a name="revise-existing-bicep-file"></a>Réviser le fichier Bicep existant

Le modèle exporté décompilé vous donne la majeure partie du code Bicep dont vous avez besoin, mais vous devez le personnaliser pour votre fichier Bicep. Portez une attention particulière aux différences de paramètres et de variables entre votre fichier Bicep et le fichier Bicep exporté. Bien entendu, le processus d’exportation ignore les paramètres et les variables que vous avez déjà définis dans votre fichier Bicep.

L’exemple suivant montre les ajouts à votre fichier Bicep. Il contient le code exporté, ainsi que quelques modifications. Tout d’abord, il modifie le nom du paramètre pour qu’il corresponde à votre convention de nommage. Ensuite, il utilise votre paramètre location pour l’emplacement du plan App Service. Enfin, il supprime certaines propriétés où la valeur par défaut convient.

Copiez l’intégralité du fichier et remplacez votre fichier Bicep par son contenu.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.bicep" range="1-53" highlight="18,34-51":::

## <a name="deploy-bicep-file"></a>Déployer le fichier Bicep

Utilisez Azure CLI ou Azure PowerShell pour déployer un fichier Bicep.

Si vous n’avez pas créé le groupe de ressources, consultez [Créer un groupe de ressources](bicep-tutorial-create-first-bicep.md#create-resource-group). L’exemple suppose que vous avez défini la variable `bicepFile` sur le chemin du fichier Bicep, comme indiqué dans le [premier tutoriel](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour exécuter cette cmdlet de déploiement, vous devez disposer de la [version la plus récente](/powershell/azure/install-az-ps) d’Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addappserviceplan `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour exécuter cette commande de déploiement, vous devez disposer de la [dernière version](/cli/azure/install-azure-cli) d’Azure CLI.

```azurecli
az deployment group create \
  --name addappserviceplan \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

> [!NOTE]
> Si le déploiement a échoué, utilisez le commutateur `verbose` pour obtenir des informations sur les ressources en cours de création. Utilisez le commutateur `debug` pour obtenir des informations supplémentaires sur le débogage.

## <a name="verify-deployment"></a>Vérifier le déploiement

Vous pouvez vérifier le déploiement en explorant le groupe de ressources à partir du portail Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Dans le menu de gauche, sélectionnez **Groupes de ressources**.
1. Sélectionnez le groupe de ressources sur lequel vous avez effectué le déploiement.
1. Le groupe de ressources contient un compte de stockage et un plan App Service.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous passez au tutoriel suivant, vous n’avez pas besoin de supprimer le groupe de ressources.

Si vous arrêtez maintenant, vous pouvez nettoyer les ressources que vous avez déployées en supprimant le groupe de ressources.

1. Dans le portail Azure, sélectionnez **Groupe de ressources** dans le menu de gauche.
2. Entrez le nom du groupe de ressources dans le champ **Filtrer par nom**.
3. Sélectionnez le nom du groupe de ressources.
4. Sélectionnez **Supprimer le groupe de ressources** dans le menu supérieur.

## <a name="next-steps"></a>Étapes suivantes

Vous avez appris à exporter un modèle JSON à partir du portail Azure, à convertir le modèle JSON en fichier Bicep et à utiliser le modèle exporté pour le développement de votre code Bicep. Vous pouvez également utiliser les modèles de démarrage rapide Azure pour simplifier le développement de code Bicep.

> [!div class="nextstepaction"]
> [Utiliser les modèles de démarrage rapide Azure](bicep-tutorial-quickstart-template.md)
