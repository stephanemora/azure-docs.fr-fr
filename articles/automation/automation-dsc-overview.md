---
title: Vue d’ensemble d’Azure Automation State Configuration
description: Vue d’ensemble d’Azure Automation State Configuration, des termes s’y rapportant et des problèmes connus
keywords: PowerShell DSC, Desired State Configuration, configuration d'état souhaité PowerShell DSC Azure
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: b486c30827ee67b58cbdc0027c8221cceed02e51
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235940"
---
# <a name="azure-automation-state-configuration-overview"></a>Vue d’ensemble d’Azure Automation State Configuration

Azure Automation State Configuration est un service Azure qui vous permet d’écrire, de gérer et de compiler des [configurations](/powershell/dsc/configurations) PowerShell Desired State Configuration (DSC), d’importer des [ressources DSC](/powershell/dsc/resources) et d’affecter des configurations à des nœuds cibles, le tout dans le cloud.

## <a name="why-use-azure-automation-state-configuration"></a>Pourquoi utiliser Azure Automation State Configuration

Azure Automation State Configuration offre plusieurs avantages par rapport à l’utilisation de DSC en dehors d’Azure.

### <a name="built-in-pull-server"></a>Serveur collecteur intégré

Azure Automation State Configuration fournit un serveur collecteur DSC semblable à celui de [Windows Feature DSC-Service](/powershell/dsc/pullserver). Les nœuds cibles reçoivent donc automatiquement les configurations, sont conformes à l’état souhaité et présentent un rapport attestant de leur conformité. Le serveur collecteur intégré dans Azure Automation vous permet d’éviter d’avoir à configurer et à gérer votre propre serveur collecteur. Azure Automation peut cibler des machines physiques ou virtuelles Windows ou Linux, dans le cloud ou en local.

### <a name="management-of-all-your-dsc-artifacts"></a>Gestion de tous vos artefacts DSC

Azure Automation State Configuration apporte la même couche de gestion à la [configuration d’état souhaité PowerShell](/powershell/dsc/overview) que celle proposée par Azure Automation pour l’écriture de scripts PowerShell.

À partir du portail Azure, ou de PowerShell, vous pouvez gérer toutes vos configurations DSC, vos ressources et vos nœuds cibles.

![Capture d’écran de la page Azure Automation](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-azure-monitor-logs"></a>Importer des données de création de rapports dans les journaux Azure Monitor

Les nœuds gérés dans Azure Automation State Configuration envoient des données détaillées sur l’état de création de rapports au serveur collecteur intégré. Vous pouvez configurer Azure Automation State Configuration de façon à envoyer ces données à votre espace de travail Log Analytics. Pour savoir comment envoyer des données d’état de Configuration d’état à votre espace de travail Analytique de journal, consultez [transférer Azure Automation la Configuration d’état données de rapport dans les journaux d’Azure Monitor](automation-dsc-diagnostics.md).

## <a name="prerequisites"></a>Conditions préalables

Veuillez prendre en compte les exigences suivantes lors de l’utilisation d’Azure Automation état Configuration (DSC).

### <a name="operating-system-requirements"></a>Configuration requise du système d’exploitation

Pour les nœuds exécutant Windows, les versions suivantes sont prises en charge :

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

Pour les nœuds Linux en cours d’exécution, les distributions/versions suivantes sont prises en charge :

L’extension DSC Linux prend en charge toutes les distributions Linux [approuvées sur Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) , à l’exception :

Distribution | Version
-|-
Debian  | toutes les versions
Ubuntu  | 18,04

### <a name="dsc-requirements"></a>Exigences de DSC

Pour tous les nœuds de Windows s’exécutant dans Azure, [WMF 5.1](https://docs.microsoft.com/powershell/wmf/setup/install-configure) sera installé lors de l’intégration.  Pour les nœuds exécutant Windows Server 2012 et Windows 7, [sera activé WinRM](https://docs.microsoft.com/powershell/dsc/troubleshooting/troubleshooting#winrm-dependency).

Pour tous les nœuds Linux s’exécutant dans Azure, [DSC PowerShell pour Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) sera installé lors de l’intégration.

### <a name="network-planning"></a>Configurer des réseaux privés

Si vos nœuds sont situés dans un réseau privé, le port et les URL suivantes sont requises pour état de Configuration (DSC) pour communiquer avec Automation :

* Port : seul le port TCP 443 est nécessaire pour l’accès Internet sortant.
* URL globale : *.azure-automation.net
* URL globale d’US Gov Virginie : *.azure-automation.us
* Service de l’agent : https://\<ID_espace_de_travail\>.agentsvc.azure-automation.net

#### <a name="proxy-support"></a>Prise en charge du proxy

Prise en charge de proxy pour l’agent DSC est disponible dans Windows version 1809 et versions ultérieure.
Pour configurer cette option, affectez la valeur de **ProxyURL** et **ProxyCredential** dans le [métaconfiguration script](automation-dsc-onboarding.md#generating-dsc-metaconfigurations) utilisé pour enregistrer des nœuds.
Proxy n’est pas disponible dans DSC pour les versions précédentes de Windows.

Pour les nœuds Linux, l’agent DSC prend en charge de proxy et utilisera la variable http_proxy pour déterminer l’url.

#### <a name="azure-state-configuration-network-ranges-and-namespace"></a>Espace de noms et les plages de réseau de Configuration état azure

Il est recommandé d’utiliser les adresses répertoriées lors de la définition des exceptions. Pour les adresses IP, vous pouvez télécharger les [Plages d’adresses IP du centre de données Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Ce fichier, qui est mis à jour chaque semaine, possède les plages actuellement déployées et tous les changements à venir des plages d’adresses IP.

Si vous avez un compte Automation défini pour une région spécifique, vous pouvez limiter les communications à ce centre de données régional. Le tableau suivant indique l’enregistrement DNS pour chaque région :

| **Région** | **Enregistrement DNS** |
| --- | --- |
| USA Centre-Ouest | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| USA Centre Sud |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| USA Est 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Centre du Canada |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Europe Ouest |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Europe Nord |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Asie Sud-Est |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Inde Centre |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Japon Est |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Sud-Est de l’Australie |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Sud du Royaume-Uni | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| Gouvernement américain - Virginie | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

Pour obtenir la liste des adresses IP régionales plutôt que celle des noms des régions, téléchargez le fichier XML [Azure Datacenter IP address](https://www.microsoft.com/download/details.aspx?id=41653) à partir du Centre de téléchargement Microsoft.

> [!NOTE]
> Le fichier XML Azure Datacenter IP address répertorie les plages d’adresses IP qui sont utilisées dans les centres de données Microsoft Azure. Le fichier comprend les plages Compute, SQL et Stockage.
>
>Un fichier mis à jour est publié chaque semaine. Le fichier reflète les plages déployées et toutes les modifications à venir dans les plages IP. Les nouvelles plages figurant dans le fichier ne sont pas utilisées dans les centres de données avant une semaine minimum.
>
> Pensez à télécharger le nouveau fichier XML chaque semaine. Ensuite, mettez à jour votre site afin qu’il identifie correctement les services en cours d’exécution dans Azure. Les utilisateurs d’Azure ExpressRoute doivent noter que ce fichier est utilisé pour mettre à jour la publication BGP (Border Gateway Protocol) de l’espace Azure la première semaine de chaque mois.

## <a name="introduction-video"></a>Vidéo de présentation

Lire de la documentation vous enchante moyennement ? Jetez un œil à la vidéo ci-dessous, publiée en mai 2015 à l’occasion de l’annonce d’Azure Automation State Configuration.

> [!NOTE]
> Bien que les concepts et le cycle de vie abordés dans cette vidéo soient corrects, Azure Automation State Configuration a beaucoup progressé depuis l’enregistrement de cette vidéo. Désormais disponible au public, il dispose d’une interface utilisateur plus étendue dans le portail Azure et prend en charge des fonctionnalités supplémentaires.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3467/player]

## <a name="next-steps"></a>Étapes suivantes

- Pour commencer, consultez [Prise en main d’Azure Automation State Configuration](automation-dsc-getting-started.md)
- Pour savoir comment intégrer des nœuds, consultez [Intégration de machines pour la gestion avec Azure Automation State Configuration](automation-dsc-onboarding.md)
- Pour savoir comment compiler des configurations DSC pour les affecter à des nœuds cibles, consultez [Compilation de configurations dans Azure Automation State Configuration](automation-dsc-compile.md).
- Pour obtenir des informations de référence sur les applets de commande PowerShell, consultez [Applets de commande d’Azure Automation State Configuration](/powershell/module/azurerm.automation/#automation)
- Pour obtenir des informations sur les prix, consultez [Tarification d’Azure Automation State Configuration](https://azure.microsoft.com/pricing/details/automation/)
- Pour voir un exemple d’utilisation d’Azure Automation State Configuration dans un pipeline de déploiement continu, consultez [Déploiement continu à l’aide d’Azure Automation State Configuration et de Chocolatey](automation-dsc-cd-chocolatey.md)
