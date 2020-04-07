---
title: Gestion des données Azure Automation
description: Cet article contient plusieurs rubriques concernant la gestion d’un environnement Azure Automation.  Il inclut actuellement Conservation des données, Sauvegarde Azure Automation et Récupération d'urgence dans Azure Automation.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: 340fa19b6f9f07e192123900bc96b07bb016542f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132895"
---
# <a name="managing-azure-automation-data"></a>Gestion des données Azure Automation

Cet article contient plusieurs rubriques concernant la gestion d’un environnement Azure Automation.

## <a name="data-retention"></a>Conservation des données

Lorsque vous supprimez une ressource dans Azure Automation, elle est conservée pendant 30 jours à des fins d’audit avant d’être supprimée définitivement. Vous ne pouvez ni voir ni utiliser la ressource pendant cette période. Cette stratégie vaut aussi pour les ressources qui appartiennent à un compte Automation supprimé.

Azure Automation supprime automatiquement et définitivement les tâches de plus de 30 jours.

Le tableau suivant récapitule la stratégie de rétention pour les différentes ressources.

| Données | Stratégie |
|:--- |:--- |
| Comptes |Supprimés définitivement 30 jours après leur suppression par un utilisateur. |
| Éléments multimédias |Supprimés définitivement 30 jours après suppression des éléments multimédias par un utilisateur ou 30 jours après la suppression par un utilisateur du compte qui contient l’élément multimédia. |
| Modules |Supprimés définitivement 30 jours après suppression du module par un utilisateur ou 30 jours après la suppression par un utilisateur du compte qui contient le module. |
| Runbooks |Supprimés définitivement 30 jours après suppression de la ressource par un utilisateur ou 30 jours après la suppression par un utilisateur du compte qui contient la ressource. |
| travaux |Effacés et supprimés définitivement 30 jours après la dernière modification. Cela peut avoir lieu après la fin du travail, son arrêt ou sa suspension. |
| Configurations de nœud/fichiers MOF |L’ancienne configuration de nœud est supprimée définitivement 30 jours après la génération d’une nouvelle configuration de nœud. |
| Nœuds DSC |Supprimés définitivement 30 jours après l’annulation de l’enregistrement du nœud dans le compte Automation à l’aide du portail Azure ou de l’applet de commande Windows PowerShell [Unregister-AzureRMAutomationDscNode](https://docs.microsoft.com/powershell/module/azurerm.automation/unregister-azurermautomationdscnode) . Les nœuds sont également supprimés définitivement 30 jours après la suppression du compte comprenant le nœud par un utilisateur. |
| Rapports sur le nœud |Supprimés définitivement 90 jours après la création d'un nouveau rapport pour le nœud en question. |

La stratégie de rétention s’applique à tous les utilisateurs et ne peut actuellement pas être personnalisée.

Toutefois, si vous souhaitez conserver les données pendant une période plus longue, vous pouvez transférer les journaux des runbooks vers les journaux Azure Monitor. Pour plus d’informations, consultez [Transférer des données de travaux Azure Automation vers les journaux Azure Monitor](automation-manage-send-joblogs-log-analytics.md).

## <a name="backing-up-azure-automation"></a>Sauvegarde d’Azure Automation

Lorsque vous supprimez un compte Automation dans Microsoft Azure, tous les objets du compte sont supprimés, notamment les Runbooks, les modules, les configurations, les paramètres, les tâches et les éléments multimédia. Il est impossible de récupérer les objets une fois que le compte a été supprimé.  Vous pouvez utiliser les informations suivantes pour sauvegarder le contenu de votre compte Automation avant de le supprimer.

### <a name="runbooks"></a>Runbooks

Vous pouvez exporter vos Runbooks vers vos fichiers de script en utilisant soit le portail Azure, soit l’applet de commande [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) dans Windows PowerShell. Ces fichiers de script peuvent être importés dans un autre compte Automation, comme indiqué dans l’article [Création ou importation d'un Runbook](/previous-versions/azure/dn643637(v=azure.100)).

### <a name="integration-modules"></a>Modules d'intégration

Vous ne pouvez pas exporter les modules d'intégration depuis Azure Automation. Vous devez vous assurer qu'ils sont disponibles à l'extérieur du compte Automation.

### <a name="assets"></a>Éléments multimédias

Vous ne pouvez pas exporter d’ [éléments](/previous-versions/azure/dn939988(v=azure.100)) depuis Azure Automation.  À l'aide du portail Azure, vous devez noter les détails des variables, des informations d'identification, des certificats, des connexions et des planifications.  Vous devez ensuite créer manuellement les ressources qui seront utilisées par les Runbooks que vous importerez dans une autre Automation.

Vous pouvez utiliser les [applets de commande Azure](https://docs.microsoft.com/powershell/module/azurerm.automation#automation) pour récupérer les détails des éléments non chiffrés et les enregistrer pour vous y reporter ultérieurement ou créer des éléments équivalents dans un autre compte Automation.

Il est impossible de récupérer la valeur des variables chiffrées ou du champ du mot de passe des informations d'identification en utilisant les applets de commande. Si vous ne connaissez pas ces valeurs, vous pouvez les récupérer à partir d’un Runbook en utilisant les activités [Get-AutomationVariable](/previous-versions/azure/dn940012(v=azure.100)) et [Get-AutomationPSCredential](/previous-versions/azure/dn940015(v=azure.100)).

Vous ne pouvez pas exporter de certificats depuis Azure Automation. Vous devez vous assurer que tous les certificats sont disponibles en dehors d'Azure.

### <a name="dsc-configurations"></a>Configurations DSC

Vous pouvez exporter vos configurations vers vos fichiers de script en utilisant soit le portail Azure, soit l'applet de commande [Export-AzureRmAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/azurerm.automation/export-azurermautomationdscconfiguration) dans Windows PowerShell. Ces configurations peuvent être importées et utilisées dans un autre compte Automation.

## <a name="geo-replication-in-azure-automation"></a>Géo-réplication dans Azure Automation

La géo-réplication, fonction standard dans les comptes Azure Automation, sauvegarde les données de compte dans une région géographique différente à des fins de redondance. Vous pouvez choisir une région primaire quand vous configurez votre compte ; une région secondaire lui est ensuite automatiquement affectée. Les données secondaires, copiées à partir de la région primaire, sont mises à jour en permanence en cas de perte de données.  

Le tableau suivant montre les paires de régions primaires et secondaires disponibles.

| Principal | Secondary |
| --- | --- |
| États-Unis - partie centrale méridionale |Centre-Nord des États-Unis |
| USA Est 2 |USA Centre |
| Europe Ouest |Europe Nord |
| Asie Sud-Est |Asie Est |
| Japon Est |OuJapon Est |

Dans l’éventualité peu probable que les données d’une région primaire soient perdues, Microsoft tente de les récupérer. Si les données primaires sont irrécupérables, un basculement géographique est effectué et les clients concernés en sont informés via leur abonnement.
