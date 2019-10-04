---
title: Connexion des données d’Azure Security Center à Azure Sentinel | Microsoft Docs
description: Découvrez comment connecter des données Azure Security Center à Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: a9c210531f2c4cab1c3c023eab795023c3ad9f0c
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240216"
---
# <a name="connect-data-from-azure-security-center"></a>Connecter des données à partir d’Azure Security Center





Azure Sentinel vous permet de connecter des alertes à partir d’[Azure Security Center](../security-center/security-center-intro.md) et de les diffuser vers Azure Sentinel. 

## <a name="prerequisites"></a>Prérequis

- Si vous souhaitez exporter des alertes à partir d’Azure Security Center, vous devez être un contributeur sur l’abonnement dont vous diffusez les journaux.

- Vous devez avoir le [niveau Standard d’Azure Security Center](../security-center/security-center-pricing.md) en cours d’exécution sur l’abonnement. Si ce n’est pas le cas, [mettez à niveau votre abonnement au niveau Standard](https://azure.microsoft.com/pricing/details/security-center/).

- Vous devez vous connecter avec un utilisateur disposant des autorisations d’administrateur général ou d’administrateur de sécurité sur chaque abonnement auquel vous souhaitez vous connecter.


## <a name="connect-to-azure-security-center"></a>Se connecter à Azure Security Center

1. Dans Azure Sentinel, sélectionnez **Connecteurs de données** puis cliquez sur la vignette du **Azure Security Center**.

1. Dans la droite, cliquez sur **Se connecter** à côté de chaque abonnement dont vous souhaitez diffuser les alertes dans Azure Sentinel. Veillez à mettre à niveau chaque abonnement au niveau Standard d’Azure Security Center pour diffuser les alertes vers Azure Sentinel.

1. Vous pouvez décider que les alertes d’Azure Security Center génèrent automatiquement des incidents dans Azure Sentinel. Sous **Créer des incidents**, sélectionnez **Activer** pour activer la règle analytique par défaut qui crée des incidents automatiquement à partir des alertes générées dans le service de sécurité associé. Vous pouvez ensuite modifier cette règle sous **Analytique**, puis **Règles actives**.

3. Cliquez sur **Connecter**.

4. Pour utiliser le schéma pertinent dans Log Analytics pour les alertes Azure Security Center, recherchez **SecurityAlert**.

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter Azure Security Center à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats-built-in.md).
