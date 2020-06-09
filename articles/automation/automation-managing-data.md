---
title: Gestion des données Azure Automation
description: Cet article décrit les concepts relatifs à la gestion des données dans Azure Automation, notamment la conservation et la sauvegarde des données.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: de60ef31a39a698f9a797a5836546f9b75b67594
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835204"
---
# <a name="management-of-azure-automation-data"></a>Gestion des données Azure Automation

Cet article contient plusieurs rubriques relatives à la gestion de données dans un environnement Azure Automation.

## <a name="data-retention"></a>Conservation des données

Quand vous supprimez une ressource dans Azure Automation, elle est conservée pendant un certain nombre de jours à des fins d’audit avant d’être supprimée définitivement. Vous ne pouvez ni voir ni utiliser la ressource pendant cette période. Cette stratégie vaut aussi pour les ressources qui appartiennent à un compte Automation supprimé.

Le tableau suivant récapitule la stratégie de rétention pour les différentes ressources.

| Données | Stratégie |
|:--- |:--- |
| Comptes |Un compte est définitivement supprimé 30 jours après avoir été supprimé par un utilisateur. |
| Éléments multimédias |Une ressource est définitivement supprimée 30 jours après avoir été supprimée par un utilisateur ou 30 jours après qu’un utilisateur a supprimé un compte qui contenait la ressource. |
| Nœuds DSC |Un nœud DSC est définitivement supprimé 30 jours après avoir été désinscrit d’un compte Automation via le portail Azure ou l’applet de commande [Unregister-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-3.7.0) dans Windows PowerShell. De même, un nœud peut être supprimé définitivement 30 jours après qu’un utilisateur a supprimé le compte qui contenait le nœud. |
| travaux |Une tâche est supprimée et définitivement retirée 30 jours après avoir été modifiée (par exemple, suite à la fin, à l’arrêt ou à l’interruption de la tâche). |
| Modules |Un module est définitivement supprimé 30 jours après avoir été supprimé par un utilisateur ou 30 jours après qu’un utilisateur a supprimé le compte qui contenait le module. |
| Configurations de nœud/fichiers MOF |Une ancienne configuration de nœud est définitivement supprimée 30 jours après la génération d’une nouvelle configuration de nœud. |
| Rapports sur le nœud |Le rapport sur un nœud est définitivement supprimé définitivement 90 jours après la génération d’un nouveau rapport pour ce même nœud. |
| Runbooks |Un runbook est définitivement supprimé 30 jours après qu’un utilisateur a supprimé la ressource ou 30 jours après qu’un utilisateur a supprimé le compte qui contenait la ressource. |

La stratégie de rétention s’applique à tous les utilisateurs et ne peut actuellement pas être personnalisée. Cependant, si vous souhaitez conserver les données sur une plus longue période, vous pouvez [transférer les données de tâches Azure Automation dans des journaux Azure Monitor](automation-manage-send-joblogs-log-analytics.md).

## <a name="data-backup"></a>Sauvegarde de données

Quand vous supprimez un compte Automation dans Azure, tous les objets du compte sont supprimés. Ces objets peuvent notamment consister en des runbooks, des modules, des configurations, des paramètres, des tâches ou des ressources. Ces objets ne peuvent pas être récupérés après que le compte a été supprimé. Vous pouvez utiliser les informations suivantes pour sauvegarder le contenu de votre compte Automation avant de le supprimer.

### <a name="runbooks"></a>Runbooks

Vous pouvez exporter vos Runbooks vers vos fichiers de script en utilisant soit le portail Azure, soit l’applet de commande [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) dans Windows PowerShell. Vous pouvez importer ces fichiers de script dans un autre compte Automation, comme expliqué dans [Gérer les runbooks dans Azure Automation](manage-runbooks.md).

### <a name="integration-modules"></a>Modules d'intégration

Vous ne pouvez pas exporter de modules d’intégration à partir d’Azure Automation. Vous devez les rendre accessibles en dehors du compte Automation.

### <a name="assets"></a>Éléments multimédias

Vous ne pouvez pas exporter de ressources Azure Automation : certificats, connexions, informations d’identification, planifications et variables. En revanche, vous pouvez utiliser le portail Azure et des applets de commande Azure pour noter les détails de ces ressources. Ces détails vous serviront par la suite à créer les ressources qui seront utilisées par les runbooks que vous importerez dans un autre compte Automation.

Vous ne pouvez pas récupérer la valeur des variables chiffrées ou des champs de mot de passe des informations d’identification en utilisant des applets de commande. Si vous ne connaissez pas ces valeurs, vous pouvez les récupérer dans un runbook. Pour récupérer des valeurs de variables, consultez [Ressources de variable dans Azure Automation](shared-resources/variables.md). Pour en savoir plus sur la récupération des valeurs d’informations d’identification, consultez [Ressources d’informations d’identification dans Azure Automation](shared-resources/credentials.md).

 ### <a name="dsc-configurations"></a>Configurations DSC

Vous pouvez exporter vos configurations DSC dans des fichiers de script en utilisant soit le portail Azure, soit l'applet de commande [Export-AzAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/az.automation/export-azautomationdscconfiguration?view=azps-3.7.0
) dans Windows PowerShell. Vous pouvez importer et utiliser ces configurations dans un autre compte Automation.

## <a name="geo-replication-in-azure-automation"></a>Géo-réplication dans Azure Automation

La géoréplication est une fonctionnalité standard des comptes Azure Automation. Au moment de configurer votre compte, vous choisissez une région primaire. Le service de géoréplication interne d’Automation affecte alors automatiquement une région secondaire au compte. Les données de compte de la région primaire sont sauvegardées en continu dans la région secondaire. La liste complète des régions primaires et secondaires se trouve dans [Continuité et reprise d’activité : régions jumelées d’Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

La sauvegarde créée par le service de géoréplication Automation est une copie complète de ressources, configurations et autre objets analogues Automation. Cette sauvegarde peut être utilisée si la région primaire perd des données à la suite d’une défaillance. Dans le cas peu probable d’une perte des données d’une région primaire, Microsoft tente de les récupérer. Si la récupération de ces données primaires échoue, le basculement automatique est utilisé et vous êtes informé de la situation par l’intermédiaire de votre abonnement Azure. 

Le service de géoréplication Automation n’est pas directement accessible aux clients externes en cas de défaillance régionale. Si vous voulez conserver les runbooks et la configuration Automation à l’occasion de défaillances régionales :

1. Sélectionnez la région secondaire à appairer avec la région géographique de votre compte Automation principal.

2. Créez un compte Automation dans la région secondaire.

3. Dans le compte principal, exporter vos runbooks sous forme de fichiers de script.

4. Importez les runbooks dans votre compte Automation de la région secondaire.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur les ressources sécurisées dans Azure Automation, consultez [Chiffrement des ressources sécurisées dans Azure Automation](automation-secure-asset-encryption.md).
* Pour découvrir plus en détail la géoréplication, consultez [Création et utilisation de la géoréplication active](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication).