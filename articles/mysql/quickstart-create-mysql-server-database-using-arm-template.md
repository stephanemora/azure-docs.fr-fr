---
title: 'Démarrage rapide : Créer une instance Azure DB pour MySQL - Modèle Resource Manager'
description: Dans ce guide de démarrage rapide, découvrez comment créer un serveur Azure Database pour MySQL avec intégration de réseau virtuel, à l’aide d’un modèle Azure Resource Manager.
author: mgblythe
ms.service: mysql
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mblythe
ms.date: 05/19/2020
ms.openlocfilehash: ed908c4711812302fffac2e1ce59d2196426b74c
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185857"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-mysql-server"></a>Démarrage rapide : Création d’un serveur Azure Database pour MySQL à l’aide d’un modèle ARM

Azure Database pour MySQL est un service géré qui vous permet d’exécuter, de gérer et de mettre à l’échelle des bases de données MySQL hautement disponibles dans le cloud. Ce guide de démarrage rapide montre comment utiliser un modèle Azure Resource Manager (modèle ARM) pour créer un serveur Azure Database pour MySQL avec intégration de réseau virtuel. Vous pouvez créer le serveur sur le Portail Azure, dans Azure CLI ou dans Azure PowerShell.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Si votre environnement remplit les prérequis et que vous êtes déjà familiarisé avec l’utilisation des modèles ARM, sélectionnez le bouton **Déployer sur Azure**. Le modèle s’ouvre dans le portail Azure.

