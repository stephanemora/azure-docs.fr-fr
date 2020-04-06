---
title: Solution Update Management dans Azure
description: Cet article décrit comment utiliser la solution Azure Update Management pour gérer les mises à jour de vos machines Windows et Linux.
services: automation
ms.subservice: update-management
ms.date: 02/27/2020
ms.topic: conceptual
ms.openlocfilehash: c76b14e4f08ec930159498da4a35fdad0341929e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79235477"
---
# <a name="update-management-solution-in-azure"></a>Solution Update Management dans Azure

Vous pouvez utiliser la solution Update Management dans Azure Automation pour gérer les mises à jour du système d’exploitation de vos machines Windows et Linux dans Azure, des environnements locaux et d’autres environnements cloud. Vous pouvez rapidement évaluer l’état des mises à jour disponibles sur toutes les machines d’agent et gérer le processus d’installation des mises à jour nécessaires pour les serveurs.

Vous pouvez activer Update Management pour des machines virtuelles à l’aide des méthodes suivantes :

- À partir de votre [compte Azure Automation](automation-onboard-solutions-from-automation-account.md) pour une ou plusieurs machines Azure et manuellement pour les machines non-Azure.

- Pour une machine virtuelle Azure unique à partir de la page de la machine virtuelle dans le portail Azure. Ce scénario est disponible pour les machines virtuelles [Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) et [Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management).

- Pour [plusieurs machines virtuelles Azure](manage-update-multi.md), sélectionnez-les dans la page **Machines virtuelles** du portail Azure. 

> [!NOTE]
> La solution Update Management vous demande d’établir un lien entre l’espace de travail Log Analytics et votre compte Automation. Pour obtenir la liste définitive des régions prises en charge, consultez [Mappages Azure Workspace](./how-to/region-mappings.md). Les mappages de région n’empêchent pas de gérer les machines virtuelles dans une autre région depuis votre compte Automation.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Un [modèle Azure Resource Manager](automation-update-management-deploy-template.md) est disponible pour vous permettre de déployer la solution Update Management sur un compte Automation et un espace de travail Log Analytics nouveaux ou existants dans votre abonnement.

## <a name="solution-overview"></a>Vue d’ensemble de la solution

Les machines gérées par Update Management utilisent les configurations suivantes pour effectuer l’évaluation et mettre à jour les déploiements :

* Agent Log Analytics pour Windows ou Linux
* PowerShell DSC (Desired State Configuration, configuration d’état souhaité) pour Linux
* Runbook Worker hybride Automation
* Services Microsoft Update ou Windows Server Update (WSUS) pour machines Windows

Le schéma suivant illustre la façon dont la solution évalue les mises à jour de sécurité et les applique à toutes les machines Windows Server et Linux connectées dans un espace de travail :

![Flux du processus Update Management](./media/automation-update-management/update-mgmt-updateworkflow.png)

Update Management peut être utilisé pour intégrer des machines en mode natif dans plusieurs abonnements du même locataire.

Après la publication d’un package, comptez un délai de deux à trois heures avant l’affichage du correctif pour l’évaluation sur des machines Linux. Sur les machines Windows, ce délai est de 12 à 15 heures.

Après qu’une machine a terminé l’analyse de conformité de la mise à jour, l’agent transfère les informations en bloc aux journaux Azure Monitor. Sur une machine Windows, l’analyse de conformité est effectuée toutes les 12 heures par défaut.

En plus de l’analyse planifiée, l’analyse de conformité de mise à jour est lancée dans les 15 minutes si l’agent Log Analytics est redémarré, avant et après l’installation de la mise à jour.

Sur une machine Linux, l’analyse de conformité est effectuée toutes les heures par défaut. Si l’agent Log Analytics est redémarré, une analyse de conformité est lancée dans les 15 minutes.

La solution rapporte l’état de mise à jour de la machine en fonction de la source avec laquelle vous avez configuré la synchronisation. Si la machine Windows est configurée pour rapporter à WSUS, en fonction de la date de dernière synchronisation de WSUS avec Microsoft Update, les résultats peuvent être différents de ce que Microsoft Update indique. Le comportement est le même pour les machines Linux configurées pour rapporter à un référentiel local et non pas à un référentiel public.

