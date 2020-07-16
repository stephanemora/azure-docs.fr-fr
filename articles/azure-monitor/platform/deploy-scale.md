---
title: Déployer Azure Monitor
description: Déployez les fonctionnalités Azure Monitor à la bonne échelle à l’aide d’Azure Policy.
ms.subservice: ''
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 4be403f8efc8e328548b6ef38b36be78a8fb96d7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84678696"
---
# <a name="deploy-azure-monitor-at-scale-using-azure-policy"></a>Déployer les fonctionnalités Azure Monitor à la bonne échelle à l’aide d’Azure Policy
Même si certaines fonctionnalités Azure Monitor sont configurées une fois pour toutes ou un nombre de fois limité, pour d’autres, l’opération doit être répétée pour chacune des ressources que vous voulez superviser. Cet article décrit des méthodes pour implémenter Azure Monitor à la bonne échelle avec Azure Policy et garantir une configuration cohérente et précise de la supervision pour toutes vos ressources Azure.

Par exemple, vous devez créer un paramètre de diagnostic pour toutes vos ressources Azure existantes et pour chaque nouvelle ressource que vous créez. Vous devez aussi installer et configurer un agent chaque fois que vous créez une machine virtuelle. Vous pouvez effectuer ces opérations avec PowerShell ou l’interface CLI, qui sont des méthodes compatibles avec toutes les fonctionnalités Azure Monitor. Avec Azure Policy, vous pouvez mettre en place une logique capable d’effectuer automatiquement la configuration appropriée chaque fois que vous créez ou modifiez une ressource.


## <a name="azure-policy"></a>Azure Policy
Cette section offre une brève présentation d’[Azure Policy](../../governance/policy/overview.md) qui permet d’évaluer et d’appliquer les standards organisationnels à l’échelle d’un abonnement ou d’un groupe d’administration Azure avec un minimum d’effort. Pour des détails complets, consultez la [documentation Azure Policy](../../governance/policy/overview.md).

Avec Azure Policy, vous pouvez spécifier des exigences de configuration pour l’ensemble des ressources qui sont créées et soit identifier celles qui ne sont pas conformes, soit empêcher la création des ressources, soit ajouter la configuration exigée. Les appels à créer une nouvelle ressource ou à en modifier une existante sont systématiquement interceptés. Ainsi, une demande peut être refusée si ses propriétés ne correspondent pas aux propriétés attendues dans une définition de stratégie (elle est alors marquée comme étant non conforme) ou une ressource associée peut être déployée. Vous pouvez corriger les ressources existantes avec une définition de stratégie **deployIfNotExists** ou **modify**.

