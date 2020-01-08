---
title: Mettre à jour le script de déploiement de modèle de Visual Studio pour utiliser Az Powershell
description: Mettre à jour le script de déploiement de modèle de Visual Studio depuis AzureRM vers Az Powershell
author: cweining
ms.topic: quickstart
ms.date: 09/27/2019
ms.author: cweining
ms.openlocfilehash: c34cde5593b48c301826be3dd2641dc2490ee88d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75455473"
---
# <a name="updating-the-visual-studio-template-deployment-script-to-use-az-powershell"></a>Mise à jour du script de déploiement de modèle de Visual Studio pour utiliser Az Powershell

Visual Studio 16.4 prend en charge l’utilisation d’Az Powershell dans le script de déploiement de modèle. Visual Studio n’installe pas Az PowerShell ou ne met pas à jour automatiquement le script de déploiement dans votre projet de groupe de ressources. Pour utiliser la version plus récente d’Az PowerShell, vous devez effectuer les quatre opérations suivantes :
1. Désinstaller AzureRM PowerShell
1. Installer Az Powershell
1. Effectuer une mise à jour vers Visual Studio 16.4
1. Mettre à jour le script de déploiement dans votre projet

## <a name="uninstall-azurerm-powershell"></a>Désinstaller AzureRM PowerShell
Pour désinstaller AzureRM PowerShell, suivez ces [instructions](https://docs.microsoft.com/powershell/azure/uninstall-az-ps?view=azps-2.7.0#uninstall-the-azurerm-module).

## <a name="install-az-powershell"></a>Installer Az Powershell
Pour installer Az Powershell, suivez ces [instructions](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.7.0).

## <a name="update-visual-studio-to-164"></a>Mettre à jour Visual Studio vers la version 16.4
Effectuez une mise à jour de Visual Studio vers la version 16.4 quand celle-ci est disponible. Pendant la mise à niveau, assurez-vous que le composant Azure PowerShell n’est pas activé. Étant donné que vous avez installé Az Powershell via la galerie, vous ne souhaitez pas la version AzureRM de PowerShell qui s’installe avec Visual Studio.

Si vous avez déjà effectué la mise à niveau vers la version 16.4 et que le composant Azure PowerShell a été activé, vous pouvez le désinstaller en exécutant Visual Studio Installer et en le désactivant dans la charge de travail Azure ou dans la page des composants individuels.

## <a name="update-the-deployment-script-in-your-project"></a>Mettre à jour le script de déploiement dans votre projet
Remplacez toutes les occurrences de la chaîne « AzureRm » par « Az » dans votre script de déploiement. Vous pouvez consulter les révisions dans ce dépôt [Gist](https://gist.github.com/cweining/d2da2479418ea403499c4306dcf4f619) pour voir les changements. Pour plus d’informations sur la mise à niveau des scripts vers AZ PowerShell, reportez-vous à cette [documentation](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-2.5.0).


