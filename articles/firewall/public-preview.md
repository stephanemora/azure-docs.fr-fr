---
title: Activer la préversion publique de Pare-feu Azure
description: Utiliser Azure PowerShell pour activer la préversion publique de Pare-feu Azure
author: vhorne
ms.service: firewall
services: firewall
ms.topic: article
ms.date: 7/11/2018
ms.author: victorh
ms.openlocfilehash: 263b16a419b5ff20a9b6d62860385f92c2a18f9c
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38992197"
---
# <a name="enable-the-azure-firewall-public-preview"></a>Activer la préversion publique de Pare-feu Azure

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

## <a name="enable-using-azure-powershell"></a>Activer à l’aide d’Azure PowerShell

Pour activer la préversion publique de Pare-feu Azure, utilisez les commandes Azure PowerShell suivantes :

```PowerShell
Register-AzureRmProviderFeature -FeatureName AllowRegionalGatewayManagerForSecureGateway -ProviderNamespace Microsoft.Network

Register-AzureRmProviderFeature -FeatureName AllowAzureFirewall -ProviderNamespace Microsoft.Network
```

Il faut compter 30 minutes pour l’inscription de la fonctionnalité. Vous pouvez vérifier votre statut d’enregistrement en exécutant les commandes Azure PowerShell suivantes :

```PowerShell

Get-AzureRmProviderFeature -FeatureName AllowRegionalGatewayManagerForSecureGateway -ProviderNamespace Microsoft.Network

Get-AzureRmProviderFeature -FeatureName AllowAzureFirewall -ProviderNamespace Microsoft.Network
```
À l’issue de l’installation, exécutez la commande suivante :

```PowerShell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="next-steps"></a>Étapes suivantes

- [Tutoriel : Déployer et configurer Pare-feu Azure avec le portail Azure](tutorial-firewall-deploy-portal.md)

