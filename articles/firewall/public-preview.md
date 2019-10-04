---
title: Activer la préversion publique de Pare-feu Azure
description: Utiliser Azure PowerShell pour activer la préversion publique de Pare-feu Azure
author: vhorne
ms.service: firewall
services: firewall
ms.topic: article
ms.date: 7/11/2018
ms.author: victorh
ms.openlocfilehash: fe1b8f9d56b0f4faa0baa25463b2aa29a59715cb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60193058"
---
# <a name="enable-the-azure-firewall-public-preview"></a>Activer la préversion publique de Pare-feu Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

## <a name="enable-using-azure-powershell"></a>Activer à l’aide d’Azure PowerShell

Pour activer la préversion publique de Pare-feu Azure, utilisez les commandes Azure PowerShell suivantes :

```PowerShell
Register-AzProviderFeature -FeatureName AllowRegionalGatewayManagerForSecureGateway -ProviderNamespace Microsoft.Network
Register-AzProviderFeature -FeatureName AllowAzureFirewall -ProviderNamespace Microsoft.Network
```

Il faut compter 30 minutes pour l’inscription de la fonctionnalité. Vous pouvez vérifier votre statut d’enregistrement en exécutant les commandes Azure PowerShell suivantes :

```powershell

Get-AzProviderFeature -FeatureName AllowRegionalGatewayManagerForSecureGateway -ProviderNamespace Microsoft.Network

Get-AzProviderFeature -FeatureName AllowAzureFirewall -ProviderNamespace Microsoft.Network
```
À l’issue de l’installation, exécutez la commande suivante :

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="next-steps"></a>Étapes suivantes

- [Tutoriel : Déployer et configurer un pare-feu Azure à l’aide du portail Azure](tutorial-firewall-deploy-portal.md)

