---
title: Modèles Azure Resource Manager
description: Modèles Azure Resource Manager pour l’utilisation des fonctionnalités d’Azure Site Recovery.
services: site-recovery
author: rishjai
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 02/04/2021
ms.author: rishjai
ms.openlocfilehash: efd5c1ab3e23348373be76c79d1b9689bc5f4941
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101720526"
---
# <a name="azure-resource-manager-templates-for-azure-site-recovery"></a>Modèles Azure Resource Manager pour Azure Site Recovery

Le tableau suivant inclut des liens vers des modèles Azure Resource Manager pour l’utilisation des fonctionnalités d’Azure Site Recovery.

| Modèle | Description |
|---|---|
|**Azure vers Azure** | |
| [Créer un coffre Recovery Services](./quickstart-create-vault-template.md)| Créez un coffre Recovery Services. Le coffre peut être utilisé pour la Sauvegarde Azure et Azure Site Recovery. |
| [Activer la réplication des machines virtuelles Azure](https://aka.ms/asr-arm-enable-replication) | Activez la réplication pour les machines virtuelles Azure à l’aide du coffre existant et des paramètres cible personnalisés.|
| [Déclencher le basculement et la reprotection](https://aka.ms/asr-arm-failover-reprotect) | Déclenchez une opération de basculement et de reprotection pour un ensemble de machines virtuelles Azure. |
| [Exécuter un flux de récupération d’urgence de bout en bout pour les machines virtuelles Azure](https://aka.ms/asr-arm-e2e-flow) | Démarrez un flux complet de récupération d’urgence de bout en bout (activer réplication + basculement, reprotection + basculement et reprotection) pour les machines virtuelles Azure, également appelé « Flux 540° ».|
|   |   |