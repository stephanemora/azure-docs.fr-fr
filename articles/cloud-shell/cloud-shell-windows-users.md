---
title: Azure Cloud Shell pour les utilisateurs Windows | Microsoft Docs
description: Guide pour les utilisateurs qui ne sont pas familiers avec les systèmes Linux
services: azure
documentationcenter: ''
author: maertendMSFT
manager: hemantm
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/03/2018
ms.author: damaerte
ms.openlocfilehash: 5e318a0f64033aa0c4b306e547c11e1994afa229
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2018
ms.locfileid: "37861086"
---
# <a name="powershell-in-azure-cloud-shell-for-windows-users"></a>PowerShell dans Azure Cloud Shell pour les utilisateurs Windows

En mai 2018, des modifications ont été [annoncées](https://azure.microsoft.com/blog/pscloudshellrefresh/) pour PowerShell dans Azure Cloud Shell.  L’expérience PowerShell dans Azure Cloud Shell correspond désormais à PowerShell Core 6 dans Linux.
Avec cette modification, certains aspects de PowerShell dans Cloud Shell peuvent être différents de ce qui est attendu dans Windows PowerShell 5.1.

## <a name="case-sensitivity"></a>Respect de la casse

Dans Windows, le système de fichiers ne respecte pas la casse.  Dans Linux, le système de fichiers respecte la casse.
Cela signifie que précédemment `file.txt` et `FILE.txt` étaient considérés comme le même fichier, mais maintenant ils sont considérés comme des fichiers différents.
La casse appropriée doit être utilisée lors de la saisie semi-automatique via la touche `tab` dans le système de fichiers.  Les expériences spécifiques à PowerShell, comme les cmdlets `tab` ne respectent pas la casse. 

## <a name="windows-powershell-alias-vs-linux-utilities"></a>Alias Windows PowerShell et utilitaires Linux

Les commandes existantes dans Linux, telles que `ls`, `sort` et `sleep` sont prioritaires sur leurs alias PowerShell.  Voici les alias supprimés courants, ainsi que les commandes équivalentes :  

|Alias supprimé   |Commande équivalente   |
|---|---|
|`ls`     | `dir` <br> `Get-ChildItem` |
|`sort`   | `Sort-Object` |
|`sleep`  | `Start-Sleep` |

## <a name="persisting-home-vs-homeclouddrive"></a>$home persistant et $home\clouddrive

Pour les utilisateurs qui conservent des scripts et autres fichiers dans leur lecteur cloud, le répertoire $HOME est maintenant conservé entre les sessions.

## <a name="powershell-profile"></a>Profil PowerShell

Par défaut, aucun profil PowerShell n’est créé.  Pour créer votre profil, créez un répertoire `PowerShell` sous `$HOME/.config`.  Dans `$HOME/.config/PowerShell`, vous pouvez créer votre profil sous le nom `Microsoft.PowerShell_profile.ps1`.

## <a name="whats-new-in-powershell-core-6"></a>Nouveautés dans PowerShell Core 6

Pour plus d’informations sur les nouveautés dans PowerShell Core 6, consultez les [documents sur PowerShell](https://docs.microsoft.com/powershell/scripting/whats-new/what-s-new-in-powershell-core-60?view=powershell-6) et le billet de blog [Bien démarrer avec PowerShell Core](https://blogs.msdn.microsoft.com/powershell/2017/06/09/getting-started-with-powershell-core-on-windows-mac-and-linux/)
