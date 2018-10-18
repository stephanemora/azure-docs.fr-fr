---
title: Déployer des modèles en ligne de commande dans Azure Stack | Microsoft Docs
description: Découvrez comment utiliser l’interface de ligne de commande (CLI) multiplateforme pour déployer des modèles sur Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 9584177f-4af3-4834-864d-930b09ae0995
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: d40f9e8bf319f327879a71b23f9d9b82da924c2f
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46365432"
---
# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>Déployer des modèles dans Azure Stack à l’aide de la ligne de commande

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Utilisez la ligne de commande pour déployer des modèles Azure Resource Manager dans l’environnement du kit de développement Azure Stack. Les modèles Azure Resource Manager déploient et approvisionnent toutes les ressources de l’application en une seule opération coordonnée.

## <a name="before-you-begin"></a>Avant de commencer

 - [Procédez à l’installation et à la connexion](azure-stack-version-profiles-azurecli2.md) à Azure Stack avec Azure CLI.
 - Télécharger les fichiers *azuredeploy.json* et *azuredeploy.parameters.json* à partir du [modèle Créer un exemple de compte de stockage](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account).
 
## <a name="deploy-template"></a>Déployer un modèle

Accédez au dossier dans lequel ces fichiers ont été téléchargés et exécutez la commande suivante pour déployer le modèle :

```azurecli
az group create "cliRG" "local" –f azuredeploy.json –d "testDeploy" –e azuredeploy.parameters.json
```

Cette commande déploie le modèle dans le groupe de ressources **cliRG** à l’emplacement par défaut d’Azure Stack POC.

## <a name="validate-template-deployment"></a>Valider le déploiement du modèle

Pour voir ce groupe de ressources et le compte de stockage associé, utilisez les commandes suivantes :

```azurecli
az group list

az storage account list
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le déploiement de modèles, consultez :

[Déployer des modèles avec PowerShell](azure-stack-deploy-template-powershell.md)

