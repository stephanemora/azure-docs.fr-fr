---
title: Connecter des données Azure ATP à Azure Sentinel | Microsoft Docs
description: Découvrez comment connecter des données Azure ATP à Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/17/2019
ms.author: rkarlin
ms.openlocfilehash: fb8f4de3b3b24d1eba372600c817627771ef0ef6
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158873"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>Connecter des données d’Azure Advanced Threat Protection (ATP)

> [!IMPORTANT]
> Le connecteur de données Azure Advanced Threat Protection dans Azure Sentinel est actuellement en préversion publique.
> Cette fonctionnalité est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Vous pouvez diffuser des journaux dans Azure Sentinel avec un seul clic à partir d’[Azure Advanced Threat Protection](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp).

## <a name="prerequisites"></a>Prérequis

- Utilisateur, doté d’autorisations d’administrateur général ou d’administrateur de la sécurité
- Vous devez être un client de la préversion d’Azure ATP et activer l’intégration entre Azure ATP et Microsoft Cloud App Security. Pour plus d’informations, consultez [Azure Advanced Protection Integration](https://docs.microsoft.com/cloud-app-security/aatp-integration).

## <a name="connect-to-azure-atp"></a>Se connecter à Azure ATP

Assurez-vous que la version de la préversion d’Azure ATP est [activée sur votre réseau](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1).
Si Azure ATP est déployé et ingère vos données, les alertes suspectes peuvent facilement être envoyées à Azure Sentinel. Le démarrage de la diffusion en continu des alertes dans Azure Sentinel peut prendre jusqu’à 24 heures.


1. Pour connecter Azure ATP à Azure Sentinel, vous devez d’abord activer l’intégration entre Azure ATP et Microsoft Cloud App Security. Pour plus d’informations sur la procédure à suivre, consultez [Intégration d’Azure Advanced Threat Protection](https://docs.microsoft.com/cloud-app-security/aatp-integration).

1. Dans Azure Sentinel, sélectionnez **Connecteurs de données**, puis cliquez sur la vignette **Azure Advanced Threat Protection (Préversion)** .

1. Vous pouvez indiquer si vous voulez que les alertes d’Azure ATP génèrent automatiquement des incidents dans Azure Sentinel. Sous **Créer des incidents**, sélectionnez **Activer** pour activer la règle analytique par défaut qui crée automatiquement des incidents à partir des alertes générées dans le service de sécurité connecté. Vous pouvez ensuite modifier cette règle sous **Analytique**, puis **Règles actives**.

1. Cliquez sur **Connecter**.

1. Pour utiliser le schéma pertinent dans Log Analytics pour les alertes Azure ATP, recherchez **SecurityAlert**.

> [!NOTE]
> Si la taille des alertes excède 30 Ko, Azure Sentinel cesse d’afficher le champ Entités dans les alertes.

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter Azure Advanced Threat Protection à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats-built-in.md).

