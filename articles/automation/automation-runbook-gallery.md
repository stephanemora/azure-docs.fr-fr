---
title: Galeries de runbooks et de modules pour Azure Automation
description: Des runbooks et des modules de Microsoft et de la communauté sont disponibles pour l’installation et l’utilisation dans votre environnement Azure Automation.  Cet article décrit comment accéder à ces ressources et comment partager vos runbooks dans la galerie.
services: automation
ms.subservice: process-automation
ms.date: 03/20/2019
ms.topic: conceptual
ms.openlocfilehash: 52a0ab0a31600c1548283c7d899b17e497811b5a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75421483"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Galeries de runbooks et de modules pour Azure Automation

Au lieu de créer vos propres runbooks et modules dans Azure Automation, vous pouvez accéder à des scénarios déjà générés par Microsoft et la communauté.

Vous pouvez obtenir des runbooks PowerShell et des [modules](#modules-in-powershell-gallery) à partir de PowerShell Gallery, et à partir des [runbooks Python](#python-runbooks) de la galerie du Centre de scripts. Vous pouvez également contribuer à la communauté en partageant des scénarios que vous développez (consultez Ajout d’un runbook à la galerie).

## <a name="runbooks-in-powershell-gallery"></a>Runbooks dans PowerShell Gallery

[PowerShell Gallery](https://www.powershellgallery.com/packages) fournit un éventail de runbooks provenant de Microsoft et de la communauté, et que vous pouvez importer dans Azure Automation. Pour utiliser l’un de ces runbooks, vous pouvez en télécharger un à partir de la galerie, ou importer directement des runbooks à partir de la galerie, ou à partir de votre compte Automation sur le portail Azure.

Seul le portail Azure permet d’importer des runbooks directement à partir de PowerShell Gallery. Vous ne pouvez pas exécuter cette fonction à l’aide de PowerShell.

> [!NOTE]
> Vous devez valider le contenu de tous les runbooks que vous obtenez de PowerShell Gallery et vous montrer très vigilant lors de leur installation et de leur exécution dans un environnement de production.

### <a name="to-import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Pour importer un runbook PowerShell à partir de la galerie de runbooks avec le portail Azure

1. Dans le portail Azure, ouvrez votre compte Automation.
2. Sous **Automatisation de processus**, cliquez sur **Galerie de runbooks**
3. Sélectionnez **Source : PowerShell Gallery**.
4. Recherchez l’élément de la galerie qui vous intéresse et sélectionnez-le pour afficher les informations le concernant. Sur la gauche, vous pouvez entrer des paramètres de recherche supplémentaires pour l’éditeur et le type.

   ![Parcourir la galerie](media/automation-runbook-gallery/browse-gallery.png)

5. Cliquez sur **Afficher le projet source** pour afficher l’élément dans le [Centre de scripts TechNet](https://gallery.technet.microsoft.com/).
6. Pour importer un élément, cliquez dessus pour afficher les informations le concernant, puis cliquez sur le bouton **Importer** .

   ![Bouton Importer](media/automation-runbook-gallery/gallery-item-detail.png)

7. Vous pouvez également modifier le nom du runbook, puis cliquer sur **OK** pour importer le runbook.
8. Ce runbook apparaît sur l’onglet **Runbooks** du compte Automation.

### <a name="adding-a-powershell-runbook-to-the-gallery"></a>Ajout d’un runbook PowerShell à la galerie

Microsoft vous invite à ajouter à PowerShell Gallery des runbooks dont vous pensez qu’ils pourraient être utiles à d’autres utilisateurs. PowerShell Gallery accepte les modules PowerShell et les scripts PowerShell. Vous pouvez ajouter un runbook en le [chargeant dans PowerShell Gallery](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package).

> [!NOTE]
> Les runbooks graphiques ne sont pas pris en charge dans PowerShell Gallery.

## <a name="modules-in-powershell-gallery"></a>Modules dans PowerShell Gallery

Les modules PowerShell contiennent des applets de commande que vous pouvez utiliser dans vos runbooks ; des modules existants que vous pouvez installer dans Azure Automation sont disponibles dans [PowerShell Gallery](https://www.powershellgallery.com). Vous pouvez lancer cette galerie à partir du portail Azure et les installer directement dans Azure Automation. Vous pouvez également les télécharger et les installer manuellement.

### <a name="to-import-a-module-from-the-automation-module-gallery-with-the-azure-portal"></a>Pour importer un module à partir d’Automation Module Gallery avec le portail Azure

1. Dans le portail Azure, ouvrez votre compte Automation.
2. Sélectionnez **Modules** sous **Ressources partagées** pour ouvrir la liste des modules.
3. Cliquez sur **Parcourir la galerie**, en haut de la page.

   ![Galerie du module](media/automation-runbook-gallery/modules-blade.png)

4. Sur la page **Parcourir la galerie**, vous pouvez effectuer des recherches en fonction des champs suivants :

   * Nom du module
   * Balises
   * Auteur
   * Nom Applet de commande/Ressource DSC

5. Recherchez un module qui vous intéresse et sélectionnez-le pour en afficher les détails.

   Lorsque vous explorez un module spécifique, vous pouvez afficher plus d’informations. Ces informations incluent un lien vers PowerShell Gallery, toutes les dépendances nécessaires et toutes les applets de commande ou ressources DSC que contient le module.

   ![Détails du module PowerShell](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. Pour installer le module directement dans Azure Automation, cliquez sur le bouton **Importer** .
7. Lorsque vous cliquez sur le bouton Importer du volet **Importer**, le nom du module que vous êtes sur le point d’importer s’affiche. Si toutes les dépendances sont installées, le bouton **OK** est activé. S'il manque des dépendances, vous devez les importer avant de pouvoir importer ce module.
8. Dans la page **Importer**, cliquez sur **OK** pour importer le module. Quand il importe un module dans votre compte, le logiciel Azure Automation extrait les métadonnées sur le module et les cmdlets. Cette action peut prendre quelques minutes, car chaque activité doit être extraite.
9. Vous recevez une première notification indiquant que le module est en cours de déploiement, puis une autre, signalant que le processus est terminé.
10. Après avoir importé le module, vous pouvez voir les activités disponibles. Vous pouvez utiliser ses ressources dans vos runbooks et dans Desired State Configuration.

> [!NOTE]
> Les modules qui prennent uniquement en charge PowerShell Core ne sont pas gérés dans Azure Automation et ne sont pas importables dans le Portail Azure, ni déployables directement à partir de PowerShell Gallery.

## <a name="python-runbooks"></a>Runbooks Python

Les runbooks Python sont disponibles dans la [galerie du Centre de scripts](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=). Vous pouvez contribuer à la galerie du Centre de scripts en y ajoutant des runbooks Python. Pour cela, cliquez sur **Upload a contribution** (Charger une contribution). Si tel est le cas, vérifiez que vous ajoutez la balise **Python** lors du chargement de votre contribution.

> [!NOTE]
> Pour charger du contenu vers le [Centre de scripts](https://gallery.technet.microsoft.com/scriptcenter), vous devez disposer au moins de 100 points.

## <a name="requesting-a-runbook-or-module"></a>Demande d’un runbook ou d’un module

Vous pouvez envoyer des demandes à [User Voice](https://feedback.azure.com/forums/246290-azure-automation/).  Si vous avez besoin d’aide pour écrire un runbook ou si vous avez une question à propos de PowerShell, publiez une question sur notre [forum](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## <a name="common-solutions-available-in-the-runbook-gallery"></a>Solutions courantes disponibles dans la galerie de runbooks

La liste ci-dessous contient quelques runbooks qui fournissent des solutions aux scénarios courants. Pour obtenir la liste complète des runbooks créés par l’équipe Azure Automation, consultez [Profil AzureAutomationTeam](https://www.powershellgallery.com/profiles/AzureAutomationTeam).

* [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) : importe la version la plus récente sur PowerShell Gallery de tous les modules dans un compte Automation.
* [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) : ce script configure Diagnostics Azure et Log Analytics pour recevoir les journaux Azure Automation contenant l’état des travaux et les flux des travaux.
* [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) : ce runbook copie un fichier distant à partir d’une machine virtuelle Microsoft Azure.
* [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) : ce runbook copie un fichier local sur une machine virtuelle Azure.

## <a name="next-steps"></a>Étapes suivantes

* Pour vous familiariser avec les runbooks, consultez [Gérer un runbook dans Azure Automation](manage-runbooks.md)
* Pour comprendre les différences entre PowerShell et un workflow PowerShell avec les Runbooks, consultez [Apprentissage du workflow PowerShell](automation-powershell-workflow.md)
* Pour plus d’informations sur PowerShell, notamment le langage de référence et les modules d’apprentissage, consultez la [Documentation PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
