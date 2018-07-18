---
title: Télécharger les outils Azure Stack à partir de GitHub | Microsoft Docs
description: Découvrez comment télécharger les outils nécessaires pour utiliser Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 28F360AD-789A-488D-965F-FC6E6CCF3329
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: mabrigg
ms.openlocfilehash: f4c4d907fe76c5e6ee5893b5b4dd1b2d766877a7
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287251"
---
# <a name="download-azure-stack-tools-from-github"></a>Télécharger les outils Azure Stack à partir de GitHub

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

AzureStack-Tools est un dépôt GitHub qui héberge des modules PowerShell que vous pouvez utiliser pour gérer et déployer des ressources sur Azure Stack.

## <a name="download-targets"></a>Télécharger des cibles

Vous pouvez télécharger et utiliser ces modules PowerShell dans le Kit de développement Azure Stack, ou sur un client externe Windows qui utilise une connexion VPN.

## <a name="how-to-get-the-tools"></a>Comment obtenir les outils

Pour obtenir ces outils, clonez le dépôt GitHub AzureStack-Tools ou téléchargez le dossier AzureStack-Tools en exécutant le script suivant :

```PowerShell
# Change directory to the root directory
cd \

# Download the tools archive
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory
cd AzureStack-Tools-master

```

## <a name="functionalities-provided-by-the-modules"></a>Fonctionnalités fournies par les modules

Le dépôt AzureStack-Tools contient des modules PowerShell qui prennent en charge les fonctionnalités suivantes pour Azure Stack :

| Fonctionnalités | Description | Qui peut utiliser ce module ? |
| --- | --- | --- |
| [Fonctionnalités de cloud](azure-stack-validate-templates.md) | Utilisez ce module pour obtenir les fonctionnalités cloud d’un cloud. Par exemple, vous pouvez obtenir les fonctionnalités de cloud telles que la version d’API, les ressources Azure Resource Manager, les extensions de machine virtuelle, et ainsi de suite, pour Azure Stack et les clouds Azure à l’aide de ce module. | Les administrateurs et les utilisateurs de cloud |
| [Stratégie Resource Manager pour Azure Stack](azure-stack-policy-module.md) | Utilisez ce module pour configurer un abonnement Azure ou un groupe de ressources Azure avec la même gestion des versions et la même disponibilité de service qu’Azure Stack. | Les administrateurs et les utilisateurs de cloud |
| [Connexion à Azure Stack](azure-stack-connect-azure-stack.md) | Utilisez ce module pour vous connecter à une instance d’Azure Stack par le biais de PowerShell et pour configurer la connectivité VPN à Azure Stack. | Les administrateurs et les utilisateurs de cloud |
| [Validateur de modèle](azure-stack-validate-templates.md) | Utilisez ce module pour vérifier si un modèle existant ou nouveau peut être déployé sur Azure Stack. | Les administrateurs et les utilisateurs de cloud |

## <a name="next-steps"></a>Étapes suivantes

* [Configurez l’environnement PowerShell de l’utilisateur Azure Stack.](azure-stack-powershell-configure-user.md)
* [Se connecter au Kit de développement Azure Stack par le biais d’un réseau privé virtuel](azure-stack-connect-azure-stack.md)
