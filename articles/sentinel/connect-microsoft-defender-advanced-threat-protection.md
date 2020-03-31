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
ms.openlocfilehash: ee2c917d02a442ebabc0e1b40bc245982af794f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588210"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>Connecter des alertes de Microsoft Defender Advanced Threat Protection 


> [!IMPORTANT]
> L’ingestion des journaux Microsoft Defender Advanced Threat Protection est actuellement disponible en préversion publique.
> Cette fonctionnalité est fournie sans contrat de niveau de service et est déconseillée pour les charges de travail de production.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 

Vous pouvez diffuser des alertes depuis [Microsoft Defender Advanced Threat Protection](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) dans Azure Sentinel en un seul clic. Cette connexion vous permet de diffuser les alertes de Microsoft Defender Advanced Threat Protection dans Azure Sentinel. 

## <a name="prerequisites"></a>Conditions préalables requises

- Licence valide de Microsoft Defender Advanced Threat Protection activée comme cela est décrit dans [Valider l’attribution de licence et terminer la configuration de Microsoft Defender Advanced Threat Protection](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing). 
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
