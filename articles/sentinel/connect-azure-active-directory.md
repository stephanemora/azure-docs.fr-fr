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
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: eb89d2a4e719e34ad5ea31656dc9e3c02472b07d
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98802256"
---
# <a name="connect-data-from-azure-active-directory-azure-ad"></a>Connecter des données à partir d’Azure Active Directory (Azure AD)

Vous pouvez utiliser le connecteur intégré d’Azure Sentinel pour collecter des données à partir d’[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) et de les diffuser en continu vers Azure Sentinel. Le connecteur vous permet de diffuser des [journaux de connexion](../active-directory/reports-monitoring/concept-sign-ins.md) et [journaux d’audit](../active-directory/reports-monitoring/concept-audit-logs.md).

## <a name="prerequisites"></a>Prérequis

- Vous devez disposer d’un abonnement [Azure AD Premium P2](https://azure.microsoft.com/pricing/details/active-directory/) pour recevoir les journaux de connexion dans Azure Sentinel. Des frais supplémentaires par gigaoctet peuvent s’appliquer pour Azure Monitor (Log Analytics) et Azure Sentinel.

- Le rôle Contributeur Azure Sentinel doit être attribué à votre utilisateur sur l’espace de travail.

- Les rôles Administrateur général ou Administrateur de sécurité doivent être attribués à votre utilisateur sur le locataire à partir duquel vous souhaitez diffuser les journaux.

- Votre utilisateur doit disposer d’autorisations en lecture et en écriture sur les paramètres de diagnostic Azure AD pour pouvoir voir l’état de la connexion. 

## <a name="connect-to-azure-active-directory"></a>Connexion à Azure Active Directory

1. Dans Azure Sentinel, sélectionnez **Connecteurs de données** dans le menu de navigation.

1. Dans la galerie des connecteurs de données, sélectionnez **Azure Active Directory**, puis sélectionnez **Ouvrir la page du connecteur**.

1. Cochez les cases associées aux types de journaux que vous souhaitez diffuser dans Azure Sentinel, puis cliquez sur **Se connecter**. Vous pouvez choisir parmi les types de journaux suivants :

    - **Journaux d’activité de connexion** : Il s’agit d’informations sur l’utilisation des applications managées et les activités de connexion des utilisateurs.
    - **Journaux d’audit**: Informations sur les activités du système liées aux utilisateurs et à la gestion des groupes, aux applications gérées et aux activités de répertoire.

## <a name="find-your-data"></a>Recherche de données

Une fois la connexion établie, les données s’affichent dans **Journaux**, sous la section **LogManagement**, dans les tables suivantes :

- `SigninLogs`
- `AuditLogs`

Pour interroger les journaux Azure AD, entrez le nom de table approprié en haut de la fenêtre de requête.

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter Azure Active Directory à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats-built-in.md).
