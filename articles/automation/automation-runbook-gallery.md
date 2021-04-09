---
title: Utiliser les runbooks et modules Azure Automation dans PowerShell Gallery
description: Cet article explique comment utiliser les runbooks et les modules de Microsoft et de la communauté dans PowerShell Gallery.
services: automation
ms.subservice: process-automation
ms.date: 03/04/2021
ms.topic: conceptual
ms.openlocfilehash: c38a6236fe3ad9164d11d94e5563a7dddf5b4b32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102452779"
---
# <a name="use-runbooks-and-modules-in-powershell-gallery"></a>Utiliser les runbooks et les modules de PowerShell Gallery

Au lieu de créer vos propres runbooks et modules dans Azure Automation, vous pouvez accéder à des scénarios déjà générés par Microsoft et la communauté. Vous pouvez obtenir des runbooks PowerShell et des [modules](#modules-in-powershell-gallery) à partir de PowerShell Gallery, et à partir des [runbooks Python](#use-python-runbooks) de l’organisation Azure Automation GitHub. Vous pouvez également contribuer à la communauté en partageant les [scénarios que vous développez](#add-a-powershell-runbook-to-the-gallery).

> [!NOTE]
> Le centre de scripts TechNet est en retrait. Tous les runbooks du Centre de scripts de la Galerie de runbooks ont été déplacés vers notre [organisation GitHub Automation](https://github.com/azureautomation). Pour plus d'informations, cliquez [ici](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337).

## <a name="runbooks-in-powershell-gallery"></a>Runbooks dans PowerShell Gallery

[PowerShell Gallery](https://www.powershellgallery.com/packages) fournit un éventail de runbooks provenant de Microsoft et de la communauté, et que vous pouvez importer dans Azure Automation. Pour utiliser l’un de ces runbooks, vous pouvez en télécharger un à partir de la galerie, ou importer directement des runbooks à partir de la galerie, ou à partir de votre compte Automation sur le portail Azure.

> [!NOTE]
> Les runbooks graphiques ne sont pas pris en charge dans PowerShell Gallery.

Seul le portail Azure permet d’importer des runbooks directement à partir de PowerShell Gallery. Vous ne pouvez pas exécuter cette fonction à l’aide de PowerShell.

> [!NOTE]
> Vous devez valider le contenu de tous les runbooks que vous obtenez de PowerShell Gallery et vous montrer très vigilant lors de leur installation et de leur exécution dans un environnement de production.

## <a name="modules-in-powershell-gallery"></a>Modules dans PowerShell Gallery

Les modules PowerShell contiennent des cmdlets que vous pouvez utiliser dans vos runbooks. Des modules existants qui peuvent être installés dans Azure Automation sont disponibles dans [PowerShell Gallery](https://www.powershellgallery.com). Vous pouvez lancer cette galerie sur le Portail Azure et installer les modules directement dans Azure Automation, ou bien les télécharger et les installer manuellement.

## <a name="common-scenarios-available-in-powershell-gallery"></a>Scénarios courants disponibles dans PowerShell Gallery

La liste ci-dessous contient quelques runbooks qui fournissent des scénarios courants. Pour obtenir la liste complète des runbooks créés par l’équipe Azure Automation, consultez [Profil AzureAutomationTeam](https://www.powershellgallery.com/profiles/AzureAutomationTeam).

   * [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) : importe la version la plus récente de tous les modules dans un compte Automation à partir de PowerShell Gallery.
   * [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) : configure Diagnostics Azure et Log Analytics pour recevoir les journaux Azure Automation contenant l’état des travaux et les flux des travaux.
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) : copie un fichier distant à partir d’une machine virtuelle Microsoft Azure.
   * [Copy-ItemToAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) : copie un fichier local vers une machine virtuelle Azure.

## <a name="import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Importer un runbook PowerShell à partir Runbook Gallery avec le portail Azure

1. Dans le portail Azure, ouvrez votre compte Automation.
1. Sélectionnez **Runbook Gallery** sous **Automatisation des processus**.
1. Sélectionnez **Source : PowerShell Gallery**. La liste des runbooks disponibles que vous pouvez parcourir s’affiche.
1. Vous pouvez utiliser la zone de recherche située au-dessus de la liste pour affiner la liste et les filtres pour affiner l’affichage par éditeur, par type et par ordre de tri. Recherchez l’élément de la galerie qui vous intéresse et sélectionnez-le pour afficher les informations le concernant.

   :::image type="content" source="media/automation-runbook-gallery/browse-gallery-sm.png" alt-text="Navigation dans la galerie de runbooks." lightbox="media/automation-runbook-gallery/browse-gallery-lg.png":::

1. Pour importer un élément, cliquez sur **Importer** dans le panneau de détails.

   :::image type="content" source="media/automation-runbook-gallery/gallery-item-detail-sm.png" alt-text="Affichage du détail d’un élément de la galerie de runbooks." lightbox="media/automation-runbook-gallery/gallery-item-detail-lg.png":::

1. Vous pouvez également modifier le nom du runbook, puis cliquer sur **OK** pour importer le runbook.
1. Ce runbook apparaît sur l’onglet **Runbooks** du compte Automation.

## <a name="import-a--powershell-runbook-from-github-with-the-azure-portal"></a>Importation d’un runbook PowerShell à partir de GitHub avec le Portail Azure

1. Dans le portail Azure, ouvrez votre compte Automation.
1. Sélectionnez **Runbook Gallery** sous **Automatisation des processus**.
1. Sélectionnez **Source : GitHub**.
1. Vous pouvez utiliser les filtres situés au-dessus de la liste pour affiner l’affichage par éditeur, par type et par ordre de tri. Recherchez l’élément de la galerie qui vous intéresse et sélectionnez-le pour afficher les informations le concernant.

   :::image type="content" source="media/automation-runbook-gallery/browse-gallery-github-sm.png" alt-text="Navigation dans la galerie GitHub." lightbox="media/automation-runbook-gallery/browse-gallery-github-lg.png":::

1. Pour importer un élément, cliquez sur **Importer** dans le panneau de détails.

   :::image type="content" source="media/automation-runbook-gallery/gallery-item-details-blade-github-sm.png" alt-text="Vue détaillée d’un runbook dans la galerie GitHub." lightbox="media/automation-runbook-gallery/gallery-item-details-blade-github-lg.png":::

1. Vous pouvez également modifier le nom du runbook, puis cliquer sur **OK** pour importer le runbook.
1. Ce runbook apparaît sur l’onglet **Runbooks** du compte Automation.

## <a name="add-a-powershell-runbook-to-the-gallery"></a>Ajouter un runbook PowerShell à la galerie

Microsoft vous invite à ajouter à PowerShell Gallery des runbooks dont vous pensez qu’ils pourraient être utiles à d’autres utilisateurs. PowerShell Gallery accepte les modules PowerShell et les scripts PowerShell. Vous pouvez ajouter un runbook en le [chargeant dans PowerShell Gallery](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package).

## <a name="import-a-module-from-the-module-gallery-with-the-azure-portal"></a>Importer un module à partir de la galerie des modules avec le portail Azure

1. Dans le portail Azure, ouvrez votre compte Automation.
1. Sélectionnez **Modules** sous **Ressources partagées** pour ouvrir la liste des modules.
1. Cliquez sur **Parcourir la galerie**, en haut de la page.

      :::image type="content" source="media/automation-runbook-gallery/modules-blade-sm.png" alt-text="Vue de la galerie de modules." lightbox="media/automation-runbook-gallery/modules-blade-lg.png":::

1. Sur la page Parcourir la galerie, vous pouvez utiliser la zone de recherche pour rechercher des correspondances dans l’un des champs suivants :

   * Nom du module
   * Balises
   * Auteur
   * Nom Applet de commande/Ressource DSC

1. Recherchez un module qui vous intéresse et sélectionnez-le pour en afficher les détails.

   Lorsque vous explorez un module spécifique, vous pouvez afficher plus d’informations. Ces informations incluent un lien vers PowerShell Gallery, toutes les dépendances nécessaires et toutes les applets de commande ou ressources DSC que contient le module.

   :::image type="content" source="media/automation-runbook-gallery/gallery-item-details-blade-sm.png" alt-text="Vue détaillée d’un module dans la galerie." lightbox="media/automation-runbook-gallery/gallery-item-details-blade-lg.png":::

1. Pour installer le module directement dans Azure Automation, cliquez sur **Importer**.
1. Dans le volet Importer, vous pouvez voir le nom du module à importer. Si toutes les dépendances sont installées, le bouton **OK** est activé. S'il manque des dépendances, vous devez les importer avant de pouvoir importer ce module.
1. Dans le volet Importer, cliquez sur **OK** pour importer le module. Quand il importe un module dans votre compte, le logiciel Azure Automation extrait les métadonnées sur le module et les cmdlets. Cette action peut prendre quelques minutes, car chaque activité doit être extraite.
1. Vous recevez une première notification indiquant que le module est en cours de déploiement, puis une autre, signalant que le processus est terminé.
1. Après avoir importé le module, vous pouvez voir les activités disponibles. Vous pouvez utiliser les ressources du module dans vos runbooks et ressources DSC.

> [!NOTE]
> Les modules qui prennent uniquement en charge PowerShell Core ne sont pas gérés dans Azure Automation et ne sont pas importables dans le Portail Azure, ni déployables directement à partir de PowerShell Gallery.

## <a name="use-python-runbooks"></a>Utiliser des runbooks Python

Les Runbooks Python sont disponibles dans l’[organisation Azure Automation GitHub](https://github.com/azureautomation). Lorsque vous contribuez à notre référentiel GitHub, ajoutez la balise **(rubrique GitHub) : Python3** lorsque vous chargez votre contribution.

## <a name="request-a-runbook-or-module"></a>Demander un runbook ou un module

Vous pouvez envoyer des demandes à [User Voice](https://feedback.azure.com/forums/246290-azure-automation/).  Si vous avez besoin d’aide pour écrire un runbook ou si vous avez une question à propos de PowerShell, publiez une question sur notre [page de questions Microsoft Q&A](/answers/topics/azure-automation.html).

## <a name="next-steps"></a>Étapes suivantes

* Pour commencer à utiliser un runbook PowerShell, voir [Tutoriel : Créer un runbook PowerShell](learn/automation-tutorial-runbook-textual-powershell.md).
* Pour savoir comment utiliser des runbooks, voir [Gérer les runbooks dans Azure Automation](manage-runbooks.md).
* Pour plus d’informations sur PowerShell, consultez la [documentation PowerShell](/powershell/scripting/overview).
* Pour obtenir des informations de référence sur les applets de commande PowerShell, consultez [Az.Automation](/powershell/module/az.automation).
