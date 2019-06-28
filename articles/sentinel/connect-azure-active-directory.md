---
title: Connecter des données Azure AD à Azure Sentinel en préversion | Microsoft Docs
description: Découvrez comment connecter des données d’Azure Active Directory à Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 357435b8a4ac396c1548c89206f269730e871f6b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65204498"
---
# <a name="connect-data-from-azure-active-directory"></a>Connecter des données à partir de Azure Active Directory

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Sentinel vous permet de collecter des données à partir d’[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) et de les diffuser en continu vers Azure Sentinel. Vous pouvez choisir de diffuser [les journaux de connexion](../active-directory/reports-monitoring/concept-sign-ins.md) et les [journaux d’audit](../active-directory/reports-monitoring/concept-audit-logs.md).

## <a name="prerequisites"></a>Prérequis

- Si vous souhaitez exporter des données de connexion à partir d’Active Directory, vous devez disposer d’une licence Azure AD P1 ou P2.

- Un utilisateur disposant des autorisations d’administrateur globale ou d’administrateur de la sécurité sur le locataire à partir duquel vous souhaitez diffuser les journaux.


## <a name="connect-to-azure-ad"></a>Se connecter à Azure AD

1. Dans Azure Sentinel, sélectionnez **Connecteurs de données** puis cliquez sur la vignette du **Azure Active Directory**.

2. À côté des journaux que vous souhaitez diffuser dans Azure Sentinel, cliquez sur **Se connecter**.

6. Pour utiliser le schéma pertinent dans Log Analytics pour les alertes Azure AD, recherchez **SigninLogs** et **AuditLogs**.




## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter Azure AD à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats.md).
