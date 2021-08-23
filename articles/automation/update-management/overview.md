---
title: Présentation d’Update Management Azure Automation
description: Cet article présente la fonctionnalité Update Management qui implémente les mises à jour de vos machines Windows et Linux.
services: automation
ms.subservice: update-management
ms.date: 06/07/2021
ms.topic: conceptual
ms.openlocfilehash: 576bc21791d088a736044a0111c25dc97c57b059
ms.sourcegitcommit: 67cdbe905eb67e969d7d0e211d87bc174b9b8dc0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111854807"
---
# <a name="update-management-overview"></a>Vue d’ensemble de Update Management

Vous pouvez utiliser Update Management dans Azure Automation pour gérer les mises à jour des systèmes d’exploitation de vos machines virtuelles Windows et Linux dans Azure, des machines virtuelles ou physiques dans les environnements locaux et dans d’autres environnements cloud. Vous pouvez rapidement évaluer l’état des mises à jour disponibles et gérer le processus d’installation des mises à jour nécessaires pour vos machines qui communiquent leurs rapports à Update Management. 

En tant que fournisseur de services, vous avez peut-être intégré les locataires de plusieurs clients à [Azure Lighthouse](../../lighthouse/overview.md). Update Management peut être utilisé pour évaluer et planifier des déploiements de mises à jour sur des machines présentes dans plusieurs abonnements, dans le même locataire Azure Active Directory (Azure AD) ou dans plusieurs locataires, à l’aide d’Azure Lighthouse.

Microsoft propose d’autres fonctionnalités pour vous aider à gérer les mises à jour de vos machines virtuelles Azure ou groupes de machines virtuelles identiques Azure que vous devez prendre en compte dans le cadre de votre stratégie globale de gestion des mises à jour. 

- Si vous souhaitez évaluer et mettre à jour automatiquement vos machines virtuelles Azure, afin de garantir la conformité en matière de sécurité à l’aide des mises à jour *Critiques* et de *Sécurité* publiées chaque mois, consultez [Mise à jour corrective automatique d’un invité de machine virtuelle](../../virtual-machines/automatic-vm-guest-patching.md) (préversion). Il s’agit d’une autre solution de gestion des mises à jour qui permet à vos machines virtuelles Azure d’être mises à jour automatiquement pendant les heures creuses, notamment les machines virtuelles d’un groupe à haute disponibilité, comparée à la gestion des déploiements de mises à jour sur ces machines virtuelles à partir d’Update Management dans Azure Automation. 

- Si vous gérez des groupes de machines virtuelles identiques Azure, regardez comment effectuer des [Mises à niveau automatiques de l’image du système d’exploitation](../../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) pour mettre à niveau le disque du système d’exploitation de toutes les instances du groupe identique de manière sécurisée et automatique. 

Avant de déployer Update Management et d’activer vos machines pour la gestion, assurez-vous de bien comprendre les informations contenues dans les sections suivantes.

## <a name="about-update-management"></a>À propos d’Update Management

Le schéma suivant illustre la façon dont Update Management évalue les mises à jour de sécurité et les applique à tous les serveurs Windows Server et Linux connectés.

![Workflow Update Management](./media/overview/update-mgmt-updateworkflow.png)

Update Management s’intègre aux journaux Azure Monitor pour stocker les évaluations de mise à jour et les résultats de déploiement des mises à jour sous la forme de données de journal, depuis des machines Azure et non Azure assignées. Pour collecter ces données, le compte Automation et l’espace de travail Log Analytics sont liés entre eux ; l’agent Log Analytics pour Windows et Linux est nécessaire sur la machine, et il est configuré pour produire des rapports vers cet espace de travail. Update Management prend en charge la collecte d’informations concernant les mises à jour système auprès des agents dans un groupe d’administration SCOM (System Center Operations Manager) connecté à l’espace de travail. L’inscription d’une machine auprès du service Update Management dans plusieurs espaces de travail Log Analytics (également appelé multihébergement) n’est pas prise en charge.

