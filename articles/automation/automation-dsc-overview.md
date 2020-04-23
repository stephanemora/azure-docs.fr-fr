---
title: Vue d’ensemble d’Azure Automation State Configuration
description: Cet article est une vue d’ensemble d’Azure Automation State Configuration, des termes s’y rapportant et de ses problèmes connus
keywords: PowerShell DSC, Desired State Configuration, configuration d'état souhaité PowerShell DSC Azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 04/15/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1166f5a1d7586c54255120a656b060c93f842fd9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406185"
---
# <a name="state-configuration-overview"></a>Présentation de la configuration de l’état

Azure Automation State Configuration est un service Azure qui vous permet d’écrire, de gérer et de compiler des [configurations](/powershell/scripting/dsc/configurations/configurations) PowerShell Desired State Configuration (DSC). Le service importe également des [ressources DSC](/powershell/scripting/dsc/resources/resources) et affecte des configurations aux nœuds cibles, le tout dans le cloud.

## <a name="why-use-azure-automation-state-configuration"></a>Pourquoi utiliser Azure Automation State Configuration ?

Azure Automation State Configuration offre plusieurs avantages par rapport à l’utilisation de DSC en dehors d’Azure.

### <a name="built-in-pull-server"></a>Serveur collecteur intégré

Azure Automation State Configuration fournit un serveur Pull DSC similaire au serveur [Windows Feature DSC-Service](/powershell/scripting/dsc/pull-server/pullserver). Les nœuds cibles peuvent recevoir automatiquement les configurations, se conformer à l’état souhaité et renvoyer un rapport attestant de leur conformité. Le serveur collecteur intégré dans Azure Automation vous permet d’éviter d’avoir à configurer et à gérer votre propre serveur collecteur. Azure Automation peut cibler des machines physiques ou virtuelles Windows ou Linux, dans le cloud ou en local.

### <a name="manage-all-your-dsc-artifacts"></a>Gérer tous vos artefacts DSC

Azure Automation State Configuration apporte la même couche de gestion à la configuration [PowerShell Desired State Configuration](/powershell/scripting/dsc/overview/overview) que celle qu’il propose pour l’écriture de scripts PowerShell. À partir du Portail Azure ou de PowerShell, vous pouvez gérer toutes vos configurations DSC, vos ressources et vos nœuds cibles.

