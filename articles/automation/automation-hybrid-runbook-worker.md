---
title: Runbooks Workers hybrides Azure Automation
description: Cet article fournit des informations sur l'installation et l'utilisation de la fonctionnalité Runbook Worker hybride d'Azure Automation qui vous permet d'exécuter des Runbooks sur les machines de votre centre de données local ou de votre fournisseur de cloud.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 0af8806cdc55b89a9ab87a8059808e4fcc9a1730
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34159184"
---
# <a name="automate-resources-in-your-data-center-or-cloud-with-hybrid-runbook-worker"></a>Automatiser des ressources dans votre centre de données ou votre cloud à l’aide d’un Runbook Worker hybride

Dans Azure Automation, les runbooks peuvent ne pas être en mesure d’accéder aux ressources d’autres clouds ou de votre environnement local car ils s’exécutent dans le cloud Azure. La fonctionnalité de Runbook Worker hybride d’Azure Automation vous permet d’exécuter des Runbooks directement sur l’ordinateur qui héberge le rôle et par rapport aux ressources de l’environnement afin de gérer ces ressources locales. Les Runbooks sont stockés et gérés dans Azure Automation, puis remis à un ou plusieurs ordinateurs désignés.

Cette fonctionnalité est illustrée dans l’image suivante :

![Vue d'ensemble des Runbooks Workers hybrides](media/automation-hybrid-runbook-worker/automation.png)

## <a name="hybrid-runbook-worker-groups"></a>Groupes de Runbooks Workers hybrides

Chaque Runbook Worker hybride est membre d'un groupe de Runbooks Workers hybrides que vous spécifiez lorsque vous installez l'agent. Un groupe peut inclure un seul agent, mais vous pouvez installer plusieurs agents dans un groupe pour une haute disponibilité.

Lorsque vous démarrez un Runbook sur un Runbook Worker hybride, vous spécifiez le groupe sur lequel il s’exécute. Chaque worker du groupe interroge Azure Automation pour voir si des travaux sont disponibles. Si un travail est disponible, alors le premier worker qui le reçoit s’en occupe. Vous ne pouvez pas spécifier un worker en particulier.

## <a name="installing-a-hybrid-runbook-worker"></a>Installation de la fonctionnalité Runbook Worker hybride

Le processus d’installation de la fonctionnalité Runbook Worker hybride diffère selon le système d’exploitation. Le tableau suivant contient des liens vers les différentes méthodes que vous pouvez utiliser pour installer une fonctionnalité Runbook Worker hybride. Il existe deux méthodes pour installer et configurer un Runbook Worker hybride Windows. La méthode recommandée utilise un Runbook Automation afin d’automatiser totalement le processus requis pour configurer un ordinateur Windows. La deuxième méthode consiste à suivre la procédure pas à pas pour installer et configurer le rôle manuellement. Pour une machine Linux, vous exécutez un script Python pour installer l’agent sur la machine.

|Système d’exploitation  |Types de déploiement  |
|---------|---------|
|Windows     | [PowerShell](automation-windows-hrw-install.md#automated-deployment)<br>[Manuel](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#installing-linux-hybrid-runbook-worker)        |

> [!NOTE]
> Pour gérer la configuration de vos serveurs prenant en charge le rôle Runbook Worker hybride avec configuration de l’état souhaité (DSC), vous devez les ajouter en tant que nœuds DSC. Pour plus d’informations sur leur intégration pour les gérer avec DSC, consultez la page [Gestion de machines avec Azure Automation DSC](automation-dsc-onboarding.md).
>
>Si vous activez la [solution Update Management](automation-update-management.md), tout ordinateur connecté à votre espace de travail Log Analytics est automatiquement configuré en tant que Runbook Worker hybride pour prendre en charge les runbooks inclus dans cette solution. Toutefois, il n’est enregistré auprès d’aucun des groupes de Workers hybrides déjà définis dans votre compte Automation. L’ordinateur peut être ajouté à un groupe de Runbooks Workers hybrides dans votre compte Automation pour prendre en charge des runbooks Automation à condition d’utiliser le même compte à la fois pour la solution et pour l’appartenance au groupe de Runbooks Workers hybrides. Cette fonctionnalité a été ajoutée à la version 7.2.12024.0 du groupe de Runbooks Workers hybrides.

Examinez les [informations de planification de votre réseau](#network-planning) avant de commencer à déployer un Runbook Worker hybride. Une fois que vous avez déployé un runbook worker avec succès, consultez [exécuter des runbooks sur un Runbook Worker hybride](automation-hrw-run-runbooks.md) pour apprendre comment configurer vos runbooks afin d’automatiser les processus dans votre centre de données local ou un autre environnement cloud.

## <a name="removing-hybrid-runbook-worker"></a>Suppression de la fonctionnalité Runbook Worker hybride

Vous pouvez supprimer un ou plusieurs Runbook Workers hybrides d’un groupe ou supprimer le groupe, selon vos besoins. Pour supprimer un Runbook Worker hybride à partir d’un ordinateur local, procédez comme suit :

1. Sur le Portail Azure, accédez à votre compte Automation.
2. À partir du panneau **Paramètres**, sélectionnez **Clés** et notez les valeurs des champs **URL** et **Clé d’accès primaire**. Vous aurez besoin de ces informations dans l’étape suivante.

### <a name="windows"></a>Windows

Ouvrez une session PowerShell en mode administrateur et exécutez la commande suivante. Utilisez le commutateur **-Verbose** pour afficher un journal détaillé du processus de suppression.

```powershell
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>
```

Pour supprimer des ordinateurs obsolètes à partir de votre groupe Worker hybride, utilisez le paramètre facultatif `machineName`.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

### <a name="linux"></a>Linux

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Cela ne supprime pas Microsoft Monitoring Agent de l’ordinateur, mais seulement les fonctionnalités et la configuration du rôle Runbook Worker hybride.

## <a name="remove-hybrid-worker-groups"></a>Suppression de groupes de Workers hybrides

Pour supprimer un groupe, vous devez tout d’abord supprimer les Runbook Workers hybrides de chaque ordinateur membre du groupe à l’aide de la procédure indiquée plus haut, puis effectuer les opérations suivantes pour supprimer le groupe.

1. Dans le portail Azure, ouvrez le compte Automation.
1. Sous **Automatisation de processus**, sélectionnez **Groupes de Workers hybrides**. Sélectionnez le groupe que vous souhaitez supprimer. Une fois que vous avez sélectionné le groupe, la page de propriétés du **groupe de Workers hybrides** s’affiche.

   ![Page du groupe Runbook Worker hybride](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

1. Dans la page de propriétés du groupe sélectionné, cliquez sur **Supprimer**. Un message apparaît, vous invitant à confirmer cette action : sélectionnez **Oui** si vous êtes sûr de vouloir continuer.

   ![Boîte de dialogue de confirmation de suppression du groupe](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Ce processus peut prendre plusieurs secondes. Vous pouvez suivre la progression sous **Notifications** dans le menu.

## <a name="network-planning"></a>Configurer votre réseau

### <a name="hybrid-worker-role"></a>Worker hybride

Pour que le Runbook Worker hybride se connecte à Log Analytics et y soit inscrit, il doit avoir accès au numéro de port et aux URL décrits dans cette section. Il s’agit d’un ajout aux [ports et URL requis pour que Microsoft Monitoring Agent](../log-analytics/log-analytics-agent-windows.md) pour se connecter à Log Analytics.

Si vous utilisez un serveur proxy pour la communication entre l’agent et le service Log Analytics, assurez-vous que les ressources appropriées sont accessibles. Si vous utilisez un pare-feu pour restreindre l’accès à Internet, vous devez configurer votre pare-feu pour autoriser l’accès.

Les port et URL suivants sont requis pour que le rôle Runbook Worker hybride communique avec Automation :

* Port : seul TCP 443 est nécessaire pour l’accès Internet sortant.
* URL globale : *.azure-automation.net
* URL globale d’US Gov Virginie : *.azure-automation.us
* Service de l’agent : https://\<ID_espace_de_travail\>.agentsvc.azure-automation.net

Si vous avez un compte Automation défini pour une région spécifique, vous pouvez limiter les communications à ce centre de données régional. Le tableau suivant indique l’enregistrement DNS pour chaque région.

| **Région** | **Enregistrement DNS** |
| --- | --- |
| Centre-Ouest des États-Unis | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| États-Unis - partie centrale méridionale |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Est des États-Unis 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Centre du Canada |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Europe de l'Ouest |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Europe du Nord |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Asie du Sud-Est |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Inde centrale |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Est du Japon |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Sud-Est de l’Australie |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Sud du Royaume-Uni | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| Gouvernement américain - Virginie | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

Pour obtenir la liste des adresses IP régionales plutôt que celle des noms des régions, téléchargez le fichier XML [Azure Datacenter IP address](https://www.microsoft.com/download/details.aspx?id=41653) à partir du Centre de téléchargement Microsoft.

> [!NOTE]
> Le fichier XML Azure Datacenter IP address répertorie les plages d’adresses IP qui sont utilisées dans les centres de données Microsoft Azure. Les plages Compute, SQL et de stockage sont incluses dans le fichier.
>
>Un fichier mis à jour est publié chaque semaine. Le fichier reflète les plages déployées et toutes les modifications à venir dans les plages IP. Les nouvelles plages figurant dans le fichier ne sont pas utilisées dans les centres de données avant une semaine minimum.
>
> Pensez à télécharger le nouveau fichier XML chaque semaine. Ensuite, mettez à jour votre site afin qu’il identifie correctement les services en cours d’exécution dans Azure. Les utilisateurs d’Azure ExpressRoute devraient remarquer que ce fichier est utilisé pour mettre à jour la publication BGP (Border Gateway Protocol) de l’espace Azure la première semaine de chaque mois.

### <a name="update-management"></a>Update Management

En plus des adresses et ports standard exigés par le Runbook Worker hybride, les adresses suivantes sont obligatoires pour Update Management. La communication avec ces adresses s’effectue par le biais du port 443.

|Azure (public)  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|

## <a name="troubleshooting"></a>Résolution de problèmes

Le Runbook Worker hybride dépend d’un agent pour communiquer avec votre compte Automation et ainsi enregistrer le worker, recevoir des travaux de runbook et signaler l’état. Pour Windows, cet agent est Microsoft Monitoring Agent. Pour Linux, il s’agit d’Agent OMS pour Linux. Si l’inscription du worker échoue, voici les causes possibles de l’erreur :

### <a name="the-hybrid-worker-is-behind-a-proxy-or-firewall"></a>Le worker hybride est derrière un pare-feu ou un proxy

Vérifiez que l’ordinateur dispose d’un accès sortant à *.azure-automation.net sur le port 443.

### <a name="the-computer-the-hybrid-worker-is-running-on-has-less-than-the-minimum-hardware-requirements"></a>L’ordinateur sur lequel le worker hybride s’exécute ne respecte pas la configuration matérielle minimale requise

Les ordinateurs qui exécutent les workers hybrides doivent respecter la configuration matérielle minimale requise avant de pouvoir héberger cette fonctionnalité. Sinon, en fonction de l’utilisation des ressources d’autres processus d’arrière-plan et de la contention due aux runbooks lors de l’exécution, l’ordinateur est surchargé, entraînant des retards ou des délais d’attente pour la tâche du runbook.

Vérifiez que l’ordinateur désigné pour exécuter la fonctionnalité Runbook Worker hybride répond à la configuration matérielle minimale requise. Si c’est le cas, surveillez l’utilisation du processeur et de la mémoire pour déterminer toute corrélation entre les performances des processus Runbook Worker hybride et de Windows. S’il existe une surcharge de la mémoire ou du processeur, cela peut indiquer la nécessité de mettre à niveau ou d’ajouter des processeurs supplémentaires, ou d’augmenter la mémoire pour résoudre le goulot d’étranglement des ressources et résoudre l’erreur. Vous pouvez également sélectionner une ressource de calcul différente qui peut prendre en charge la configuration minimale requise et évoluer lorsque les demandes en matière de charge de travail indiquent qu’une augmentation est nécessaire.

Pour plus d’informations sur la résolution des problèmes pour un système d’exploitation spécifique, consultez [Runbook Worker hybride Linux](automation-linux-hrw-install.md#troubleshooting) ou [Runbook Worker hybride Windows](automation-windows-hrw-install.md#troubleshooting).

## <a name="next-steps"></a>Étapes suivantes

Consultez [exécuter des runbooks sur un Runbook Worker hybride](automation-hrw-run-runbooks.md) pour apprendre comment configurer vos runbooks afin d’automatiser les processus dans votre centre de données local ou un autre environnement cloud.