Le tableau suivant récapitule les sources connectées prises en charge avec Update Management.

| Source connectée | Prise en charge | Description |
| --- | --- | --- |
| Windows |Oui |Update Management collecte des informations concernant les mises à jour système auprès des machines Windows par l’intermédiaire de l’agent Log Analytics et de l’installation des mises à jour obligatoires. |
| Linux |Oui |Update Management collecte des informations concernant les mises à jour système auprès des machines Linux par l’intermédiaire de l’agent Log Analytics et de l’installation des mises à jour obligatoires sur les distributions prises en charge. |
| Groupe d’administration d’Operations Manager |Oui |Update Management collecte des informations concernant les mises à jour logicielles auprès d’agents dans un groupe d’administration connecté.<br/><br/>Une connexion directe entre l’agent Operations Manager et les journaux Azure Monitor n’est pas obligatoire. Les données de journal sont transférées du groupe d’administration à l’espace de travail Log Analytics. |

Les machines affectées à Update Management signalent l’état de mise à jour dans lequel elles se trouvent, en fonction de la source avec laquelle elles sont configurées pour se synchroniser. Les machines Windows peuvent être configurées pour envoyer les rapports à Windows Server Update Services ou à Microsoft Update tandis que les machines Linux peuvent le faire à un référentiel local ou public. Vous pouvez également utiliser Update Management avec Microsoft Endpoint Configuration Manager ; pour en savoir plus, consultez [Intégrer Update Management à Windows Endpoint Configuration Manager](mecmintegration.md). 

Si l’agent Windows Update sur la machine Windows est configuré pour transmettre les rapports à WSUS (Windows Server Update Services), en fonction de la date de la dernière synchronisation de WSUS à Microsoft Update, les résultats peuvent être différents de ce qu’indique Microsoft Update. Le comportement est le même pour les machines Linux configurées pour rendre compte à un référentiel local et non pas à un référentiel public. Sur une machine Windows, l’analyse de conformité est effectuée toutes les 12 heures par défaut. Sur une machine Linux, l’analyse de conformité est effectuée toutes les heures par défaut. Si l’agent Log Analytics est redémarré, une analyse de conformité est démarrée dans les 15 minutes. À l’issue de l’analyse de conformité de la mise à jour effectuée par la machine, l’agent transfère les informations en bloc aux journaux Azure Monitor. 

Vous pouvez déployer et installer des mises à jour logicielles sur des machines qui nécessitent les mises à jour en créant un déploiement planifié. Les mises à jour considérées comme *facultatives* ne sont pas incluses dans le déploiement des machines Windows. Seules les mises à jour nécessaires sont incluses dans le déploiement.

Le déploiement planifié définit quelles machines cibles reçoivent les mises à jour applicables. Soit il désigne explicitement certaines machines, soit il sélectionne un [groupe d’ordinateurs](../../azure-monitor/logs/computer-groups.md) d’après des recherches dans les journaux d’un ensemble spécifique de machines (ou selon une [requête Azure](query-logs.md) qui sélectionne des machines virtuelles Azure de manière dynamique en fonction de critères spécifiés). Ces groupes diffèrent de la [configuration d’étendue](../../azure-monitor/insights/solution-targeting.md), utilisée pour contrôler le ciblage des machines qui reçoivent la configuration permettant d’activer Update Management. Cela les empêche de procéder à la conformité des mises à jour et de créer des rapports, puis d’installer les mises à jour nécessaires approuvées.

Lorsque vous définissez un déploiement, vous spécifiez également une planification pour approuver et définir la période pendant laquelle les mises à jour peuvent être installées. Cette période est appelée fenêtre de maintenance. Vingt minutes de la fenêtre de maintenance sont réservées aux redémarrages si un redémarrage est nécessaire et que vous avez sélectionné l’option de redémarrage appropriée. Si la mise à jour corrective prend plus longtemps que prévu et qu’il reste moins de vingt minutes dans la fenêtre de maintenance, il n’y aura pas de redémarrage.

