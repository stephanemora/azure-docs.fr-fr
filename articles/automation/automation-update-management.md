---
title: Solution Update Management dans Azure
description: Cet article décrit comment utiliser la solution Azure Update Management pour gérer les mises à jour de vos ordinateurs Windows et Linux.
services: automation
ms.subservice: update-management
ms.date: 01/21/2020
ms.topic: conceptual
ms.openlocfilehash: e7a86f3a709566bcf18241ce3c329c0355be2743
ms.sourcegitcommit: 163be411e7cd9c79da3a3b38ac3e0af48d551182
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2020
ms.locfileid: "77539550"
---
# <a name="update-management-solution-in-azure"></a>Solution Update Management dans Azure

Vous pouvez utiliser la solution Update Management dans Azure Automation pour gérer les mises à jour du système d’exploitation de vos ordinateurs Windows et Linux dans Azure, des environnements locaux et d’autres fournisseurs cloud. Vous pouvez rapidement évaluer l’état des mises à jour disponibles sur tous les ordinateurs d’agent et gérer le processus d’installation des mises à jour requises pour les serveurs.

Vous pouvez activer Update Management pour les machines virtuelles directement depuis votre compte Azure Automation. Pour savoir comment, consultez [Gérer les mises à jour pour plusieurs machines virtuelles Azure](manage-update-multi.md). Vous pouvez également activer Update Management pour une machine virtuelle à partir de sa page dans le portail Azure. Ce scénario est disponible pour les machines virtuelles [Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) et [Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management).

> [!NOTE]
> La solution Update Management vous demande d’établir un lien entre l’espace de travail Log Analytics et votre compte Automation. Pour obtenir la liste définitive des régions prises en charge, consultez [Mappages Azure Workspace](./how-to/region-mappings.md). Les mappages de région n’empêchent pas de gérer les machines virtuelles dans une autre région depuis votre compte Automation.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="solution-overview"></a>Vue d’ensemble de la solution

Les ordinateurs gérés par Update Management utilisent les configurations suivantes pour effectuer l’évaluation et mettre à jour les déploiements :

* Microsoft Monitoring Agent (MMA) pour Windows ou Linux
* PowerShell DSC (Desired State Configuration, configuration d’état souhaité) pour Linux
* Runbook Worker hybride Automation
* Services Microsoft Update ou Windows Server Update (WSUS) pour ordinateurs Windows

Le schéma suivant illustre comment la solution évalue les mises à jour de sécurité et les applique à tous les ordinateurs Windows Server et Linux connectés dans un espace de travail :

![Flux du processus Update Management](./media/automation-update-management/update-mgmt-updateworkflow.png)

Update Management peut être utilisé pour intégrer des machines en mode natif dans plusieurs abonnements du même locataire.

Après la publication d’un package, comptez un délai de deux à trois heures avant l’affichage du correctif pour l’évaluation sur des machines Linux. Sur les machines Windows, ce délai est de 12 à 15 heures.

Après qu’une machine a terminé l’analyse de conformité de la mise à jour, l’agent transfère les informations en bloc aux journaux Azure Monitor. Sur une machine Windows, l’analyse de conformité est effectuée toutes les 12 heures par défaut.

En plus de l’analyse planifiée, l’analyse de conformité de mise à jour est lancée dans les 15 minutes si MMA est redémarré, avant et après l’installation de la mise à jour.

Sur un ordinateur Linux, l’analyse de conformité est effectuée toutes les heures par défaut. Si l’agent MMA est redémarré, une analyse de conformité est lancée dans les 15 minutes.

La solution rapporte l’état de mise à jour de l’ordinateur en fonction de la source avec laquelle vous avez configuré la synchronisation. Si l’ordinateur Windows est configuré pour rapporter à WSUS, en fonction de la date de dernière synchronisation de WSUS avec Microsoft Update, les résultats peuvent être différents de ce que Microsoft Update indique. Le comportement est le même pour les machines Linux qui sont configurées pour rapporter à un référentiel local et non pas à un référentiel public.

