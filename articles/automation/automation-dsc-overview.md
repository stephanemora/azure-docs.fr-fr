---
title: Vue d’ensemble d’Azure Automation State Configuration
description: Vue d’ensemble d’Azure Automation State Configuration, des termes s’y rapportant et des problèmes connus
keywords: PowerShell DSC, Desired State Configuration, configuration d'état souhaité PowerShell DSC Azure
services: automation
ms.service: automation
ms.component: dsc
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d2d46803f81b369f8f24a6f0e3c7f32955503e4a
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/09/2018
ms.locfileid: "40006059"
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

![Capture d’écran du panneau Azure Automation](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-log-analytics"></a>Importer des données de création de rapports dans Log Analytics

Les nœuds gérés dans Azure Automation State Configuration envoient des données détaillées sur l’état de création de rapports au serveur collecteur intégré. Vous pouvez configurer Azure Automation State Configuration de façon à envoyer ces données à votre espace de travail Log Analytics. Pour savoir comment envoyer des données d’état State Configuration à votre espace de travail Log Analytics, consultez [Transférer des données de rapport Azure Automation State Configuration à Log Analytics](automation-dsc-diagnostics.md).

## <a name="introduction-video"></a>Vidéo de présentation

Lire de la documentation vous enchante moyennement ? Jetez un œil à la vidéo ci-dessous, publiée en mai 2015 à l’occasion de l’annonce d’Azure Automation State Configuration.

> [!NOTE]
> Bien que les concepts et le cycle de vie abordés dans cette vidéo soient corrects, Azure Automation State Configuration a beaucoup progressé depuis l’enregistrement de cette vidéo. Désormais disponible au public, il dispose d’une interface utilisateur plus étendue dans le portail Azure et prend en charge des fonctionnalités supplémentaires.

[!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3467/player]

## <a name="next-steps"></a>Étapes suivantes

- Pour commencer, consultez [Bien démarrer avec Azure Automation State Configuration](automation-dsc-getting-started.md)
- Pour savoir comment intégrer des nœuds, consultez [Intégration de machines pour la gestion avec Azure Automation State Configuration](automation-dsc-onboarding.md)
- Pour savoir comment compiler des configurations DSC pour les affecter à des nœuds cibles, consultez [Compilation de configurations dans Azure Automation State Configuration](automation-dsc-compile.md).
- Pour obtenir des informations de référence sur les applets de commande PowerShell, consultez [Applets de commande Azure Automation State Configuration](/powershell/module/azurerm.automation/#automation).
- Pour obtenir des informations sur les prix, consultez [Prix associés à Azure Automation State Configuration](https://azure.microsoft.com/pricing/details/automation/).
- Pour voir un exemple d’utilisation d’Azure Automation State Configuration dans un pipeline de déploiement continu, consultez [Déploiement continu à l’aide d’Azure Automation State Configuration et de Chocolatey](automation-dsc-cd-chocolatey.md).