Après la planification du déploiement d’un package de mise à jour, il faut compter 2 à 3 heures pour que la mise à jour apparaisse sur les machines Linux à des fins d’évaluation. Pour les machines Windows, ce délai est de 12 à 15 heures après la publication. Avant et après l’installation de la mise à jour, une analyse de conformité de la mise à jour est effectuée, et les résultats des données de journal sont transférés à l’espace de travail.

Les mises à jour sont installées par des Runbooks dans Azure Automation. Vous ne pouvez pas visualiser ces runbooks, ils ne nécessitent par ailleurs aucune configuration. Lorsqu’un déploiement de mises à jour est créé, il génère une planification qui démarre un runbook de mise à jour principal au moment indiqué pour les machines incluses. Le runbook principal démarre un runbook enfant sur chaque agent qui lance l’installation des mises à jour obligatoires avec l’agent Windows Update sur Windows, ou la commande correspondante sur les distributions Linux prises en charge.

À la date et l’heure spécifiées dans le déploiement de mises à jour, les machines cibles exécutent le déploiement en parallèle. Avant l’installation, une analyse est lancée pour vérifier que les mises à jour sont encore requises. Pour les machines clientes WSUS, si les mises à jour ne sont pas approuvées dans WSUS, leur déploiement échoue.

## <a name="limits"></a>Limites

