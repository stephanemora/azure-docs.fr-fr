---
title: Utiliser l'Analyse comportementale des entités pour détecter les menaces avancées | Microsoft Docs
description: Activer l'Analyse comportementale des utilisateurs et des entités dans Azure Sentinel, et configurer des sources de données
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2020
ms.author: yelevin
ms.openlocfilehash: c55ea0e7753faa6dc21b955d63a57d96e3849f70
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90992719"
---
# <a name="enable-user-and-entity-behavior-analytics-ueba-in-azure-sentinel"></a>Activer l'Analyse comportementale des utilisateurs et des entités dans Azure Sentinel 



## <a name="prerequisites"></a>Prérequis

- Votre utilisateur doit disposer du rôle d'**Administrateur général** ou d'**Administrateur de la sécurité** dans Azure AD pour pouvoir activer ou désactiver l'Analyse comportementale des utilisateurs et des entités, mais pas pour l'exécuter.

## <a name="how-to-enable-user-and-entity-behavior-analytics"></a>Activer l'Analyse comportementale des utilisateurs et des entités

1. Dans le menu de navigation d'Azure Sentinel, sélectionnez **Comportement des entités (préversion)** .

1. Sous l'en-tête **Activer**, placez le commutateur sur **Activé**.

1. Cliquez sur le bouton **Sélectionner des sources de données**.

1. Dans le volet **Sélection des sources de données**, cochez les cases situées en regard des sources de données sur lesquelles vous souhaitez activer l'Analyse comportementale des utilisateurs et des entités, puis sélectionnez **Appliquer**.

    > [!NOTE]
    >
    > La partie inférieure du volet **Sélection des sources de données** répertorie les sources de données prises en charge par l'Analyse comportementale des utilisateurs et des entités et que vous n'avez pas encore activées. 
    >
    > Après avoir activé l'Analyse comportementale des utilisateurs et des entités, vous aurez la possibilité, lors de la connexion de nouvelles sources de données, de les activer à partir du volet des connecteurs de données si elles sont compatibles avec l'Analyse comportementale des utilisateurs et des entités.

1. Sélectionnez **Accéder à la recherche d'entités**. Vous accédez alors au volet de recherche d'entités. Désormais, celui-ci s'affichera lorsque vous choisirez **Comportement des entités** dans le menu principal d'Azure Sentinel.

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à activer et configurer l'Analyse comportementale des utilisateurs et des entités dans Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats-built-in.md).
