---
title: Déployer Azure Monitor pour machines virtuelles (préversion) | Microsoft Docs
description: Cet article décrit la façon dont vous déployez et configurez Azure Monitor pour machines virtuelles afin que vous puissiez comprendre comment votre application distribuée s’exécute et quels problèmes d’intégrité ont été identifiés.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/07/2018
ms.author: magoedte
ms.openlocfilehash: 3c1caa2485437768781ada2c7271445ccd3c19e1
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54190326"
---
# <a name="deploy-azure-monitor-for-vms-preview"></a>Déployer Azure Monitor pour machines virtuelles (préversion)
Cet article décrit comment configurer Azure Monitor pour machines virtuelles. Le service surveille l’intégrité du système d’exploitation de vos machines virtuelles Azure et de vos groupes de machines virtuelles identiques, mais aussi des machines virtuelles de votre environnement. Cette supervision inclut la détection et le mappage de dépendances d’application qui peuvent être hébergées sur ces derniers. 

Pour activer Azure Monitor pour machines virtuelles, utilisez l’une des méthodes suivantes :

* Activez une seule machine virtuelle Azure en sélectionnant **Insights (préversion)** directement à partir de la machine virtuelle.
* Activez plusieurs machines virtuelles Azure à l’aide d’Azure Policy. Grâce à cette méthode, les dépendances requises des machines virtuelles existantes et nouvelles sont installées et configurées correctement. Les machines virtuelles non conformes sont signalées afin que vous puissiez décider si vous souhaitez les activer, et choisir comment les corriger.
* Activez plusieurs machines virtuelles Azure ou groupes de machines virtuelles identiques sur un abonnement ou un groupe de ressources spécifié à l’aide de PowerShell.

Des informations supplémentaires sur chaque méthode sont fournies plus loin dans l’article.

## <a name="prerequisites"></a>Prérequis
Avant de commencer, prenez connaissance de ce qui suit.

### <a name="log-analytics"></a>Log Analytics

L’espace de travail Log Analytics est actuellement pris en charge dans les régions suivantes :

- USA Centre-Ouest
- USA Est
- Europe Ouest
- Asie Sud-Est<sup>1</sup>

<sup>1</sup> Cette région ne prend pas encore en charge la fonctionnalité d’intégrité d’Azure Monitor pour machines virtuelles.

>[!NOTE]
>Il est possible de déployer des machines virtuelles Azure à partir de n’importe quelle région, et pas seulement des régions qui prennent en charge l’espace de travail Log Analytics.
>