Pour les limites qui s’appliquent à Update Management, consultez [Limites du service Azure Automation](../../azure-resource-manager/management/azure-subscription-service-limits.md#update-management).

## <a name="permissions"></a>Autorisations

Pour créer et gérer des déploiements de mises à jour, vous devez disposer d’autorisations spécifiques. Pour en savoir plus sur ces autorisations, consultez [Accès en fonction du rôle - Update Management](../automation-role-based-access-control.md#update-management-permissions).

## <a name="update-management-components"></a>Composants Update Management

Update Management utilise les ressources décrites dans cette section. Ces ressources sont automatiquement ajoutées à votre compte Automation quand vous activez Update Management.

### <a name="hybrid-runbook-worker-groups"></a>Groupes de Runbooks Workers hybrides

Après avoir activé Update Management, toute machine Windows directement connectée à votre espace de travail Log Analytics est automatiquement configurée en tant que Runbook Worker hybride système, afin de gérer les runbooks qui prennent en charge Update Management.

Chaque machine Windows gérée par Update Management est répertoriée dans le volet Groupes de Workers hybrides en tant que Groupe de Workers hybrides système pour le compte Automation. Les groupes utilisent la convention d’affectation de noms `Hostname FQDN_GUID`. Vous ne pouvez pas cibler ces groupes avec des Runbooks dans votre compte. Si vous essayez, la tentative échoue. Ces groupes sont destinés à prendre uniquement en charge Update Management. Pour en savoir plus sur l’affichage de la liste des machines Windows configurées en tant que Runbook Worker hybride, consultez [Afficher les Runbooks Workers hybrides](../automation-hybrid-runbook-worker.md#view-system-hybrid-runbook-workers).

Vous pouvez ajouter la machine Windows à un groupe Runbook Worker hybride utilisateur dans votre compte Automation, afin de prendre en charge des runbooks Automation, à condition d’utiliser le même compte pour Update Management et pour l’appartenance au groupe Runbook Worker hybride. Cette fonctionnalité a été ajoutée à la version 7.2.12024.0 du Runbook Worker hybride.

### <a name="management-packs"></a>Packs d’administration

Les packs d’administration suivants sont installés sur les machines managées par Update Management. Si votre groupe d’administration Operations Manager est [connecté à un espace de travail Log Analytics](../../azure-monitor/agents/om-agents.md), les packs d’administration sont installés dans le groupe d’administration Operations Manager. Il n’est pas nécessaire de les configurer ou de les gérer.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Pack d’administration du déploiement des mises à jour

> [!NOTE]
> Si vous avez un groupe d’administration Operations Manager 1807 ou 2019 connecté à un espace de travail Log Analytics, avec des agents configurés dans le groupe d’administration pour collecter les données de journal, vous devez remplacer le paramètre `IsAutoRegistrationEnabled` et le définir sur `True` dans la règle **Microsoft.IntelligencePacks.AzureAutomation.HybridAgent.Init**.

Pour plus d’informations sur les mises à jour des packs d’administration, consultez [Connecter Operations Manager aux journaux Azure Monitor](../../azure-monitor/agents/om-agents.md).

> [!NOTE]
> Pour que Update Management gère entièrement les machines avec l’agent Log Analytics, vous devez mettre à jour l’agent Log Analytics pour Windows ou l’agent Log Analytics pour Linux. Pour savoir comment mettre à jour l’agent, consultez [Guide pratique pour mettre à niveau un agent Operations Manager](/system-center/scom/deploy-upgrade-agents). Dans les environnements qui utilisent Operations Manager, vous devez exécuter System Center Operations Manager 2012 R2 UR 14 ou une version ultérieure.

## <a name="data-collection-frequency"></a>Fréquence de collecte de données

Update Management analyse les données des machines gérées à l’aide des règles suivantes. L’affichage sur le tableau de bord des données mises à jour provenant des machines gérées peut prendre entre 30 minutes et 6 heures.

* Chaque machine Windows : Update Management effectue une analyse deux fois par jour pour chaque machine.

* Chaque machine Linux : Update Management effectue une analyse toutes les heures.

La consommation moyenne de données par les journaux Azure Monitor pour une machine utilisant Update Management est d’environ 25 Mo par mois. Cette valeur est approximative et sujette à modification en fonction de votre environnement. Nous vous recommandons de surveiller votre environnement pour assurer le suivi de votre consommation exacte. Pour plus d'informations sur l'analyse de l'utilisation des données des journaux d'activité Azure Monitor, consultez [Gérer l'utilisation et les coûts](../../azure-monitor/logs/manage-cost-storage.md).

## <a name="update-classifications"></a>Classifications des mises à jour

Le tableau suivant définit les classifications que Update Management prend en charge pour les mises à jour Windows.

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

Le tableau suivant définit les classifications prises en charge pour les mises à jour Linux.

|classification ;  |Description  |
|---------|---------|
|Mises à jour critiques et de sécurité     | Mises à jour pour un problème spécifique ou un problème de sécurité propre à un produit.         |
|Autres mises à jour     | Toutes les autres mises à jour qui ne sont ni critiques par nature, ni des mises à jour de sécurité.        |

> [!NOTE]
> La classification des mises à jour pour les machines Linux n’est disponible que dans les régions de cloud public Azure prises en charge. Les mises à jour Linux ne font l’objet d’aucune classification quand Update Management est utilisé dans les régions de cloud national suivantes :
>
>* Azure US Government
>* 21Vianet en Chine
>
> Au lieu d’être classées, les mises à jour sont signalées sous la catégorie **Autres mises à jour**.
>
> Update Management utilise les données publiées par les distributions prises en charge, en particulier leurs fichiers publiés [OVAL](https://oval.mitre.org/) (Open Vulnerability and Assessment Language). Étant donné que l’accès à Internet est limité à partir de ces clouds nationaux, Update Management ne peut pas accéder aux fichiers.

Pour Linux, Update Management peut faire la différence entre les mises à jour critiques et les mises à jour de sécurité dans le cloud sous la classification **Sécurité** ou **Autres**, tout en affichant les données d’évaluation en raison de l’enrichissement des données dans le cloud. Pour la mise à jour corrective, Update Management s’appuie sur les données de classification disponibles sur l’ordinateur. Contrairement à d’autres distributions, CentOS n’a pas accès à ces informations dans la version RTM. Si vous disposez d’ordinateurs CentOS configurés pour retourner les données de sécurité pour la commande suivante, Update Management peut appliquer la mise à jour corrective en fonction des classifications.

```bash
sudo yum -q --security check-update
```

Il n’existe actuellement aucune méthode prise en charge permettant d’activer la disponibilité des données de classification natives sur CentOS. Pour le moment, une prise en charge limitée est proposée aux clients qui pourraient avoir activé cette fonctionnalité eux-mêmes.

Pour classifier les mises à jour sur Red Hat Enterprise version 6, vous devez installer le plug-in yum-security. Sur Red Hat Enterprise Linux 7, le plug-in faisant déjà partie de yum lui-même, il est inutile d’installer quoi que ce soit. Pour plus d’informations, consultez l’[article de base de connaissances](https://access.redhat.com/solutions/10021) suivant sur Red Hat.

Quand vous planifiez une mise à jour à exécuter sur une machine Linux qui, par exemple, est configurée pour installer uniquement les mises à jour correspondant à la classification **Sécurité**, les mises à jour installées peuvent être différentes des mises à jour correspondant à cette classification, ou n’en constituer qu’une partie. Quand une évaluation des mises à jour du système d’exploitation en attente pour votre ordinateur Linux est effectuée, les fichiers [OVAL](https://oval.mitre.org/) (Open Vulnerability and Assessment Language) fournis par le fournisseur de distribution Linux sont utilisés par Update Management pour la classification.

La catégorisation est effectuée pour les mises à jour Linux en tant que **Sécurité** ou **Autres** en fonction des fichiers OVAL, ce qui inclut les mises à jour traitant des problèmes de sécurité ou des vulnérabilités. Toutefois, lorsque la planification des mises à jour est effectuée, elle s’exécute sur la machine Linux à l’aide du gestionnaire de package approprié pour les installer (p. ex., YUM, APT ou ZYPPER). Le gestionnaire de package pour la distribution Linux peut avoir un mécanisme différent pour classifier les mises à jour, où les résultats peuvent différer de ceux obtenus à partir des fichiers OVAL par Update Management. Pour vérifier manuellement la machine et savoir quelles mises à jour sont pertinentes pour la sécurité du point de vue de votre gestionnaire de package, consultez [Résoudre les problèmes liés à Update Management pour Linux](../troubleshoot/update-management.md#updates-linux-installed-different).

## <a name="integrate-update-management-with-configuration-manager"></a>Intégrer Update Management à Configuration Manager

Les clients qui ont investi dans Microsoft Endpoint Configuration Manager pour gérer des PC, serveurs et appareils mobiles s’appuient aussi sur la puissance et la maturité de Configuration Manager pour gérer les mises à jour logicielles. Pour savoir comment intégrer Update Management à Configuration Manager, consultez [Intégrer Update Management à Windows Endpoint Configuration Manager](mecmintegration.md).

## <a name="third-party-updates-on-windows"></a>Mises à jour tierces sur Windows

Update Management s’appuie sur le référentiel de mise à jour configuré localement pour mettre à jour les systèmes Windows pris en charge : WSUS ou Windows Update. Des outils, tels que l’[éditeur de mise à jour System Center ](/configmgr/sum/tools/updates-publisher), vous permettent d’importer et de publier des mises à jour personnalisées avec WSUS. Ce scénario permet à Update Management de mettre à jour des machines qui utilisent Configuration Manager comme référentiel de mise à jour avec des logiciels tiers. Pour savoir comment configurer l’éditeur de mise à jour, consultez [Installer l’éditeur de mise à jour](/configmgr/sum/tools/install-updates-publisher).

## <a name="next-steps"></a>Étapes suivantes

* Avant d’activer et d’utiliser Update Management, consultez [Planifier votre déploiement Update Management](plan-deployment.md).

* Consultez les questions fréquemment posées au sujet d’Update Management sur le [forum aux questions Azure Automation](../automation-faq.md).