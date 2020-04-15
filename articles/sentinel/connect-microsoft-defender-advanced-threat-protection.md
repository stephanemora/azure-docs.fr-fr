---
title: Connecter des données Microsoft Defender ATP à Azure Sentinel | Microsoft Docs
description: Découvrez comment connecter des données Microsoft Defender Advanced Threat Protection à Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: yelevin
ms.openlocfilehash: 831baabcc26130d9b2c730afd85f2ed4b3842690
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756342"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>Connecter des alertes de Microsoft Defender Advanced Threat Protection 


> [!IMPORTANT]
> L’ingestion des alertes Microsoft Defender Advanced Threat Protection est actuellement disponible en préversion publique.
> Cette fonctionnalité est fournie sans contrat de niveau de service et est déconseillée pour les charges de travail de production.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 

Le connecteur [Microsoft Defender - Protection avancée contre les menaces](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) vous permet de transmettre en continu les alertes de Microsoft Defender - Protection avancée contre les menaces vers Azure Sentinel. Vous pouvez ainsi analyser de manière plus détaillée les événements de sécurité se produisant dans votre organisation et générer des playbooks afin d'y répondre immédiatement et efficacement.

## <a name="prerequisites"></a>Prérequis

- Vous devez disposer d’une licence valide pour Microsoft Defender Advanced Threat Protection, comme décrit dans [Configurer le déploiement de Microsoft Defender ATP](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing). 
- Vous devez être administrateur ou administrateur de la sécurité sur le locataire Azure Sentinel.


## <a name="connect-to-microsoft-defender-advanced-threat-protection"></a>Connexion à Microsoft Defender Advanced Threat Protection

Si Microsoft Defender Advanced Threat Protection est déployé et ingère vos données, les alertes peuvent facilement être envoyées à Azure Sentinel.


1. Dans Azure Sentinel, sélectionnez **Connecteurs de données**, cliquez sur la vignette **Microsoft Defender Advanced Threat Protection** et sélectionnez **Ouvrir la page du connecteur**.
1. Cliquez sur **Connecter**. 
1. Pour utiliser le schéma pertinent dans Log Analytics pour les alertes Defender ATP, recherchez **SecurityAlert** et le **Nom du fournisseur** est **MDATP**.




## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter Microsoft Defender ATP à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats.md).
