---
title: Collecter des données d’Azure AD dans Azure en version préliminaire Sentinel | Microsoft Docs
description: Découvrez comment collecter des données d’Azure Active Directory dans Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2019
ms.author: rkarlin
ms.openlocfilehash: 315b18feb74862bbeca6ff8265ee003fbad48595
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2019
ms.locfileid: "57242308"
---
# <a name="collect-data-from-azure-active-directory"></a>Collecter des données à partir d’Azure Active Directory

> [!IMPORTANT]
> Sentinel Azure est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Sentinel Azure vous permet de collecter des données à partir de [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) et diffuser en continu dans Azure Sentinel. Vous pouvez choisir dans flux [connectez-vous journaux](../active-directory/reports-monitoring/concept-sign-ins.md) et [journaux d’audit](../active-directory/reports-monitoring/concept-audit-logs.md) .

## <a name="prerequisites"></a>Conditions préalables

- Si vous souhaitez exporter les données de connexion à partir d’Active Directory, vous devez disposer d’une licence Azure AD P1 ou P2.

- Utilisateur avec des autorisations administrateur globales admin ou la sécurité sur le client que vous souhaitez diffuser les journaux à partir de.


## <a name="connect-to-azure-ad"></a>Se connecter à Azure AD

1. Dans Azure Sentinel, sélectionnez **collecte des données** puis cliquez sur le **Azure Active Directory** vignette.

2. En regard les journaux que vous souhaitez diffuser en continu dans Azure Sentinel, cliquez sur **Connect**.






## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter Azure AD pour Azure Sentinel. Pour en savoir plus sur Azure Sentinel, consultez les articles suivants :
- Découvrez comment [obtenez une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main [détecter des menaces avec Azure Sentinel](tutorial-detect-threats.md).