Si vous n’avez pas d’espace de travail, vous pouvez en créer un comme suit :
* [L’interface de ligne de commande Microsoft Azure](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Le portail Azure](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

Si vous activez la supervision pour une seule machine virtuelle Azure sur le Portail Azure, vous pouvez créer un espace de travail pendant ce processus.

L’activation de la solution pour le scénario à l’échelle exige dans un premier temps de configurer ce qui suit dans votre espace de travail Log Analytics :

* Installez les solutions ServiceMap et InfrastructureInsights. Vous pouvez effectuer cette installation uniquement à l’aide d’un modèle Azure Resource Manager fourni dans l’article.
* Configurez l’espace de travail Log Analytics pour collecter les compteurs de performances.

Pour configurer votre espace de travail dans le scénario à l’échelle, consultez [Configurer un espace de travail Log Analytics pour un déploiement à l’échelle](#setup-log-analytics-workspace).

### <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge

Le tableau ci-après répertorie les systèmes d’exploitation Windows et Linux qui sont pris en charge avec Azure Monitor pour machines virtuelles. Une liste complète détaillant les versions principales et mineures de système d’exploitation et de noyau Linux prises en charge est fournie plus loin dans cette section.

|Version du SE |Performances |Cartes |Intégrité |
|-----------|------------|-----|-------|
|Windows Server 2016 1803 | X | X | X |
|Windows Server 2016 | X | X | X |
|Windows Server 2012 R2 | X | X | |
|Windows Server 2012 | X | X | |
|Windows Server 2008 R2 | X | X| |
|Red Hat Enterprise Linux (RHEL) 7, 6| X | X| X |
|Ubuntu 18.04, 16.04, 14.04 | X | X | X |
|CentOS Linux 7, 6 | X | X | X |
|SUSE Linux Enterprise Server (SLES) 12 | X | X | X |
|Oracle Linux 7 | X<sup>1</sup> | | X |
|Oracle Linux 6 | X | X | X |
|Debian 9.4, 8 | X<sup>1</sup> | | X |

<sup>1</sup> La fonctionnalité Performances d’Azure Monitor pour machines virtuelles est disponible uniquement à partir d’Azure Monitor. Elle n’est pas disponible lorsque vous accédez directement au volet gauche de la machine virtuelle Azure.

>[!NOTE]
>Les informations ci-après concernent la prise en charge du système d’exploitation Linux :
> - Seules les versions du noyau SMP Linux et par défaut sont prises en charge.
> - Les versions non standard du noyau, par exemple Physical Address Extension (PAE) et Xen, ne sont prises en charge par aucune distribution Linux. Par exemple, un système avec la chaîne de version *2.6.16.21-0.8-xen* n’est pas pris en charge.
> - Les noyaux personnalisés, y compris les recompilations de noyaux standard, ne sont pas pris en charge.
> - Le noyau CentOSPlus n’est pas pris en charge.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Version du SE | Version du noyau |
|:--|:--|
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7,2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Version du SE | Version du noyau |
|:--|:--|
| 6.0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4. | 2.6.32-358 |
| 6.5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6.7 | 2.6.32-573 |
| 6,8 | 2.6.32-642 |
| 6.9 | 2.6.32-696 |

#### <a name="ubuntu-server"></a>Serveur Ubuntu

| Version du SE | Version du noyau |
|:--|:--|
| Ubuntu 18.04 | noyau 4.15.* |
| Ubuntu 16.04.3 | noyau 4.15.* |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

#### <a name="oracle-enterprise-linux-6-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux 6 avec noyau Unbreakable Enterprise
| Version du SE | Version du noyau
|:--|:--|
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4. | Oracle 2.6.39-400 (UEK R2) |
| 6.5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-enterprise-linux-5-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux 5 avec noyau Unbreakable Enterprise

| Version du SE | Version du noyau
|:--|:--|
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Version du SE | Version du noyau
|:--|:--|
|12 SP2 | 4.4.* |
|12 SP3 | 4.4.* |

### <a name="the-microsoft-dependency-agent"></a>Microsoft Dependency Agent
La fonctionnalité Azure Monitor pour machines virtuelles Map obtient ses données à partir de l’agent Microsoft Dependency. Celui-ci dépend de l’agent Log Analytics pour ses connexions à Log Analytics. Par conséquent, Log Analytics Agent doit être installé et configuré avec l’agent Dependency sur le système.

Que vous activiez Azure Monitor pour machines virtuelles pour une seule machine virtuelle Azure ou que vous utilisiez les méthodes de déploiement à l’échelle, vous devez utiliser l’extension de l’agent Azure VM Dependency pour installer l’agent dans le cadre de cette expérience.

Dans un environnement hybride, vous pouvez télécharger et installer l’agent Dependency de deux manières : manuellement ou par le biais d’une méthode de déploiement automatisé pour les machines virtuelles qui sont hébergées en dehors d’Azure.

Le tableau suivant décrit les sources connectées prises en charge par la fonctionnalité Map dans un environnement hybride.

| Source connectée | Prise en charge | Description |
|:--|:--|:--|
| Agents Windows | Oui | Outre [l’agent Log Analytics pour Windows](../../azure-monitor/platform/log-analytics-agent.md), les agents Windows nécessitent l’agent de dépendances Microsoft. Pour obtenir la liste complète des versions des systèmes d’exploitation, consultez les [systèmes d’exploitation pris en charge](#supported-operating-systems). |
| Agents Linux | Oui | Outre [l’agent Log Analytics pour Linux](../../azure-monitor/platform/log-analytics-agent.md), les agents Linux nécessitent l’agent de dépendances Microsoft. Pour obtenir la liste complète des versions des systèmes d’exploitation, reportez-vous aux [systèmes d’exploitation pris en charge](#supported-operating-systems). |
| Groupe d’administration Microsoft System Center Operations Manager | Non  | |

L’agent Dependency peut être téléchargé à partir des emplacements suivants :

| Fichier | SE | Version | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) |  Windows | 9.7.4 | A111B92AB6CF28EB68B696C60FE51F980BFDFF78C36A900575E17083972989E0 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.4 | AB58F3DB8B1C3DEE7512690E5A65F1DFC41B43831543B5C040FCCE8390F2282C |

## <a name="role-based-access-control"></a>Contrôle d’accès en fonction du rôle
Pour activer les fonctionnalités dans Azure Monitor pour machines virtuelles et y accéder, vous devez avoir le rôle d’accès suivant :

- Pour activer la solution, vous devez avoir le rôle *Contributeur Log Analytics*.

- Pour afficher les données de performances, d’intégrité et de mappage, vous devez avoir le rôle *Lecteur d’analyse* pour la machine virtuelle Azure. L’espace de travail Log Analytics doit être configuré pour Azure Monitor pour machines virtuelles.

Pour plus d’informations sur la façon de contrôler l’accès à un espace de travail Log Analytics, consultez [Gérer les espaces de travail](../../azure-monitor/platform/manage-access.md).

## <a name="enable-monitoring-in-the-azure-portal"></a>Activer la surveillance dans le portail Azure
Pour activer la surveillance de votre machine virtuelle Azure à partir du portail Azure, effectuez les étapes suivantes :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

1. Sélectionnez **Machines virtuelles**.

1. Sélectionnez une machine virtuelle dans la liste.

1. Sur la page de la machine virtuelle, dans la section **Surveillance**, sélectionnez **Insights (préversion)**.

1. Sur la page **Insights (préversion)**, sélectionnez **Essayer maintenant**.

    ![Activer Azure Monitor pour machines virtuelles pour une machine virtuelle](./media/vminsights-onboard/enable-vminsights-vm-portal-01.png)
1. Dans la page **Intégration des insights de Azure Monitor**, si vous disposez d’un espace de travail Log Analytics appartenant au même abonnement, sélectionnez-le dans la liste déroulante.  
    La liste présélectionne l’espace de travail par défaut et l’emplacement où la machine virtuelle est déployée dans l’abonnement. 

    >[!NOTE]
    >Si vous souhaitez créer un espace de travail Log Analytics pour stocker les données de supervision de la machine virtuelle, suivez les instructions de l’article [Créer un espace de travail Log Analytics](../../azure-monitor/learn/quick-create-workspace.md) dans l’une des régions prises en charge répertoriées précédemment.

Une fois que vous avez activé la supervision, 10 minutes peuvent s’écouler avant que vous ne puissiez voir les métriques d’intégrité de la machine virtuelle.

![Activer Azure Monitor pour machines virtuelles surveillant le traitement du déploiement](./media/vminsights-onboard/onboard-vminsights-vm-portal-status.png)


## <a name="deploy-at-scale"></a>Déployer à l’échelle
Dans cette section, vous déployez Azure Monitor pour machines virtuelles à l’échelle grâce à Azure Policy ou Azure PowerShell.

Avant de déployer vos machines virtuelles, préconfigurez votre espace de travail Log Analytics en procédant comme suit :

1. Si vous ne disposez pas déjà d’un espace de travail, créez-en un capable de prendre en charge Azure Monitor pour machines virtuelles.  
    Avant de procéder, consultez [Gestion des espaces de travail](../../log-analytics/log-analytics-manage-access.md?toc=/azure/azure-monitor/toc.json) pour comprendre les considérations de coût, de gestion et de conformité.

1. Si nécessaire, créez un espace de travail qui prendra en charge Azure Monitor pour machines virtuelles. Avant de créer un espace de travail, consultez [Gérer les espaces de travail](../../azure-monitor/platform/manage-access.md?toc=/azure/azure-monitor/toc.json) afin de comprendre les enjeux liés au coût, à la gestion et à la conformité.

1. Activez les compteurs de performances de l'espace de travail pour procéder à la collecte sur les machines virtuelles Linux et Windows.

1. Installez et activez les solutions ServiceMap et InfrastructureInsights dans votre espace de travail.

### <a name="set-up-a-log-analytics-workspace"></a>Configurer un espace de travail Log Analytics
Si vous n’avez pas d’espace de travail Log Analytics, créez-en un au moyen de l’une des méthodes suggérées dans la section [Conditions préalables](#log-analytics).

#### <a name="enable-performance-counters"></a>Activer les compteurs de performance
Si l’espace de travail Log Analytics référencé par la solution n’est pas déjà configuré pour collecter les compteurs de performance requis par la solution, vous devez activer ces derniers. Vous pouvez le faire de deux manières :
* Manuellement, tel que décrit dans [Sources de données de performance Windows et Linux dans Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* En téléchargeant et en exécutant un script PowerShell disponible dans [Azure PowerShell Gallery](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

#### <a name="install-the-servicemap-and-infrastructureinsights-solutions"></a>Installer les solutions ServiceMap et InfrastructureInsights
Cette méthode inclut un modèle JSON spécifiant la configuration requise pour activer les composants de la solution dans votre espace de travail Log Analytics.

Si vous n’êtes pas familiarisé avec le déploiement de ressources à l’aide d’un modèle, consultez les rubriques suivantes :
* [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Déployer des ressources à l’aide de modèles Resource Manager et de l’interface de ligne de commande Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Si vous avez choisi d’utiliser Azure CLI, vous devez d’abord l’installer et l’utiliser localement. Vous devez exécuter Azure CLI 2.0.27 ou version ultérieure. Pour identifier votre version, exécutez `az --version`. Si vous devez installer ou mettre à niveau Azure CLI, consultez [Installer Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Copiez et collez la syntaxe JSON suivante dans votre fichier :

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "WorkspaceName": {
                "type": "string"
            },
            "WorkspaceLocation": {
                "type": "string"
            }
        },
        "resources": [
            {
                "apiVersion": "2017-03-15-preview",
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('WorkspaceName')]",
                "location": "[parameters('WorkspaceLocation')]",
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                            "promotionCode": ""
                        }
                    },
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },
                        "plan": {
                            "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'InfrastructureInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Enregistrez ce fichier sous *installsolutionsforvminsights.json* dans un dossier local.

1. Modifiez les valeurs pour *WorkspaceName*, *ResourceGroupName*, et *WorkspaceLocation*. La valeur de *WorkspaceName* correspond à l’ID de ressource complet de votre espace de travail Log Analytics, qui inclut le nom de l’espace de travail. La valeur de *WorkspaceLocation* correspond à la région dans laquelle l’espace de travail est défini.

1. Vous êtes prêt à déployer ce modèle à l’aide de la commande PowerShell suivante :

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    Le changement de configuration peut prendre quelques minutes. Lorsqu’il est terminé, un message semblable à celui qui suit s’affiche avec les résultats :

    ```powershell
    provisioningState       : Succeeded
    ```

### <a name="enable-by-using-azure-policy"></a>Activer à l’aide d’Azure Policy
Pour activer Azure Monitor pour machines virtuelles à l’échelle d’une manière qui aide à garantir la conformité et l’activation automatique des machines virtuelles qui viennent d’être configurées, nous vous recommandons [Azure Policy](../../azure-policy/azure-policy-introduction.md). Ces stratégies permettent de :

* Déployer Log Analytics Agent et Dependency Agent.
* Créer un rapport sur les résultats de conformité.
* Corriger les machines virtuelles non conformes.

Pour activer Azure Monitor pour machines virtuelles à l’aide d’Azure Policy dans votre tenant :

- Assigner l’initiative à une étendue : groupe d’administration, abonnement ou groupe de ressources
- Examiner et corriger les résultats de conformité

Pour plus d’informations sur l’affectation d’Azure Policy, consultez [Vue d’ensemble d’Azure Policy](../../governance/policy/overview.md#policy-assignment) et examinez la [présentation des groupes d’administration](../../governance/management-groups/index.md) avant de continuer.

Les définitions de stratégie sont répertoriées dans le tableau suivant :

|NOM |Description |type |
|-----|------------|-----|
|[Préversion] : Activer Azure Monitor pour machines virtuelles |Activer Azure Monitor pour machines virtuelles dans l’étendue spécifiée (groupe d’administration, abonnement ou groupe de ressources). Utilise l’espace de travail Log Analytics comme paramètre. |Initiative |
|[Préversion] : Vérifier le déploiement de Dependency Agent : image de machine virtuelle (OS) non listée |Présente les machines virtuelles comme non conformes si l’image de machine virtuelle (OS) n’est pas définie dans la liste et si l’agent n’est pas installé. |Stratégie |
|[Préversion] : Vérifier le déploiement de Log Analytics Agent : image de machine virtuelle (OS) non listée |Présente les machines virtuelles comme non conformes si l’image de machine virtuelle (OS) n’est pas définie dans la liste et si l’agent n’est pas installé. |Stratégie |
|[Préversion] : Déployer Dependency Agent pour les machines virtuelles Linux |Déployer Dependency Agent pour les machines virtuelles Linux si l’image de machine virtuelle (OS) est définie dans la liste et si l’agent n’est pas installé. |Stratégie |
|[Préversion] : Déployer Dependency Agent pour les machines virtuelles Windows |Déployer Dependency Agent pour les machines virtuelles Windows si l’image de machine virtuelle (OS) est définie dans la liste et si l’agent n’est pas installé. |Stratégie |
|[Préversion] : Déployer Log Analytics Agent pour les machines virtuelles Linux |Déployer Log Analytics Agent pour les machines virtuelles Linux si l’image de machine virtuelle (OS) est définie dans la liste et si l’agent n’est pas installé. |Stratégie |
|[Préversion] : Déployer Log Analytics Agent pour les machines virtuelles Windows |Déployer Log Analytics Agent pour les machines virtuelles Windows si l’image de machine virtuelle (OS) est définie dans la liste et si l’agent n’est pas installé. |Stratégie |

La stratégie autonome (non incluse avec l’initiative) est décrite ici :

|NOM |Description |type |
|-----|------------|-----|
|[Préversion] : Vérifier les machines virtuelles de l’espace de travail Log Analytics - Signaler les incompatibilités |Signale les machines virtuelles comme non conformes si elles n’alimentent pas de journal dans l’espace de travail Log Analytics spécifié dans l’attribution de stratégie/d’initiative. |Stratégie |

#### <a name="assign-the-azure-monitor-initiative"></a>Assigner l’initiative Azure Monitor
Avec cette version initiale, vous pouvez créer l’affectation de stratégie uniquement sur le Portail Azure. Pour savoir comment effectuer ces étapes, consultez  [Créer une affectation de stratégie à partir du portail Azure](../../governance/policy/assign-policy-portal.md).

1. Pour lancer le service Azure Policy sur le Portail Azure, cliquez sur **Tous les services**, puis recherchez **Stratégie** et cliquez dessus.

1. Dans le volet gauche de la page Azure Policy, sélectionnez **Attributions**.  
    Une affectation est une stratégie qui a été affectée pour être appliquée dans une étendue spécifique.
    
1. En haut de la page **Stratégie - Affectations**, sélectionnez **Affecter l’initiative**.

1. Sur la page **Affecter l’initiative**, choisissez l’**étendue** en cliquant sur les points de suspension et en sélectionnant un groupe d’administration ou un abonnement.  
    Dans cet exemple, une étendue limite l’affectation de stratégie à un regroupement de machines virtuelles pour la mise en conformité.
    
1. Au bas de la page **Étendue**, cliquez sur **Sélectionner** pour enregistrer vos modifications.

1. (Facultatif) Pour supprimer une ou plusieurs ressources à partir de l’étendue, sélectionnez **Exclusions**.

1. Sélectionnez les points de suspension **Définition d’initiative** pour afficher la liste des définitions disponibles, sélectionnez **[Préversion] Activer Azure Monitor pour machines virtuelles**, puis cliquez sur **Sélectionner**.  
    Le champ **Nom de l’attribution** est automatiquement rempli avec le nom d’initiative que vous avez sélectionné, mais vous pouvez le modifier. Vous pouvez également ajouter une description (facultatif). Le champ **Attribué par** est rempli automatiquement en fonction de l’utilisateur connecté. Cette valeur est facultative.
    
1. Dans la liste déroulante **Espace de travail Log Analytics** de la région prise en charge, sélectionnez un espace de travail.

    >[!NOTE]
    >Si l’espace de travail se trouve en dehors de l’étendue de l’affectation, accordez des autorisations de *Contributeur Log Analytics* à l’ID du principal de l’affectation de stratégie. Si vous ne le faites pas, vous risquez d’obtenir un message d’échec de déploiement du type : `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ... ` Pour accorder l’accès, reportez-vous à [Configurer manuellement l’identité managée](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity).
    >  
    La case à cocher **Identité managée** est sélectionnée, car l’initiative affectée inclut une stratégie avec l’effet *deployIfNotExists*.
    
1. Dans la liste déroulante **Gérer l’emplacement d’identité**, sélectionnez la région appropriée.

1. Sélectionnez **Attribuer**.

#### <a name="review-and-remediate-the-compliance-results"></a>Examiner et corriger les résultats de conformité

Pour savoir comment examiner les résultats de conformité, vous pouvez consulter [Identifier les ressources non conformes](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources). Dans le volet gauche, sélectionnez **Conformité**, puis repérez l’initiative **[Préversion] Activer Azure Monitor pour machines virtuelles** pour les machines virtuelles qui ne sont pas conformes à l’affectation que vous avez créée.

![Conformité de stratégie pour les machines virtuelles Azure](./media/vminsights-onboard/policy-view-compliance-01.png)

Sur la base des résultats des stratégies incluses avec l’initiative, les machines virtuelles sont présentées comme étant non conformes dans les scénarios suivants :

* Log Analytics Agent ou Dependency Agent n’est pas déployé.  
    Il s’agit d’un cas courant pour une étendue comportant des machines virtuelles existantes. Pour limiter ce risque, déployez les agents requis en [créant des tâches de correction](../../governance/policy/how-to/remediate-resources.md) sur une stratégie non conforme.  
    - [Préversion]: Deploy Dependency Agent for Linux VMs
    - [Préversion]: Deploy Dependency Agent for Windows VMs
    - [Préversion]: Deploy Log Analytics Agent for Linux VMs
    - [Préversion]: Deploy Log Analytics Agent for Windows VMs

* L’image de machine virtuelle (OS) n’est pas identifiée dans la définition de stratégie.  
    Les critères de la stratégie de déploiement incluent uniquement les machines virtuelles qui sont déployées à partir d’images de machine virtuelle Azure connues. Consultez la documentation pour savoir si le système d’exploitation de la machine virtuelle est pris en charge. Si ce n’est pas le cas, dupliquez la stratégie de déploiement et mettez-la à jour ou modifiez-la pour rendre l’image conforme.  
    - [Préversion] : Vérifier le déploiement de Dependency Agent : image de machine virtuelle (OS) non listée
    - [Préversion] : Vérifier le déploiement de Log Analytics Agent : image de machine virtuelle (OS) non listée

* Les machines virtuelles ne sont pas connectées à l’espace de travail Log Analytics spécifié.  
    Il est possible que certaines machines virtuelles de l’étendue de l’initiative soient connectées à un espace de travail Log Analytics différent de celui spécifié dans l’affectation de stratégie. Cette stratégie est un outil qui permet d’identifier les machines virtuelles qui relèvent d’un espace de travail non conforme.  
    - [Préversion]: Audit Log Analytics Workspace for VM - Report Mismatch

### <a name="enable-with-powershell"></a>Activer avec PowerShell
Si vous souhaitez activer Azure Monitor pour machines virtuelles pour plusieurs machines virtuelles ou des groupes de machines virtuelles identiques, vous pouvez utiliser le script PowerShell [Install-VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0), disponible dans Azure PowerShell Gallery. Ce script effectue une itération dans chaque machine virtuelle et groupe de machines virtuelles identiques de votre abonnement, au sein du groupe de ressources à portée spécifié par *ResourceGroup*, ou dans une seule machine virtuelle ou un seul groupe de machines virtuelles identiques spécifié par *Name*. Pour chaque machine virtuelle ou groupe de machines virtuelles identiques, le script vérifie si l’extension de machine virtuelle est déjà installée. Si l’extension de machine virtuelle n’est pas installée, le script tente de la réinstaller. Si l’extension de machine virtuelle est installée, le script installe les extensions de machine virtuelle Log Analytics Agent et Dependency Agent.

Ce script requiert le module Azure PowerShell version 5.7.0 ou ultérieure. Exécutez `Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également exécuter `Connect-AzureRmAccount` pour créer une connexion avec Azure.

Pour obtenir une liste des détails de l’argument du script et un exemple d’utilisation, exécutez `Get-Help`.

```powershell
Get-Help .\Install-VMInsights.ps1 -Detailed

SYNOPSIS
    This script installs VM extensions for Log Analytics and the Dependency agent as needed for VM Insights.


SYNTAX
    .\Install-VMInsights.ps1 [-WorkspaceId] <String> [-WorkspaceKey] <String> [-SubscriptionId] <String> [[-ResourceGroup]
    <String>] [[-Name] <String>] [[-PolicyAssignmentName] <String>] [-ReInstall] [-TriggerVmssManualVMUpdate] [-Approve] [-WorkspaceRegion] <String>
    [-WhatIf] [-Confirm] [<CommonParameters>]


DESCRIPTION
    This script installs or re-configures following on VMs and VM Scale Sets:
    - Log Analytics VM Extension configured to supplied Log Analytics Workspace
    - Dependency Agent VM Extension

    Can be applied to:
    - Subscription
    - Resource Group in a Subscription
    - Specific VM/VM Scale Set
    - Compliance results of a policy for a VM or VM Extension

    Script will show you list of VMs/VM Scale Sets that will apply to and let you confirm to continue.
    Use -Approve switch to run without prompting, if all required parameters are provided.

    If the extensions are already installed will not install again.
    Use -ReInstall switch if you need to for example update the workspace.

    Use -WhatIf if you would like to see what would happen in terms of installs, what workspace configured to, and status of the extension.


PARAMETERS
    -WorkspaceId <String>
        Log Analytics WorkspaceID (GUID) for the data to be sent to

    -WorkspaceKey <String>
        Log Analytics Workspace primary or secondary key

    -SubscriptionId <String>
        SubscriptionId for the VMs/VM Scale Sets
        If using PolicyAssignmentName parameter, subscription that VMs are in

    -ResourceGroup <String>
        <Optional> Resource Group to which the VMs or VM Scale Sets belong

    -Name <String>
        <Optional> To install to a single VM/VM Scale Set

    -PolicyAssignmentName <String>
        <Optional> Take the input VMs to operate on as the Compliance results from this Assignment
        If specified will only take from this source.

    -ReInstall [<SwitchParameter>]
        <Optional> If VM/VM Scale Set is already configured for a different workspace, set this to change to the new workspace

    -TriggerVmssManualVMUpdate [<SwitchParameter>]
        <Optional> Set this flag to trigger update of VM instances in a scale set whose upgrade policy is set to Manual

    -Approve [<SwitchParameter>]
        <Optional> Gives the approval for the installation to start with no confirmation prompt for the listed VMs/VM Scale Sets

    -WorkspaceRegion <String>
        Region the Log Analytics Workspace is in
        Supported values: "East US","eastus","Southeast Asia","southeastasia","West Central US","westcentralus","West Europe","westeurope"
        For Health supported is: "East US","eastus","West Central US","westcentralus"

    -WhatIf [<SwitchParameter>]
        <Optional> See what would happen in terms of installs.
        If extension is already installed will show what workspace is currently configured, and status of the VM extension

    -Confirm [<SwitchParameter>]
        <Optional> Confirm every action

    <CommonParameters>
        This cmdlet supports the common parameters: Verbose, Debug,
        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
        OutBuffer, PipelineVariable, and OutVariable. For more information, see
        about_CommonParameters (https:/go.microsoft.com/fwlink/?LinkID=113216).

    -------------------------- EXAMPLE 1 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup>

    Install for all VMs in a Resource Group in a subscription

    -------------------------- EXAMPLE 2 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup> -ReInstall

    Specify to reinstall extensions even if already installed, for example to update to a different workspace

    -------------------------- EXAMPLE 3 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -PolicyAssignmentName a4f79f8ce891455198c08736 -ReInstall

    Specify to use a PolicyAssignmentName for source, and to reinstall (move to a new workspace)
```

L’exemple suivant illustre l’utilisation des commandes PowerShell dans le dossier pour activer Azure Monitor pour machines virtuelles et comprendre la sortie attendue :

```powershell
$WorkspaceId = "<GUID>"
$WorkspaceKey = "<Key>"
$SubscriptionId = "<GUID>"
.\Install-VMInsights.ps1 -WorkspaceId $WorkspaceId -WorkspaceKey $WorkspaceKey -SubscriptionId $SubscriptionId -WorkspaceRegion eastus

Getting list of VMs or VM ScaleSets matching criteria specified

VMs or VM ScaleSets matching criteria:

db-ws-1 VM running
db-ws2012 VM running

This operation will install the Log Analytics and Dependency agent extensions on above 2 VMs or VM Scale Sets.
VMs in a non-running state will be skipped.
Extension will not be reinstalled if already installed. Use -ReInstall if desired, for example to update workspace

Confirm
Continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

db-ws-1 : Deploying DependencyAgentWindows with name DAExtension
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Deploying DependencyAgentWindows with name DAExtension
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Summary:

Already Onboarded: (0)

Succeeded: (4)
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Connected to different workspace: (0)

Not running - start VM to configure: (0)

Failed: (0)
```

## <a name="enable-for-a-hybrid-environment"></a>Activation pour un environnement hybride
Cette section explique comment déployer des machines virtuelles ou des ordinateurs physiques hébergés dans votre centre de données ou d’autres environnements cloud pour la supervision par Azure Monitor pour machines virtuelles.

L’agent Map Dependency Agent Azure Monitor pour machines virtuelles ne transmet pas de données par lui-même et ne requiert pas de modifications des pare-feu ni des ports. Log Analytics Agent transmet toujours les données Map au service Azure Monitor, directement ou par le biais de la [passerelle OMS](../../azure-monitor/platform/gateway.md), si vos stratégies de sécurité n’autorisent pas les ordinateurs du réseau à se connecter à Internet.

Passez en revue les exigences et les méthodes de déploiement pour l’[agent Log Analytique Linux et Windows](../../log-analytics/log-analytics-agent-overview.md).

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

En bref, les étapes sont les suivantes :

1. Installez Log Analytics Agent pour Windows ou Linux.

1. Téléchargez et installez Map Dependency Agent Azure Monitor pour machines virtuelles pour [Windows](https://aka.ms/dependencyagentwindows) ou [Linux](https://aka.ms/dependencyagentlinux).

1. Activez la collecte des compteurs de performance.

1. Déployez Azure Monitor pour machines virtuelles.

### <a name="install-the-dependency-agent-on-windows"></a>Installer l’agent de dépendances sous Windows
Vous pouvez installer Dependency Agent manuellement sur les ordinateurs Windows en exécutant `InstallDependencyAgent-Windows.exe`. Si vous exécutez ce fichier exécutable sans aucune option, il démarre un assistant de configuration que vous pouvez suivre pour une installation interactive de l’agent.

>[!NOTE]
>Des privilèges d’*administrateur* sont requis pour installer ou désinstaller l’agent.

Le tableau suivant répertorie les paramètres pris en charge par le programme de configuration pour l’agent à partir de la ligne de commande.

| Paramètre | Description |
|:--|:--|
| /? | Retourne la liste des options de ligne de commande. |
| /S | Effectue une installation silencieuse sans interaction utilisateur. |

Par exemple, pour exécuter le programme d’installation avec le paramètre `/?`, tapez **InstallDependencyAgent-Windows.exe /?**.

Par défaut, les fichiers Dependency Agent pour Windows sont installés dans le répertoire *C:\Program Files\Microsoft Dependency Agent*. Si le démarrage de l’agent de dépendances échoue une fois l’installation terminée, recherchez des informations détaillées sur l’erreur dans les journaux. Le répertoire des journaux est *%Programfiles%\Microsoft Dependency Agent\logs*.

### <a name="install-the-dependency-agent-on-linux"></a>Installer l’agent de dépendances sous Linux
Dependency Agent est installé sur les ordinateurs Linux avec *InstallDependencyAgent-Linux64.bin*, script shell comportant un fichier binaire à extraction automatique. Vous pouvez exécuter le fichier à l’aide de `sh` ou ajouter des autorisations d’exécution au fichier lui-même.

>[!NOTE]
> L’accès racine est requis pour installer ou configurer l’agent.
>

| Paramètre | Description |
|:--|:--|
| -help | Récupérez la liste des options de ligne de commande. |
| -s | Effectuez une installation silencieuse sans invite utilisateur. |
| --check | Vérifiez les autorisations et le système d’exploitation, sans installer l’agent. |

Par exemple, pour exécuter le programme d’installation avec le paramètre `-help`, tapez **InstallDependencyAgent-Linux64.bin -help**.

Installez Dependency Agent pour Linux en tant que racine en exécutant la commande suivante, `sh InstallDependencyAgent-Linux64.bin`.

Si le démarrage de l’agent de dépendances échoue, recherchez des informations détaillées sur l’erreur dans les journaux. Sur les agents Linux, le répertoire des journaux est */var/opt/microsoft/dependency-agent/log*.

Les fichiers de l’agent de dépendances sont placés dans les répertoires suivants :

| Fichiers | Lieu |
|:--|:--|
| Fichiers principaux | /opt/microsoft/dependency-agent |
| Fichiers journaux | /var/opt/microsoft/dependency-agent/log |
| Fichiers de configuration | /etc/opt/microsoft/dependency-agent/config |
| Fichiers exécutables de service | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Fichiers de stockage binaires | /var/opt/microsoft/dependency-agent/storage |

### <a name="enable-performance-counters"></a>Activer les compteurs de performance
Si l’espace de travail Log Analytics référencé par la solution n’est pas déjà configuré pour collecter les compteurs de performance requis par la solution, vous devez activer ces derniers. Vous pouvez le faire de deux manières :
* Manuellement, tel que décrit dans [Sources de données de performance Windows et Linux dans Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* En téléchargeant et en exécutant un script PowerShell disponible dans [Azure PowerShell Gallery](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

### <a name="deploy-azure-monitor-for-vms"></a>Déployer Azure Monitor pour machines virtuelles
Cette méthode inclut un modèle JSON spécifiant la configuration requise pour activer les composants de la solution dans votre espace de travail Log Analytics.

Si vous n’êtes pas familiarisé avec le déploiement de ressources à l’aide d’un modèle, consultez les rubriques suivantes :
* [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Déployer des ressources à l’aide de modèles Resource Manager et de l’interface de ligne de commande Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Si vous avez choisi d’utiliser Azure CLI, vous devez d’abord l’installer et l’utiliser localement. Vous devez exécuter Azure CLI 2.0.27 ou version ultérieure. Pour identifier votre version, exécutez `az --version`. Si vous devez installer ou mettre à niveau Azure CLI, consultez [Installer Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

#### <a name="create-and-execute-a-template"></a>Créer et exécuter un modèle

1. Copiez et collez la syntaxe JSON suivante dans votre fichier :

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "WorkspaceName": {
                "type": "string"
            },
            "WorkspaceLocation": {
                "type": "string"
            }
        },
        "resources": [
            {
                "apiVersion": "2017-03-15-preview",
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('WorkspaceName')]",
                "location": "[parameters('WorkspaceLocation')]",
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                            "promotionCode": ""
                        }
                    },
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },
                        "plan": {
                            "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'InfrastructureInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Enregistrez ce fichier sous *installsolutionsforvminsights.json* dans un dossier local.

1. Modifiez les valeurs pour *WorkspaceName*, *ResourceGroupName*, et *WorkspaceLocation*. La valeur de *WorkspaceName* correspond à l’ID de ressource complet de votre espace de travail Log Analytics, qui inclut le nom de l’espace de travail. La valeur de *WorkspaceLocation* correspond à la région dans laquelle l’espace de travail est défini.

1. Vous êtes prêt à déployer ce modèle à l’aide de la commande PowerShell suivante :

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    Le changement de configuration peut prendre quelques minutes. Lorsqu’il est terminé, un message semblable à celui qui suit s’affiche avec les résultats :

    ```powershell
    provisioningState       : Succeeded
    ```
Une fois que vous avez activé la supervision, 10 minutes peuvent s’écouler avant que vous ne puissiez voir les métriques et l’état d’intégrité pour l’ordinateur hybride.

## <a name="performance-counters-enabled"></a>Compteurs de performances activés
Azure Monitor pour machines virtuelles configure un espace de travail Log Analytics pour collecter des compteurs de performances utilisés par la solution. Le tableau suivant répertorie les objets et les compteurs configurés par la solution et qui sont collectés toutes les 60 secondes.

### <a name="windows-performance-counters"></a>Compteurs de performances Windows

|Nom d’objet |Nom du compteur |
|------------|-------------|
|LogicalDisk |% Free Space |
|LogicalDisk |Avg. Disk sec/Read |
|LogicalDisk |Avg. Disk sec/Transfer |
|LogicalDisk |Avg. Disk sec/Write |
|LogicalDisk |Disk Bytes/sec |
|LogicalDisk |Nb d’octets de lecture de disque/s  |
|LogicalDisk |Nb d’opérations de lectures de disque/s  |
|LogicalDisk |Disk Transfers/sec |
|LogicalDisk | Nb d’octets d’écriture de disque/s |
|LogicalDisk | Nb d’opération d’écriture de disque/s |
|LogicalDisk |Free Megabytes |
|Mémoire |Nombre d’octets disponibles |
|Carte réseau |Octets reçus/s |
|Carte réseau |Octets envoyés/s |
|Processeur |% temps processeur |

### <a name="linux-performance-counters"></a>Compteurs de performances Linux

|Nom d’objet |Nom du compteur |
|------------|-------------|
|Logical Disk |% Used Space |
|Logical Disk |Nb d’octets de lecture de disque/s  |
|Logical Disk |Nb d’opérations de lectures de disque/s  |
|Logical Disk |Disk Transfers/sec |
|Logical Disk | Nb d’octets d’écriture de disque/s |
|Logical Disk | Nb d’opération d’écriture de disque/s |
|Logical Disk |Free Megabytes |
|Logical Disk |Logical Disk Bytes/sec |
|Mémoire |Available MBytes Memory |
|Réseau |Total Bytes Received |
|Réseau |Total Bytes Transmitted |
|Processeur |% temps processeur |

## <a name="diagnostic-and-usage-data"></a>Données relatives aux diagnostics et à l’utilisation
Microsoft collecte automatiquement les données sur l’utilisation et les performances via votre utilisation du service Azure Monitor. Microsoft utilise ces données pour fournir et améliorer la qualité, la sécurité et l’intégrité du service. Afin d’offrir des fonctionnalités de dépannage précises et efficaces, les données de la fonctionnalité Map incluent des informations sur la configuration de votre logiciel, telles que le système d’exploitation et la version, l’adresse IP, le nom DNS et le nom de la station de travail. Microsoft ne collecte pas de nom, d’adresse ou d’autres coordonnées.

Pour plus d’informations sur l’utilisation et la collecte de données, voir la [Déclaration de confidentialité des Services en ligne de Microsoft](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]
## <a name="next-steps"></a>Étapes suivantes

Une fois la supervision activée pour votre machine virtuelle, ces informations peuvent être analysées par Azure Monitor pour machines virtuelles. Pour savoir comment utiliser la fonctionnalité de contrôle de l’intégrité, consultez [Comprendre l’intégrité de vos machines virtuelles Azure grâce à Azure Monitor pour machines virtuelles](vminsights-health.md). Pour afficher les dépendances des applications détectées, consultez [Utilisation de la fonctionnalité Map d’Azure Monitor pour machines virtuelles dans le but de comprendre les composants d’application](vminsights-maps.md).