[![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-mysql-with-vnet%2fazuredeploy.json)

## <a name="prerequisites"></a>Prérequis

# <a name="portal"></a>[Portail](#tab/azure-portal)

Compte Azure avec un abonnement actif. [Créez-en un gratuitement](https://azure.microsoft.com/free/).

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* Compte Azure avec un abonnement actif. [Créez-en un gratuitement](https://azure.microsoft.com/free/).
* Si vous souhaitez exécuter le code localement, [Azure PowerShell](/powershell/azure/).

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/CLI)

* Compte Azure avec un abonnement actif. [Créez-en un gratuitement](https://azure.microsoft.com/free/).
* Si vous souhaitez exécuter le code localement, [Azure CLI](/cli/azure/).

---

## <a name="review-the-template"></a>Vérifier le modèle

Vous créez un serveur Azure Database pour MySQL avec un ensemble défini de ressources de calcul et de stockage. Pour en savoir plus, consultez [Niveaux tarifaires Azure Database pour MySQL](concepts-pricing-tiers.md). Vous créez ce serveur dans un [groupe de ressources Azure](../azure-resource-manager/management/overview.md).

Le modèle utilisé dans ce démarrage rapide est tiré des [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/101-managed-mysql-with-vnet/).

:::code language="json" source="~/quickstart-templates/101-managed-mysql-with-vnet/azuredeploy.json" range="001-231" highlight="149,162,176,199,213":::

Le modèle définit cinq ressources Azure :

* [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
* [**Microsoft.Network/virtualNetworks/subnets**](/azure/templates/microsoft.network/virtualnetworks/subnets)
* [**Microsoft.DBforMySQL/servers**](/azure/templates/microsoft.dbformysql/servers)
* [**Microsoft.DBforMySQL/servers/virtualNetworkRules**](/azure/templates/microsoft.dbformysql/servers/virtualnetworkrules)
* [**Microsoft.DBforMySQL/servers/firewallRules**](/azure/templates/microsoft.dbformysql/servers/firewallrules)

Vous trouverez d’autres exemples de modèles Azure Database pour MySQL dans la [galerie de modèles de démarrage rapide](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Dbformysql&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Déployer le modèle

# <a name="portal"></a>[Portail](#tab/azure-portal)

Sélectionnez le lien suivant afin de déployer le modèle de serveur Azure Database pour MySQL dans le portail Azure :

[![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-mysql-with-vnet%2fazuredeploy.json)

Dans la page **Déployer Azure Database pour MySQL avec un réseau virtuel** :

1. Pour **Groupe de ressources**, sélectionnez **Créer**, entrez un nom pour le nouveau groupe de ressources, puis sélectionnez **OK**.

2. Si vous avez créé un groupe de ressources, sélectionnez une **Localisation** pour le groupe de ressources et le nouveau serveur.

3. Entrez un **Nom de serveur**, une **Connexion de l’administrateur** et un **Mot de passe de connexion de l’administrateur**.

    ![Fenêtre Déployer Azure Database pour MySQL avec un réseau virtuel, modèle de démarrage rapide Azure, portail Azure](./media/quickstart-create-mysql-server-database-using-arm-template/deploy-azure-database-for-mysql-with-vnet.png)

4. Changez les autres paramètres par défaut si vous le souhaitez :

    * **Abonnement** : sélectionnez l’abonnement Azure que vous souhaitez utiliser pour le serveur.
    * **Capacité de la référence** : capacité vCore, qui peut être *2* (valeur par défaut), *4*, *8*, *16*, *32* ou *64*.
    * **Nom de la référence** : préfixe du niveau de référence, famille de références et capacité de référence, joints par des traits de soulignement, par exemple, *B_Gen5_1*, *GP_Gen5_2* (valeur par défaut) ou *MO_Gen5_32*.
    * **Taille de la référence (Mo)**  : taille de stockage, en mégaoctets, du serveur Azure Database pour MySQL (par défaut, *5120*).
    * **Niveau de référence SKU** : niveau de déploiement, tel que *Basic*, *GeneralPurpose* (valeur par défaut) ou *MemoryOptimized*.
    * **Famille de références** : *Gen4* ou *Gen5* (valeur par défaut), qui indique la génération du matériel pour le déploiement du serveur.
    * **Version de MySQL** : version de MySQL Server à déployer, par exemple *5.6* ou *5.7* (valeur par défaut).
    * **Nombre de jours de conservation de la sauvegarde** : période souhaitée pour la conservation des sauvegardes géoredondantes, en jours (par défaut, *7*).
    * **Sauvegarde géoredondante** : *Activé* ou *Désactivé* (valeur par défaut), en fonction des exigences de géo-reprise d’activité après sinistre.
    * **Nom du réseau virtuel** : nom du réseau virtuel (par défaut, *azure_mysql_vnet*).
    * **Nom du sous-réseau** : nom du sous-réseau (par défaut, *azure_mysql_subnet*).
    * **Nom de la règle de réseau virtuel** : nom de la règle de réseau virtuel autorisant le sous-réseau (par défaut, *AllowSubnet*).
    * **Préfixe d’adresse de réseau virtuel** : préfixe d’adresse du réseau virtuel (par défaut, *10.0.0.0/16*).
    * **Préfixe de sous-réseau** : préfixe de l’adresse du sous-réseau (par défaut, *10.0.0.0/16*).

5. Lisez les conditions générales, puis cochez la case **J’accepte les conditions générales mentionnées ci-dessus**.

6. Sélectionnez **Achat**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Utilisez le code interactif suivant pour créer un serveur Azure Database pour MySQL à l’aide du modèle. Le code vous invite à entrer le nom du nouveau serveur, le nom et la localisation d’un nouveau groupe de ressources ainsi qu’un nom et un mot de passe de compte d’administrateur.

Pour exécuter le code dans Azure Cloud Shell, sélectionnez **Essayer** dans le coin supérieur de n’importe quel bloc de code.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for MySQL server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for MySQL server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-mysql-with-vnet/azuredeploy.json `
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/CLI)

Utilisez le code interactif suivant pour créer un serveur Azure Database pour MySQL à l’aide du modèle. Le code vous invite à entrer le nom du nouveau serveur, le nom et la localisation d’un nouveau groupe de ressources ainsi qu’un nom et un mot de passe de compte d’administrateur.

Pour exécuter le code dans Azure Cloud Shell, sélectionnez **Essayer** dans le coin supérieur de n’importe quel bloc de code.

```azurecli-interactive
echo "Enter a name for the new Azure Database for MySQL server:" &&
read serverName &&
echo "Enter a name for the new resource group where the server will exist:" &&
read resourceGroupName &&
echo "Enter an Azure region (for example, centralus) for the resource group:" &&
read location &&
echo "Enter the Azure Database for MySQL server's administrator account name:" &&
read adminUser &&
echo "Enter the administrator password:" &&
read adminPassword &&
params='serverName='$serverName' administratorLogin='$adminUser' administratorLoginPassword='$adminPassword &&
az group create --name $resourceGroupName --location $location &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-mysql-with-vnet/azuredeploy.json &&
echo "Press [ENTER] to continue ..."
```

---

## <a name="review-deployed-resources"></a>Vérifier les ressources déployées

# <a name="portal"></a>[Portail](#tab/azure-portal)

Pour obtenir une vue d’ensemble de votre nouveau serveur Azure Database pour MySQL, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com), cherchez et sélectionnez **Serveurs Azure Database pour MySQL**.

2. Dans la liste des bases de données, sélectionnez votre nouveau serveur. La page **Vue d’ensemble** de votre nouveau serveur Azure Database pour MySQL s’affiche.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Exécutez le code interactif suivant pour afficher les détails de votre serveur Azure Database pour MySQL. Vous devrez entrer le nom du nouveau serveur.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for MySQL server"
Get-AzResource -ResourceType "Microsoft.DBforMySQL/servers" -Name $serverName | ft
Write-Host "Press [ENTER] to continue..."
```

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/CLI)

Exécutez le code interactif suivant pour afficher les détails de votre serveur Azure Database pour MySQL. Vous devrez entrer le nom et le groupe de ressources du nouveau serveur.

```azurecli-interactive
echo "Enter your Azure Database for MySQL server name:" &&
read serverName &&
echo "Enter the resource group where the Azure Database for MySQL server exists:" &&
read resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DbForMySQL/servers"
```

---

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’en avez plus besoin, supprimez le groupe de ressources, ce qui supprime également les ressources qu’il contient.

# <a name="portal"></a>[Portail](#tab/azure-portal)

1. Dans le [portail Azure](https://portal.azure.com), recherchez et sélectionnez **Groupes de ressources**.

2. Dans la liste des groupes de ressources, choisissez le nom de votre groupe de ressources.

3. Dans la page **Vue d’ensemble** de votre groupe de ressources, sélectionnez **Supprimer le groupe de ressources**.

4. Dans la boîte de dialogue de confirmation, entrez le nom de votre groupe de ressources, puis sélectionnez **Supprimer**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

---

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir un tutoriel pas à pas expliquant comment créer un modèle ARM, consultez :

> [!div class="nextstepaction"]
> [Tutoriel : Créer et déployer votre premier modèle Resource Manager](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
