---
title: Déplacer le registre de conteneurs Azure vers une autre région
description: Déplacez manuellement les paramètres et les données du registre de conteneurs Azure vers une autre région Azure.
ms.topic: article
ms.date: 06/08/2021
ms.openlocfilehash: e2bc00287923a95e2e4d3698b22c4c2ca65bebc6
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122835888"
---
# <a name="manually-move-a-container-registry-to-another-region"></a>Déplacer manuellement un registre de conteneurs vers une autre région

Vous devrez peut-être déplacer un registre de conteneurs Azure d’une région Azure vers une autre. Par exemple, vous pouvez exécuter un pipeline de développement ou héberger une nouvelle cible de déploiement dans une autre région et vous souhaitez fournir un registre proche.

Alors qu' [Azure Resource Mover](../resource-mover/overview.md) ne peut actuellement pas automatiser un déplacement pour un registre de conteneurs Azure, vous pouvez déplacer manuellement un registre de conteneurs vers une autre région :

* Exporter les paramètres de registre dans un modèle Resource Manager
* Utiliser le modèle pour déployer un registre dans une autre région Azure
* Importer le contenu du registre à partir du registre source vers le registre cible

[!INCLUDE [container-registry-geo-replication-include](../../includes/container-registry-geo-replication-include.md)]

## <a name="prerequisites"></a>Prérequis

Azure CLI

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="considerations"></a>Considérations

