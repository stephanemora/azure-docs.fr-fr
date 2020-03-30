---
title: Connecter des données Azure AD à Azure Sentinel | Microsoft Docs
description: Découvrez comment connecter des données d’Azure Active Directory à Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: be9241a6156621d3f90dbab2da5bebeb463b4232
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588618"
---
# <a name="connect-data-from-azure-active-directory"></a>Connecter des données à partir de Azure Active Directory



Azure Sentinel vous permet de collecter des données à partir d’[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) et de les diffuser en continu vers Azure Sentinel. Vous pouvez choisir de diffuser [les journaux de connexion](../active-directory/reports-monitoring/concept-sign-ins.md) et les [journaux d’audit](../active-directory/reports-monitoring/concept-audit-logs.md).

## <a name="prerequisites"></a>Conditions préalables requises

- Si vous souhaitez exporter des données de connexion à partir d’Active Directory, vous devez disposer d’une licence Azure AD P1 ou P2.

- Un utilisateur disposant des autorisations d’administrateur globale ou d’administrateur de la sécurité sur le locataire à partir duquel vous souhaitez diffuser les journaux.

- Pour afficher l’état de connexion, vous devez être autorisé à accéder aux journaux de diagnostic d’Azure AD. 


## <a name="connect-to-azure-ad"></a>Se connecter à Azure AD

1. Dans Azure Sentinel, sélectionnez **Connecteurs de données** puis cliquez sur la vignette du **Azure Active Directory**.

1. À côté des journaux que vous souhaitez diffuser dans Azure Sentinel, cliquez sur **Se connecter**.

1. Vous pouvez indiquer si vous voulez que les alertes d’Azure AD génèrent automatiquement des incidents dans Azure Sentinel. Sous **Créer des incidents**, sélectionnez **Activer** pour activer la règle analytique par défaut qui crée automatiquement des incidents à partir des alertes générées dans le service de sécurité connecté. Vous pouvez ensuite modifier cette règle sous **Analytique**, puis **Règles actives**.

1. Pour utiliser le schéma pertinent dans Log Analytics pour les alertes Azure AD, recherchez **SigninLogs** et **AuditLogs**.




## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter Azure AD à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats-built-in.md).
