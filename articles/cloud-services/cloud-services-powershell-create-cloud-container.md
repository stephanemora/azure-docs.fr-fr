---
title: Créer un conteneur de service cloud (classique) avec PowerShell | Microsoft Docs
description: Cet article explique comment créer un conteneur de service cloud avec PowerShell. Le conteneur héberge les rôles Web et de travail.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 39fe439e37b1af4e833396ef83205729af8c7ad3
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102610413"
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-classic-container"></a>Utiliser une commande Azure PowerShell pour créer un conteneur de service cloud (classique) vide

> [!IMPORTANT]
> [Azure Cloud Services (support étendu)](../cloud-services-extended-support/overview.md) est un nouveau modèle de déploiement basé sur Azure Resource Manager pour le produit Azure Cloud Services. Du fait de ce changement, les instances Azure Cloud Services qui s’exécutent sur le modèle de déploiement basé sur Azure Service Manager ont été renommées Cloud Services (classique). Tous les nouveaux déploiements doivent passer par [Cloud Services (support étendu)](../cloud-services-extended-support/overview.md).

Cet article explique comment créer rapidement un conteneur Cloud Services à l’aide d’applets de commande Azure PowerShell. Procédez comme suit :

1. Installez l’applet de commande Microsoft Azure PowerShell à partir de la page [Téléchargements d’Azure PowerShell](https://aka.ms/webpi-azps) .
2. Ouvrez l’invite de commandes PowerShell.
3. Utilisez l’applet de commande [Add-AzureAccount](/powershell/module/servicemanagement/azure.service/add-azureaccount) pour vous connecter.

   > [!NOTE]
   > Pour obtenir des instructions complémentaires sur l’installation de l’applet de commande Azure PowerShell et sur la connexion à votre abonnement Azure, consultez l’article [Installation et configuration d’Azure PowerShell](/powershell/azure/).
   >
   >
4. Créez un conteneur de service cloud Azure vide à l’aide de l’applet de commande **New-AzureService** .

   ```
   New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   ```

5. Suivez cet exemple pour appeler l’applet de commande :

   ```powershell
   New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
   ```

Pour plus d’informations sur la création du service cloud Azure, exécutez :

```powershell
Get-help New-AzureService
```

### <a name="next-steps"></a>Étapes suivantes

* Pour gérer le déploiement du service cloud, reportez-vous aux rubriques relatives aux commandes [Get-AzureService](/powershell/module/servicemanagement/azure.service/Get-AzureService), [Remove-AzureService](/powershell/module/servicemanagement/azure.service/Remove-AzureService) et [Set-AzureService](/powershell/module/servicemanagement/azure.service/set-azureservice). Pour plus d’informations, vous pouvez également consulter l’article [Configuration des services cloud](cloud-services-how-to-configure-portal.md) .
* Pour publier votre projet de service cloud dans Azure, consultez l’exemple de code **PublishCloudService.ps1** issu du [référentiel de services cloud archivé](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Scripts/cloud-services-continuous-delivery).