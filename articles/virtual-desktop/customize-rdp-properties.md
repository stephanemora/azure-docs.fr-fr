---
title: Personnaliser les propriétés de RDP avec PowerShell – Azure
description: Comment personnaliser les propriétés de RDP pour Windows Virtual Desktop avec des applets de commande PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4a0f193437353bac1f5998b50b9d7b4d43bedefa
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128067"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Personnaliser les propriétés de RDP pour un pool d’hôtes

La personnalisation des propriétés du protocole RDP (Remote Desktop Protocol) d’un pool d’hôtes, comme l’utilisation de plusieurs écrans et la redirection audio, vous permet d’offrir une expérience optimale pour vos utilisateurs en fonction de leurs besoins. Vous pouvez personnaliser les propriétés de RDP dans Windows Virtual Desktop avec le paramètre **-CustomRdpProperty** dans l’applet de commande **Set-RdsHostPool**.

Pour obtenir la liste complète des propriétés prises en charge et leur valeur par défaut, consultez [Paramètres de fichier RDP pris en charge](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context).

Tout d’abord, si vous ne l’avez pas déjà fait, [téléchargez et importez le module PowerShell Windows Virtual Desktop](/powershell/windows-virtual-desktop/overview/) à utiliser dans votre session PowerShell. Exécutez ensuite l’applet de commande suivante pour vous connecter à votre compte :

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="default-rdp-properties"></a>Propriétés RDP par défaut

Par défaut, les fichiers RDP publiés contiennent les propriétés suivantes :

|Propriétés RDP | Bureaux | RemoteApps |
|---|---| --- |
| Mode à plusieurs écrans | activé | N/A |
| Redirections de lecteur activées | Lecteurs, presse-papiers, imprimantes, ports COM, périphériques USB et cartes à puce| Lecteurs, presse-papiers et imprimantes |
| Mode audio distant | Lire localement | Lire localement |

Toutes les propriétés personnalisées que vous définissez pour le pool hôte remplacent ces valeurs par défaut.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Ajouter ou modifier une seule propriété RDP personnalisée

Pour ajouter ou modifier une seule propriété RDP personnalisée, exécutez l’applet de commande PowerShell suivante :

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```

![Capture d’écran de l’applet de commande PowerShell Get-RDSRemoteApp avec le nom et le nom convivial en évidence.](media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Ajouter ou modifier plusieurs propriétés RDP personnalisées

Pour ajouter ou modifier plusieurs propriétés RDP personnalisées, exécutez les applets de commande PowerShell suivantes en fournissant les propriétés RDP personnalisées sous forme de chaîne avec des points-virgules comme séparateur :

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```

![Capture d’écran de l’applet de commande PowerShell Get-RDSRemoteApp avec le nom et le nom convivial en évidence.](media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>Réinitialiser toutes les propriétés RDP personnalisées

Vous pouvez réinitialiser des propriétés RDP personnalisées à leurs valeurs par défaut en suivant les instructions de [Ajouter ou modifier une seule propriété RDP personnalisée](#add-or-edit-a-single-custom-rdp-property), ou vous pouvez réinitialiser toutes les propriétés RDP personnalisées pour un pool d’hôtes en exécutant l’applet de commande PowerShell suivante :

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```

![Capture d’écran de l’applet de commande PowerShell Get-RDSRemoteApp avec le nom et le nom convivial en évidence.](media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez personnalisé les propriétés RDP pour un pool d’hôtes donné, connectez-vous à un client Windows Virtual Desktop pour les tester dans le cadre d’une session utilisateur. Ces deux procédures suivantes vous indiquent comment vous connecter à une session à l’aide du client de votre choix :

- [Se connecter avec le client Windows Desktop](connect-windows-7-and-10.md)
- [Se connecter avec le client web](connect-web.md)