> [!NOTE]
> Pour pouvoir rapporter au service, Update Management nécessite certaines URL et l’activation de ports. Pour en savoir plus sur la configuration requise, consultez la section [Planification du réseau pour les Workers hybrides](automation-hybrid-runbook-worker.md#network-planning).

Vous pouvez déployer et installer des mises à jour logicielles sur des ordinateurs qui nécessitent les mises à jour en créant un déploiement planifié. Les mises à jour considérées comme *facultatives* ne sont pas incluses dans le déploiement des ordinateurs Windows. Seules les mises à jour nécessaires sont incluses dans le déploiement.

Le déploiement planifié définit quels ordinateurs cibles reçoivent les mises à jour applicables. Soit il désigne explicitement les ordinateurs certains ordinateurs, soit il sélectionne un [groupe d’ordinateurs](../azure-monitor/platform/computer-groups.md) d’après des recherches dans les journaux d’un ensemble spécifique d’ordinateurs (ou sur une [requête Azure](automation-update-management-query-logs.md) qui sélectionne des machines virtuelles Azure de manière dynamique selon des critères spécifiés). Ces groupes sont différents de la [configuration de l’étendue](../azure-monitor/insights/solution-targeting.md), qui est utilisée uniquement pour déterminer quelles machines reçoivent les packs d’administration qui activent la solution.

Vous spécifiez également une planification pour approuver et définir la période pendant laquelle les mises à jour peuvent être installées. Cette période est appelée fenêtre de maintenance. Vingt minutes de la fenêtre de maintenance sont réservées aux redémarrages si un redémarrage est nécessaire et que vous avez sélectionné l’option de redémarrage appropriée. Si la mise à jour corrective prend plus longtemps que prévu et qu’il reste moins de vingt minutes dans la fenêtre de maintenance, il n’y aura pas de redémarrage.

Les mises à jour sont installées par des Runbooks dans Azure Automation. Vous ne pouvez pas visualiser ces runbooks, qui ne nécessitent aucune configuration. Lorsqu’un déploiement de mises à jour est créé, il génère une planification qui démarre un runbook de mise à jour principal au moment indiqué pour les ordinateurs inclus. Ce runbook principal lance un runbook enfant sur chaque agent pour installer les mises à jour obligatoires.

À la date et l’heure spécifiées dans le déploiement de mises à jour, les ordinateurs cibles exécutent le déploiement en parallèle. Avant l’installation, une analyse est lancée pour vérifier que les mises à jour sont encore requises. Pour les ordinateurs clients WSUS, si les mises à jour ne sont pas approuvées dans WSUS, leur déploiement échoue.

L’inscription d’une machine auprès du service Update Management dans plusieurs espaces de travail Log Analytics (multihébergement) n’est pas prise en charge.

## <a name="clients"></a>Clients

### <a name="supported-client-types"></a>Types de clients pris en charge

Le tableau suivant répertorie les systèmes d’exploitation pris en charge pour les évaluations des mises à jour. Une mise à jour corrective nécessite un Runbook Worker hybride. Pour plus d’informations sur les exigences de Runbook Worker hybride, voir les guides d’installation de [Runbook Worker hybride Windows](automation-windows-hrw-install.md) et de [Runbook Worker hybride Linux](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker).

|Système d’exploitation  |Notes  |
|---------|---------|
|Windows Server 2019 (Datacenter/Datacenter Core/Standard)<br><br>Windows Server 2016 (Datacenter/Datacenter Core/Standard)<br><br>Windows Server 2012 R2 (Datacenter/Standard)<br><br>Windows Server 2012 || 
|Windows Server 2008 R2 (RTM et SP1 Standard)| Update Management prend uniquement en charge l’exécution d’évaluations pour ce système d’exploitation. Sa mise à jour corrective n’est pas prise en charge, car le [Runbook Worker hybride](automation-windows-hrw-install.md) n’est pas pris en charge pour Windows Server 2008 R2. |
|CentOS 6 (x86/x64) et 7 (x64)      | Les agents Linux doivent avoir accès à un référentiel de mise à jour. La mise à jour corrective basée sur la classification nécessite que `yum` retourne les données de sécurité que CentOS n’a pas dans ses versions RTM. Pour plus d’informations sur la mise à jour corrective basée sur des classifications sur CentOS, consultez [Mettre à jour des classifications sur Linux](automation-view-update-assessments.md#linux-2).          |
|Red Hat Enterprise 6 (x86/x64) et 7 (x64)     | Les agents Linux doivent avoir accès à un référentiel de mise à jour.        |
|SUSE Linux Enterprise Server 11 (x86/x64) et 12 (x64)     | Les agents Linux doivent avoir accès à un référentiel de mise à jour.        |
|Ubuntu 14.04 LTS, 16.04 LTS et 18.04 (x86/x64)      |Les agents Linux doivent avoir accès à un référentiel de mise à jour.         |

> [!NOTE]
> Les groupes de machines virtuelles identiques Azure peuvent être gérés via Update Management. Update Management fonctionne sur les instances, mais pas sur l’image de base. Vous devez planifier les mises à jour de façon incrémentielle, afin que toutes les instances de machines virtuelles ne soient pas mises à jour en même temps.
> Vous pouvez ajouter des nœuds pour les groupes de machines virtuelles identiques en suivant la procédure décrite sous [Intégrer un ordinateur non Azure](automation-tutorial-installed-software.md#onboard-a-non-azure-machine).

### <a name="unsupported-client-types"></a>Types de clients non pris en charge

Le tableau suivant répertorie les systèmes d’exploitation qui ne sont pas pris en charge :

|Système d’exploitation  |Notes  |
|---------|---------|
|Client Windows     | Les systèmes d’exploitation client (par exemple, Windows 7 et Windows 10) ne sont pas pris en charge.        |
|Windows Server 2016 Nano Server     | Non pris en charge.       |
|Nœuds Azure Kubernetes Service (AKS) | Non pris en charge. Utilisez le processus de correction décrit dans [Appliquer des mises à jour de sécurité et du noyau à des nœuds Linux dans Azure Kubernetes Service (AKS)](../aks/node-updates-kured.md)|

### <a name="client-requirements"></a>Configuration requise des clients

Les informations suivantes décrivent la configuration requise du client spécifique au système d’exploitation. Pour obtenir des conseils supplémentaires, consultez [Planification réseau](#ports).

#### <a name="windows"></a>Windows

Les agents Windows doivent être configurés pour communiquer avec un serveur WSUS, ou avoir accès à Microsoft Update.

Vous pouvez utiliser Azure Update Management avec Configuration Manager. Pour en savoir plus sur les scénarios d’intégration, voir [Intégrer Configuration Manager avec Update Management](oms-solution-updatemgmt-sccmintegration.md#configuration). L’[agent Windows](../azure-monitor/platform/agent-windows.md) est obligatoire. Cet agent est automatiquement installé si vous intégrez une machine virtuelle Azure.

Par défaut, les machines virtuelles Windows déployées à partir de la place de marché Azure sont configurées pour recevoir des mises à jour automatiques de Windows Update Service. Ce comportement ne change pas lorsque vous ajoutez cette solution ou des machines virtuelles Windows à votre espace de travail. Si vous n’avez pas géré activement les mises à jour avec cette solution, le comportement par défaut (appliquer automatiquement les mises à jour) s’applique.

> [!NOTE]
> Un utilisateur peut modifier la stratégie de groupe afin que les redémarrages de la machine ne puissent être effectués que par lui et non par le système. Les machines managées peuvent rester bloquées si Update Management ne dispose pas des droits nécessaires pour les redémarrer sans intervention manuelle de l'utilisateur.
>
> Pour plus d'informations, consultez [Configurer les paramètres de stratégie de groupe pour les mises à jour automatiques](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates).

#### <a name="linux"></a>Linux

Pour Linux, la machine doit avoir accès à un référentiel de mises à jour, qui peut être privé ou public. TLS 1.1 ou TLS 1.2 est exigé pour interagir avec Update Management. La configuration de Log Analytics Agent pour Linux afin d’envoyer des rapports à plusieurs espaces de travail Log Analytics n’est pas prise en charge avec cette solution. Python 2.x doit également être installé sur la machine.

Pour plus d’informations sur la manière d’installer Log Analytics Agent pour Linux et télécharger la dernière version, consultez [Log Analytics Agent pour Linux](https://github.com/microsoft/oms-agent-for-linux). Pour plus d’informations sur la manière d’installer Log Analytics Agent pour Windows, consultez [Connecter des ordinateurs Windows à Azure Monitor](../log-analytics/log-analytics-windows-agent.md).

Les machines virtuelles créées à partir des images Red Hat Enterprise Linux (RHEL) à la demande disponibles dans le service Place de marché Azure sont inscrites pour accéder à l’infrastructure [RHUI (Red Hat Update Infrastructure)](../virtual-machines/workloads/redhat/redhat-rhui.md) déployée dans Azure. Toute autre distribution Linux doit être mise à jour à partir du référentiel de fichiers de distribution en ligne en utilisant les méthodes de distribution prises en charge.

## <a name="permissions"></a>Autorisations

Pour créer et gérer des déploiements de mises à jour, vous devez disposer d’autorisations spécifiques. Pour en savoir plus sur ces autorisations, consultez [Accès en fonction du rôle – Update Management](automation-role-based-access-control.md#update-management).

## <a name="solution-components"></a>Composants de la solution

La solution se compose des éléments suivants. Les ressources sont ajoutées à votre compte Automation. Il s’agit soit d’agents connectés directement ou se trouvant dans un groupe d'administration connecté à Operations Manager.

### <a name="hybrid-worker-groups"></a>Groupes de Workers hybrides

Après avoir activé cette solution, tout ordinateur Windows directement connecté à votre espace de travail Log Analytics est automatiquement configuré comme un Runbook Worker hybride afin de prendre en charge les Runbooks inclus dans cette solution.

Chaque ordinateur Windows géré par la solution est répertorié dans le panneau **Groupes de Workers hybrides** en tant que **Groupe de Workers hybrides système** du compte Automation. Les solutions utilisent la convention d’affectation de noms *Hostname FQDN_GUID*. Vous ne pouvez pas cibler ces groupes avec des Runbooks dans votre compte. Si vous essayez, la tentative échoue. Ces groupes sont destinés à prendre en charge uniquement la solution de gestion.

Vous pouvez ajouter les ordinateurs Windows à un groupe de Runbooks Workers hybrides dans votre compte Automation pour prendre en charge des runbooks Automation à condition d’utiliser le même compte pour la solution et pour l’appartenance au groupe de Runbooks Workers hybrides. Cette fonctionnalité a été ajoutée à la version 7.2.12024.0 du Runbook Worker hybride.

### <a name="management-packs"></a>Packs d’administration

Si votre groupe d’administration System Center Operations Manager est connecté à un espace de travail Log Analytics, les packs d’administration suivants sont installés dans Operations Manager. Ces packs d’administration sont également installés sur des ordinateurs Windows directement connectés après l’ajout de la solution. Il n’est pas nécessaire de les configurer ou de les gérer.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Pack d’administration du déploiement des mises à jour

> [!NOTE]
> Supposons que vous disposez d’un groupe d’administration Operations Manager 1807 ou 2019 avec des agents configurés au niveau du groupe d’administration pour les associer à un espace de travail. Pour qu’ils s’affichent, la solution de contournement actuelle consiste à remplacer **IsAutoRegistrationEnabled** par **True** dans la règle **Microsoft.IntelligencePacks.AzureAutomation.HybridAgent.Init**.

Pour plus d’informations sur la façon dont ces packs d’administration de solution sont mis à jour, consultez [Connecter Operations Manager aux journaux Azure Monitor](../azure-monitor/platform/om-agents.md).

> [!NOTE]
> Pour les systèmes avec l’agent Operations Manager : Pour qu’un agent soit entièrement géré par Update Management, il doit être mis à jour vers MMA. Pour savoir comment mettre à jour l’agent, consultez [Guide pratique pour mettre à niveau un agent Operations Manager](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents). Dans les environnements qui utilisent Operations Manager, vous devez exécuter System Center Operations Manager 2012 R2 UR 14 ou une version ultérieure.

## <a name="data-collection"></a>Collecte de données

### <a name="supported-agents"></a>Agents pris en charge

Le tableau suivant décrit les sources connectées prises en charge par cette solution :

| Source connectée | Prise en charge | Description |
| --- | --- | --- |
| Agents Windows |Oui |La solution collecte des informations sur les mises à jour système auprès des agents Windows et lance l’installation des mises à jour obligatoires. |
| Agents Linux |Oui |La solution collecte des informations sur les mises à jour système auprès des agents Linux et lance l’installation des mises à jour obligatoires sur les versions prises en charge. |
| Groupe d’administration d’Operations Manager |Oui |La solution collecte des informations sur les mises à jour système des agents dans un groupe d’administration connecté.<br/><br/>Une connexion directe entre l’agent Operations Manager et les journaux Azure Monitor n’est pas obligatoire. Les données sont transférées du groupe d’administration à l’espace de travail Log Analytics. |

### <a name="collection-frequency"></a>Fréquence de collecte

Une analyse est effectuée deux fois par jour sur chaque ordinateur Windows géré. Les API Windows sont appelées toutes les 15 minutes pour rechercher l’heure de la dernière mise à jour afin de déterminer si l’état a changé. Si l’état a changé, une analyse de conformité est lancée.

Une analyse est effectuée toutes les heures sur chaque ordinateur Linux géré.

L’affichage sur le tableau de bord des données mises à jour provenant des ordinateurs gérés peut prendre entre 30 minutes et 6 heures.

La consommation moyenne de données des journaux Azure Monitor pour une machine utilisant la solution Update Management est d’environ 25 mégaoctets (Mo) par mois. Cette valeur est approximative et sujette à modification en fonction de votre environnement. Nous vous recommandons de surveiller votre environnement pour assurer le suivi de votre consommation exacte.

## <a name="ports"></a>Planification réseau

Les adresses suivantes sont exigées particulièrement pour Update Management. La communication avec ces adresses s’effectue par le biais du port 443.

|Azure (public)  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|\* .azure-automation.net|*.azure-automation.us|

Pour les machines Windows, vous devez également autoriser le trafic vers tous les points de terminaison requis par Windows Update. Vous trouverez une liste actualisée des points de terminaison requis dans [Problèmes liés à HTTP/au proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy). Si vous disposez d’un [serveur Windows Update](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment) local, vous devez également autoriser le trafic vers le serveur spécifié dans votre [clé WSUS](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry).

Pour les machines Red Hat Linux, consultez [Adresses IP des serveurs de distribution de contenu RHUI](../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers) pour les points de terminaison requis. Pour les autres distributions Linux, reportez-vous à la documentation du fournisseur.

Pour plus d’informations sur les ports exigés par le Runbook Worker hybride, consultez [Ports du rôle de Worker hybride](automation-hybrid-runbook-worker.md#hybrid-worker-role).

Il est recommandé d’utiliser les adresses répertoriées lors de la définition d’exceptions. Pour les adresses IP, vous pouvez télécharger [Plages d’adresses IP du centre de données Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Ce fichier, qui est mis à jour chaque semaine et reflète les plages actuellement déployées ainsi que tous les changements à venir des plages d’adresses IP.

Suivez les instructions dans [Connecter des ordinateurs sans accès Internet](../azure-monitor/platform/gateway.md) pour configurer des ordinateurs qui n’ont pas accès à Internet.

## <a name="view-update-assessments"></a>Voir les évaluations des mises à jour

Dans votre compte Automation, cliquez sur **Update Management** pour afficher l’état de vos ordinateurs.

Cet affichage fournit des informations sur vos ordinateurs, les mises à jour manquantes, les déploiements de mises à jour et les déploiements de mises à jour planifiés. Dans la colonne **CONFORMITÉ**, vous pouvez voir quand l’ordinateur a été évalué pour la dernière fois. Dans la colonne **METTRE À JOUR LA DISPONIBILITÉ DE L’AGENT**, vous pouvez vérifier l’intégrité de l’agent de mise à jour. En cas de problème, sélectionnez le lien pour accéder à la documentation de résolution des problèmes, qui peut vous aider à remédier au problème.

Pour exécuter une recherche dans les journaux qui permet de retourner des informations sur l’ordinateur, la mise à jour ou le déploiement, sélectionnez l’élément correspondant dans la liste. Le volet **Recherche dans les journaux** s’ouvre avec une requête sur l’élément sélectionné :

![Vue par défaut de Update Management](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>Afficher les mises à jour manquantes

Sélectionnez **Mises à jour manquantes** pour afficher la liste des mises à jour qui manquent à vos ordinateurs. Chaque mise à jour est répertoriée et peut être sélectionnée. Les informations sur le nombre d’ordinateurs qui nécessitent la mise à jour, le système d’exploitation et le lien vers plus d’informations s’affichent. Le volet **Recherche dans les journaux** affiche plus d’informations sur les mises à jour.

![Mises à jour manquantes](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>Classifications des mises à jour

Les tableaux suivants répertorient les classifications des mises à jour dans Update Management, avec une définition de chaque classification.

### <a name="windows"></a>Windows

|classification ;  |Description  |
|---------|---------|
|Mises à jour critiques     | Mise à jour d’un problème spécifique qui concerne un bogue critique non lié à la sécurité.        |
|Mises à jour de sécurité     | Mise à jour pour un problème de sécurité propre à un produit.        |
|Correctifs cumulatifs     | Cumul de correctifs logiciels regroupés pour faciliter leur déploiement.        |
|Packs de fonctionnalités     | Nouvelles fonctionnalités de produit distribuées en dehors d’une version de produit.        |
|Service Packs     | Cumul de correctifs logiciels appliqués à une application.        |
|Mises à jour de définitions     | Mise à jour de fichiers de virus ou d’autres définitions.        |
|Outils     | Utilitaire ou fonctionnalité permettant d’effectuer une ou plusieurs tâches.        |
|Mises à jour     | Mise à jour d’une application ou d’un fichier actuellement installé.        |

### <a name="linux-2"></a>Linux

|classification ;  |Description  |
|---------|---------|
|Mises à jour critiques et de sécurité     | Mises à jour pour un problème spécifique ou un problème de sécurité propre à un produit.         |
|Autres mises à jour     | Toutes les autres mises à jour qui ne sont ni critiques par nature, ni des mises à jour de sécurité.        |

Pour Linux, Update Management peut faire la différence entre les mises à jour critiques et les mises à jour de sécurité dans le cloud, tout en affichant les données d’évaluation en raison de l’enrichissement des données dans le cloud. Pour la mise à jour corrective, Update Management s’appuie sur les données de classification disponibles sur l’ordinateur. Contrairement à d’autres distributions, CentOS n’a pas accès à ces informations dans la version RTM. Si vous disposez d’ordinateurs CentOS configurés pour retourner les données de sécurité pour la commande suivante, Update Management peut appliquer la mise à jour corrective en fonction des classifications.

```bash
sudo yum -q --security check-update
```

Il n’existe actuellement aucune méthode prise en charge permettant d’activer la disponibilité des données de classification natives sur CentOS. Pour le moment, seule la meilleure prise en charge possible est proposée aux clients qui l’ont éventuellement activée eux-mêmes. 

Pour classifier les mises à jour sur Red Hat Enterprise version 6, vous devez installer le plug-in yum-security. Sur Red Hat Enterprise Linux 7, le plug-in faisant déjà partie de yum lui-même, il est inutile d'installer quoi que ce soit. Pour plus d'informations, consultez l'[article de base de connaissances](https://access.redhat.com/solutions/10021) Red Hat suivant.

## <a name="integrate-with-configuration-manager"></a>Intégreravec Configuration Manager

Les clients qui ont investi dans Microsoft Endpoint Configuration Manager pour gérer des PC, serveurs et appareils mobiles s’appuient aussi sur la puissance et la maturité de Configuration Manager pour gérer les mises à jour logicielles. Configuration Manager fait partie de leur cycle de gestion des mises à jour logicielles (SUM).

Pour découvrir comment intégrer la solution de gestion avec Configuration Manager, voir [Intégrer Configuration Manager avec Update Management](oms-solution-updatemgmt-sccmintegration.md).

### <a name="third-party-patches-on-windows"></a>Correctifs tiers sur Windows

Update Management s’appuie sur le référentiel de mise à jour configuré localement pour corriger les systèmes Windows pris en charge. Il s’agit de WSUS ou de Windows Update. Des outils tels que l’[éditeur de mise à jour System Center ](https://docs.microsoft.com/configmgr/sum/tools/updates-publisher) (Éditeur de mise à jour) vous permettent de publier des mises à jour personnalisées dans WSUS. Ce scénario permet à Update Management de corriger des machines qui utilisent Configuration Manager comme référentiel de mise à jour avec des logiciels tiers. Pour savoir comment configurer l’éditeur de mise à jour, consultez [Installer l’éditeur de mise à jour](https://docs.microsoft.com/configmgr/sum/tools/install-updates-publisher).

## <a name="patch-linux-machines"></a>Appliquer une mise à jour corrective aux ordinateurs Linux

Les sections suivantes décrivent les problèmes potentiels liés à la mise à jour corrective des distributions Linux.

### <a name="unexpected-os-level-upgrades"></a>Mises à niveau inattendues au niveau du système d’exploitation

Sur certaines variantes Linux, comme Red Hat Enterprise Linux, les mises à niveau du système d’exploitation peuvent se produire par le biais de packages. Celles-ci peuvent alors entraîner des exécutions Update Management au cours desquelles le numéro de version du système d’exploitation change. Étant donné que la solution Update Management utilise les mêmes méthodes pour mettre à jour des packages qu’un administrateur sur l’ordinateur Linux local, ce comportement est volontaire.

Pour éviter de mettre à jour la version du système d’exploitation par le biais d’exécutions d’Update Management, vous utilisez la fonctionnalité **Exclusion**.

Dans Red Hat Enterprise Linux, le nom du package à exclure est : redhat-release-server.x86_64.

![Packages à exclure pour Linux](./media/automation-update-management/linuxpatches.png)

### <a name="criticalsecurity-patches-arent-applied"></a>Les correctifs de sécurité/critiques ne sont pas appliqués

Lorsque vous déployez des mises à jour sur un ordinateur Linux, vous pouvez sélectionner des classifications. Cette option filtre les mises à jour afin d’appliquer à la machine uniquement celles qui remplissent les critères de classification spécifiés. Ce filtre est appliqué localement sur l’ordinateur lorsque la mise à jour est déployée.

Comme Update Management enrichit les mises à jour dans le cloud, certaines mises à jour peuvent être signalées dans Update Management comme ayant un impact sur la sécurité quand bien même l’ordinateur local n’a pas ces informations. Ainsi, si vous appliquez des mises à jour critiques à un ordinateur Linux, certaines mises à jour, non signalées comme ayant un impact sur la sécurité pour cet ordinateur, peuvent ne pas être appliquées. Toutefois, Update Management peut quand même signaler cet ordinateur comme étant non conforme, car il contient des informations supplémentaires sur la mise à jour concernée.

Le déploiement de mises à jour par classification ne fonctionne pas sur les versions RTM de CentOS. Pour déployer correctement les mises à jour pour CentOS, sélectionnez toutes les classifications pour garantir que les mises à jour sont appliquées. Pour SUSE, le fait de sélectionner *uniquement* **Autres mises à jour** en tant que classification peut également entraîner l’installation de certaines mises à jour de sécurité si les mises à jour de sécurité associées à zypper (gestionnaire de package) ou à ses dépendances doivent être d’abord installées. Il s’agit d’une limitation de zypper. Dans certains cas, vous devrez peut-être réexécuter le déploiement des mises à jour. Pour savoir si cela est nécessaire, consultez le journal des mises à jour.

### <a name="multi-tenant"></a>Déploiements de mise à jour interlocataires

Si des machines se trouvent dans un autre rapport de tenant Azure pour Update Management que vous devez corriger, vous devez utiliser la solution de contournement suivante pour planifier l’opération. Vous pouvez utiliser la cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) avec le commutateur `-ForUpdate` pour créer une planification, et utiliser la cmdlet [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) et faire passer les machines de l’autre tenant au paramètre `-NonAzureComputer`. L’exemple suivant vous montre comment procéder :

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="onboard"></a>Activer Update Management

Pour commencer à appliquer des correctifs aux systèmes, vous devez activer la solution Update Management. Vous pouvez intégrer des machines à Update Management de différentes manières. Voici des méthodes recommandées et prises en charge pour intégrer la solution :

* [Depuis une machine virtuelle](automation-onboard-solutions-from-vm.md)
* [Depuis plusieurs machines](automation-onboard-solutions-from-browse.md)
* [Depuis votre compte Automation](automation-onboard-solutions-from-automation-account.md)
* [Avec un runbook Azure Automation](automation-onboard-solutions.md)

## <a name="next-steps"></a>Étapes suivantes

Utilisez le tutoriel suivant pour apprendre à gérer les mises à jour pour vos machines virtuelles Windows :

> [!div class="nextstepaction"]
> [Gérer les mises à jour et les correctifs pour vos machines virtuelles Windows Azure](automation-tutorial-update-management.md)

* Effectuez des recherches dans les [journaux Azure Monitor](../log-analytics/log-analytics-log-searches.md) pour voir les données de mise à jour détaillées.
* [Créez des alertes](automation-tutorial-update-management.md#configure-alerts) pour connaître l’état de déploiement des mises à jour.

* Pour savoir comment utiliser Update Management avec l’API REST, consultez [Configurations des mises à jour logicielles](/rest/api/automation/softwareupdateconfigurations).
* Pour savoir comment résoudre les problèmes d’Update Management, consultez [Résolution des problèmes d’Update Management](troubleshoot/update-management.md).
