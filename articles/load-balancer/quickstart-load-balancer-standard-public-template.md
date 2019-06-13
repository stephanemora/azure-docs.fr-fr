---
title: 'Démarrage rapide : Créer un équilibreur de charge standard - modèle Azure Resource Manager'
titlesuffix: Azure Load Balancer
description: Ce guide de démarrage rapide vous montre comment créer un équilibreur de charge standard avec le modèle Azure Resource Manager.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: I want to create a Standard Load Balancer by using Azure Resource Manager template so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/30/2019
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 0e47560ca43b23f4779da701f3e6f11f53a6b1ce
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66480405"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-by-using-azure-resource-manager-template"></a>Démarrage rapide : créer un équilibreur de charge standard pour équilibrer la charge des machines virtuelles avec un modèle Azure Resource Manager

L’équilibrage de charge offre un niveau plus élevé de disponibilité et d’évolutivité en répartissant les demandes entrantes sur plusieurs machines virtuelles. Vous pouvez déployer un modèle Azure Resource Manager afin de créer un équilibreur de charge permettant d’équilibrer la charge des machines virtuelles. Ce démarrage rapide vous montre comment équilibrer la charge des machines virtuelles à l’aide d’un équilibreur de charge standard.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-a-standard-load-balancer"></a>Créer un équilibreur de charge standard

Dans cette section, vous allez créer un équilibreur de charge standard qui équilibre la charge de machines virtuelles. L’équilibreur de charge standard prend uniquement en charge une adresse IP publique standard. Lorsque vous créez un équilibreur de charge standard, vous devez également créer une nouvelle adresse IP publique standard configurée en tant que frontale (nommée *LoadBalancerFrontend* par défaut) pour cet équilibreur de charge standard. Il existe de nombreuses méthodes pour créer un équilibreur de charge standard. Dans ce démarrage rapide, vous utilisez Azure PowerShell pour déployer un [modèle Resource Manager](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/101-load-balancer-standard-create/azuredeploy.json). Les modèles Azure Resource Manager sont des fichiers JSON qui définissent les ressources nécessaires au déploiement de votre solution. Pour comprendre les concepts associés au déploiement et à la gestion de vos solutions Azure, voir [Documentation Azure Resource Manager](/azure/azure-resource-manager/). Pour plus d’informations sur le modèle Azure Load Balancer, consultez [Modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

Pour déployer le modèle, sélectionnez **Try it** afin d’ouvrir Azure Cloud Shell, puis collez le script PowerShell suivant dans la fenêtre de l’interpréteur de commandes. Pour coller le code, cliquez avec le bouton droit sur la fenêtre de l’interpréteur de commandes, puis sélectionnez **Coller**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name with 12 or less letters or numbers that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUserName = Read-Host -Prompt "Enter the virtual machine administrator account name"
$adminPassword = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/101-load-balancer-standard-create/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -location $location -adminUsername $adminUsername -adminPassword $adminPassword
```

Notez que le nom du groupe de ressources est le nom du projet avec **rg** ajouté. Vous aurez besoin du nom du groupe de ressources dans la section suivante.  La création des ressources prend quelques minutes.

## <a name="test-the-load-balancer"></a>Tester l’équilibreur de charge

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Sélectionnez **Groupes de ressources** dans le volet gauche.
1. Sélectionnez le groupe de ressources que vous avez créé dans la dernière section.  Le nom du groupe de ressources par défaut est le nom du projet avec **rg** ajouté.
1. Sélectionnez l’équilibreur de charge.  Il n’existe qu’un seul équilibreur de charge. Le nom par défaut est le nom du projet avec **-lb** ajouté.
1. Copiez l’adresse IP publique (uniquement la partie adresse IP), puis collez cette donnée dans la barre d’adresses de votre navigateur. La page par défaut du serveur Web IIS s’affiche sur le navigateur.

   ![Serveur Web IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Pour visualiser la distribution de trafic Load Balancer sur les trois machines virtuelles, vous pouvez forcer l’actualisation de votre navigateur web depuis l’ordinateur client.

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, supprimez le groupe de ressources, l’équilibreur de charge Load Balancer et toutes les ressources associées. Pour ce faire, sélectionnez le groupe de ressources qui contient l’équilibreur de charge depuis le portail Azure, puis cliquez sur **Supprimer le groupe de ressources**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez créé un Standard Load Balancer, associé des machines virtuelles à celui-ci, configuré la règle de trafic d’équilibreur de charge, la sonde d’intégrité, puis testé Load Balancer. Pour en savoir plus sur Azure Load Balancer, consultez les didacticiels qui lui sont consacrés.

> [!div class="nextstepaction"]
> [Didacticiels Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
