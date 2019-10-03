---
title: Connecter des données Cloud App Security à Azure Sentinel | Microsoft Docs
description: Découvrez comment connecter des données Cloud App Security à Azure Sentinel.
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
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 6e71028855344d083dedf3493682e1e27685de48
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240123"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Connecter des données à partir de Microsoft Cloud App Security 



Vous pouvez diffuser des journaux dans Azure Sentinel en un seul clic à partir de [Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security). Cette connexion vous permet de diffuser les alertes en continu à partir de Cloud App Security dans Azure Sentinel. 

## <a name="prerequisites"></a>Prérequis

- Utilisateur, doté d’autorisations d’administrateur général ou d’administrateur de la sécurité

## <a name="connect-to-cloud-app-security"></a>Connexion à Cloud App Security

Si vous avez déjà Cloud App Security, assurez-vous qu’il est [activé sur votre réseau](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Si Cloud App Security est déployé et ingère vos données, les données d’alerte peuvent facilement être envoyés à Azure Sentinel.


1. Dans Azure Sentinel, sélectionnez **Connecteurs de données** puis cliquez sur la vignette de **Cloud App Security**.

1. Pour sélectionner les journaux que vous souhaitez diffuser en continu dans Azure Sentinel, vous pouvez sélectionner **Alertes**. 

1. Vous pouvez décider que les alertes provenant de Microsoft Cloud App Security génèrent automatiquement des incidents dans Azure Sentinel. Sous **Créer des incidents**, sélectionnez **Activer** pour activer la règle analytique par défaut qui crée des incidents automatiquement à partir des alertes générées dans le service de sécurité associé. Vous pouvez ensuite modifier cette règle sous **Analytique**, puis **Règles actives**.

1. Cliquez sur **Connecter**.

1. Pour utiliser le schéma pertinent dans Log Analytics pour les alertes Cloud App Security, recherchez **SecurityAlert**.




## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter Microsoft Cloud App Security à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats-built-in.md).
