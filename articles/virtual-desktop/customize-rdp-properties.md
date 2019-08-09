---
title: Personnaliser les propriétés de RDP avec PowerShell - Azure
description: Comment personnaliser les propriétés de RDP pour Windows Virtual Desktop avec des applets de commande PowerShell.
services: virtual-desktop
author: v-hevem
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: v-hevem
ms.openlocfilehash: ce14f990272fa1e70d07c0f4a1f18025b536eccc
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618864"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Personnaliser les propriétés de RDP pour un pool d’hôtes

La personnalisation des propriétés du protocole RDP (Remote Desktop Protocol) d’un pool d’hôtes, comme l’utilisation de plusieurs écrans et la redirection audio, vous permet d’offrir une expérience optimale pour vos utilisateurs en fonction de leurs besoins. Vous pouvez personnaliser les propriétés de RDP dans Windows Virtual Desktop avec le paramètre **-CustomRdpProperty** dans l’applet de commande **Set-RdsHostPool**.

Pour obtenir la liste complète des propriétés prises en charge et leur valeur par défaut, consultez [Paramètres du fichier RDP de Bureau à distance](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files).

Tout d’abord, si vous ne l’avez pas déjà fait, [téléchargez et importez le module PowerShell Windows Virtual Desktop](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) à utiliser dans votre session PowerShell.

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

Maintenant que vous avez personnalisé les propriétés RDP pour un pool d’hôtes donné, connectez-vous à un client Windows Virtual Desktop pour les tester dans le cadre d’une session utilisateur. Pour cela, passez aux rubriques de procédures Se connecter à Windows Virtual Desktop :

- [Se connecter à partir de Windows 10 et Windows 7](connect-windows-7-and-10.md)
- [Se connecter à partir d’un navigateur web](connect-web.md)
