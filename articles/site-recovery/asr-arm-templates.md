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
ms.openlocfilehash: 0477045ac48ee2746e3d6a1dd95051673412ffee
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99551089"
---
# <a name="azure-resource-manager-templates-for-azure-site-recovery"></a>Modèles Azure Resource Manager pour Azure Site Recovery

Le tableau suivant inclut des liens vers des modèles Azure Resource Manager pour l’utilisation des fonctionnalités d’Azure Site Recovery.

| Modèle | Description |
|---|---|
|**Azure vers Azure** | |
| [Créer un coffre Recovery Services](https://docs.microsoft.com/azure/site-recovery/quickstart-create-vault-template)| Créez un coffre Recovery Services. Le coffre peut être utilisé pour la Sauvegarde Azure et Azure Site Recovery. |
| [Activer la réplication des machines virtuelles Azure](https://aka.ms/asr-arm-enable-replication) | Activez la réplication pour les machines virtuelles Azure à l’aide du coffre existant et des paramètres cible personnalisés.|
| [Déclencher le basculement et la reprotection](https://aka.ms/asr-arm-failover-reprotect) | Déclenchez une opération de basculement et de reprotection pour un ensemble de machines virtuelles Azure. |
| [Exécuter un flux de récupération d’urgence de bout en bout pour les machines virtuelles Azure](https://aka.ms/asr-arm-e2e-flow) | Démarrez un flux complet de récupération d’urgence de bout en bout (activer réplication + basculement, reprotection + basculement et reprotection) pour les machines virtuelles Azure, également appelé « Flux 540° ».|
|   |   |