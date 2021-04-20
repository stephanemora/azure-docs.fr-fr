---
title: Intégration d’une machine à Azure Automanage avec un modèle ARM
description: Découvrez comment intégrer une machine à Azure Automanage avec un modèle Azure Resource Manager.
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 04/09/2021
ms.author: alsin
ms.openlocfilehash: 78cf28903311c542a83c9ace4f794e1cdda9a61c
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368440"
---
# <a name="onboard-a-machine-to-automanage-with-an-azure-resource-manager-arm-template"></a>Intégration d’une machine à Automanage avec un modèle Azure Resource Manager (ARM)


## <a name="overview"></a>Vue d’ensemble
Suivez les étapes ci-dessous pour intégrer une machine pour la gestion automatique des meilleures pratiques avec Automanage. Le modèle ARM ci-dessous crée un objet `configurationProfileAssignment`, qui est la ressource Azure qui représente une machine intégrée à Automanage.

## <a name="prerequisites"></a>Prérequis
* Vous devez avoir créé un compte Automanage existant. Consultez [ce document](./automanage-account.md) pour plus d’informations sur le compte Automanage et la procédure à suivre pour en créer un.
* Vous devez disposer du rôle **contributeur** sur le groupe de ressources contenant les machines que vous souhaitez intégrer à Automanage

## <a name="arm-template-overview"></a>Présentation des modèles ARM
Le modèle ARM suivant intégrera votre machine spécifiée sur les meilleures pratiques d’Azure Automanage. Pour plus d’informations sur le modèle ARM et les étapes de déploiement, reportez-vous à la section Déploiement de modèle ARM [ci-dessous](#arm-template-deployment).
```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "machineName": {
            "type": "String"
        },
        "automanageAccountName": {
            "type": "String"
        },
        "configurationProfileAssignment": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Compute/virtualMachines/providers/configurationProfileAssignments",
            "apiVersion": "2020-06-30-preview",
            "name": "[concat(parameters('machineName'), '/Microsoft.Automanage/', 'default')]",
            "properties": {
                "configurationProfile": "[parameters('configurationProfileAssignment')]",
                "accountId": "[concat(resourceGroup().id, '/providers/Microsoft.Automanage/accounts/', parameters('automanageAccountName'))]"
            }
        }
    ]
}
```

## <a name="arm-template-deployment"></a>Déploiement de modèle ARM
Le modèle ARM ci-dessus crée une attribution de profil de configuration pour votre machine spécifiée, à l’aide d’un compte Automanage spécifié. Si vous n’avez pas créé de compte Automanage, consultez [ce document](./automanage-account.md).

Les valeurs `configurationProfileAssignment` possibles sont les suivantes :
* "Production"
* "DevTest"

Suivez ces étapes pour déployer le modèle ARM :
1. Enregistrez le modèle ARM ci-dessous en tant que `azuredeploy.json`
1. Exécutez le déploiement de modèle ARM avec `az deployment group create --resource-group myResourceGroup --template-file azuredeploy.json`
1. Fournissez les valeurs pour machineName, automanageAccountName et configurationProfileAssignment lorsque vous y êtes invité
1. Vous avez terminé !

Comme avec n’importe quel modèle ARM, il est possible de factoriser les paramètres dans un fichier `azuredeploy.parameters.json` distinct et de les utiliser en tant qu’arguments lors du déploiement.

## <a name="next-steps"></a>Étapes suivantes
Apprenez-en davantage sur Automanage pour [Linux](./automanage-linux.md) et [Windows](./automanage-windows-server.md)