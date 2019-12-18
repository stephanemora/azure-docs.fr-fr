---
title: Solution Start/Stop VMs during off-hours
description: Cette solution de gestion de machines virtuelles assure le démarrage et l’arrêt de vos machines virtuelles Azure Resource Manager selon une planification, ainsi qu’une surveillance proactive à partir de journaux Azure Monitor.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 12/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c0b022ed759837fc6d922386dd48a2f3a109527a
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951494"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Solution de démarrage/arrêt des machines virtuelles durant les heures creuses dans Azure Automation

La solution Start/Stop VMs during off-hours démarre et arrête vos machines virtuelles Azure selon une planification définie par l’utilisateur. En outre, elle fournit des informations via Azure les Azure journaux Azure Monitor et peut envoyer des e-mails à l’aide de [groupes d’actions](../azure-monitor/platform/action-groups.md). Elle prend en charge Azure Resource Manager et les machines virtuelles classiques pour la plupart des scénarios. Pour utiliser cette solution avec des machines virtuelles Classic, vous avez besoin d’un compte d’identification Classic, lequel n’est pas créé par défaut. Pour savoir comment créer un compte d’identification Classic, consultez [Comptes d’identification Classic](automation-create-standalone-account.md#classic-run-as-accounts).

> [!NOTE]
> La solution Start/Stop VMs during off-hours a été testée avec les modules Azure importés dans votre compte Automation lors du déploiement de la solution. La solution ne fonctionne actuellement pas avec des versions plus récentes du module Azure. Cela affecte uniquement le compte Automation que vous utilisez pour exécuter la solution Start/Stop VMs during off-hours. Vous pouvez toujours utiliser des versions les plus récentes du module Azure dans vos autres comptes Automation, comme décrit dans le [Guide de mise à jour des modules Azure PowerShell dans Azure Automation](automation-update-azure-modules.md).

Cette solution offre une option décentralisée pour les utilisateurs souhaitant réduire les coûts de leurs machines virtuelles. Cette option permet d’effectuer les tâches suivantes :

- Planification du démarrage et de l’arrêt de machines virtuelles
- Planification du démarrage et de l’arrêt de machines virtuelles dans un ordre croissant à l’aide de balises Azure (non prises en charge pour les machines virtuelles classiques)
- Arrêt automatique de machines virtuelles en fonction de la faible utilisation de l’UC

Les limitations de la solution actuelle sont les suivantes :

- Cette solution permet de gérer les machines virtuelles de toutes les régions, mais seulement dans le même abonnement que le compte Azure Automation.
- Cette solution est disponible dans Azure et AzureGov pour toutes les régions qui prennent en charge un espace de travail Log Analytics, un compte Azure Automation et les alertes. À l’heure actuelle, les régions AzureGov ne gèrent pas les fonctionnalités de messagerie électronique.

> [!NOTE]
> Si vous utilisez la solution pour les machines virtuelles classiques, toutes vos machines virtuelles sont traitées séquentiellement par service cloud. Les machines virtuelles sont toujours traitées en parallèle à travers les différents services cloud. Si vous avez plus de 20 machines virtuelles par service cloud, nous vous recommandons de créer plusieurs planifications avec le runbook parent **ScheduledStartStop_Parent** et de spécifier 20 machines virtuelles par planification. Dans les propriétés de planification, spécifiez les noms des machines virtuelles dans le paramètre **VMList**, sous forme d’une liste séparée par des virgules. Sinon, si le travail d’automatisation pour cette solution s’exécute pendant plus de trois heures, il est momentanément déchargé ou arrêté par la limite de [répartition de charge équilibrée](automation-runbook-execution.md#fair-share).
>
> Les abonnements Azure Cloud Solution Provider (Azure CSP) prennent uniquement en charge le modèle Azure Resource Manager ; les services hors Azure Resource Manager ne sont pas disponibles dans le programme. Quand la solution Start/Stop s’exécute, vous risquez de recevoir des erreurs du fait qu’elle contient des cmdlets pour gérer des ressources classiques. Pour en savoir plus sur CSP, consultez [Services disponibles dans les abonnements CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services#comments). Si vous utilisez un abonnement CSP, vous devez définir la variable [**External_EnableClassicVMs**](#variables) sur **False** après le déploiement.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Prérequis

Les runbooks de cette solution fonctionnent avec un [compte d’identification Azure](automation-create-runas-account.md). Le compte d’identification est la méthode d’authentification recommandée, car elle utilise l’authentification par certificat au lieu d’un mot de passe, susceptible d’expirer ou de changer fréquemment.

Il vous est recommandé d’utiliser un compte Automation distinct pour la solution Start/Stop VM. En effet, les versions de module Azure sont fréquemment mises à niveau et leurs paramètres peuvent changer. La solution Start/Stop VM n’est pas mise à niveau à la même cadence, de sorte qu’elle risque de ne pas fonctionner avec des versions plus récentes des cmdlets qu’elle utilise. Il vous est recommandé de tester les mises à jour de module dans un compte Automation de test avant de les importer dans votre ou vos comptes Automation de production.

### <a name="permissions-needed-to-deploy"></a>Autorisations nécessaires pour déployer

Pour déployer la solution Start/Stop VMs during off-hours, un utilisateur doit disposer de certaines autorisations. Ces autorisations diffèrent selon que vous utilisez un compte Automation et un espace de travail Log Analytics, ou en créez de nouveaux lors du déploiement. Si vous êtes un contributeur pour l’abonnement et un administrateur général dans votre locataire Azure Active Directory, vous n’avez pas besoin de configurer les autorisations suivantes. Si vous ne disposez pas de ces droits ou devez configurer un rôle personnalisé, consultez les autorisations requises ci-dessous.

#### <a name="pre-existing-automation-account-and-log-analytics-workspace"></a>Compte Automation et espace de travail Log Analytics préexistants

Pour déployer la solution Start/Stop VMs during off-hours sur un compte Automation existant et un espace de travail Log Analytics, l’utilisateur qui déploie la solution à besoin des autorisations suivantes sur le **Groupe de ressources**. Pour en savoir plus sur les rôles, voir [Rôles personnalisés pour les ressources Azure](../role-based-access-control/custom-roles.md).

| Autorisation | Étendue|
| --- | --- |
| Microsoft.Automation/automationAccounts/read | Groupe de ressources |
| Microsoft.Automation/automationAccounts/variables/write | Groupe de ressources |
| Microsoft.Automation/automationAccounts/schedules/write | Groupe de ressources |
| Microsoft.Automation/automationAccounts/runbooks/write | Groupe de ressources |
| Microsoft.Automation/automationAccounts/connections/write | Groupe de ressources |
| Microsoft.Automation/automationAccounts/certificates/write | Groupe de ressources |
| Microsoft.Automation/automationAccounts/modules/write | Groupe de ressources |
| Microsoft.Automation/automationAccounts/modules/read | Groupe de ressources |
| Microsoft.automation/automationAccounts/jobSchedules/write | Groupe de ressources |
| Microsoft.Automation/automationAccounts/jobs/write | Groupe de ressources |
| Microsoft.Automation/automationAccounts/jobs/read | Groupe de ressources |
| Microsoft.OperationsManagement/solutions/write | Groupe de ressources |
| Microsoft.OperationalInsights/workspaces/* | Groupe de ressources |
| Microsoft.Insights/diagnosticSettings/write | Groupe de ressources |
| Microsoft.Insights/ActionGroups/Write | Groupe de ressources |
| Microsoft.Insights/ActionGroups/read | Groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Groupe de ressources |
| Microsoft.Resources/deployments/* | Groupe de ressources |

#### <a name="new-automation-account-and-a-new-log-analytics-workspace"></a>Nouveaux compte Automation et espace de travail Log Analytics

Pour déployer la solution Start/Stop VMs during off-hours sur un nouveau compte Automation et espace de travail Log Analytics, l’utilisateur qui déploie la solution doit disposer des autorisations définies dans la section précédente, ainsi que les autorisations suivantes :

- Coadministrateur pour l’abonnement – Cela n’est nécessaire que pour créer le compte d’identification Classic si vous comptez gérer des machines virtuelles Classic. Les [comptes d’identification Classic](automation-create-standalone-account.md#classic-run-as-accounts) ne sont plus créés par défaut.
- Un membre du rôle [Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md) **Développeur d’applications**. Pour plus d’informations sur la configuration de comptes d’identification, voir [Autorisations pour configurer des comptes d’identification](manage-runas-account.md#permissions).
- Contributeur sur l’abonnement ou les autorisations suivantes.

| Autorisation |Étendue|
| --- | --- |
| Microsoft.Authorization/Operations/read | Subscription|
| Microsoft.Authorization/permissions/read |Subscription|
| Microsoft.Authorization/roleAssignments/read | Subscription |
| Microsoft.Authorization/roleAssignments/write | Subscription |
| Microsoft.Authorization/roleAssignments/delete | Subscription |
| Microsoft.Automation/automationAccounts/connections/read | Groupe de ressources |
| Microsoft.Automation/automationAccounts/certificates/read | Groupe de ressources |
| Microsoft.Automation/automationAccounts/write | Groupe de ressources |
| Microsoft.OperationalInsights/workspaces/write | Groupe de ressources |

## <a name="deploy-the-solution"></a>Déployer la solution

Procédez comme suit pour ajouter la solution Start/Stop VMs during off-hours (préversion) à votre compte Automation, puis configurer les variables pour personnaliser la solution.

1. Dans un compte Automation, sélectionnez **Start/Stop VM** sous **Ressources associées**. Vous pouvez alors cliquer sur **En savoir plus sur la solution et l’activer**. Si vous avez déjà une solution Start/Stop VM déployée, sélectionnez-la en cliquant sur **Gérer la solution** et en la recherchant dans la liste.

   ![Activer à partir d’un compte Automation](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Vous pouvez également la créer n’importe où sur le Portail Azure en cliquant sur **Créer une ressource**. Dans la page Place de marché, saisissez un mot clé, tel que **Démarrer** ou **Arrêter/Démarrer**. Au fur et à mesure de la saisie, la liste est filtrée. Vous pouvez également saisir un ou plusieurs des mots clés à partir du nom complet de la solution, puis appuyer sur la touche Entrée. Sélectionnez **Start/Stop VMs during off-hours** dans les résultats de la recherche.

2. Sur la page **Start/Stop VMs during off-hours** de la solution sélectionnée, vérifiez les informations résumées, puis cliquez sur **Créer**.

   ![Portail Azure](media/automation-solution-vm-management/azure-portal-01.png)

3. La page **Ajouter une solution** s’affiche. Vous êtes invité à configurer la solution pour pouvoir l’importer dans votre abonnement Automation.

   ![Page Ajouter une solution de VM Management (Gestion de machines virtuelles)](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

4. Sur la page **Ajouter une solution**, sélectionnez **Espace de travail**. Sélectionnez un espace de travail Log Analytics lié au même abonnement Azure que celui dans lequel le compte Automation se trouve. Si vous ne disposez pas d’espace de travail, sélectionnez **Créer un espace de travail**. Sur la page **Espace de travail Log Analytics**, suivez les étapes suivantes :
   - Spécifiez un nom pour le nouvel **espace de travail Log Analytics**, comme « ContosoLAWorkspace ».
   - Dans la liste déroulante **Abonnement**, sélectionnez un abonnement à lier si la valeur par défaut sélectionnée n’est pas appropriée.
   - Sous **Groupe de ressources**, vous pouvez créer un groupe de ressources ou en sélectionner un qui existe déjà.
   - Sélectionnez un **emplacement**.
   - Sélectionner un **niveau de tarification**. Choisissez l’option **Par Go (autonome)** . Les journaux Azure Monitor ont mis à jour les [tarifs](https://azure.microsoft.com/pricing/details/log-analytics/), et le niveau Par Go est la seule option disponible.

   > [!NOTE]
   > Lors de l’activation de solutions, seules certaines régions sont prises en charge pour la liaison d’un espace de travail Log Analytics et d’un compte Automation.
   >
   > Pour obtenir la liste des paires de mappages prises en charge, consultez [Mappage de région pour un compte Automation et l’espace de travail Log Analytics](how-to/region-mappings.md).

5. Après avoir entré les informations requises sur la page **Espace de travail Log Analytics**, cliquez sur **Créer**. Vous pouvez suivre sa progression sous **Notifications** dans le menu, qui vous renvoie à la page **Ajouter une solution** une fois terminé.
6. Sur la page **Ajouter une solution**, sélectionnez **Compte Automation**. Si vous créez un espace de travail Log Analytics, vous pouvez créer un compte Automation associé ou en sélectionner un qui ne soit pas déjà lié à un espace de travail Log Analytics. Sélectionnez un compte Automation existant ou cliquez sur **Créer un compte Automation**, puis, sur la page **Ajouter un compte Automation**, indiquez les informations suivantes :
   - Dans le champ **Nom**, saisissez le nom du compte Automation.

     Toutes les autres options sont renseignées automatiquement en fonction de l’espace de travail Log Analytics sélectionné. et ne peuvent pas être modifiées. Un compte d’identification Azure est la méthode d’authentification par défaut pour les runbooks inclus dans cette solution. Après avoir cliqué sur **OK**, les options de configuration sont validées et le compte Automation est créé. Vous pouvez suivre la progression sous **Notifications** dans le menu.

7. Enfin, sur la page **Ajouter une solution**, sélectionnez **Configuration**. La page **Paramètres** s’affiche.

   ![Page Paramètres pour la solution](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   Ce volet vous permet de :
   - Spécifier les **noms des groupes de ressources cibles**. Ces valeurs sont les noms des groupes de ressources qui contiennent les machines virtuelles devant être gérées par cette solution. Vous pouvez entrer plusieurs noms en les séparant par des virgules (les valeurs ne respectent pas la casse). Si vous souhaitez cibler les machines virtuelles de tous les groupes de ressources de l’abonnement, l’utilisation d’un caractère générique est prise en charge. Cette valeur est stockée dans les variables **External_Start_ResourceGroupNames** et **External_Stop_ResourceGroupnames**.
   - Spécifier la **liste d’exclusion de machines virtuelles (chaîne)** . Cette valeur est le nom d’une ou de plusieurs machines virtuelles appartenant au groupe de ressources cible. Vous pouvez entrer plusieurs noms en les séparant par des virgules (les valeurs ne respectent pas la casse). Les caractères génériques sont pris en charge. Cette valeur est stockée dans la variable **External_ExcludeVMNames**.
   - Sélectionner une **planification**. Sélectionnez une date et une heure pour votre planification. Un calendrier quotidien récurrent sera créé, commençant à l’heure que vous avez sélectionnée. La sélection d’une autre région n’est pas possible. Pour configurer la planification sur votre propre fuseau horaire après la configuration de la solution, consultez [Modification de la planification de démarrage et d’arrêt](#modify-the-startup-and-shutdown-schedules).
   - Pour recevoir des **notifications par e-mail** de la part d’un groupe d’actions, acceptez la valeur par défaut **Oui**, puis fournissez une adresse e-mail valide. Si vous sélectionnez **Non**, mais décidez ultérieurement de recevoir les notifications par e-mail, vous pouvez mettre à jour le [groupe d’actions](../azure-monitor/platform/action-groups.md) qui est créé en y ajoutant des adresses e-mail valides, séparées par une virgule. Activez également les règles d’alerte suivantes :

     - AutoStop_VM_Child
     - Scheduled_StartStop_Parent
     - Sequenced_StartStop_Parent

     > [!IMPORTANT]
     > La valeur par défaut pour les **noms des groupes de ressources cibles** est un **&ast;** . Elle cible toutes les machines virtuelles dans un abonnement. Si vous ne souhaitez pas que la solution cible toutes les machines virtuelles dans votre abonnement, vous devez définir cette valeur sur une liste de noms de groupes de ressources avant d’activer les planifications.

8. Après avoir configuré les paramètres initiaux requis pour la solution, cliquez sur **OK** pour fermer la page **Paramètres** et sélectionnez **Créer**. Quand tous les paramètres sont validés, la solution est déployée dans votre abonnement. Ce processus peut prendre plusieurs secondes. Vous pouvez suivre la progression sous **Notifications** dans le menu.

> [!NOTE]
> Si vous avez un abonnement Azure Cloud Solution Provider (Azure CSP), une fois le déploiement terminé, dans votre compte Automation, accédez à **Variables** sous **Ressources partagées** et définissez la variable [**External_EnableClassicVMs**](#variables) sur **False**. La solution arrête de rechercher des ressources de machine virtuelle classiques.

## <a name="scenarios"></a>Scénarios

La solution inclut trois scénarios distincts. Ces scénarios sont les suivants :

### <a name="scenario-1-startstop-vms-on-a-schedule"></a>Scénario 1 : Démarrer/arrêter les machines virtuelles selon une planification

Ce scénario est la configuration par défaut quand vous déployez la solution pour la première fois. Par exemple, vous pouvez le configurer pour arrêter toutes les machines virtuelles d’un abonnement quand vous quittez le travail le soir, et pour les démarrer le matin quand vous arrivez au bureau. Quand vous configurez les planifications **Scheduled-StartVM** et **Scheduled-StopVM** pendant le déploiement, celles-ci démarrent et arrêtent les machines virtuelles cibles. Il est possible de configurer cette solution simplement pour arrêter les machines virtuelles, consultez la section [Modifier les planifications de démarrage et d’arrêt](#modify-the-startup-and-shutdown-schedules) pour savoir comment configurer une planification personnalisée.

> [!NOTE]
> Le fuseau horaire est votre fuseau horaire actuel quand vous configurez le paramètre du temps de planification. Toutefois, il est conservé au format UTC dans Azure Automation. Il est inutile de convertir un fuseau horaire car cela est géré pendant le déploiement.

Vous pouvez contrôler les machines virtuelles couvertes en configurant les variables suivantes : **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames** et **External_ExcludeVMNames**.

Vous pouvez activer cette solution pour prendre un charge un abonnement et un groupe de ressources, ou bien une liste spécifique de machines virtuelles, mais pas les deux.

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Configurer le démarrage et l’arrêt pour un abonnement et un groupe de ressources

1. Configurez les variables **External_Stop_ResourceGroupNames** et **External_ExcludeVMNames** pour spécifier les machines virtuelles cibles.
2. Activez et mettez à jour les planifications **Scheduled-StartVM** et **Scheduled-StopVM**.
3. Exécutez le runbook **ScheduledStartStop_Parent** avec le paramètre ACTION défini sur **Start** et le paramètre WHATIF défini sur **True** pour afficher un aperçu des modifications.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Configurer le démarrage et l’arrêt pour une liste de machines virtuelles

1. Exécutez le runbook **ScheduledStartStop_Parent** avec le paramètre ACTION défini sur **Start**, ajoutez une liste de machines virtuelles séparées par des virgules dans le paramètre *VMList* et définissez le paramètre WHATIF sur **True**. Affichez un aperçu de vos modifications.
1. Configurez le paramètre **External_ExcludeVMNames** avec une liste de noms de machines virtuelles séparés par une virgule (VM1, VM2, VM3).
1. Ce scénario ne tient pas compte des variables **External_Start_ResourceGroupNames** et **External_Stop_ResourceGroupnames**. Pour ce scénario, vous devez créer votre propre planification Automation. Pour plus d’informations, consultez [Planification d'un Runbook dans Azure Automation](../automation/automation-schedules.md).

> [!NOTE]
> La valeur de **Target ResourceGroup Names** est stockée comme étant également la valeur de **External_Start_ResourceGroupNames** et **External_Stop_ResourceGroupNames**. Pour plus de granularité, vous pouvez modifier chacune de ces variables pour cibler des groupes de ressources différents. Utilisez **External_Start_ResourceGroupNames** pour une action de démarrage, et **External_Stop_ResourceGroupNames** pour une action d’arrêt. Les machines virtuelles sont automatiquement ajoutées aux planifications de démarrage et d’arrêt.

### <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a>Scénario 2 : Démarrer/arrêter les machines virtuelles dans un certain ordre en utilisant des balises

Dans un environnement comprenant deux composants ou plus sur plusieurs machines virtuelles prenant en charge une charge de travail distribuée, il est important de prendre en charge l’ordre de démarrage et d’arrêt des composants. Pour ce scénario, effectuez les étapes suivantes :

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Configurer le démarrage et l’arrêt pour un abonnement et un groupe de ressources

1. Ajoutez une balise **sequencestart** et une balise **sequencestop** avec une valeur entière positive aux machines virtuelles cibles dans les variables **External_Start_ResourceGroupNames** et **External_Stop_ResourceGroupNames**. Les actions de démarrage et d’arrêt sont effectuées dans l’ordre croissant. Pour en savoir plus sur le balisage d’une machine virtuelle, consultez [Baliser une machine virtuelle Windows dans Azure](../virtual-machines/windows/tag.md) et [Baliser une machine virtuelle Linux dans Azure](../virtual-machines/linux/tag.md).
1. Modifiez les planifications **Sequenced-StartVM** et **StopVM-Sequenced** avec la date et l’heure répondant à vos besoins et activez la planification.
1. Exécutez le runbook **SequencedStartStop_Parent** avec le paramètre ACTION défini sur **Start** et le paramètre WHATIF défini sur **True** pour afficher un aperçu des modifications.
1. Affichez un aperçu de l’action et apportez les modifications nécessaires avant l’implémentation des machines virtuelles de production. Une fois prêt, vous pouvez exécuter manuellement le runbook avec le paramètre défini sur **False** ou laisser les planifications de Automation **Sequenced-StartVM** et **Sequenced-StopVM** s’exécuter automatiquement d’après votre planification établie.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Configurer le démarrage et l’arrêt pour une liste de machines virtuelles

1. Ajoutez des balises **sequencestart** et **sequencestop** contenant une valeur entière positive aux machines virtuelles que vous prévoyez d’ajouter au paramètre **VMList**.
1. Exécutez le runbook **SequencedStartStop_Parent** avec le paramètre ACTION défini sur **Start**, ajoutez une liste de machines virtuelles séparées par des virgules dans le paramètre *VMList* et définissez le paramètre WHATIF sur **True**. Affichez un aperçu de vos modifications.
1. Configurez le paramètre **External_ExcludeVMNames** avec une liste de noms de machines virtuelles séparés par une virgule (VM1, VM2, VM3).
1. Ce scénario ne tient pas compte des variables **External_Start_ResourceGroupNames** et **External_Stop_ResourceGroupnames**. Pour ce scénario, vous devez créer votre propre planification Automation. Pour plus d’informations, consultez [Planification d'un Runbook dans Azure Automation](../automation/automation-schedules.md).
1. Affichez un aperçu de l’action et apportez les modifications nécessaires avant l’implémentation des machines virtuelles de production. Une fois prêt, vous pouvez exécuter manuellement le Runbook Monitoring-and-diagnostics/Monitoring-action-groups, avec le paramètre défini sur **False**, ou laisser les planifications Automation **Sequenced-StartVM** et **Sequenced-StopVM** s’exécuter automatiquement, selon la planification établie.

### <a name="scenario-3-startstop-automatically-based-on-cpu-utilization"></a>Scénario 3 : Démarrer/arrêter automatiquement les machines virtuelles en fonction de l’utilisation de l’UC

Cette solution peut aider à gérer le coût d’exécution des machines virtuelles de votre abonnement en évaluant les machines virtuelles Azure non utilisées pendant les heures creuses, par exemple après les heures de travail, et en les arrêtant automatiquement si l’utilisation de l’UC est inférieure à X %.

Par défaut, la solution est préconfigurée de manière à évaluer les mesures du pourcentage d’utilisation de l’UC pour vérifier si l’utilisation moyenne est de 5 % ou moins. Ce scénario est contrôlé par les variables suivantes et peut être modifié si leurs valeurs par défaut ne répondent pas à vos besoins :

- External_AutoStop_MetricName
- External_AutoStop_Threshold
- External_AutoStop_TimeAggregationOperator
- External_AutoStop_TimeWindow

Vous pouvez activer cette solution pour prendre un charge un abonnement et un groupe de ressources, ou bien une liste spécifique de machines virtuelles, mais pas les deux.

#### <a name="target-the-stop-action-against-a-subscription-and-resource-group"></a>Configurer l’arrêt pour un abonnement et un groupe de ressources

1. Configurez les variables **External_Stop_ResourceGroupNames** et **External_ExcludeVMNames** pour spécifier les machines virtuelles cibles.
1. Activez et mettez à jour la planification **Schedule_AutoStop_CreateAlert_Parent**.
1. Exécutez le runbook **AutoStop_CreateAlert_Parent** avec le paramètre ACTION défini sur **Start** et le paramètre WHATIF défini sur **True** pour afficher un aperçu des modifications.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Configurer le démarrage et l’arrêt pour une liste de machines virtuelles

1. Exécutez le runbook **AutoStop_CreateAlert_Parent** avec le paramètre ACTION défini sur **Start**, ajoutez une liste de machines virtuelles séparées par des virgules dans le paramètre *VMList* et définissez le paramètre WHATIF sur **True**. Affichez un aperçu de vos modifications.
1. Configurez le paramètre **External_ExcludeVMNames** avec une liste de noms de machines virtuelles séparés par une virgule (VM1, VM2, VM3).
1. Ce scénario ne tient pas compte des variables **External_Start_ResourceGroupNames** et **External_Stop_ResourceGroupnames**. Pour ce scénario, vous devez créer votre propre planification Automation. Pour plus d’informations, consultez [Planification d'un Runbook dans Azure Automation](../automation/automation-schedules.md).

Maintenant que vous avez une planification pour l’arrêt des machines virtuelles en fonction de l’utilisation de l’UC, vous devez activer l’une des planifications suivantes pour les démarrer.

- Configurez le démarrage pour un abonnement et un groupe de ressources. Consultez les étapes du [Scénario 1](#scenario-1-startstop-vms-on-a-schedule) pour tester et activer les planifications **Scheduled-StartVM**.
- Configurez le démarrage pour un abonnement, un groupe de ressources et des balises. Consultez les étapes du [Scénario 2](#scenario-2-startstop-vms-in-sequence-by-using-tags) pour tester et activer les planifications **Sequenced-StartVM**.

## <a name="solution-components"></a>Composants de la solution

Cette solution inclut des runbooks et des planifications préconfigurés et une intégration avec les journaux Azure Monitor qui vous permet de personnaliser le démarrage et l’arrêt de vos machines virtuelles selon les besoins de votre entreprise.

### <a name="runbooks"></a>Runbooks

Le tableau suivant répertorie les runbooks déployés sur votre compte Automation par cette solution. Ne modifiez pas le code de runbook. À la place, écrivez votre propre runbook pour une nouvelle fonctionnalité.

> [!IMPORTANT]
> N’exécutez pas directement de runbook dont le nom contient le suffixe « child » (enfant).

Tous les runbooks parents incluent le paramètre _WhatIf_. Une fois configuré sur **True**, _WhatIf_ prend en charge la description du comportement exact du runbook lors de l’exécution sans le paramètre _WhatIf_ et valide les machines virtuelles correctes ciblées. Un runbook effectue uniquement ses actions définies quand le paramètre _WhatIf_ est défini sur **False**.

|Runbook | parameters | Description|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Appelé par le runbook parent. Ce runbook crée des alertes en fonction des ressources pour le scénario d’AutoStop.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf : True ou False  | Crée ou met à jour des règles d’alerte Azure sur des machines virtuelles dans l’abonnement ou les groupes de ressource ciblées. <br> VMList : liste des machines virtuelles séparée par des virgules. Par exemple, _vm1, vm2, vm3_.<br> *WhatIf* valide la logique du runbook sans l’exécuter.|
|AutoStop_Disable | Aucun | Désactive les alertes et la planification par défaut d’AutoStop.|
|AutoStop_StopVM_Child | WebHookData | Appelé par le runbook parent. Les règles d’alerte appellent ce runbook pour arrêter la machine virtuelle.|
|Bootstrap_Main | Aucun | Utilisé une seule fois pour établir les configurations Bootstrap, telles que webhookURI, qui ne sont généralement pas accessibles à partir d’Azure Resource Manager. Ce runbook est automatiquement supprimé après un déploiement réussi.|
|ScheduledStartStop_Child | VMName <br> Action : Start ou Stop <br> ResourceGroupName | Appelé par le runbook parent. Exécute une action de démarrage ou d’arrêt pour l’arrêt planifié.|
|ScheduledStartStop_Parent | Action : Start ou Stop <br>VMList <br> WhatIf : True ou False | Ce paramètre s’applique à toutes les machines virtuelles de l’abonnement. Modifiez les variables **External_Start_ResourceGroupNames** et **External_Stop_ResourceGroupNames** pour exécuter le runbook uniquement sur les groupes de ressources ciblés. Vous pouvez également exclure des machines virtuelles spécifiques en mettant à jour la variable **External_ExcludeVMNames**.<br> VMList : liste des machines virtuelles séparée par des virgules. Par exemple, _vm1, vm2, vm3_.<br> _WhatIf_ valide la logique du runbook sans l’exécuter.|
|SequencedStartStop_Parent | Action : Start ou Stop <br> WhatIf : True ou False<br>VMList| Créez des balises nommées **sequencestart** et **sequencestop** sur chaque machine virtuelle pour laquelle vous souhaitez séquencer l’activité de démarrage et d’arrêt. Ces noms de balises respectent la casse. La valeur de la balise doit être un entier positif (1, 2, 3) correspondant à l’ordre du démarrage ou d’arrêt. <br> VMList : liste des machines virtuelles séparée par des virgules. Par exemple, _vm1, vm2, vm3_. <br> _WhatIf_ valide la logique du runbook sans l’exécuter. <br> **Remarque**: les machines virtuelles doivent se trouver dans des groupes de ressources définis External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames et External_ExcludeVMNames dans des variables d’Azure Automation. Elles doivent disposer des étiquettes appropriées pour que les actions puissent prendre effet.|

### <a name="variables"></a>variables

Le tableau suivant répertorie les variables créées dans votre compte Automation. Modifiez uniquement les variables ayant le préfixe **External**. La modification de variables avec le préfixe **Internal** risque d’avoir un impact indésirable.

|Variable | Description|
|---------|------------|
|External_AutoStop_Condition | Opérateur conditionnel requis pour configurer la condition avant le déclenchement d’une alerte. Les valeurs acceptables sont **GreaterThan** (supérieur à), **GreaterThanOrEqual** (supérieur ou égal à), **LessThan** (Inférieur à) et **LessThanOrEqual** (Inférieur ou égal à).|
|External_AutoStop_Description | Alerte pour arrêter la machine virtuelle si le pourcentage d’utilisation de l’UC dépasse le seuil.|
|External_AutoStop_MetricName | Nom de la métrique de performances pour laquelle la règle d’alerte Azure doit être configurée.|
|External_AutoStop_Threshold | Seuil de la règle d’alerte Azure spécifiée dans la variable _External_AutoStop_MetricName_. Les valeurs de pourcentage vont de 1 à 100.|
|External_AutoStop_TimeAggregationOperator | Opérateur d’agrégation de temps appliqué à la taille de la fenêtre sélectionnée pour évaluer la condition. Les valeurs admises sont **Average** (moyen), **Minimum**, **Maximum**, **Total** et **Last** (dernier).|
|External_AutoStop_TimeWindow | Taille de la fenêtre durant laquelle Azure analyse la métrique sélectionnée pour déclencher une alerte. Ce paramètre accepte une entrée au format d’un intervalle de temps. Les valeurs possibles sont comprises entre 5 minutes et 6 heures.|
|External_EnableClassicVMs| Spécifie si les machines virtuelles classiques sont ciblées par la solution. La valeur par défaut est True. Il doit être défini sur False pour les abonnements CSP. Un [compte d’identification Classic](automation-create-standalone-account.md#classic-run-as-accounts) est nécessaire pour les machines virtuelles Classic.|
|External_ExcludeVMNames | Saisissez les noms des machines virtuelles à exclure, en séparant les noms par une virgule et sans espace. Le nombre de machines virtuelles est limité à 140. Si vous ajoutez plus de 140 machines virtuelles à cette liste séparée par des virgules, les machines virtuelles définies comme exclues peuvent être démarrées ou arrêtées par inadvertance.|
|External_Start_ResourceGroupNames | Spécifie un ou plusieurs groupes de ressources (avec les valeurs séparées par une virgule) ciblés pour les actions de démarrage.|
|External_Stop_ResourceGroupNames | Spécifie un ou plusieurs groupes de ressources (avec les valeurs séparées par une virgule) ciblés pour les actions d’arrêt.|
|Internal_AutomationAccountName | Spécifie le nom du compte Automation.|
|Internal_AutoSnooze_WebhookUri | Spécifie l’URI du Webhook appelée pour le scénario AutoStop.|
|Internal_AzureSubscriptionId | Spécifie l’ID d’abonnement Azure.|
|Internal_ResourceGroupName | Spécifie le nom du groupe de ressources du compte Automation.|

Pour tous les scénarios, les variables **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames** et **External_ExcludeVMNames** sont nécessaires pour le ciblage de machines virtuelles, à l’exception de la fourniture d’une liste de machines virtuelles séparées par des virgules pour les runbooks **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent** et **ScheduledStartStop_Parent**. Autrement dit, vos machines virtuelles doivent résider dans des groupes de ressources cibles pour que les actions de démarrage et d’arrêt se produisent. La logique fonctionne de manière similaire à la stratégie d’Azure, dans la mesure où vous pouvez cibler l’abonnement ou un groupe de ressources et faire en sorte que les machines virtuelles nouvellement créées héritent des actions. Cette approche évite de devoir mettre à jour une planification distincte pour chaque machine virtuelle et de gérer le démarrage et l’arrêt à l’échelle.

### <a name="schedules"></a>Planifications

Le tableau suivant répertorie chacune des planifications par défaut créées dans votre compte Automation. Vous pouvez les modifier ou créer vos propres planifications personnalisées. Par défaut, toutes les planifications sont désactivées à l’exception de **Scheduled_StartVM** et **Scheduled_StopVM**.

Vous ne devez pas activer toutes les planifications, car vous risqueriez de créer des actions de planification qui se chevauchent. Il est préférable de déterminer les optimisations que vous souhaitez réaliser et d’effectuer les modifications en conséquence. Consultez les exemples de scénarios dans la section Vue d’ensemble pour obtenir davantage d’explications.

|Nom de la planification | Fréquence | Description|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | Toutes les 8 heures | Exécute le runbook AutoStop_CreateAlert_Parent toutes les 8 heures, qui arrête les valeurs basées sur les machines virtuelles dans External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames et External_ExcludeVMNames dans les variables d’Azure Automation. Vous pouvez également spécifier une liste de machines virtuelles séparées par des virgules en utilisant le paramètre VMList.|
|Scheduled_StopVM | Définie par l’utilisateur, tous les jours | Exécute le runbook Scheduled_Parent avec un paramètre _Stop_ tous les jours à l’heure spécifiée. Arrête automatiquement toutes les machines virtuelles répondant aux règles définies par les variables de ressource. Activez la planification associée, **Scheduled-StartVM**.|
|Scheduled_StartVM | Définie par l’utilisateur, tous les jours | Exécute le runbook Scheduled_Parent avec un paramètre _Start_ tous les jours à l’heure spécifiée. Démarre automatiquement toutes les machines virtuelles répondant aux règles définies par les variables appropriées. Activez la planification associée, **Scheduled-StopVM**.|
|Sequenced-StopVM | 01:00 (UTC), tous les vendredis | Exécute le runbook Sequenced_Parent avec un paramètre _Stop_ tous les vendredis à l’heure spécifiée. Arrête séquentiellement (dans l’ordre croissant) toutes les machines virtuelles avec la balise **SequenceStop** définie par les variables appropriées. Pour plus d’informations sur les valeurs de balises et les variables de ressources, consultez la section Runbooks. Activez la planification associée, **Sequenced-StartVM**.|
|Sequenced-StartVM | 13:00 (UTC), tous les lundis | Exécute le runbook Sequenced_Parent avec un paramètre _Start_ tous les lundis à un instant donné. Démarre séquentiellement (dans l’ordre décroissant) toutes les machines virtuelles avec la balise **SequenceStart** définie par les variables appropriées. Pour plus d’informations sur les valeurs de balises et les variables de ressources, consultez la section Runbooks. Activez la planification associée, **Sequenced-StopVM**.|

## <a name="azure-monitor-logs-records"></a>Enregistrements de journaux Azure Monitor

Automation crée deux types d’enregistrements dans l’espace de travail Log Analytics : les journaux d’activité de tâches et les flux de tâches.

### <a name="job-logs"></a>Journaux d’activité de tâches

|Propriété | Description|
|----------|----------|
|Appelant |  Ce qui a initié l’opération. Les valeurs possibles sont une adresse de messagerie ou un système pour les travaux planifiés.|
|Category | Classification du type de données. Pour Automation, la valeur est JobLogs.|
|CorrelationId | GUID représentant l’ID de corrélation du travail du runbook.|
|JobId | GUID représentant l’ID du travail du runbook.|
|operationName | Spécifie le type d’opération exécutée dans Azure. Pour Automation, la valeur est Job.|
|resourceId | Spécifie le type de ressource dans Azure. Pour Automation, la valeur est le compte Automation associé au runbook.|
|ResourceGroup | Spécifie le nom du groupe de ressources de la tâche du runbook.|
|ResourceProvider | Spécifie le service qui fournit les ressources que vous pouvez déployer et gérer. Pour Automation, la valeur est Azure Automation.|
|ResourceType | Spécifie le type de ressource dans Azure. Pour Automation, la valeur est le compte Automation associé au runbook.|
|resultType | L’état du travail du runbook. Les valeurs possibles sont les suivantes :<br>Démarré<br>Arrêté<br>Interrompu<br>Échec<br>- Succeeded|
|resultDescription | Décrit l’état résultant du travail du runbook. Les valeurs possibles sont les suivantes :<br>- Job is started<br>- Job Failed<br>- Job Completed|
|RunbookName | Spécifie le nom du runbook.|
|SourceSystem | Spécifie le système source pour les données soumises. Pour Automation, la valeur est OpsManager.|
|StreamType | Spécifie le type d’événement. Les valeurs possibles sont les suivantes :<br>- Verbose<br>Sortie<br>Error<br>Avertissement|
|SubscriptionId | Spécifie l’ID d’abonnement de la tâche.
|Temps | Date et heure d’exécution du travail du runbook.|

### <a name="job-streams"></a>Flux de tâches

|Propriété | Description|
|----------|----------|
|Appelant |  Ce qui a initié l’opération. Les valeurs possibles sont une adresse de messagerie ou un système pour les travaux planifiés.|
|Category | Classification du type de données. Pour Automation, la valeur est JobStreams.|
|JobId | GUID représentant l’ID du travail du runbook.|
|operationName | Spécifie le type d’opération exécutée dans Azure. Pour Automation, la valeur est Job.|
|ResourceGroup | Spécifie le nom du groupe de ressources de la tâche du runbook.|
|resourceId | Spécifie l’ID de ressource dans Azure. Pour Automation, la valeur est le compte Automation associé au runbook.|
|ResourceProvider | Spécifie le service qui fournit les ressources que vous pouvez déployer et gérer. Pour Automation, la valeur est Azure Automation.|
|ResourceType | Spécifie le type de ressource dans Azure. Pour Automation, la valeur est le compte Automation associé au runbook.|
|resultType | Résultat de la tâche du runbook au moment où l’événement a été généré. Une valeur possible est :<br>- InProgress|
|resultDescription | Inclut le flux de sortie du runbook.|
|RunbookName | Nom du runbook.|
|SourceSystem | Spécifie le système source pour les données soumises. Pour Automation, la valeur est OpsManager.|
|StreamType | Type de flux de travail. Les valeurs possibles sont les suivantes :<br>- Progress<br>Sortie<br>Avertissement<br>error<br>DEBUG<br>- Verbose|
|Temps | Date et heure d’exécution du travail du runbook.|

Quand vous effectuez une recherche de journal qui retourne des enregistrements de catégorie de **JobLogs** ou **JobStreams**, vous pouvez sélectionner la vue **JobLogs** ou **JobStreams** pour afficher un ensemble de vignettes résumant les informations retournées par la recherche.

## <a name="sample-log-searches"></a>Exemples de recherches dans les journaux

Le tableau suivant fournit des exemples de recherches de journaux pour les enregistrements de tâches collectés par cette solution.

|Requête | Description|
|----------|----------|
|Rechercher les tâches du runbook ScheduledStartStop_Parent terminées avec succès | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Rechercher les tâches du runbook SequencedStartStop_Parent terminées avec succès | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|

## <a name="viewing-the-solution"></a>Affichage de la solution

Pour accéder à la solution, rendez-vous dans votre compte Automation et sous **RESSOURCES CONNEXES**, sélectionnez **Espace de travail**. Sur la page Log Analytics, sélectionnez **Solutions** dans la section **GÉNÉRAL**. Sur la page **Solutions**, sélectionnez la solution **Start-Stop-VM[espace de travail]** dans la liste.

La sélection de la solution affiche la page Solution de **Start-Stop-VM[espace de travail]** . Vous pouvez y consulter des informations importantes, telles que la vignette **StartStopVM**. Tout comme dans votre espace de travail Log Analytics, cette vignette affiche un compteur et une représentation graphique des tâches de runbooks démarrées et terminées avec succès.

![Page de solution de gestion des mises à jour de Automation](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

À ce stade, vous pouvez analyser plus en détail les enregistrements de tâche en cliquant sur la vignette en forme d’anneau. Le tableau de bord des solutions affiche l’historique des travaux et les requêtes de recherche dans les journaux prédéfinies. Passez au portail avancé Log Analytics pour effectuer des recherches en fonction de vos requêtes de recherche.

## <a name="configure-email-notifications"></a>Configurer les notifications par e-mail

Pour modifier les notifications par e-mail une fois la solution déployée, modifiez le groupe d’actions qui a été créé au cours du déploiement.  

> [!NOTE]
> Les abonnements dans le cloud Azure Government ne prennent pas en charge la fonctionnalité e-mail de cette solution.

Dans le portail Azure, accédez à Surveiller -> Groupes d’actions. Sélectionnez le groupe d’actions intitulé **StartStop_VM_Notication**.

![Page de solution de gestion des mises à jour de Automation](media/automation-solution-vm-management/azure-monitor.png)

Dans la page **StartStop_VM_Notification**, cliquez sur **Modifier les détails** sous **Détails**. La page **E-mail/SMS/Push/Voix** s’ouvre. Mettez à jour l’adresse e-mail, puis cliquez sur **OK** pour enregistrer vos modifications.

![Page de solution de gestion des mises à jour de Automation](media/automation-solution-vm-management/change-email.png)

Vous pouvez également ajouter des actions supplémentaires au groupe d’actions. Pour plus d’informations sur les groupes d’actions, consultez [Groupes d’actions](../azure-monitor/platform/action-groups.md).

Voici un exemple d’e-mail envoyé lorsque la solution arrête les machines virtuelles.

![Page de solution de gestion des mises à jour de Automation](media/automation-solution-vm-management/email.png)

## <a name="add-exclude-vms"></a>Ajouter/exclure des machines virtuelles

La solution offre la possibilité d’ajouter des machines virtuelles que la solution doit cible, ou d’exclure spécifiquement des machines de la solution.

### <a name="add-a-vm"></a>Ajouter une machine virtuelle

Il existe quelques options que vous pouvez utiliser pour vous assurer qu’une machine virtuelle est incluse dans la solution Start/Stop lorsqu’elle s’exécute.

* Chaque [runbook](#runbooks) parent de la solution a un paramètre **VMList**. Vous pouvez passer d’une liste séparée par des virgules des noms de machines virtuelles à ce paramètre lors de la planification du runbook parent approprié pour votre situation. Ces machines virtuelles seront alors incluses lors de l’exécution de la solution.

* Pour sélectionner plusieurs machines virtuelles, définissez les variables **External_Start_ResourceGroupNames** et **External_Stop_ResourceGroupNames** en utilisant les noms de groupe de ressources contenant les machines virtuelles que vous souhaitez démarrer ou arrêter. Vous pouvez également définir cette valeur sur `*` pour que la solution s’exécute sur tous les groupes de ressources dans l’abonnement.

### <a name="exclude-a-vm"></a>Exclure une machine virtuelle

Pour exclure une machine virtuelle de la solution, vous pouvez l’ajouter à la variable **External_ExcludeVMNames**. Cette variable est une liste séparée par des virgules de machines virtuelles spécifiques à exclure de la solution Start/Stop. Le nombre de machines virtuelles est limité à 140. Si vous ajoutez plus de 140 machines virtuelles à cette liste séparée par des virgules, les machines virtuelles définies comme exclues peuvent être démarrées ou arrêtées par inadvertance.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Modifier les planifications de démarrage et d’arrêt

La gestion de la planification du démarrage et de l’arrêt dans cette solution suit la procédure indiquée dans [Planification d’un Runbook dans Azure Automation](automation-schedules.md). Il doit être une planification distincte pour le démarrage et l’arrêt des machines virtuelles.

Il est possible de configurer la solution pour simplement arrêter les machines virtuelles à une certaine heure. Dans ce scénario vous créez simplement une planification d’**arrêt**, sans planification de **démarrage** correspondante. Pour cela, vous devez procéder comme suit :

1. Assurez-vous d’avoir ajouté les groupes de ressources des machines virtuelles à arrêter dans la variable **External_Sop_ResourceGroupNames**.
2. Créez votre propre planification pour l’heure à laquelle vous souhaitez arrêter les machines virtuelles.
3. Accédez au runbook **ScheduledStartStop_Parent** et cliquez sur **Planification**. Cela vous permet de sélectionner la planification créée à l’étape précédente.
4. Sélectionnez **Paramètres et valeurs pour l’exécution** et définissez le paramètre ACTION sur « Stop ».
5. Cliquez sur **OK** pour enregistrer vos modifications.

## <a name="update-the-solution"></a>Mettre à jour la solution

Si vous avez déployé une version précédente de cette solution, vous devez tout d’abord la supprimer de votre compte avant de déployer une version mise à jour. Suivez les étapes de [suppression de la solution](#remove-the-solution), puis effectuez les étapes ci-dessus pour [déployer la solution](#deploy-the-solution).

## <a name="remove-the-solution"></a>Supprimer la solution

Si vous estimez que vous n’avez plus besoin d’utiliser la solution, vous pouvez la supprimer à partir du compte Automation. La suppression de la solution supprime uniquement les runbooks. Elle ne supprime pas les planifications ni les variables créées quand la solution a été ajoutée. Vous devez supprimer manuellement ces ressources si vous ne les utilisez pas avec d’autres runbooks.

Pour supprimer la solution, procédez comme suit :

1. Dans votre compte Automation, sous **Ressources associées**, sélectionnez **Espace de travail lié**.
1. Sélectionnez **Accéder à l’espace de travail**.
1. Sous **Général**, sélectionnez **Solutions**. 
1. Sur la page **Solutions**, sélectionnez la solution **Start-Stop-VM [Espace de travail]** . Sur la page **VMManagementSolution[Espace de travail]** , sélectionnez l’option **Supprimer** dans le menu.<br><br> ![Supprimer la solution de gestion de machine virtuelle](media/automation-solution-vm-management/vm-management-solution-delete.png)
1. Dans la fenêtre **Supprimer la solution**, confirmez que vous souhaitez supprimer la solution.
1. Pendant que les informations sont vérifiées et la solution supprimée, vous pouvez suivre la progression sous **Notifications** dans le menu. Vous serez redirigé vers la page **Solutions** après le démarrage du processus de suppression de la solution.

Le compte Automation et l’espace de travail Log Analytics ne sont pas supprimés au cours de ce processus. Si vous ne souhaitez pas conserver l’espace de travail Log Analytics, vous devez le supprimer manuellement. Cette opération peut se faire à partir du portail Azure :

1. Dans le Portail Azure, recherchez et sélectionnez **Espaces de travail Log Analytics**.
1. Sur la page **Espaces de travail Log Analytics**, sélectionnez l’espace de travail.
1. Sur la page des paramètres de l’espace de travail, sélectionnez **Supprimer** dans le menu.

Si vous ne souhaitez pas conserver les composants du compte Azure Automation, vous pouvez les supprimer manuellement. Pour connaître la liste des runbooks, des variables et des planifications créés par la solution, voir [Composants de la solution](#solution-components).

## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment construire différentes requêtes de recherche et consulter les journaux d’activité de travaux Automation avec les journaux Azure Monitor, consultez la page [Recherches dans les journaux d’activité dans les journaux Azure Monitor](../log-analytics/log-analytics-log-searches.md).
- Pour plus d’informations sur l’exécution d’un runbook, la manière de surveiller des tâches de runbook et autres détails techniques, voir [Suivi d’une tâche de runbook](automation-runbook-execution.md).
- Pour plus d’informations sur les journaux Azure Monitor et sur les sources de collecte de données, consultez la page [Collecter des données de stockage Azure dans les journaux Azure Monitor – Vue d’ensemble](../azure-monitor/platform/collect-azure-metrics-logs.md).
