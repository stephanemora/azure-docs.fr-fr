---
title: Personnaliser les propriétés de RDP avec PowerShell – Azure
description: Comment personnaliser les propriétés de RDP pour Windows Virtual Desktop avec des applets de commande PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 06/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: d41df8fe4f411cf28af83c8b4b667f1e86bdb45d
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86259909"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Personnaliser les propriétés de RDP pour un pool d’hôtes

>[!IMPORTANT]
>Ce contenu s’applique à la mise à jour Printemps 2020 avec des objets Azure Resource Manager Windows Virtual Desktop. Si vous utilisez la version Automne 2019 de Windows Virtual Desktop sans objets Azure Resource Manager, consultez [cet article](./virtual-desktop-fall-2019/customize-rdp-properties-2019.md).
>
> La mise à jour Printemps 2020 de Windows Virtual Desktop est en préversion publique. Cette préversion est fournie sans contrat de niveau de service, c’est pourquoi nous déconseillons son utilisation pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

La personnalisation des propriétés du protocole RDP (Remote Desktop Protocol) d’un pool d’hôtes, comme l’utilisation de plusieurs écrans et la redirection audio, vous permet d’offrir une expérience optimale pour vos utilisateurs en fonction de leurs besoins. Vous pouvez personnaliser les propriétés RDP dans Windows Virtual Desktop en utilisant le portail Azure ou le paramètre *-CustomRdpProperty* dans la cmdlet **Update-AzWvdHostPool**.

Pour obtenir la liste complète des propriétés prises en charge et leur valeur par défaut, consultez [Paramètres de fichier RDP pris en charge](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context).

## <a name="prerequisites"></a>Prérequis

Avant de commencer, suivez les instructions fournies dans [Configurer le module PowerShell Windows Virtual Desktop](powershell-module.md) pour configurer votre module PowerShell et vous connecter à Azure.

## <a name="default-rdp-properties"></a>Propriétés RDP par défaut

Par défaut, les fichiers RDP publiés contiennent les propriétés suivantes :

|Propriétés RDP | Bureaux | RemoteApps |
|---|---| --- |
| Mode à plusieurs écrans | activé | N/A |
| Redirections de lecteur activées | Lecteurs, presse-papiers, imprimantes, ports COM, périphériques USB et cartes à puce| Lecteurs, presse-papiers et imprimantes |
| Mode audio distant | Lire localement | Lire localement |

Toutes les propriétés personnalisées que vous définissez pour le pool hôte remplacent ces valeurs par défaut.

## <a name="configure-rdp-properties-in-the-azure-portal"></a>Configurer les propriétés RDP dans le portail Azure

Pour configurer les propriétés RDP dans le portail Azure :

1. Connectez-vous à Azure sur <https://portal.azure.com>.
2. Entrez **Windows Virtual Desktop** dans la barre de recherche.
3. Sous Services, sélectionnez **Windows Virtual Desktop**.
4. Dans la page Windows Virtual Desktop, dans le menu sur le côté gauche de l’écran, sélectionnez **pools d’hôtes**.
5. Sélectionnez le **nom du pool d’hôtes** que vous souhaitez mettre à jour.
6. Dans le menu sur le côté gauche de l’écran, sélectionnez **Propriétés**.
7. Dans l’onglet **Propriétés**, accédez à **Paramètres RDP** pour commencer à modifier les propriétés RDP. Les propriétés doivent suivre un format séparé par des points-virgules, comme les exemples PowerShell.
8. Lorsque vous avez terminé, sélectionnez **Enregistrer** pour enregistrer vos paramètres.

Les sections suivantes expliquent comment modifier manuellement des propriétés RDP personnalisées dans PowerShell.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Ajouter ou modifier une seule propriété RDP personnalisée

Pour ajouter ou modifier une seule propriété RDP personnalisée, exécutez l’applet de commande PowerShell suivante :

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty <property>
```

Pour vérifier si la cmdlet que vous venez d’exécuter a mis à jour la propriété, exécutez la cmdlet suivante :

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <customRDPpropertystring>
```

Par exemple, pour rechercher la propriété « audiocapturemode » sur un pool d’hôtes nommé 0301HP, vous devez entrer la cmdlet suivante :

```powershell
Get-AzWvdHostPool -ResourceGroupName 0301rg -Name 0301hp | format-list Name, CustomRdpProperty

Name              : 0301HP
CustomRdpProperty : audiocapturemode:i:1;
```

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Ajouter ou modifier plusieurs propriétés RDP personnalisées

Pour ajouter ou modifier plusieurs propriétés RDP personnalisées, exécutez les applets de commande PowerShell suivantes en fournissant les propriétés RDP personnalisées sous forme de chaîne avec des points-virgules comme séparateur :

```powershell
$properties="<property1>;<property2>;<property3>"
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty $properties
```

Vous pouvez vérifier que la propriété RDP a été ajoutée en exécutant la cmdlet suivante :

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <customRDPpropertystring>
```

Sur la base de notre exemple de cmdlet précédent, si vous configurez plusieurs propriétés RDP sur le pool d’hôtes 0301HP, votre cmdlet doit ressembler à ceci :

```powershell
Get-AzWvdHostPool -ResourceGroupName 0301rg -Name 0301hp | format-list Name, CustomRdpProperty

Name              : 0301HP
CustomRdpProperty : audiocapturemode:i:1;audiomode:i:0;
```

## <a name="reset-all-custom-rdp-properties"></a>Réinitialiser toutes les propriétés RDP personnalisées

Vous pouvez réinitialiser des propriétés RDP personnalisées à leurs valeurs par défaut en suivant les instructions de [Ajouter ou modifier une seule propriété RDP personnalisée](#add-or-edit-a-single-custom-rdp-property), ou vous pouvez réinitialiser toutes les propriétés RDP personnalisées pour un pool d’hôtes en exécutant l’applet de commande PowerShell suivante :

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty ""
```

Pour vous assurer que vous avez correctement supprimé le paramètre, entrez la cmdlet suivante :

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <CustomRDPpropertystring>
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez personnalisé les propriétés RDP pour un pool d’hôtes donné, connectez-vous à un client Windows Virtual Desktop pour les tester dans le cadre d’une session utilisateur. Les guides pratiques suivants indiquent comment se connecter à une session à l’aide du client choisi :

- [Se connecter avec le client Windows Desktop](connect-windows-7-10.md)
- [Se connecter avec le client web](connect-web.md)
- [Se connecter avec le client Android](connect-android.md)
- [Se connecter avec le client macOS](connect-macos.md)
- [Se connecter avec le client iOS](connect-ios.md)
