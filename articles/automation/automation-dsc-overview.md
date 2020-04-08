---
title: Vue d’ensemble d’Azure Automation State Configuration
description: Vue d’ensemble d’Azure Automation State Configuration, des termes s’y rapportant et des problèmes connus
keywords: PowerShell DSC, Desired State Configuration, configuration d'état souhaité PowerShell DSC Azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 46cf0d6a12ffbc836db7bd79c0f2738a94e23085
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283189"
---
# <a name="azure-automation-state-configuration-overview"></a>Vue d’ensemble d’Azure Automation State Configuration

Azure Automation State Configuration est un service Azure qui vous permet d’écrire, de gérer et de compiler des [configurations](/powershell/scripting/dsc/configurations/configurations) PowerShell Desired State Configuration (DSC). Le service importe également des [ressources DSC](/powershell/scripting/dsc/resources/resources) et attribue des configurations aux nœuds cibles, le tout dans le cloud.

## <a name="why-use-azure-automation-state-configuration"></a>Pourquoi utiliser Azure Automation State Configuration

Azure Automation State Configuration offre plusieurs avantages par rapport à l’utilisation de DSC en dehors d’Azure.

### <a name="built-in-pull-server"></a>Serveur collecteur intégré

Azure Automation State Configuration fournit un serveur Pull DSC similaire au serveur [Windows Feature DSC-Service](/powershell/scripting/dsc/pull-server/pullserver). Les nœuds cibles peuvent recevoir automatiquement les configurations, se conformer à l’état souhaité et renvoyer un rapport attestant de leur conformité. Le serveur collecteur intégré dans Azure Automation vous permet d’éviter d’avoir à configurer et à gérer votre propre serveur collecteur. Azure Automation peut cibler des machines physiques ou virtuelles Windows ou Linux, dans le cloud ou en local.

### <a name="management-of-all-your-dsc-artifacts"></a>Gestion de tous vos artefacts DSC

Azure Automation State Configuration apporte la même couche de gestion à la configuration [PowerShell Desired State Configuration](/powershell/scripting/dsc/overview/overview) que celle qu’il propose pour l’écriture de scripts PowerShell. À partir du Portail Azure ou de PowerShell, vous pouvez gérer toutes vos configurations DSC, vos ressources et vos nœuds cibles.

