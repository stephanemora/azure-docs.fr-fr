---
title: Connexion de données Azure Security Center à Sentinel version préliminaire d’Azure | Microsoft Docs
description: Découvrez comment connecter des données Azure Security Center à Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: bd6fe027da8c16157c7fce6c348e49c0a6b73ea3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60596613"
---
# <a name="connect-data-from-azure-security-center"></a>Connectez des données à partir d’Azure Security Center

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Sentinel Azure vous permet de connecter des alertes à partir de [Azure Security Center](../security-center/security-center-intro.md) et les diffuser vers Azure Sentinel. 

## <a name="prerequisites"></a>Conditions préalables

- Si vous souhaitez exporter les alertes à partir d’Azure Security Center, vous devez être un contributeur sur l’abonnement dont vous diffusez les journaux.

- Vous devez avoir le [niveau Standard d’Azure Security Center](../security-center/security-center-pricing.md) en cours d’exécution sur l’abonnement. Si ce n’est pas le cas, [mettre à niveau votre abonnement à la norme](https://azure.microsoft.com/pricing/details/security-center/).

- Vous devez vous connecter avec un utilisateur disposant de l’administrateur général ou autorisations d’administrateur de sécurité sur chaque abonnement que vous souhaitez vous connecter.


## <a name="connect-to-azure-security-center"></a>Se connecter à Azure Security Center

1. Dans Azure Sentinel, sélectionnez **connecteurs de données** puis cliquez sur le **Azure Security Center** vignette.
1. Dans la droite, cliquez sur **Connect** en regard de chaque abonnement dont vous souhaitez diffuser en continu dans Azure Sentinel les alertes. Veillez à mettre à niveau de chaque abonnement au niveau de l’Azure Security Center Standard pour les alertes de flux de données pour Azure Sentinel.

3. Cliquez sur **Connecter**.

4. Pour utiliser le schéma pertinent dans Analytique de journal pour les alertes Azure Security Center, recherchez **SecurityEvent**.

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter Azure Security Center pour Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [obtenez une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main [détecter des menaces avec Azure Sentinel](tutorial-detect-threats.md).