Azure Policy comporte les objets présents dans le tableau suivant. Pour obtenir une description plus détaillée de chacun d’eux, consultez [Objets Azure Policy](../../governance/policy/overview.md#azure-policy-objects).

| Élément | Description |
|:---|:---|
| Définition de stratégie | Décrit les conditions de conformité des ressources et l’effet à produire si une condition est remplie. Elle peut s’appliquer à toutes les ressources d’un type particulier ou seulement à celles qui ont certaines propriétés. L’effet peut consister simplement à marquer la ressource pour conformité ou à déployer une ressource associée. Les définitions de stratégie sont écrites en JSON, comme indiqué dans [Structure de définition Azure Policy](../../governance/policy/concepts/definition-structure.md). Les effets sont décrits dans [Comprendre les effets d’Azure Policy](../../governance/policy/concepts/effects.md).
| Initiative de stratégie | Groupe de définitions de stratégie qui doivent être appliquées ensemble. Par exemple, vous pouvez disposer d’une définition de stratégie pour l’envoi des journaux de ressources à un espace de travail Log Analytics et une autre pour l’envoi des journaux de ressources à des hubs d’événements. Créez une initiative qui regroupe les deux définitions de stratégie, puis appliquez l’initiative aux ressources au lieu des définitions de stratégie individuelles. Les initiatives sont écrites en JSON, comme indiqué dans [Structure de définition Azure Policy](../../governance/policy/concepts/initiative-definition-structure.md). |
| Affectation | Une définition ou une initiative de stratégie ne prend pas effet tant qu’elle n’est pas affectée à une étendue. Par exemple, affectez une stratégie à un groupe de ressources pour l’appliquer à toutes les ressources créées dans cette ressource, ou appliquez-la à un abonnement pour l’appliquer à toutes les ressources de cet abonnement.  Pour plus d’informations, consultez [Structure d’attribution Azure Policy](../../governance/policy/concepts/assignment-structure.md). |

## <a name="built-in-policy-definitions-for-azure-monitor"></a>Définitions de stratégie intégrées pour Azure Monitor
Azure Policy comprend plusieurs définitions prédéfinies en rapport avec Azure Monitor. Vous pouvez affecter ces définitions de stratégie à votre abonnement existant ou vous en servir de base pour créer vos propres définitions personnalisées. Pour obtenir la liste complète des politiques intégrées de la catégorie **Supervision**, consultez [Définitions de stratégie intégrées d’Azure Policy pour Azure Monitor](../policy-samples.md).

Pour consulter les définitions de stratégie intégrées en rapport avec la supervision, procédez comme suit :

1. Accédez à **Azure Policy** sur le portail Azure.
2. Sélectionnez **Définitions**.
3. Pour **Type**, sélectionnez *Intégré* pour **Catégorie**, sélectionnez *Supervision*.

  ![Définitions de stratégie intégrées](media/deploy-scale/builtin-policies.png)


## <a name="diagnostic-settings"></a>Paramètres de diagnostic
Les [paramètres de diagnostic](../platform/diagnostic-settings.md) recueillent les journaux et les métriques des ressources Azure à plusieurs emplacements, généralement dans un espace de travail Log Analytics, ce qui vous permet d’analyser les données avec des [requêtes de journal](../log-query/log-query-overview.md) et des [alertes de journal](alerts-log.md). Utilisez Policy pour créer automatiquement un paramètre de diagnostic chaque fois que vous créez une ressource.

Chaque type de ressource Azure est constitué d’un ensemble unique de catégories qui doivent être listées dans le paramètre de diagnostic. Pour cette raison, chaque type de ressource a besoin d’une définition de stratégie distincte. Certains types de ressources comportent des définitions de stratégie intégrées que vous pouvez affecter sans modification. Pour les autres types de ressources, vous devez créer une définition personnalisée.

### <a name="built-in-policy-definitions-for-azure-monitor"></a>Définitions de stratégie intégrées pour Azure Monitor
Il existe deux définitions de stratégie intégrées pour chaque type de ressource, l’une à envoyer à un espace de travail Log Analytics et l’autre à Event Hub. Si vous n’avez besoin que d’un seul emplacement, affectez cette stratégie pour le type de ressource. Si vous avez besoin des deux, affectez les deux définitions de stratégie pour la ressource.

Par exemple, l’image suivante montre les définitions de stratégie de paramètres de diagnostic intégrées pour Data Lake Analytics.

  ![Définitions de stratégie intégrées](media/deploy-scale/builtin-diagnostic-settings.png)

### <a name="custom-policy-definitions"></a>Définitions de stratégie personnalisées
Pour les types de ressource qui n’ont pas de stratégie intégrée, vous devez créer une définition de stratégie personnalisée. Vous pouvez le faire manuellement sur le portail Azure en copiant une stratégie intégrée existante et en la modifiant pour votre type de ressource. Il est néanmoins plus efficace de créer la stratégie par programmation en utilisant un script de PowerShell Gallery.

Le script [Create-AzDiagPolicy](https://www.powershellgallery.com/packages/Create-AzDiagPolicy) crée des fichiers de stratégie pour un type de ressource déterminé que vous pouvez installer via PowerShell ou l’interface CLI. Pour créer une définition de stratégie personnalisée pour des paramètres de diagnostic, utilisez la procédure suivante.


1. Vérifiez qu’[Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) est installé.
2. Installez le script avec la commande suivante :
  
    ```azurepowershell
    Install-Script -Name Create-AzDiagPolicy
    ```

3. Exécutez le script en spécifiant la destination des journaux à l’aide des paramètres. Vous êtes ensuite invité à spécifier un abonnement et un type de ressource. Par exemple, pour créer une définition de stratégie qui envoie vers un espace de travail Log Analytics et Event Hub, utilisez la commande suivante.

   ```azurepowershell
   Create-AzDiagPolicy.ps1 -ExportLA -ExportEH -ExportDir ".\PolicyFiles"  
   ```

4. Vous pouvez aussi spécifier un abonnement et un type de ressource dans la commande. Ainsi, pour créer une définition de stratégie qui envoie vers un espace de travail Log Analytics et Event Hub pour des bases de données Azure SQL Server, utilisez la commande suivante.

   ```azurepowershell
   Create-AzDiagPolicy.ps1 -SubscriptionID xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -ResourceType Microsoft.Sql/servers/databases  -ExportLA -ExportEH -ExportDir ".\PolicyFiles"  
   ```

5. Le script crée des dossiers distincts pour chaque définition de stratégie, chacun contenant trois fichiers nommés azurepolicy.json, azurepolicy.rules.json, azurepolicy.parameters.json. Si vous voulez créer la stratégie manuellement sur le portail Azure, vous pouvez copier et coller le contenu du fichier azurepolicy.json, car il contient la définition de stratégie complète. Utilisez les deux autres fichiers avec PowerShell ou l’interface CLI pour créer la définition de stratégie à partir d’une ligne de commande.

    Les exemples suivants montrent comment installer la définition de stratégie à partir de PowerShell et de l’interface CLI. Chacun comprend des métadonnées pour spécifier une catégorie de **supervision** pour regrouper la nouvelle définition de stratégie avec les définitions de stratégie intégrées.

      ```azurepowershell
      New-AzPolicyDefinition -name "Deploy Diagnostic Settings for SQL Server database to Log Analytics workspace" -policy .\Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.rules.json -parameter .\Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.parameters.json -mode All -Metadata '{"category":"Monitoring"}'
      ```

      ```azurecli
      az policy definition create --name 'deploy-diag-setting-sql-database--workspace' --display-name 'Deploy Diagnostic Settings for SQL Server database to Log Analytics workspace'  --rules 'Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.rules.json' --params 'Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.parameters.json' --subscription 'AzureMonitor_Docs' --mode All
      ```

### <a name="initiative"></a>Initiative
Au lieu de créer une affectation pour chaque définition de stratégie, une stratégie courante consiste à créer une initiative incluant les définitions de stratégie pour créer des paramètres de diagnostic pour chaque service Azure. Créez une affectation entre l’initiative et un groupe d’administration, un abonnement ou un groupe de ressources en fonction de la façon dont vous gérez votre environnement. Cette stratégie offre les avantages suivants :

- Créez une seule affectation pour l’initiative au lieu de plusieurs pour chaque type de ressource. Utilisez la même initiative pour plusieurs groupes de supervision, abonnements ou groupes de ressources.
- Modifiez l’initiative quand vous devez ajouter un nouveau type de ressource ou une nouvelle destination. Par exemple, vos exigences initiales peuvent être d’envoyer les données uniquement vers un espace de travail Log Analytics et d’ajouter par la suite Event Hub. Au lieu de créer des affectations, modifiez l’initiative.

Pour plus d’informations sur la création d’une initiative, consultez [Créer et attribuer une définition d’initiative](../../governance/policy/tutorials/create-and-manage.md#create-and-assign-an-initiative-definition). Tenez compte des recommandations suivantes :

- Définissez la **Catégorie**sur **Supervision** pour la regrouper avec les définitions de stratégie intégrées et personnalisées associées.
- Au lieu de spécifier les détails de l’espace de travail Log Analytics et du hub d’événements pour la définition de stratégie incluse dans l’initiative, utilisez un paramètre d’initiative commun. Vous pourrez ainsi spécifier facilement une valeur commune pour toutes les définitions de stratégie et changer cette valeur si nécessaire.

![Définition d’initiative](media/deploy-scale/initiative-definition.png)

### <a name="assignment"></a>Affectation 
Affectez l’initiative à un groupe d’administration, un abonnement ou un groupe de ressources Azure en fonction de l’étendue de vos ressources à superviser. Un [groupe d’administration ](../../governance/management-groups/overview.md) est particulièrement utile pour définir l’étendue de la stratégie, surtout si votre organisation possède plusieurs abonnements.

![Affectation d’initiative](media/deploy-scale/initiative-assignment.png)

Les paramètres initiative vous permettent de spécifier l’espace de travail ou d’autres détails une seule fois pour toutes les définitions de stratégie de l’initiative. 

![Paramètres d’initiative](media/deploy-scale/initiative-parameters.png)

### <a name="remediation"></a>Correction
L’initiative s’applique à chaque machine virtuelle à mesure qu’elle est créée. Une [tâche de correction](../../governance/policy/how-to/remediate-resources.md) déploie les définitions de stratégie de l’initiative dans les ressources existantes, ce qui vous permet de créer des paramètres de diagnostic pour toutes les ressources qui ont déjà été créées. Quand vous créez l’affectation à partir du portail Azure, vous avez la possibilité de créer une tâche de correction en même temps. Pour plus d’informations sur la correction, consultez [Corriger les ressources non conformes avec Azure Policy](../../governance/policy/how-to/remediate-resources.md).

![Correction d’initiative](media/deploy-scale/initiative-remediation.png)


## <a name="azure-monitor-for-vms"></a>Azure Monitor pour machines virtuelles
[Azure Monitor pour machines virtuelles](../insights/vminsights-overview.md) est le principal outil de supervision des machines virtuelles d’Azure Monitor. L’activation d’Azure Monitor pour machines virtuelles a pour effet d’installer l’agent Log Analytics et Dependency Agent. Au lieu d’effectuer ces tâches manuellement, utilisez Azure Policy pour faire en sorte que chaque machine virtuelle soit configurée à mesure que vous la créez.

Azure Monitor pour machines virtuelles comprend deux initiatives intégrées appelées **Activer Azure Monitor pour machines virtuelles** et **Activer Azure Monitor pour les groupes de machines virtuelles identiques**. Ces initiatives comportent un ensemble de définitions de stratégie nécessaires à l’installation de l’agent Log Analytics et de Dependency Agent qui sont nécessaires à l’activation d’Azure Monitor pour machines virtuelles. 

Au lieu de créer des affectations pour ces initiatives à l’aide de l’interface Azure Policy, Azure Monitor pour machines virtuelles est doté d’une fonctionnalité qui vous permet d’inspecter le nombre de machines virtuelles dans chaque étendue pour déterminer si l’initiative a été appliquée. Vous pouvez ensuite configurer l’espace de travail et créer les affectations nécessaires à l’aide de cette interface.

Pour obtenir des détails sur ce processus, consultez [Activer Azure Monitor pour machines virtuelles VM à l’aide d’Azure Policy](../insights/vminsights-enable-at-scale-policy.md).

![Stratégie Azure Monitor pour machines virtuelles](../platform/media/deploy-scale/vminsights-policy.png)


## <a name="next-steps"></a>Étapes suivantes

- Découvrir plus en détail [Azure Policy](../../governance/policy/overview.md).
- Découvrir plus en détail les [paramètres de diagnostic](diagnostic-settings.md).
