---
title: Connecter des données Cloud App Security à Azure Sentinel en préversion | Microsoft Docs
description: Découvrez comment connecter des données Cloud App Security à Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: cd9e5e27-fdd4-4717-8924-be4c1c430f23
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: a418bb318654752eaf48ffbdd05b80cabb487ece
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65207545"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Connecter des données à partir de Microsoft Cloud App Security 

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Vous pouvez diffuser des journaux dans Azure Sentinel en un seul clic à partir de [Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security). Cette connexion vous permet de diffuser les alertes en continu à partir de Cloud App Security dans Azure Sentinel. 

## <a name="prerequisites"></a>Prérequis

- Utilisateur, doté d’autorisations d’administrateur général ou d’administrateur de la sécurité

## <a name="connect-to-cloud-app-security"></a>Connexion à Cloud App Security

Si vous avez déjà Cloud App Security, assurez-vous qu’il est [activé sur votre réseau](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Si Cloud App Security est déployé et ingère vos données, les données d’alerte peuvent facilement être envoyés à Azure Sentinel.


1. Dans Azure Sentinel, sélectionnez **Connecteurs de données** puis cliquez sur la vignette de **Cloud App Security**.

2. Cliquez sur **Connecter**.

3. Pour utiliser le schéma pertinent dans Log Analytics pour les alertes Cloud App Security, recherchez **SecurityAlert**.


## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter Microsoft Cloud App Security à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats.md).
