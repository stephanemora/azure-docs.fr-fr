---
title: Galeries de runbooks et de modules pour Azure Automation
description: Des runbooks et des modules de Microsoft et de la communauté sont disponibles pour l’installation et l’utilisation dans votre environnement Azure Automation.  Cet article décrit comment accéder à ces ressources et comment partager vos runbooks dans la galerie.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/11/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 68eb3f3e5c568bb518251aca2a4e76932ce2eee5
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/19/2019
ms.locfileid: "56416205"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Galeries de runbooks et de modules pour Azure Automation

Au lieu de créer vos propres runbooks et modules dans Azure Automation, vous pouvez accéder à des scénarios déjà générés par Microsoft et la communauté.

Vous pouvez obtenir des runbooks à partir de la [galerie de runbooks](#runbooks-in-runbook-gallery) et des modules à partir de la [PowerShell Gallery](#modules-in-powerShell-gallery).  Vous pouvez également contribuer à la communauté en partageant des scénarios que vous développez. Consultez [Ajout d’un runbook à la galerie](automation-runbook-gallery.md#adding-a-runbook-to-the-runbook-gallery)

## <a name="runbooks-in-runbook-gallery"></a>Runbooks dans la galerie de runbooks

La [galerie de runbooks](https://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=RootCategory&f\[0\].Value=WindowsAzure&f\[1\].Type=SubCategory&f\[1\].Value=WindowsAzure_automation&f\[1\].Text=Automation) fournit un éventail de runbooks de Microsoft et de la communauté que vous pouvez importer dans Azure Automation. Pour en utiliser un, téléchargez un runbook à partir de la galerie qui est hébergée dans le [Centre de scripts TechNet](https://gallery.technet.microsoft.com/scriptcenter/site/upload), ou vous pouvez importer directement les runbooks depuis la galerie dans le portail Azure.

Vous pouvez uniquement importer directement à partir de la galerie de runbooks à l’aide du portail Azure. Vous ne pouvez pas exécuter cette fonction à l’aide de Windows PowerShell.

> [!NOTE]
> Vous devez valider le contenu de tous les runbooks que vous obtenez de la galerie de runbooks et vous montrer très vigilant lors de leur installation et de leur exécution dans un environnement de production.

### <a name="to-import-a-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Pour importer un runbook à partir de la galerie de runbooks avec le portail Azure

1. Dans le portail Azure, ouvrez votre compte Automation.
2. Sous **Automatisation de processus**, cliquez sur **Galerie de runbooks**
3. Recherchez l’élément de la galerie qui vous intéresse et sélectionnez-le pour afficher les informations le concernant. Sur la gauche, vous pouvez entrer des paramètres de recherche supplémentaires pour l’éditeur et le type.

   ![Parcourir la galerie](media/automation-runbook-gallery/browse-gallery.png)

4. Cliquez sur **Afficher le projet source** pour afficher l’élément dans le [Centre de scripts TechNet](https://gallery.technet.microsoft.com/).
5. Pour importer un élément, cliquez dessus pour afficher les informations le concernant, puis cliquez sur le bouton **Importer** .

   ![Bouton Importer](media/automation-runbook-gallery/gallery-item-detail.png)

6. Vous pouvez également modifier le nom du runbook, puis cliquer sur **OK** pour importer le runbook.
7. Ce runbook apparaît sur l’onglet **Runbooks** du compte Automation.

### <a name="adding-a-runbook-to-the-runbook-gallery"></a>Ajout d’un runbook à la galerie de runbooks

Microsoft vous invite à ajouter à la galerie de runbooks des runbooks dont vous pensez qu’ils pourraient être utiles à d’autres utilisateurs.  Vous pouvez ajouter un runbook en [le téléchargeant vers le Centre de scripts](https://gallery.technet.microsoft.com/site/upload) en tenant compte des informations suivantes.

* Vous devez spécifier *Windows Azure* comme **Catégorie** et *Automation* comme **Sous-catégorie** pour le runbook à afficher dans l’Assistant.  
* Le chargement doit être un seul fichier `.ps1` ou `.graphrunbook`.  Si le runbook nécessite des modules, des runbooks enfants ou des ressources, vous devez répertorier ces éléments dans la description de l’envoi et dans la section commentaires du runbook.  Si vous disposez d’un scénario nécessitant plusieurs runbooks, téléchargez-les séparément et répertoriez les noms des runbooks associés dans chacune de leurs descriptions. Assurez-vous que vous utilisez les mêmes balises afin qu’elles s’affichent dans la même catégorie. Un utilisateur doit lire la description pour savoir que les autres runbooks sont requis pour que le scénario fonctionne.
* Ajoutez la balise « GraphicalPS » si vous publiez un **Runbook graphique** (et non un workflow graphique).
* Insérer un extrait de code PowerShell ou PowerShell Workflow dans la description à l’aide de l’icône **Insérer la section de code** .
* Le résumé du chargement s’affiche dans les résultats de la galerie de runbooks. Vous devez donc fournir des informations détaillées qui aideront un utilisateur à identifier la fonctionnalité du runbook.
* Vous devez attribuer une à trois des balises suivantes pour le téléchargement.  Le runbook est répertorié dans l’Assistant sous les catégories qui correspondent à ses balises.  Toutes les balises ne figurant pas sur cette liste sont ignorées par l’Assistant. Si vous ne spécifiez pas les balises correspondantes, le runbook est répertorié dans la catégorie Autre.
  
  * Sauvegarde
  * Gestion de la capacité
  * Contrôle des modifications
  * Conformité
  * Environnements de développement / test
  * Récupération d’urgence
  * Surveillance
  * Application de correctifs
  * Approvisionnement
  * Correction
  * Gestion du cycle de vie des machines virtuelles

* Automation met à jour la galerie toutes les heures, donc vous ne verrez pas vos contributions immédiatement.

## <a name="modules-in-powershell-gallery"></a>Modules dans PowerShell Gallery

Les modules PowerShell contiennent des applets de commande que vous pouvez utiliser dans vos runbooks ; des modules existants que vous pouvez installer dans Azure Automation sont disponibles dans [PowerShell Gallery](https://www.powershellgallery.com). Vous pouvez lancer cette galerie à partir du portail Azure et les installer directement dans Azure Automation. Vous pouvez également les télécharger et les installer manuellement.  

### <a name="to-import-a-module-from-the-automation-module-gallery-with-the-azure-portal"></a>Pour importer un module à partir d’Automation Module Gallery avec le portail Azure

1. Dans le portail Azure, ouvrez votre compte Automation.
2. Sélectionnez **Modules** sous **Ressources partagées** pour ouvrir la liste des modules.
3. Cliquez sur **Parcourir la galerie**, en haut de la page.

   ![Galerie du module](media/automation-runbook-gallery/modules-blade.png)

4. Sur la page **Parcourir la galerie**, vous pouvez effectuer des recherches en fonction des champs suivants :

   * Nom du module
   * Tags
   * Auteur
   * Nom Applet de commande/Ressource DSC

5. Recherchez un module qui vous intéresse et sélectionnez-le pour en afficher les détails.  

   Lorsque vous explorez un module spécifique, vous pouvez afficher plus d’informations. Ces informations incluent un lien vers PowerShell Gallery, toutes les dépendances nécessaires et toutes les applets de commande ou ressources DSC que contient le module.

   ![Détails du module PowerShell](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. Pour installer le module directement dans Azure Automation, cliquez sur le bouton **Importer** .
7. Lorsque vous cliquez sur le bouton Importer du volet **Importer**, le nom du module que vous êtes sur le point d’importer s’affiche. Si toutes les dépendances sont installées, le bouton **OK** est activé. S'il manque des dépendances, vous devez les importer avant de pouvoir importer ce module.
8. Dans la page **Importer**, cliquez sur **OK** pour importer le module. Quand il importe un module dans votre compte, le logiciel Azure Automation extrait les métadonnées sur le module et les cmdlets. Cette action peut prendre quelques minutes, car chaque activité doit être extraite.
9.  Vous recevez une première notification indiquant que le module est en cours de déploiement, puis une autre, signalant que le processus est terminé.
10. Après avoir importé le module, vous pouvez voir les activités disponibles. Vous pouvez utiliser ses ressources dans vos runbooks et dans Desired State Configuration.

> [!NOTE]
> Les modules qui prennent uniquement en charge PowerShell Core ne sont pas gérés dans Azure Automation et ne sont pas importables dans le Portail Azure, ni déployables directement à partir de PowerShell Gallery.

## <a name="python-runbooks"></a>Runbooks Python

Les runbooks Python sont disponibles dans la [galerie du Centre de scripts](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=). Vous pouvez contribuer à la galerie du Centre de scripts en y ajoutant des runbooks Python. Si tel est le cas, vérifiez que vous ajoutez la balise **Python** lors du chargement de votre contribution.

## <a name="requesting-a-runbook-or-module"></a>Demande d’un runbook ou d’un module

Vous pouvez envoyer des demandes à [User Voice](https://feedback.azure.com/forums/246290-azure-automation/).  Si vous avez besoin d’aide pour écrire un runbook ou si vous avez une question à propos de PowerShell, publiez une question sur notre [forum](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## <a name="next-steps"></a>Étapes suivantes

* Pour vous familiariser avec les runbooks, consultez [Gérer un runbook dans Azure Automation](manage-runbooks.md)
* Pour comprendre les différences entre PowerShell et un workflow PowerShell avec les Runbooks, consultez [Apprentissage du workflow PowerShell](automation-powershell-workflow.md)