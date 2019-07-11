---
title: Connexion des données Azure Security Center à Azure Sentinel (préversion) | Microsoft Docs
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
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 697983000f84f9e4efe7e2c8ef9dbb2f636e0735
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620576"
---
# <a name="connect-data-from-azure-security-center"></a>Connecter des données à partir d’Azure Security Center

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Azure Sentinel vous permet de connecter des alertes à partir d’[Azure Security Center](../security-center/security-center-intro.md) et de les diffuser vers Azure Sentinel. 

## <a name="prerequisites"></a>Prérequis

- Si vous souhaitez exporter des alertes à partir d’Azure Security Center, vous devez être un contributeur sur l’abonnement dont vous diffusez les journaux.

- Vous devez avoir le [niveau Standard d’Azure Security Center](../security-center/security-center-pricing.md) en cours d’exécution sur l’abonnement. Si ce n’est pas le cas, [mettez à niveau votre abonnement au niveau Standard](https://azure.microsoft.com/pricing/details/security-center/).

- Vous devez vous connecter avec un utilisateur disposant des autorisations d’administrateur général ou d’administrateur de sécurité sur chaque abonnement auquel vous souhaitez vous connecter.


## <a name="connect-to-azure-security-center"></a>Se connecter à Azure Security Center

1. Dans Azure Sentinel, sélectionnez **Connecteurs de données** puis cliquez sur la vignette du **Azure Security Center**.
1. Dans la droite, cliquez sur **Se connecter** à côté de chaque abonnement dont vous souhaitez diffuser les alertes dans Azure Sentinel. Veillez à mettre à niveau chaque abonnement au niveau Standard d’Azure Security Center pour diffuser les alertes vers Azure Sentinel.

3. Cliquez sur **Connecter**.

4. Pour utiliser le schéma pertinent dans Log Analytics pour les alertes Azure Security Center, recherchez **SecurityEvent**.

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter Azure Security Center à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats.md).
