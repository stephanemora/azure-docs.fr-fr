---
title: Connecter des données Azure Active Directory à Azure Sentinel | Microsoft Docs
description: Découvrez comment collecter des données à partir d’Azure Active Directory et diffuser en continu des journaux de connexion Azure AD et des journaux d’audit dans Azure Sentinel.
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
ms.openlocfilehash: 37106517c47c86f4a4a562eebd6d120e31e22334
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564528"
---
# <a name="connect-data-from-azure-active-directory-azure-ad"></a>Connecter des données à partir d’Azure Active Directory (Azure AD)



Azure Sentinel vous permet de collecter des données à partir d’[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) et de les diffuser en continu vers Azure Sentinel. Vous pouvez choisir de diffuser [les journaux de connexion](../active-directory/reports-monitoring/concept-sign-ins.md) et les [journaux d’audit](../active-directory/reports-monitoring/concept-audit-logs.md).

## <a name="prerequisites"></a>Prérequis

- Si vous souhaitez exporter des données de connexion à partir d’Azure AD, vous devez disposer d’une licence Azure AD P1 ou P2.

- Un utilisateur disposant des autorisations d’administrateur globale ou d’administrateur de la sécurité sur le locataire à partir duquel vous souhaitez diffuser les journaux.

- Pour afficher l’état de connexion, vous devez être autorisé à accéder aux journaux de diagnostic d’Azure AD. 


## <a name="connect-to-azure-active-directory"></a>Connexion à Azure Active Directory

1. Dans Azure Sentinel, sélectionnez **Connecteurs de données** dans le menu de navigation.

1. Dans la galerie des connecteurs de données, sélectionnez **Azure Active Directory**, puis cliquez sur le bouton **Ouvrir la page du connecteur**.

1. Cochez les cases associées aux journaux que vous souhaitez diffuser dans Azure Sentinel, puis cliquez sur **Se connecter**.

1. Vous pouvez indiquer si vous voulez que les alertes d’Azure AD génèrent automatiquement des incidents dans Azure Sentinel. Sous **Créer des incidents**, sélectionnez **Activer** pour activer la règle analytique par défaut qui crée automatiquement des incidents à partir des alertes générées dans le service de sécurité connecté. Vous pouvez ensuite modifier cette règle sous **Analytique**, puis **Règles actives**.

1. Pour utiliser le schéma approprié dans Log Analytics afin d’exécuter une requête pour les alertes Azure AD, saisissez `SigninLogs` ou `AuditLogs` dans la fenêtre de requête.




## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter Azure Active Directory à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats-built-in.md).
