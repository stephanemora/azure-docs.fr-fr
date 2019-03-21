---
title: Collecter des données d’Azure ATP dans Azure en version préliminaire Sentinel | Microsoft Docs
description: Découvrez comment collecter des données d’Azure ATP dans Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 5bf3cc44-ecda-4c78-8a63-31ab42f43605
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2019
ms.author: rkarlin
ms.openlocfilehash: 154af3988084792331db082b99cae0ae06126f1b
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2019
ms.locfileid: "57242171"
---
# <a name="collect-data-from-azure-advanced-threat-protection-atp"></a>Collecter des données à partir d’Azure Advanced Threat Protection (ATP)

> [!IMPORTANT]
> Sentinel Azure est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Vous pouvez diffuser des journaux à partir de [Azure-Protection avancée contre les menaces](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) dans Azure Sentinel avec un seul clic.

## <a name="prerequisites"></a>Conditions préalables

- Utilisateur avec l’administrateur général ou d’autorisations d’administrateur de sécurité
- Vous devez être un client de la préversion privée d’Azure ATP

## <a name="connect-to-azure-atp"></a>Se connecter à Azure ATP

Assurez-vous que la version de la version préliminaire privée Azure ATP est [activé sur votre réseau](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1).
Si Azure ATP est déployé et l’ingestion de vos données, les alertes suspectes peuvent facilement être envoyés à Azure Sentinel. Il peut prendre jusqu'à 24 heures pour les alertes démarrer la diffusion en continu dans Azure Sentinel.



1. Dans Azure Sentinel, sélectionnez **collecte des données** puis cliquez sur le **Azure ATP** vignette.

2. Cliquez sur **Connecter**.


## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter Azure-Protection avancée contre les menaces pour Azure Sentinel. Pour en savoir plus sur Azure Sentinel, consultez les articles suivants :
- Découvrez comment [obtenez une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main [détecter des menaces avec Azure Sentinel](tutorial-detect-threats.md).

