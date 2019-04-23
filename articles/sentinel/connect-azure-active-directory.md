---
title: Connecter des données d’Azure AD vers Azure Sentinel Preview | Microsoft Docs
description: Découvrez comment connecter des données d’Azure Active Directory à Azure Sentinel.
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
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: f29ae9a8fcaecfc345efae02084f31d133e67b5d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59786865"
---
# <a name="connect-data-from-azure-active-directory"></a>Connectez des données à partir d’Azure Active Directory

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Sentinel Azure vous permet de collecter des données à partir de [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) et diffuser en continu dans Azure Sentinel. Vous pouvez choisir dans flux [connectez-vous journaux](../active-directory/reports-monitoring/concept-sign-ins.md) et [journaux d’audit](../active-directory/reports-monitoring/concept-audit-logs.md) .

## <a name="prerequisites"></a>Conditions préalables

- Si vous souhaitez exporter les données de connexion à partir d’Active Directory, vous devez disposer d’une licence Azure AD P1 ou P2.

- Utilisateur avec des autorisations administrateur globales admin ou la sécurité sur le client que vous souhaitez diffuser les journaux à partir de.


## <a name="connect-to-azure-ad"></a>Se connecter à Azure AD

1. Dans Azure Sentinel, sélectionnez **connecteurs de données** puis cliquez sur le **Azure Active Directory** vignette.

2. En regard les journaux que vous souhaitez diffuser en continu dans Azure Sentinel, cliquez sur **Connect**.

6. Pour utiliser le schéma pertinent dans Analytique de journal pour les alertes d’Azure AD, recherchez **SigninLogs** et **AuditLogs**.




## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter Azure AD pour Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [obtenez une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main [détecter des menaces avec Azure Sentinel](tutorial-detect-threats.md).
