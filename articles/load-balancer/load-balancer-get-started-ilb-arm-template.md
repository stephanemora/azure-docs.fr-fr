---
title: Créer un équilibreur de charge interne - Modèle Azure
titleSuffix: Azure Load Balancer
description: Découvrez comment créer un équilibreur de charge interne à l’aide d’un modèle dans Resource Manager
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18, devx-track-azurecli
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: fc64f4b3d338704568cb70a542a82fe58d08d4d9
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87502305"
---
# <a name="create-an-internal-load-balancer-using-a-template"></a>Créer un équilibrage de charge interne à l’aide d’un modèle

> [!div class="op_single_selector"]
> * [Portail Azure](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [Modèle](../load-balancer/load-balancer-get-started-ilb-arm-template.md)

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="deploy-the-template-by-using-click-to-deploy"></a>Déployer le modèle en un clic

L’exemple de modèle disponible dans le référentiel public utilise un fichier de paramètres contenant les valeurs par défaut utilisées pour générer le scénario décrit ci-dessus. Pour déployer ce modèle en un clic, suivez [ce lien](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-internal-load-balancer), cliquez sur **Déployer dans Azure**, remplacez les valeurs de paramètre par défaut si nécessaire, puis suivez les instructions dans le portail.

## <a name="deploy-the-template-by-using-powershell"></a>Déployer le modèle à l’aide de PowerShell

Pour déployer le modèle téléchargé à l’aide de PowerShell, suivez les étapes ci-dessous.

1. Si vous n'avez jamais utilisé Azure PowerShell, consultez la page [Installation et configuration d'Azure PowerShell](/powershell/azure/) et suivez les instructions jusqu'à la fin pour vous connecter à Azure et sélectionner votre abonnement.
2. Téléchargez le fichier de paramètres sur votre disque local.
3. Modifiez et enregistrez le fichier.
4. Pour créer un groupe de ressources à l’aide du modèle, exécutez la cmdlet **New-AzResourceGroupDeployment**.

    ```azurepowershell-interactive
    New-AzResourceGroupDeployment -Name TestRG -Location westus `
        -TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json' `
        -TemplateParameterFile 'C:\temp\azuredeploy.parameters.json'
    ```

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Déployer le modèle à l’aide de l’interface de ligne de commande Azure

Pour déployer le modèle à l’aide de l’interface de ligne de commande Azure, procédez comme suit.

1. Si vous n’avez jamais utilisé l’interface de ligne de commande Azure, consultez [Installer et configurer l’interface de ligne de commande Azure](../cli-install-nodejs.md) et suivez les instructions jusqu’à l’étape où vous sélectionnez votre compte et votre abonnement Azure.
2. Accédez à [https://shell.azure.com](https://shell.azure.com) pour ouvrir Cloud Shell dans votre navigateur. Exécutez la commande **azure config mode** pour passer en mode Resource Manager, comme illustré ci-dessous.

    ```console
    azure config mode arm
    ```

    Voici le résultat attendu pour la commande ci-dessus :

    ```output
    info:    New mode is arm
    ```

3. Ouvrez le fichier de paramètres, sélectionnez son contenu et enregistrez-le dans un fichier sur votre ordinateur. Pour cet exemple, nous avons enregistré le fichier de paramètres sous le nom *parameters.json*.
4. Exécutez la commande **azure group deployment create** pour déployer le nouvel équilibreur de charge interne à l’aide du modèle et des fichiers de paramètres que vous avez téléchargés et modifiés plus tôt. La liste affichée après le résultat présente les différents paramètres utilisés.

    ```console
    azure group create --name TestRG --location westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json --parameters-file parameters.json
    ```

## <a name="next-steps"></a>Étapes suivantes

[Configurer le mode de distribution d’équilibreur de charge à l’aide de l’affinité d’IP source](load-balancer-distribution-mode.md)

[Configuration des paramètres du délai d’expiration TCP inactif pour votre équilibrage de charge](load-balancer-tcp-idle-timeout.md)

Pour connaître la syntaxe JSON et les propriétés d’un équilibreur de charge dans un modèle, consultez [Microsoft.Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers).
