---
title: Connecter des données Cloud App Security à Azure Sentinel | Microsoft Docs
description: Découvrez comment connecter des données Cloud App Security à Azure Sentinel.
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
ms.date: 10/23/2019
ms.author: yelevin
ms.openlocfilehash: 348576fbbdd1037f9e2e792218b96bbbecf36668
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588363"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Connecter des données à partir de Microsoft Cloud App Security 



Vous pouvez diffuser des journaux dans Azure Sentinel en un seul clic à partir de [Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security). Cette connexion vous permet de diffuser les alertes en continu à partir de Cloud App Security dans Azure Sentinel. 

## <a name="prerequisites"></a>Conditions préalables requises

- Utilisateur, doté d’autorisations d’administrateur général ou d’administrateur de la sécurité
- Pour diffuser des journaux Cloud Discovery dans Azure Sentinel, [activez Azure Sentinel comme SIEM dans Microsoft Cloud App Security](https://aka.ms/AzureSentinelMCAS).

> [!IMPORTANT]
> L’ingestion des journaux Cloud Discovery est actuellement en préversion publique.
> Cette fonctionnalité est fournie sans contrat de niveau de service et est déconseillée pour les charges de travail de production.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="connect-to-cloud-app-security"></a>Connexion à Cloud App Security

Si vous avez déjà Cloud App Security, assurez-vous qu’il est [activé sur votre réseau](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Si Cloud App Security est déployé et ingère vos données, les données d’alerte peuvent facilement être envoyés à Azure Sentinel.


1. Dans Azure Sentinel, sélectionnez **Connecteurs de données**, puis cliquez sur la vignette **Cloud App Security** et sélectionnez **Ouvrir la page du connecteur**.

1. Pour sélectionner les journaux que vous souhaitez diffuser en continu dans Azure Sentinel, vous pouvez sélectionner **Alertes** et **Journaux Cloud Discovery** (préversion). 

1. Cliquez sur **Connecter**.

1. Pour utiliser le schéma pertinent dans Log Analytics pour les alertes Cloud App Security, recherchez **SecurityAlert**.




## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter Microsoft Cloud App Security à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats.md).