> [!NOTE]
> Pour pouvoir rapporter au service, Update Management nécessite certaines URL et l’activation de ports. Pour en savoir plus sur la configuration requise, consultez la section [Planification du réseau pour les Workers hybrides](automation-hybrid-runbook-worker.md#network-planning).

Vous pouvez déployer et installer des mises à jour logicielles sur des machines qui nécessitent les mises à jour en créant un déploiement planifié. Les mises à jour considérées comme *facultatives* ne sont pas incluses dans le déploiement des machines Windows. Seules les mises à jour nécessaires sont incluses dans le déploiement.

Le déploiement planifié définit quelles machines cibles reçoivent les mises à jour applicables. Soit il désigne explicitement certaines machines, soit il sélectionne un [groupe d’ordinateurs](../azure-monitor/platform/computer-groups.md) d’après des recherches dans les journaux d’un ensemble spécifique de machines (ou sur une [requête Azure](automation-update-management-query-logs.md) qui sélectionne des machines virtuelles Azure de manière dynamique selon des critères spécifiés). Ces groupes sont différents de la [configuration de l’étendue](../azure-monitor/insights/solution-targeting.md), qui est utilisée uniquement pour déterminer quelles machines reçoivent les packs d’administration qui activent la solution.

Vous spécifiez également une planification pour approuver et définir la période pendant laquelle les mises à jour peuvent être installées. Cette période est appelée fenêtre de maintenance. Vingt minutes de la fenêtre de maintenance sont réservées aux redémarrages si un redémarrage est nécessaire et que vous avez sélectionné l’option de redémarrage appropriée. Si la mise à jour corrective prend plus longtemps que prévu et qu’il reste moins de vingt minutes dans la fenêtre de maintenance, il n’y aura pas de redémarrage.

Les mises à jour sont installées par des Runbooks dans Azure Automation. Vous ne pouvez pas visualiser ces runbooks, ils ne nécessitent par ailleurs aucune configuration. Lorsqu’un déploiement de mises à jour est créé, il génère une planification qui démarre un runbook de mise à jour principal au moment indiqué pour les machines incluses. Ce runbook principal lance un runbook enfant sur chaque agent pour installer les mises à jour obligatoires.

À la date et l’heure spécifiées dans le déploiement de mises à jour, les machines cibles exécutent le déploiement en parallèle. Avant l’installation, une analyse est lancée pour vérifier que les mises à jour sont encore requises. Pour les machines clientes WSUS, si les mises à jour ne sont pas approuvées dans WSUS, leur déploiement échoue.

L’inscription d’une machine auprès du service Update Management dans plusieurs espaces de travail Log Analytics (également appelé multihébergement) n’est pas prise en charge.

## <a name="clients"></a>Clients

### <a name="supported-client-types"></a>Types de clients pris en charge

Le tableau suivant répertorie les systèmes d’exploitation pris en charge pour les évaluations des mises à jour. Une mise à jour corrective nécessite un Runbook Worker hybride. Pour plus d’informations sur les exigences de Runbook Worker hybride, voir les guides d’installation de [Runbook Worker hybride Windows](automation-windows-hrw-install.md) et de [Runbook Worker hybride Linux](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker).

|Système d’exploitation  |Notes  |
|---------|---------|
|Windows Server 2019 (Datacenter/Datacenter Core/Standard)<br><br>Windows Server 2016 (Datacenter/Datacenter Core/Standard)<br><br>Windows Server 2012 R2 (Datacenter/Standard)<br><br>Windows Server 2012 || 
|Windows Server 2008 R2 (RTM et SP1 Standard)| Update Management prend uniquement en charge l’exécution d’évaluations pour ce système d’exploitation. Sa mise à jour corrective n’est pas prise en charge, car le [Runbook Worker hybride](automation-windows-hrw-install.md) n’est pas pris en charge pour Windows Server 2008 R2. |
|CentOS 6 (x86/x64) et 7 (x64)      | Les agents Linux nécessitent un accès à un référentiel de mise à jour. La mise à jour corrective basée sur la classification nécessite que `yum` retourne les données de sécurité que CentOS n’a pas dans ses versions RTM. Pour plus d’informations sur la mise à jour corrective basée sur des classifications sur CentOS, consultez [Mettre à jour des classifications sur Linux](automation-view-update-assessments.md#linux-2).          |
|Red Hat Enterprise 6 (x86/x64) et 7 (x64)     | Les agents Linux nécessitent un accès à un référentiel de mise à jour.        |
|SUSE Linux Enterprise Server 11 (x86/x64) et 12 (x64)     | Les agents Linux nécessitent un accès à un référentiel de mise à jour.        |
|Ubuntu 14.04 LTS, 16.04 LTS et 18.04 (x86/x64)      |Les agents Linux nécessitent un accès à un référentiel de mise à jour.         |

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

Les agents Windows doivent être configurés pour communiquer avec un serveur WSUS, ou ils nécessitent un accès à Microsoft Update.

Vous pouvez utiliser Azure Update Management avec Configuration Manager. Pour en savoir plus sur les scénarios d’intégration, voir [Intégrer Configuration Manager avec Update Management](oms-solution-updatemgmt-sccmintegration.md#configuration). L’[agent Windows Log Analytics](../azure-monitor/platform/agent-windows.md) est nécessaire. Cet agent est automatiquement installé si vous intégrez une machine virtuelle Azure.

Par défaut, les machines virtuelles Windows déployées à partir de la place de marché Azure sont configurées pour recevoir des mises à jour automatiques de Windows Update Service. Ce comportement ne change pas lorsque vous ajoutez cette solution ou des machines virtuelles Windows à votre espace de travail. Si vous n’avez pas géré activement les mises à jour avec cette solution, le comportement par défaut (appliquer automatiquement les mises à jour) s’applique.

> [!NOTE]
> Un utilisateur peut modifier la stratégie de groupe afin que les redémarrages de la machine ne puissent être effectués que par lui et non par le système. Les machines managées peuvent rester bloquées si Update Management ne dispose pas des droits nécessaires pour les redémarrer sans intervention manuelle de l'utilisateur.
>
> Pour plus d'informations, consultez [Configurer les paramètres de stratégie de groupe pour les mises à jour automatiques](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates).

#### <a name="linux"></a>Linux

Pour Linux, la machine nécessite un accès à un référentiel de mise à jour. qui peut être privé ou public. TLS 1.1 ou TLS 1.2 est exigé pour interagir avec Update Management. La configuration de Log Analytics Agent pour Linux afin d’envoyer des rapports à plusieurs espaces de travail Log Analytics n’est pas prise en charge avec cette solution. Python 2.x doit également être installé sur la machine.

Pour plus d’informations sur la manière d’installer l’agent Log Analytics pour Linux et de télécharger la dernière version, consultez [Agent Log Analytics pour Linux](../azure-monitor/platform/agent-linux.md). Pour plus d’informations sur la manière d’installer l’agent Log Analytics pour Windows, consultez [Connecter des ordinateurs Windows à Azure Monitor](../log-analytics/log-analytics-windows-agent.md).

Les machines virtuelles créées à partir des images Red Hat Enterprise Linux (RHEL) à la demande disponibles dans le service Place de marché Azure sont inscrites pour accéder à l’infrastructure [RHUI (Red Hat Update Infrastructure)](../virtual-machines/workloads/redhat/redhat-rhui.md) déployée dans Azure. Toute autre distribution Linux doit être mise à jour à partir du référentiel de fichiers de distribution en ligne en utilisant les méthodes de distribution prises en charge.

## <a name="permissions"></a>Autorisations

Pour créer et gérer des déploiements de mises à jour, vous devez disposer d’autorisations spécifiques. Pour en savoir plus sur ces autorisations, consultez [Accès en fonction du rôle – Update Management](automation-role-based-access-control.md#update-management).

## <a name="solution-components"></a>Composants de la solution

La solution se compose des éléments suivants. Ces ressources sont automatiquement ajoutées à votre compte Automation quand vous activez la solution. 

### <a name="hybrid-worker-groups"></a>Groupes de Workers hybrides

Après avoir activé cette solution, toute machine Windows directement connectée à votre espace de travail Log Analytics est automatiquement configurée en tant que runbook Worker hybride afin de prendre en charge les runbooks inclus dans cette solution.

Chaque machine Windows gérée par la solution est listée dans le panneau **Groupes de Workers hybrides** en tant que **Groupe de Workers hybrides système** du compte Automation. Les solutions utilisent la convention d’affectation de noms *Hostname FQDN_GUID*. Vous ne pouvez pas cibler ces groupes avec des Runbooks dans votre compte. Si vous essayez, la tentative échoue. Ces groupes sont destinés à prendre en charge uniquement cette solution de gestion.

Vous pouvez ajouter la machine Windows à un groupe de runbooks Workers hybrides dans votre compte Automation pour prendre en charge des runbooks Automation à condition d’utiliser le même compte pour la solution et pour l’appartenance au groupe de runbooks Workers hybrides. Cette fonctionnalité a été ajoutée à la version 7.2.12024.0 du Runbook Worker hybride.

### <a name="management-packs"></a>Packs d’administration

Si votre groupe d’administration System Center Operations Manager est [connecté à un espace de travail Log Analytics](../azure-monitor/platform/om-agents.md), les packs d’administration suivants sont installés dans Operations Manager. Ces packs d’administration sont également installés sur des machines Windows directement connectées après l’ajout de la solution. Il n’est pas nécessaire de les configurer ou de les gérer.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Pack d’administration du déploiement des mises à jour

> [!NOTE]
> Si vous avez un groupe d’administration Operations Manager 1807 ou 2019 connecté à un espace de travail Log Analytics avec des agents configurés dans le groupe d’administration pour collecter des données de journal, vous avez besoin de remplacer la règle suivante pour les gérer avec Update Management : Remplacez le paramètre **IsAutoRegistrationEnabled** et affectez-lui la valeur **true** dans la règle **Microsoft.IntelligencePacks.AzureAutomation.HybridAgent.Init**.

Pour plus d’informations sur la façon dont ces packs d’administration de solution sont mis à jour, consultez [Connecter Operations Manager aux journaux Azure Monitor](../azure-monitor/platform/om-agents.md).

> [!NOTE]
> Pour que les machines dotées de l’agent Operations Manager soient entièrement gérées par Update Management, l’agent doit être mis à jour vers l’agent Log Analytics pour Windows ou Linux. Pour savoir comment mettre à jour l’agent, consultez [Guide pratique pour mettre à niveau un agent Operations Manager](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents). Dans les environnements qui utilisent Operations Manager, vous devez exécuter System Center Operations Manager 2012 R2 UR 14 ou une version ultérieure.

## <a name="data-collection"></a>Collecte de données

### <a name="supported-agents"></a>Agents pris en charge

Le tableau suivant décrit les sources connectées prises en charge par cette solution :

| Source connectée | Prise en charge | Description |
| --- | --- | --- |
| Agents Windows |Oui |La solution collecte des informations sur les mises à jour système auprès des agents Windows et lance l’installation des mises à jour obligatoires. |
| Agents Linux |Oui |La solution collecte des informations sur les mises à jour système auprès des agents Linux et lance l’installation des mises à jour obligatoires sur les versions prises en charge. |
| Groupe d’administration d’Operations Manager |Oui |La solution collecte des informations sur les mises à jour système des agents dans un groupe d’administration connecté.<br/><br/>Une connexion directe entre l’agent Operations Manager et les journaux Azure Monitor n’est pas obligatoire. Les données sont transférées du groupe d’administration à l’espace de travail Log Analytics. |

### <a name="collection-frequency"></a>Fréquence de collecte

Une analyse est effectuée deux fois par jour sur chaque machine Windows gérée. Les API Windows sont appelées toutes les 15 minutes pour rechercher l’heure de la dernière mise à jour afin de déterminer si l’état a changé. Si l’état a changé, une analyse de conformité est lancée.

Une analyse est effectuée toutes les heures sur chaque machine Linux gérée.

L’affichage sur le tableau de bord des données mises à jour provenant des machines gérées peut prendre entre 30 minutes et 6 heures.

La consommation moyenne de données des journaux Azure Monitor pour une machine utilisant la solution Update Management est d’environ 25 mégaoctets (Mo) par mois. Cette valeur est approximative et sujette à modification en fonction de votre environnement. Nous vous recommandons de surveiller votre environnement pour assurer le suivi de votre consommation exacte. Pour plus d’informations sur l’analyse de l’utilisation des données, consultez [Gérer l’utilisation et le coût](../azure-monitor/platform/manage-cost-storage.md).

## <a name="network-planning"></a><a name="ports"></a>Planification réseau

Les adresses suivantes sont exigées particulièrement pour Update Management. La communication avec ces adresses s’effectue par le biais du port 443.

|Azure (public)  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com    | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net | *.blob.core.usgovcloudapi.net|
|\* .azure-automation.net | *.azure-automation.us|

Pour les machines Windows, vous devez également autoriser le trafic vers tous les points de terminaison requis par Windows Update. Vous trouverez une liste actualisée des points de terminaison requis dans [Problèmes liés à HTTP/au proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy). Si vous disposez d’un [serveur Windows Update](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment) local, vous devez également autoriser le trafic vers le serveur spécifié dans votre [clé WSUS](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry).

Pour les machines Red Hat Linux, consultez [Adresses IP des serveurs de distribution de contenu RHUI](../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers) pour les points de terminaison requis. Pour les autres distributions Linux, reportez-vous à la documentation du fournisseur.

Pour plus d’informations sur les ports nécessaires pour le runbook Worker hybride, consultez [Ports du rôle de Worker hybride](automation-hybrid-runbook-worker.md#hybrid-worker-role).

Il est recommandé d’utiliser les adresses répertoriées lors de la définition d’exceptions. Pour les adresses IP, vous pouvez télécharger [Plages d’adresses IP du centre de données Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Ce fichier, qui est mis à jour chaque semaine et reflète les plages actuellement déployées ainsi que tous les changements à venir des plages d’adresses IP.

Suivez les instructions dans [Connecter des ordinateurs sans accès Internet](../azure-monitor/platform/gateway.md) pour configurer des ordinateurs qui n’ont pas accès à Internet.

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

### <a name="linux"></a><a name="linux-2"></a>Linux

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

### <a name="third-party-updates-on-windows"></a>Mises à jour tierces sur Windows

Update Management s’appuie sur le référentiel de mise à jour configuré localement pour mettre à jour les systèmes Windows pris en charge. Il s’agit de WSUS ou de Windows Update. Des outils comme l’[éditeur de mise à jour System Center ](https://docs.microsoft.com/configmgr/sum/tools/updates-publisher) (Éditeur de mise à jour) vous permettent d’importer et de publier des mises à jour personnalisées avec WSUS. Ce scénario permet à Update Management de mettre à jour des machines qui utilisent Configuration Manager comme référentiel de mise à jour avec des logiciels tiers. Pour savoir comment configurer l’éditeur de mise à jour, consultez [Installer l’éditeur de mise à jour](https://docs.microsoft.com/configmgr/sum/tools/install-updates-publisher).

## <a name="enable-update-management"></a><a name="onboard"></a>Activer Update Management

Pour commencer à mettre à jour des systèmes, vous devez activer la solution Update Management. Voici des méthodes recommandées et prises en charge pour intégrer la solution :

- [Depuis une machine virtuelle](automation-onboard-solutions-from-vm.md)

- [Depuis plusieurs machines](automation-onboard-solutions-from-browse.md)

- [Depuis votre compte Automation](automation-onboard-solutions-from-automation-account.md)

- [Avec un runbook Azure Automation](automation-onboard-solutions.md)

- [Avec un modèle Azure Resource Manager](automation-update-management-deploy-template.md)

## <a name="next-steps"></a>Étapes suivantes

Consultez les [questions fréquentes (FAQ)](automation-faq.md) sur Azure Automation pour passer en revue les questions couramment posées sur cette solution.
