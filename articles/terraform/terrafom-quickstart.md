---
title: Présentation de Terraform sur Azure
description: Commencez à utiliser Terraform sur Azure en déployant une instance Azure Cosmos DB et une instance Azure Container Instances.
services: terraform
author: neilpeterson
ms.service: terraform
ms.topic: quickstart
ms.date: 02/04/2019
ms.author: nepeters
ms.openlocfilehash: 408e07e9b6214dbd223bd4ec7959b00ecc414f1e
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55869175"
---
# <a name="create-a-terraform-configuration-for-azure"></a>Créer une configuration Terraform pour Azure

Dans cet exemple, vous allez apprendre à créer une configuration Terraform et à la déployer sur Azure. Au final, vous aurez déployé une instance Azure Cosmos DB, une instance Azure Container Instances et une application qui fonctionne sur ces deux ressources. Ce document part du principe que tout le travail est effectué dans Azure Cloud Shell (où les outils Terraform sont préinstallés). Si vous souhaitez suivre l’exemple sur votre propre système, vous pouvez installer Terraform en tenant compte des instructions fournies [ici](../virtual-machines/linux/terraform-install-configure.md).

## <a name="create-first-configuration"></a>Créer la première configuration

Dans cette section, vous allez créer la configuration d’une instance Azure Cosmos DB.

Sélectionnez **Essayer maintenant** pour ouvrir Azure Cloud Shell. Ensuite, entrez `code .` pour ouvrir l’éditeur de code de Cloud Shell.

```azurecli-interactive
code .
```

Copiez et collez la configuration Terraform suivante.

Cette configuration modélise un groupe de ressources Azure, un entier aléatoire et une instance Azure Cosmos DB. L’entier aléatoire est utilisé dans le nom de l’instance Cosmos DB. Plusieurs paramètres Cosmos DB sont également configurés. Pour obtenir la liste complète des configurations Terraform pour Cosmos DB, consultez les [informations de référence sur Terraform pour Cosmos DB](https://www.terraform.io/docs/providers/azurerm/r/cosmosdb_account.html).

Quand vous avez terminé, enregistrez le fichier sous le nom `main.tf`. Pour cela, utilisez les points de suspension dans la partie supérieure droite de l’éditeur de code.

```azurecli-interactive
resource "azurerm_resource_group" "vote-resource-group" {
  name     = "vote-resource-group"
  location = "westus"
}

resource "random_integer" "ri" {
  min = 10000
  max = 99999
}

resource "azurerm_cosmosdb_account" "vote-cosmos-db" {
  name                = "tfex-cosmos-db-${random_integer.ri.result}"
  location            = "${azurerm_resource_group.vote-resource-group.location}"
  resource_group_name = "${azurerm_resource_group.vote-resource-group.name}"
  offer_type          = "Standard"
  kind                = "GlobalDocumentDB"

  consistency_policy {
    consistency_level       = "BoundedStaleness"
    max_interval_in_seconds = 10
    max_staleness_prefix    = 200
  }

  geo_location {
    location          = "westus"
    failover_priority = 0
  }
}
```

La commande [terraform init](https://www.terraform.io/docs/commands/init.html) initialise le répertoire de travail. Exécutez `terraform init` dans le terminal Cloud Shell pour préparer le déploiement de la nouvelle configuration.

```azurecli-interactive
terraform init
```

La commande [terraform plan](https://www.terraform.io/docs/commands/plan.html) peut être utilisée pour valider que la configuration est correctement mise en forme et pour visualiser les ressources créées, mises à jour ou détruites. Vous pouvez stocker les résultats dans un fichier et les utiliser par la suite pour appliquer la configuration.

Exécutez `terraform plan` pour tester la nouvelle configuration Terraform.

```azurecli-interactive
terraform plan --out plan.out
```

Appliquez la configuration. Pour cela, utilisez la commande [terraform apply](https://www.terraform.io/docs/commands/apply.html) et spécifiez le nom du fichier de plan. Cette commande déploie les ressources dans votre abonnement Azure.

```azurecli-interactive
terraform apply plan.out
```

Une fois l’opération terminée, vous pouvez voir que le groupe de ressources a été créé et qu’une instance Azure Cosmos DB a été placée dans le groupe de ressources.

## <a name="update-configuration"></a>Mettre à jour la configuration

Mettez à jour la configuration de manière à inclure une instance Azure Container Instances. Le conteneur exécute une application qui lit et écrit des données dans l’instance Cosmos DB.

Copiez la configuration suivante dans la partie inférieure du fichier `main.tf`. Enregistrez le fichier une fois terminé.

Deux variables d’environnement sont définies : `COSMOS_DB_ENDPOINT` et `COSMOS_DB_MASTERKEY`. Ces variables contiennent l’emplacement de la base de données et la clé permettant d’y accéder. Les valeurs de ces variables proviennent de l’instance de base de données créée à l’étape précédente. Ce processus est connu sous le nom d’interpolation. Pour en savoir plus, consultez l’article consacré à la [syntaxe d’interpolation dans Terraform](https://www.terraform.io/docs/configuration/interpolation.html).


La configuration inclut également un bloc de sortie qui retourne le nom de domaine complet de l’instance de conteneur.

```azurecli-interactive
resource "azurerm_container_group" "vote-aci" {
  name                = "vote-aci"
  location            = "${azurerm_resource_group.vote-resource-group.location}"
  resource_group_name = "${azurerm_resource_group.vote-resource-group.name}"
  ip_address_type     = "public"
  dns_name_label      = "vote-aci"
  os_type             = "linux"

  container {
    name   = "vote-aci"
    image  = "microsoft/azure-vote-front:cosmosdb"
    cpu    = "0.5"
    memory = "1.5"
    ports  = {
      port     = 80
      protocol = "TCP"
    }

    secure_environment_variables {
      "COSMOS_DB_ENDPOINT"  = "${azurerm_cosmosdb_account.vote-cosmos-db.endpoint}"
      "COSMOS_DB_MASTERKEY" = "${azurerm_cosmosdb_account.vote-cosmos-db.primary_master_key}"
      "TITLE"               = "Azure Voting App"
      "VOTE1VALUE"          = "Cats"
      "VOTE2VALUE"          = "Dogs"
    }
  }
}

output "dns" {
  value = "${azurerm_container_group.vote-aci.fqdn}"
}
```

Exécutez `terraform plan` pour créer le plan mis à jour et visualiser les changements à effectuer. Vous devriez voir qu’une ressource Azure Container Instances a été ajoutée à la configuration.

```azurecli-interactive
terraform plan --out plan.out
```

Enfin, exécutez `terraform apply` pour appliquer la configuration.

```azurecli-interactive
terraform apply plan.out
```

Une fois terminé, notez le nom de domaine complet de l’instance de conteneur.

## <a name="test-application"></a>Tester l’application

Accédez au nom de domaine complet de l’instance de conteneur. Si tout a bien été configuré, l’application suivante doit apparaître.

![Application de vote Azure](media/terraform-quickstart/azure-vote.jpg)

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous avez terminé, vous pouvez supprimer les ressources et le groupe de ressources Azure à l’aide de la commande [terraform destroy](https://www.terraform.io/docs/commands/destroy.html).

```azurecli-interactive
terraform destroy -auto-approve
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet exemple, vous avez créé, déployé et détruit une configuration Terraform. Pour plus d’informations sur l’utilisation de Terraform dans Azure, consultez la documentation du fournisseur Terraform pour Azure.

> [!div class="nextstepaction"]
> [Fournisseur Terraform pour Azure](https://www.terraform.io/docs/providers/azurerm/)