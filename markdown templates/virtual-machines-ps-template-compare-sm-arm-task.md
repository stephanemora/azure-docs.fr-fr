---
ms.openlocfilehash: aec6282daadc61b4e1bcf6bbaf1266d9bc98cdac
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58895798"
---
<!--save a copy of this file to your local repo. It's important that you follow the naming conventions by starting with the service name, and use lowercase only for the file name. See "file-names-and-locations.md" under the "contributor-guide" folder in your repo.

Info to help you use the template are enclosed in the Markdown comments using the caret, hyphen, dash syntax. Delete these from your file.

Text not wrapped in comment syntax is intended to be used as is, or with updates enclosed in [  ]. Add the info and delete the bracket. 

Pay attention to spacing and indents. They affect formatting. 

--> 

<!--replace this with Properties and Tags sections. These are required sections. See "article-metadata.md" in under the "contributor-guide" folder in your repo. Attributes in each section can be placed on separate lines to make them easier to read and check-->

# <a name="use-azure-powershell-to-task"></a>Utiliser Azure PowerShell pour [tâches]
Cet article vous montre comment [tâches], à l’aide des commandes à partir du module Azure et le module Azure Resource Manager. Cela vise à vous aider à apprendre les nouvelles commandes, ainsi que de migrer des scripts existants vers les nouvelles commandes.

## <a name="prerequisite-install-a-recent-version-of-azure-powershell"></a>Configuration requise : Installer une Version récente d’Azure PowerShell
Si vous n’avez pas encore fait, installez au moins la version [numéro de version] d’Azure PowerShell sur votre ordinateur local. Si vous utilisez une version antérieure, il n’auront pas les applets de commande Azure Resource Manager décrits dans cet article. Pour plus d'informations, consultez les rubriques :

* [Comment installer et configurer Azure PowerShell](install-configure-powershell.md) pour obtenir des instructions sur la configuration d’Azure PowerShell.
* [À l’aide de Windows PowerShell avec Resource Manager](powershell-azure-resource-manager.md) concepts de base sur l’utilisation de Resource Manager.

> [!NOTE]
> La plupart des tâches vous obligent à utiliser une invite de commandes de niveau administrateur Azure PowerShell.
> 
> 

## <a name="command-comparison"></a>Comparaison de la commande
Cela [table | section] affiche la syntaxe de commande.

<!--[optional image - to use an image in this article, add a folder with the same name as the article file name without extension, inside the Media folder of the repo. Use only this folder to store the images. Don't attempt to use a common folder to share images you want to use in more than 1 file.]
Then, use the following syntax to add a reference to the image in your article:
![](./media/name-of-file-without-extension/image-name-no-spaces.png)
-->

<!--if a command string uses variables, define the variables first, using the  following construction. In some cases the variable is straightforward and doesn't need much explanation. But parameters such as location and size can benefit from brief explanation or listing all accepted values:--> 

Ces exemples de commande utilisent les variables suivantes :

$FriendlyName"<Describe value>"

<!-- if it makes more sense to present this in a table, use this. Otherwise, delete. The table won't render until it's in GitHub or published to Sandbox.-->

| Gestion des services | Gestionnaire de ressources |
| --- | --- |
| `syntax` |`syntax` |

<!--if it makes more sense to present this one command block after the other instead of a table, use this. Otherwise, delete-->

[Phrase d’introduction court sur la commande. Omettre si il n’a rien à dire. Mais si elle utilise des approches telles un pipeline, qui expliquent] :

    [command string]

## <a name="script-examples"></a>Exemples de script
Voici un exemple qui utilise des [noms de l’applet de commande)] à [tâches]. Il inclut des commandes qui :

* [verbe court, utilisations, a, est, etc.]
* [next short verb] 

<!--include this statement if it uses variables that weren't introduced earlier--> Il inclut les variables suivantes :

* [variable 1]
* [variable 2]

<!--This shows you how a recent example was presented as well as how it was formatted. Preceding each line with one tab or four spaces to format in a code block-->

    $family="Windows Server 2012 R2 Datacenter"
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
    $vmname="AZDC1"
    $vmsize="Medium"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    $cred=Get-Credential -Message "Type the name and password of the local administrator account."
    $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

    $vm1 | Set-AzureSubnet -SubnetNames "BackEnd"

    $vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.244.4

    $disksize=20
    $disklabel="DCData"
    $lun=0
    $hcaching="None"
    $vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

    $svcname="Azure-TailspinToys"
    $vnetname="AZDatacenter"
    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname


## <a name="additional-resources"></a>Ressources supplémentaires
<!--At a minimum, include a link back to the migration task list article. Use the formats shown below. See create-links-markdown.md for more info -->
<!--use this format for links to other articles, such as the migration task list. -->
[Gestion de disponibilité](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

<!--To link to an ACOM page outside the /documentation/ subdomain (such as a pricing page, SLA page or anything else that is not a documentation article), use an absolute URL, but omit the locale:

    [link text](https://azure.microsoft.com/pricing/details/virtual-machines/)-->

<!--use this for URLs outside of ACOM. Be sure to locale, and if you're linking to the Azure library on MSDN, include the '/azure/' part of the URL-->
[Documentation sur les machines virtuelles](/previous-versions/azure/jj156003(v=azure.100))