* Suivez les étapes de cet article pour déplacer le registre vers une autre région dans le même abonnement. Une configuration supplémentaire peut être nécessaire pour déplacer un registre vers un autre abonnement Azure dans le même locataire Active Directory.
* L’exportation et l’utilisation d’un modèle de Gestionnaire des ressources peuvent vous aider à recréer de nombreux paramètres du registre. Vous pouvez modifier le modèle pour configurer des paramètres supplémentaires ou mettre à jour le registre cible après sa création.
* Actuellement, Azure Container Registry ne prend pas en charge le déplacement d’un registre vers un autre locataire Active Directory. Cette limitation s’applique à la fois aux registres chiffrés avec une [clé gérée par le client](container-registry-customer-managed-keys.md) et aux registres non chiffrés.
* Si vous ne parvenez pas à déplacer un registre de la manière décrite dans cet article, créez un nouveau registre, recréez manuellement les paramètres et [importez le contenu du registre dans le registre cible](#import-registry-content-in-target-registry).

## <a name="export-template-from-source-registry"></a>Exporter le modèle à partir du registre source 

utilisez le portail Azure, Azure CLI, Azure PowerShell ou d’autres outils Azure pour exporter un modèle de Gestionnaire des ressources. Pour utiliser le portail Azure :

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre registre source.
1. Dans le menu, sous **Automatisation**, sélectionnez **Exporter un modèle** > **Télécharger**.

    :::image type="content" source="media/manual-regional-move/export-template.png" alt-text="Exporter le modèle pour le registre de conteneurs":::

## <a name="redeploy-target-registry-in-new-region"></a>Redéployer le registre cible dans une nouvelle région

### <a name="modify-template"></a>Modifier un modèle

Examinez les propriétés du registre dans le modèle de fichier JSON que vous avez téléchargé et apportez les modifications nécessaires. Au minimum :

* Remplacez le nom du registre par `defaultValue` le nom souhaité du registre cible.
* Mettez à jour le `location` vers la région Azure souhaitée pour le registre cible.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "registries_myregistry_name": {
            "defaultValue": "myregistry",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.ContainerRegistry/registries",
            "apiVersion": "2020-11-01-preview",
            "name": "[parameters('myregistry_name')]",
            "location": "centralus",
[...]
```

Pour plus d’informations, voir [Utiliser un modèle exporté à partir du portail Azure](../azure-resource-manager/templates/template-tutorial-export-template.md) et la [référence de modèle](/azure/templates/microsoft.containerregistry/registries).

> [!IMPORTANT]
> Si vous souhaitez chiffrer le registre cible à l’aide d’une clé gérée par le client, assurez-vous de mettre à jour le modèle avec les paramètres de l’identité gérée, du coffre de clés et de la clé requis. Vous ne pouvez activer la clé gérée par le client que lorsque vous déployez le registre.
> 
> Pour plus d’informations, consultez [Chiffrer un registre à l’aide d’une clé gérée par le client](./container-registry-customer-managed-keys.md#enable-customer-managed-key---template).

### <a name="create-resource-group"></a>Créer un groupe de ressources 

Créez un groupe de ressources pour le registre cible avec la commande [az group create](/cli/azure/group#az_group_create). L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus*. 

```azurecli
az group create --name myResourceGroup --location eastus
```

### <a name="deploy-target-registry-in-new-region"></a>Déployer le registre cible dans une nouvelle région

Utilisez la commande [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) pour déployer le registre cible à l’aide du modèle :

```azurecli
az deployment group --resource-group myResourceGroup \
   --template-file template.json --name mydeployment
```

> [!NOTE]
> Si vous constatez des erreurs au cours du déploiement, vous devrez peut-être mettre à jour certaines configurations dans le fichier de modèle et relancer la commande.

## <a name="import-registry-content-in-target-registry"></a>Importer le contenu du registre dans le registre cible

Après avoir créé le registre dans la région cible, utilisez la commande [az acr import](/cli/azure/acr#az_acr_import) ou la commande PowerShell équivalente `Import-AzContainerImage` pour importer les images et autres artefacts que vous souhaitez conserver du registre source vers le registre cible. Pour obtenir des exemples de commande, consultez [Importation d’images conteneur dans un registre de conteneurs](container-registry-import-images.md).

* Utilisez les commandes Azure CLI [az acr repository list](/cli/azure/acr/repository#az_acr_repository_list) et [az acr repository show-tags](/cli/azure/acr/repository#az_acr_repository_show_tags) ou Azure PowerShell équivalentes pour vous aider à énumérer le contenu de votre registre source.
* Exécutez la commande d’importation pour des artefacts individuels ou scriptez-la pour qu’elle s’exécute sur une liste d’artefacts.

L’exemple suivant de script Azure CLI énumère les référentiels source et les balises, puis importe les artefacts dans un registre cible au sein du même abonnement Azure. Apportez les modifications nécessaires pour importer des référentiels ou des balises spécifiques. Pour importer à partir d’un registre dans un autre abonnement ou locataire, consultez les exemples dans [Importer des images de conteneur dans un registre de conteneurs](container-registry-import-images.md).

```azurecli
#!/bin/bash
# Modify registry names for your environment
SOURCE_REG=myregistry
TARGET_REG=targetregistry

# Get list of source repositories
REPO_LIST=$(az acr repository list \
    --name $SOURCE_REG --output tsv)

# Enumerate tags and import to target registry
for repo in $REPO_LIST; do
    TAGS_LIST=$(az acr repository show-tags --name $SOURCE_REG --repository $repo --output tsv);
    for tag in $TAGS_LIST; do
        echo "Importing $repo:$tag";
        az acr import --name $TARGET_REG --source $SOURCE_REG.azurecr.io/$repo":"$tag;
    done
done
```



## <a name="verify-target-registry"></a>Vérifier le registre cible

Vérifiez les informations suivantes dans votre registre cible :

* Paramètres du registre tels que le nom du registre, le niveau de service, l’accès public et les réplications
* Référentiels et balises pour le contenu que vous souhaitez conserver.


### <a name="additional-configuration"></a>Configuration supplémentaire

* Si nécessaire, configurez manuellement les paramètres dans le registre cible, tels que les points de terminaison privés, les règles d’accès IP et les identités gérées.

* Mettez à jour les systèmes de développement et de déploiement pour utiliser le registre cible au lieu du registre source.

* Mettez à jour les règles de pare-feu du client pour autoriser l’accès au registre cible.

## <a name="delete-original-registry"></a>Supprimer le registre d’origine

Une fois que vous avez déployé avec succès le registre cible, migré le contenu et vérifié les paramètres de registre, vous pouvez supprimer le registre source.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur l’[importation d’images conteneur](container-registry-import-images.md) dans un registre de conteneurs Azure à partir d’un registre public ou d’un autre registre privé. 
* Consultez les [Informations de référence sur les modèles Resource Manager](/azure/templates/microsoft.containerregistry/registries) pour le registre de conteneurs Azure.