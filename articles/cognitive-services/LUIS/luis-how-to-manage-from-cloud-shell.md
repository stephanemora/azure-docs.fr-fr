---
title: Données d’utilisation - Cloud Shell
titleSuffix: Language Understanding - Azure Cognitive Services
description: Découvrez comment obtenir des informations sur l'utilisation du nombre d'accès aux points de terminaison sur LUIS dans Azure Cloud Shell.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/18/2018
ms.author: diberry
ms.openlocfilehash: 703332ece0208856bfbedb852b4b1e985d157dc9
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53605942"
---
# <a name="usage-data-for-luis-service-from-azure-cloud-shell"></a>Données d’utilisation pour le service LUIS à partir d’Azure Cloud Shell

Découvrez comment obtenir des informations sur l'utilisation du nombre d'accès aux points de terminaison sur LUIS dans Azure Cloud Shell.

Le portail Azure vous permet de saisir des cmdlets PowerShell pour utiliser des ressources LUIS. 

Ces cmdlets permettent de [créer](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/new-azurermcognitiveservicesaccount?view=azurermps-6.0.0) un abonnement LUIS, d’obtenir des informations sur l’abonnement, y compris son [utilisation](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/get-azurermcognitiveservicesaccountusage?view=azurermps-6.0.0), et de [supprimer](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/remove-azurermcognitiveservicesaccount?view=azurermps-6.0.0) l’abonnement. 

## <a name="cloud-shell-storage-account-and-authentication"></a>Compte de stockage de l’interpréteur de commandes cloud et authentification

Pour pouvoir utiliser PowerShell dans l’[interpréteur de commandes cloud](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell) du portail Azure, vous devez disposer d’un compte de stockage Azure. Si vous n’avez pas de [compte de stockage](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage), vous êtes invité à en créer un. Le compte de stockage vous permet d’enregistrer des scripts PowerShell dans l’interpréteur de commandes cloud.  

Vous devez également vous authentifier auprès d’Azure dans l’interpréteur de commandes cloud pour accéder aux ressources. 

Une fois que vous avez un compte de stockage et que vous êtes authentifié, vous pouvez exécuter les cmdlets PowerShell.

## <a name="open-cloud-shell"></a>Ouvrir Cloud Shell

Lorsque vous utilisez l’interpréteur de commandes cloud du portail Azure, vous êtes toujours sur la dernière version de PowerShell. 

Utilisez le bouton **Lancer Cloud Shell** pour ouvrir l’interpréteur de commandes cloud ou ouvrez un navigateur avec [https://shell.azure.com](https://shell.azure.com). Sélectionnez PowerShell comme environnement. Si vous n’avez pas de compte Stockage Azure, vous devez en créer un. 

<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><image src="https://shell.azure.com/images/launchcloudshell.png" alt="Start powershell" /></a>

## <a name="luis-endpoint-usage-information"></a>Informations sur l’utilisation des points de terminaison LUIS

La cmdlet PowerShell 6.x, `Get-AzureRmCognitiveServicesAccountUsage`, fournit des informations sur l’utilisation de Microsoft Cognitive Services, y compris LUIS. [Get-AzureRmCognitiveServicesAccountUsage](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/get-azurermcognitiveservicesaccountusage?view=azurermps-6.0.0) nécessite le groupe de ressources et le nom de la ressource du service. 

Syntaxe de la commande :

```powershell
Get-AzureRmCognitiveServicesAccountUsage -ResourceGroupName my-resource-group -Name my-luis-service-name
```

Dans l’exemple suivant, le nom du groupe de ressources est `luis-westus-rg`, et le nom de l’abonnement au service LUIS est `luis-westus-1`. Ces deux noms sont choisis lors de la création du service LUIS. 

La cmdlet renvoie des informations sur l’utilisation de 16 des 10 000 accès de point de terminaison utilisés pendant une période de 30 jours allant jusqu’au 7 juin :

```powershell
CurrentValue  : 16
Name          : LUIS.Calls
Limit         : 10000
Status        : Included
Unit          : Count
QuotaPeriod   : 30.00:00:00
NextResetTime : 2018-06-07T18:28:52Z
```

Enregistrez la commande en tant que fichier PowerShell, *.ps1, dans le compte de stockage Azure associé à l’interpréteur de commandes cloud et exécutez-la à tout moment. 

![Exécuter un script à partir du stockage](./media/luis-how-to-manage-from-powershell/run-script-from-storage.png)

Une fois que le script est enregistré sur le disque cloud, vous pouvez exécuter le script PowerShell de l’interpréteur de commandes cloud de l’application de téléphone Azure. 

![Exécuter un script à partir du stockage dans une application de téléphone](./media/luis-how-to-manage-from-powershell/phone-app.png)