![Capture d’écran de la page Azure Automation](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-azure-monitor-logs"></a>Importer des données de rapport dans les journaux Azure Monitor

Les nœuds gérés dans Azure Automation State Configuration envoient des données détaillées sur l’état de création de rapports au serveur collecteur intégré. Vous pouvez configurer Azure Automation State Configuration de façon à envoyer ces données à votre espace de travail Log Analytics. Pour plus d’informations, consultez [Transférer des données de rapport Azure Automation State Configuration vers des journaux Azure Monitor](automation-dsc-diagnostics.md).

## <a name="prerequisites"></a>Prérequis

Prenez en compte les exigences suivantes lors de l’utilisation d’Azure Automation State Configuration pour DSC.

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
>La référence SKU due produit autonome [Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) ne contient pas d’implémentation de Desired State Configuration, elle ne peut pas être gérée par PowerShell DSC ni par Azure Automation State Configuration.

Pour les nœuds exécutant Linux, l’extension Linux DSC prend en charge toutes les distributions Linux listées sous [Distributions Linux prises en charge](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions).

### <a name="dsc-requirements"></a>Configuration requise de DSC

Pour tous les nœuds Windows s’exécutant dans Azure, [Windows Management Framework 5.1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure) est installé lors de l’intégration. Pour les nœuds exécutant Windows Server 2012 et Windows 7, [WinRM](https://docs.microsoft.com/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency) est activé.

Pour tous les nœuds Linux s’exécutant dans Azure, [PowerShell DSC pour Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) est installé lors de l’intégration.

### <a name="configuration-of-private-networks"></a><a name="network-planning"></a>Configuration de réseaux privés

Si vos nœuds se trouvent dans un réseau privé, le port et les URL suivants sont requis. Ces ressources fournissent une connectivité réseau pour le nœud géré et permettent à DSC de communiquer avec Azure Automation.

* Port : seul le port TCP 443 est nécessaire pour l’accès Internet sortant
* URL globale : * **.azure-automation.net**
* URL globale d’US Gov Virginie : * **.azure-automation.us**
* Service de l’agent : **https://\<ID_espace_de_travail\>.agentsvc.azure-automation.net**

Si vous utilisez des ressources DSC qui communiquent entre des nœuds, comme des [ressources WaitFor*](https://docs.microsoft.com/powershell/scripting/dsc/reference/resources/windows/waitForAllResource), vous devez également autoriser la communication entre les nœuds. Pour connaître la configuration réseau requise, consultez la documentation de chaque ressource DSC.

#### <a name="proxy-support"></a>Prise en charge du proxy

La prise en charge de proxy pour l’agent DSC est disponible dans Windows version 1809 et versions ultérieures. Vous activez cette option en définissant les valeurs pour `ProxyURL` et `ProxyCredential` dans le [script de métaconfiguration](automation-dsc-onboarding.md#generate-dsc-metaconfigurations) utilisé pour inscrire des nœuds.

>[!NOTE]
>Azure Automation State Configuration ne prend pas en charge le proxy DSC pour les versions antérieures de Windows.

Pour les nœuds Linux, l’agent DSC prend en charge un serveur proxy et utilise la variable `http_proxy` pour spécifier l’URL.

#### <a name="azure-automation-state-configuration-network-ranges-and-namespace"></a>Plages de réseaux et espace de noms Azure Automation State Configuration

Quand vous définissez des exceptions, nous vous recommandons d’utiliser les adresses IP listées dans le tableau suivant. Pour les adresses IP, vous pouvez télécharger le fichier XML des [plages d’adresses IP des centres de données Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653) auprès du Centre de téléchargement Microsoft. Ce fichier contient les plages actuellement déployées et toutes les modifications à venir dans les plages IP. Elle est mise à jour chaque semaine.

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

> [!NOTE]
> Le fichier XML Azure Datacenter IP address répertorie les plages d’adresses IP qui sont utilisées dans les centres de données Microsoft Azure. Le fichier comprend les plages Compute, SQL et Stockage.
>
>Un fichier mis à jour est publié chaque semaine. Le fichier reflète les plages déployées et toutes les modifications à venir dans les plages IP. Les nouvelles plages figurant dans le fichier ne sont pas utilisées dans les centres de données avant une semaine minimum. Pensez à télécharger un nouveau fichier XML chaque semaine. Vous pouvez ensuite mettre à jour votre site afin qu’il identifie correctement les services qui s’exécutent dans Azure. 

Si vous êtes utilisateur d’Azure ExpressRoute, notez que ce fichier est utilisé pour mettre à jour la publication BGP (Border Gateway Protocol) de l’espace Azure la première semaine de chaque mois.

## <a name="next-steps"></a>Étapes suivantes

- Pour commencer à utiliser DSC dans Azure Automation State Configuration, consultez [Bien démarrer avec Azure Automation State Configuration](automation-dsc-getting-started.md).
- Pour découvrir comment intégrer des nœuds, consultez [Intégrer des machines pour la gestion avec Azure Automation State Configuration](automation-dsc-onboarding.md).
- Pour découvrir comment compiler des configurations DSC pour pouvoir les affecter à des nœuds cibles, consultez [Compiler des configurations dans Azure Automation State Configuration](automation-dsc-compile.md).
- Pour obtenir des informations de référence sur les applets de commande PowerShell, consultez [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation).
- Pour obtenir des informations sur les prix, consultez [Tarification d’Azure Automation State Configuration](https://azure.microsoft.com/pricing/details/automation/).
- Pour obtenir un exemple d’utilisation d’Azure Automation State Configuration dans un pipeline de déploiement continu, consultez [Déploiement continu sur des machines virtuelles à l’aide d’Azure Automation State Configuration et de Chocolatey](automation-dsc-cd-chocolatey.md).
