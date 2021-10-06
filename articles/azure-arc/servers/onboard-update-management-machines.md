---
title: Connecter des machines à partir d’Azure Automation Update Management
description: Dans cet article, vous découvrez comment connecter des machines hybrides à Azure Arc géré par Automation Update Management.
ms.date: 09/14/2021
ms.topic: conceptual
ms.openlocfilehash: fc5de9146ea07e490e69760397a661f4bbb99eec
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700546"
---
# <a name="connect-hybrid-machines-to-azure-from-automation-update-management"></a>Connecter des machines hybrides à Azure à partir d’Automation Update Management

Vous pouvez activer des serveurs avec Azure Arc pour une ou plusieurs de vos machines virtuelles Windows ou Linux, pour un ou plusieurs de vos serveurs physiques hébergés en local ou pour un autre environnement cloud qui sont gérés avec Azure Automation Update Management. Ce processus d’intégration automatise le téléchargement et l’installation de l’[agent Connected Machine](agent-overview.md). Pour connecter les machines à des serveur avec Azure Arc, un [principal de service](../../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory est utilisé à la place de votre identité privilégiée pour [connecter la machine de manière interactive](onboard-portal.md). Ce principal de service est créé automatiquement dans le cadre du processus d’intégration de ces ordinateurs.

Avant de commencer, veillez à consulter les [conditions préalables](agent-overview.md#prerequisites) et vérifiez que votre abonnement et vos ressources répondent aux exigences. Pour plus d'informations sur les régions prises en charge et d'autres considérations connexes, consultez [Régions Azure prises en charge](overview.md#supported-regions).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="how-it-works"></a>Fonctionnement

Une fois le processus d’intégration lancé, un [principal de service](../../active-directory/fundamentals/service-accounts-principal.md) Active Directory est créé dans le locataire. 

Pour installer et configurer l’agent Connected Machine sur la machine cible, un runbook maître nommé **Add-AzureConnectedMachines** s’exécute dans le bac à sable (sandbox) Azure. En fonction du système d’exploitation détecté sur la machine, le runbook maître appelle un runbook enfant nommé **Add-AzureConnectedMachineWindows** ou **Add-AzureConnectedMachineLinux** qui s’exécute sous le rôle système [Runbook Worker hybride](../../automation/automation-hybrid-runbook-worker.md) directement sur la machine. La sortie des tâches de runbook est écrite dans l’historique des tâches et vous pouvez voir la [synthèse de leur état](../../automation/automation-runbook-execution.md#job-statuses) ou examiner les détails d’une tâche de runbook spécifique dans le [portail Azure](../../automation/manage-runbooks.md#view-statuses-in-the-azure-portal) ou à l’aide d’[Azure PowerShell](../../automation/manage-runbooks.md#retrieve-job-statuses-using-powershell). L’exécution de runbooks dans Azure Automation consigne les détails dans un journal d’activité pour le compte Automation. Pour plus d’informations sur l’utilisation du journal, consultez [Récupérer les détails à partir du journal d’activité](../../automation/manage-runbooks.md#retrieve-details-from-activity-log).

La dernière étape établit la connexion à Azure Arc à l’aide de la commande `azcmagent` à l' {1} avec le principal de service pour inscrire la machine en tant que ressource dans Azure.

## <a name="prerequisites"></a>Prérequis

Pour que vous puissiez créer des tâches de runbook dans le compte Automation, cette méthode exige que vous soyez membre du rôle [Opérateur de travaux Automation](../../automation/automation-role-based-access-control.md#automation-job-operator) ou d’un rôle supérieur. 

Si vous avez activé Azure Policy pour [gérer l’exécution d’un runbook](../../automation/enforce-job-execution-hybrid-worker.md) et appliquer le ciblage de cette exécution sur un groupe Runbook Worker hybride, cette stratégie doit être désactivée. Dans le cas contraire, les tâches de runbook qui intègrent la ou les machines à des serveurs avec Azure Arc échouent. 

## <a name="add-machines-from-the-azure-portal"></a>Ajouter des machines à partir du portail Azure

Effectuez les étapes suivantes pour configurer la machine hybride avec des serveurs avec Azure Arc. Le serveur ou la machine doit être sous tension et en ligne pour que le processus se termine correctement.

1. À partir de votre navigateur, accédez au [portail Azure](https://portal.azure.com).

1. Accédez à la page **Serveurs - Azure Arc**, puis sélectionnez **Ajouter** en haut à gauche.

1. Dans la page **Sélectionner une méthode**, sélectionnez la vignette **Ajouter des serveurs gérés à partir d’Update Management (préversion)** , puis sélectionnez **Ajouter des serveurs**.

1. Dans la page **Informations de base**, configurez les options suivantes :

    1. Dans la liste déroulante **Groupe de ressources**, sélectionnez le groupe de ressources à partir duquel sera gérée la machine.
    1. Dans la liste déroulante **Région**, sélectionnez la région Azure dans laquelle seront stockées les métadonnées des serveurs.
    1. Si la machine passe par un serveur proxy pour se connecter à Internet, spécifiez l’adresse IP du serveur proxy ou le nom et le numéro de port que la machine doit utiliser pour communiquer avec le serveur proxy. Saisissez la valeur au format `http://<proxyURL>:<proxyport>`.
    1. Sélectionnez **Suivant : Machines**.

1. Dans la page **Machines**, sélectionnez l’**Abonnement** et le **Compte Automation** dans la liste déroulante pour lequel la fonctionnalité Update Management est activée et qui inclut les machines que vous voulez intégrer à des serveurs Azure Arc.

   Une fois le compte Automation spécifié, la liste ci-dessous retourne les machines non Azure gérées par Update Management pour ce compte Automation. Les machines Windows et Linux sont répertoriées. Pur chacune d’elles, sélectionnez **Ajouter**.

   Vous pouvez examiner votre sélection en sélectionnant **Vérifier la sélection**. Si vous voulez supprimer une machine, sélectionnez **Supprimer** sous la colonne **Action**. 

   Une fois votre sélection confirmée, sélectionnez **Suivant : Étiquettes**.

1. Dans la page **Étiquettes**, spécifiez une ou plusieurs paires **Nom**/**Valeur** pour prendre en charge vos critères. Sélectionnez **Vérifier + ajouter**.

1. Dans la page **Vérifier + ajouter**, passez en revue les informations récapitulatives, puis sélectionnez **Ajouter des machines**. Si vous avez encore besoin d’apporter des modifications, sélectionnez **Précédent**.

## <a name="verify-the-connection-with-azure-arc"></a>Vérifier la connexion avec Azure Arc

Une fois l’agent installé et configuré pour qu’il se connecte aux serveurs avec Azure Arc, accédez au portail Azure pour vérifier que le serveur s’est correctement connecté. Affichez vos machines dans le [portail Azure](https://aka.ms/hybridmachineportal).

![Connexion au serveur réussie](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la résolution des problèmes, consultez le [guide Résoudre les problèmes de l’agent Connected Machine](troubleshoot-agent-onboard.md).

- Consultez le [Guide de planification et de déploiement](plan-at-scale-deployment.md) pour planifier le déploiement de serveurs avec Azure Arc à n’importe quelle échelle et implémenter la gestion et la supervision centralisées.

- Apprenez à gérer votre machine à l’aide d’[Azure Policy](../../governance/policy/overview.md), par exemple pour la [configuration d’invité](../../governance/policy/concepts/guest-configuration.md) des machines virtuelles, à vérifier que la machine crée des rapports sur l’espace de travail Log Analytics prévu, à activer la surveillance avec [VM Insights](../../azure-monitor/vm/vminsights-enable-policy.md) et bien plus encore.