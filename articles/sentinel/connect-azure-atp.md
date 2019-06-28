---
title: Connecter des données Azure ATP à Azure Sentinel en préversion | Microsoft Docs
description: Découvrez comment connecter des données Azure ATP à Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 5bf3cc44-ecda-4c78-8a63-31ab42f43605
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: f0d86a62c59df5bebd34137d0903fcaa7014573d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65204271"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>Connecter des données d’Azure Advanced Threat Protection (ATP)

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Vous pouvez diffuser des journaux dans Azure Sentinel avec un seul clic à partir d’[Azure Advanced Threat Protection](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp).

## <a name="prerequisites"></a>Prérequis

- Utilisateur, doté d’autorisations d’administrateur général ou d’administrateur de la sécurité
- Vous devez être un client de la préversion privée d’Azure ATP

## <a name="connect-to-azure-atp"></a>Se connecter à Azure ATP

Assurez-vous que la version de la préversion privée d’Azure ATP est [activée sur votre réseau](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1).
Si Azure ATP est déployé et ingère vos données, les alertes suspectes peuvent facilement être envoyées à Azure Sentinel. Le démarrage de la diffusion en continu des alertes dans Azure Sentinel peut prendre jusqu’à 24 heures.



1. Dans Azure Sentinel, sélectionnez **Connecteurs de données** puis cliquez sur la vignette **Azure ATP**.

2. Cliquez sur **Connecter**.

6. Pour utiliser le schéma pertinent dans Log Analytics pour les alertes Azure ATP, recherchez **SecurityAlert**.

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter Azure Advanced Threat Protection à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats.md).