![Capture d’écran de la page Azure Automation](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-of-reporting-data-into-azure-monitor-logs"></a>Importer des données de création de rapports dans les journaux d’activité Azure Monitor

Les nœuds gérés dans Azure Automation State Configuration envoient des données détaillées sur l’état de création de rapports au serveur collecteur intégré. Vous pouvez configurer Azure Automation State Configuration de façon à envoyer ces données à votre espace de travail Log Analytics. Consultez [Transférer des données de création de rapports Azure Automation State Configuration vers des journaux d’activité Azure Monitor](automation-dsc-diagnostics.md).

## <a name="prerequisites-for-using-azure-automation-state-configuration"></a>Conditions préalables à l’utilisation d’Azure Automation State Configuration

Veuillez prendre en compte les exigences suivantes lors de l’utilisation d’Azure Automation State Configuration pour DSC.

### <a name="operating-system-requirements"></a>Système d'exploitation requis

Pour les nœuds exécutant Windows, les versions suivantes sont prises en charge :

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

>[!NOTE]
>La référence SKU du produit autonome [Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) ne contient pas d’implémentation de DSC. Par conséquent, il ne peut pas être géré par PowerShell DSC ni par Azure Automation State Configuration.

Pour les nœuds exécutant Linux, l’extension Linux DSC prend en charge toutes les distributions Linux listées sous [Distributions Linux prises en charge](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions).

### <a name="dsc-requirements"></a>Configuration requise de DSC

Pour tous les nœuds Windows s’exécutant dans Azure, [WMF 5.1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure) est installé lors de l’intégration. Pour les nœuds exécutant Windows Server 2012 et Windows 7, [WinRM](https://docs.microsoft.com/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency) est activé.

Pour tous les nœuds Linux s’exécutant dans Azure, [PowerShell DSC pour Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) est installé lors de l’intégration.

### <a name="configuration-of-private-networks"></a><a name="network-planning"></a>Configuration de réseaux privés

Si vos nœuds se trouvent dans un réseau privé, le port et les URL suivants sont requis. Ces ressources fournissent une connectivité réseau pour le nœud géré et permettent à DSC de communiquer avec Azure Automation.

* Port : seul le port TCP 443 est nécessaire pour l’accès Internet sortant
* URL globale : * **.azure-automation.net**
* URL globale d’US Gov Virginie : * **.azure-automation.us**
* Service de l’agent : **https://\<ID_espace_de_travail\>.agentsvc.azure-automation.net**

Si vous utilisez des ressources DSC qui communiquent entre des nœuds, telles que les [ressources WaitFor*](https://docs.microsoft.com/powershell/scripting/dsc/reference/resources/windows/waitForAllResource), vous devez également autoriser le trafic entre les nœuds. Pour connaître la configuration réseau requise, consultez la documentation de chaque ressource DSC.

#### <a name="proxy-support"></a>Prise en charge du proxy

La prise en charge de proxy pour l’agent DSC est disponible dans Windows version 1809 et versions ultérieures. Cette option est activée en définissant les valeurs pour `ProxyURL` et `ProxyCredential` dans le [script de métaconfiguration](automation-dsc-onboarding.md#generating-dsc-metaconfigurations) utilisé pour inscrire des nœuds.

>[!NOTE]
>Azure Automation State Configuration ne prend pas en charge le proxy DSC pour les anciennes versions de Windows.

Pour les nœuds Linux, l’agent DSC prend en charge le proxy et utilise la variable `http_proxy` pour déterminer l’URL.

#### <a name="azure-automation-state-configuration-network-ranges-and-namespace"></a>Plages de réseaux et espace de noms Azure Automation State Configuration

Il est recommandé d’utiliser les adresses répertoriées ci-dessous lors de la définition des exceptions. Pour les adresses IP, vous pouvez télécharger les [plages d’adresses IP du centre de données Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Ce fichier, qui est mis à jour chaque semaine, possède les plages actuellement déployées et tous les changements à venir des plages d’adresses IP.

Si vous avez un compte Automation défini pour une région spécifique, vous pouvez limiter les communications à ce centre de données régional. Le tableau suivant indique l’enregistrement DNS pour chaque région :

| **Région** | **Enregistrement DNS** |
| --- | --- |
| Centre-USA Ouest | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| États-Unis - partie centrale méridionale |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| USA Est    | eus-jobruntimedata-prod-su1.azure-automation.net</br>eus-agentservice-prod-1.azure-automation.net |
| USA Est 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Centre du Canada |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Europe Ouest |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Europe Nord |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Asie Sud-Est |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Inde centrale |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Japon Est |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Sud-Est de l’Australie |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Sud du Royaume-Uni | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| Gouvernement américain - Virginie | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

Pour obtenir la liste des adresses IP régionales plutôt que celle des noms des régions, téléchargez le fichier XML [Azure Datacenter IP address](https://www.microsoft.com/download/details.aspx?id=41653) à partir du Centre de téléchargement Microsoft.

> [!NOTE]
> Le fichier XML Azure Datacenter IP address répertorie les plages d’adresses IP qui sont utilisées dans les centres de données Microsoft Azure. Le fichier comprend les plages Compute, SQL et Stockage.
>
>Un fichier mis à jour est publié chaque semaine. Le fichier reflète les plages déployées et toutes les modifications à venir dans les plages IP. Les nouvelles plages figurant dans le fichier ne sont pas utilisées dans les centres de données avant une semaine minimum. Pensez à télécharger le nouveau fichier XML chaque semaine. Ensuite, mettez à jour votre site afin qu’il identifie correctement les services qui s’exécutent dans Azure. 

Les utilisateurs d’Azure ExpressRoute doivent noter que ce fichier est utilisé pour mettre à jour la publication BGP (Border Gateway Protocol) de l’espace Azure la première semaine de chaque mois.

## <a name="next-steps"></a>Étapes suivantes

- Pour prendre en main l’utilisation de DSC dans Azure Automation State Configuration, consultez [Prise en main d’Azure Automation State Configuration](automation-dsc-getting-started.md).
- Pour savoir comment intégrer des nœuds, consultez [Intégration des machines pour la gestion avec Azure Automation State Configuration](automation-dsc-onboarding.md).
- Pour savoir comment compiler des configurations DSC pour les attribuer à des nœuds cibles, consultez [Compilation de configurations dans Azure Automation State Configuration](automation-dsc-compile.md).
- Pour obtenir des informations de référence sur les cmdlets PowerShell, consultez [Cmdlets Azure Automation State Configuration](/powershell/module/azurerm.automation/#automation).
- Pour obtenir des informations sur les prix, consultez [Tarification d’Azure Automation State Configuration](https://azure.microsoft.com/pricing/details/automation/).
- Pour voir un exemple d’utilisation d’Azure Automation State Configuration dans un pipeline de déploiement continu, consultez [Déploiement continu à l’aide d’Azure Automation State Configuration et de Chocolatey](automation-dsc-cd-chocolatey.md).
