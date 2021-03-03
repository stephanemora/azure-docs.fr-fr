---
title: Azure Automanage pour Windows Server
description: Découvrez les bonnes pratiques Azure Automanage relatives aux machines virtuelles pour les services automatiquement intégrés et configurés sur les machines Windows Server.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: deanwe
ms.openlocfilehash: a3bb0a2877c71d19b05f424dc44e302c69577b2d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101660201"
---
# <a name="azure-automanage-for-virtual-machines-best-practices---windows-server"></a>Bonnes pratiques Azure Automanage pour les machines virtuelles - Windows Server

Ces services Azure sont automatiquement intégrés quand vous utilisez Automanage pour les machines virtuelles sur une machine virtuelle Windows Server. Ils sont essentiels pour notre livre blanc sur les meilleures pratiques, que vous pouvez trouver dans notre [Cloud Adoption Framework](/azure/cloud-adoption-framework/manage/azure-server-management).

Nous allons intégrer et configurer automatiquement tous ces services, superviser leur fonctionnement et apporter les corrections nécessaires en cas de dérive. Pour en savoir plus sur ce processus, consultez [Azure Automanage pour machines virtuelles](automanage-virtual-machines.md).

## <a name="supported-windows-server-versions"></a>Versions de Windows Server prises en charge

Automanage prend en charge les versions suivantes de Windows Server :

- Windows Server 2012/R2
- Windows Server 2016
- Windows Server 2019
- Windows Server 2019 Azure Edition

## <a name="participating-services"></a>Services participant

|Service    |Description    |Environnements pris en charge<sup>1</sup>    |Préférences prises en charge<sup>1</sup>    |
|-----------|---------------|----------------------|-------------------------|
|Surveillance des insights de machine virtuelle    |Azure Monitor pour machines virtuelles surveille les performances et l’intégrité de vos machines virtuelles, y compris de leurs processus en cours d’exécution et de leurs dépendances vis-à-vis d’autres ressources. En savoir [plus](../azure-monitor/vm/vminsights-overview.md).    |Production    |Non    |
|Backup    |Le service Sauvegarde Azure propose des sauvegardes indépendantes et isolées pour éviter une destruction involontaire des données de vos machines virtuelles. En savoir [plus](../backup/backup-azure-vms-introduction.md). Les frais sont calculés en fonction du nombre et de la taille des machines virtuelles protégées. En savoir [plus](https://azure.microsoft.com/pricing/details/backup/).    |Production    |Oui    |
|Azure Security Center    |Azure Security Center est un système de gestion de la sécurité de l’infrastructure unifié qui renforce la posture de sécurité de vos centres de données et fournit une protection avancée contre les menaces pour vos charges de travail hybrides dans le cloud. En savoir [plus](../security-center/security-center-introduction.md).  Automanage configure l’abonnement dans lequel votre machine virtuelle réside en offre de niveau gratuit d’Azure Security Center. Si votre abonnement est déjà intégré avec Azure Security Center, Automanage ne le reconfigure pas.    |Production, Dev/Test    |Non    |
|Microsoft Antimalware    |Microsoft Antimalware pour Azure offre une protection en temps réel qui permet d’identifier et de supprimer les virus, logiciels espions et autres logiciels malveillants. Il fournit des alertes lorsqu’un logiciel malveillant ou indésirable connu essaye de s’installer ou de s’exécuter sur vos systèmes Azure. En savoir [plus](../security/fundamentals/antimalware.md). |Production, Dev/Test    |Oui    |
|Update Management    |Vous pouvez utiliser la solution Update Management d’Azure Automation pour gérer les mises à jour du système d’exploitation de vos machines virtuelles. Vous pouvez rapidement évaluer l’état des mises à jour disponibles sur toutes les machines d’agent et gérer le processus d’installation des mises à jour nécessaires pour les serveurs. En savoir [plus](../automation/update-management/overview.md).    |Production, Dev/Test    |Non    |
|Suivi des modifications et inventaire    |La fonctionnalité Suivi des modifications et inventaire combine les fonctions de suivi des modifications et d’inventaire pour vous permettre d’effectuer le suivi des modifications apportées à l’infrastructure des machines virtuelles et des serveurs. Le service prend en charge le suivi des modifications sur les services, les démons, les logiciels, le registre et les fichiers de votre environnement pour vous aider à diagnostiquer les changements indésirables et à déclencher des alertes. La prise en charge de l’inventaire vous permet d’interroger les ressources intégrées pour voir les applications installées et d’autres éléments de configuration.  En savoir [plus](../automation/change-tracking/overview.md).    |Production, Dev/Test    |Non    |
|Configuration d’invité Azure    | La stratégie Guest Configuration est utilisée pour superviser la configuration et signaler la conformité de la machine. Le service de gestion automatique installe les [lignes de base de sécurité Windows](/windows/security/threat-protection/windows-security-baselines) à l’aide de l’extension de configuration invitée. Pour les machines Windows, le service Guest Configuration réapplique automatiquement les paramètres de base de référence s’ils ne sont pas conformes. En savoir [plus](../governance/policy/concepts/guest-configuration.md).    |Production, Dev/Test    |Non    |
|Compte Azure Automation    |Azure Automation prend en charge la gestion tout au long du cycle de vie de votre infrastructure et des applications. En savoir [plus](../automation/automation-intro.md).    |Production, Dev/Test    |Non    |
|Espace de travail Log Analytics    |Azure Monitor stocke des données de journal dans un espace de travail Log Analytics, qui est une ressource Azure et un conteneur où les données sont collectées, agrégées, et qui sert de limite d’administration. En savoir [plus](../azure-monitor/logs/design-logs-deployment.md).    |Production, Dev/Test    |Non    |


<sup>1</sup> La sélection de l’environnement est disponible quand vous activez Automanage. En savoir [plus](automanage-virtual-machines.md#environment-configuration). Vous pouvez également modifier les paramètres par défaut de l’environnement, et définir vos propres préférences dans le respect des bonnes pratiques.


## <a name="next-steps"></a>Étapes suivantes

Essayez d’activer le service Automanage pour machines virtuelles dans le portail Azure.

> [!div class="nextstepaction"]
> [Activer le service Automanage pour machines virtuelles dans le portail Azure](quick-create-virtual-machines-portal.md)