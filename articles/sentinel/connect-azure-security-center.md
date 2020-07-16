---
title: Connecter les données Azure Security Center à Azure Sentinel
description: Découvrez comment connecter des alertes à partir du niveau Standard d’Azure Security Center (ASC) et les diffuser vers Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 2fc7744600a9652ad43fd0aae8d886dc94acd58f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559147"
---
# <a name="connect-data-from-azure-security-center-asc"></a>Connecter des données à partir d’Azure Security Center (ASC)

Azure Sentinel vous permet de connecter des alertes à partir d’[Azure Security Center](../security-center/security-center-intro.md) et de les diffuser vers Azure Sentinel. 

## <a name="prerequisites"></a>Prérequis

- Pour exporter des alertes à partir d’Azure Security Center, vous devez avoir le rôle Lecteur Sécurité dans l’abonnement des journaux que vous diffusez.

- Vous devez avoir le [niveau Standard d’Azure Security Center](../security-center/security-center-pricing.md) en cours d’exécution sur l’abonnement. Si ce n’est pas le cas, [mettez à niveau votre abonnement au niveau Standard](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="connect-to-azure-security-center"></a>Se connecter à Azure Security Center

1. Dans Azure Sentinel, sélectionnez **Connecteurs de données** dans le menu de navigation.

1. Dans la galerie des connecteurs de données, sélectionnez **Azure Security Center**, puis cliquez sur le bouton **Ouvrir la page du connecteur**.

1. Sous **Configuration**, cliquez sur **Se connecter** à côté de chaque abonnement dont vous souhaitez diffuser les alertes dans Azure Sentinel. Le bouton Se connecter n’est disponible que si vous disposez des autorisations requises et de l’abonnement ASC de niveau Standard.

1. Vous pouvez décider que les alertes d’Azure Security Center génèrent automatiquement des incidents dans Azure Sentinel. Sous **Créer des incidents**, sélectionnez **Activé** pour activer la règle analytique par défaut qui crée automatiquement des incidents à partir d’alertes. Vous pouvez ensuite modifier cette règle sous **Analytique** dans l’onglet **Règles actives**.

1. Pour utiliser le schéma pertinent dans Log Analytics pour les alertes Azure Security Center, recherchez **SecurityAlert**.

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter Azure Security Center à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats-built-in.md).
