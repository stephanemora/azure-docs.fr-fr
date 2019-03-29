---
title: Collecter des données Office 365 dans Azure en version préliminaire Sentinel | Microsoft Docs
description: Découvrez comment collecter des données d’Office 365 dans Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: ff7c862e-2e23-4a28-bd18-f2924a30899d
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/26/2019
ms.author: rkarlin
ms.openlocfilehash: ad501958a5f88c821e48a3e21f69a960160b3c8e
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2019
ms.locfileid: "58574856"
---
# <a name="collect-data-from-office-365-logs"></a>Collecter des données à partir des journaux d’Office 365

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Vous pouvez diffuser des journaux d’audit à partir de [Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) dans Azure Sentinel avec un seul clic. Vous pouvez diffuser des journaux d’audit à partir de plusieurs clients à un seul espace de travail dans Azure Sentinel. Le connecteur de journal d’activité Office 365 fournit des informations sur les activités de l’utilisateur en cours. Vous obtiendrez des informations sur plusieurs utilisateur, administrateur, système et les actions de stratégie et les événements à partir d’Office 365. En connectant les journaux d’Office 365 dans Azure Sentinel, vous pouvez utiliser ces données pour afficher des tableaux de bord, créer des alertes personnalisées et améliorer votre processus d’investigation.


## <a name="prerequisites"></a>Conditions préalables

- Vous devez être un administrateur global ou administrateur de sécurité sur votre client
- Sur votre ordinateur, à partir duquel vous connecté Azure Sentinel pour créer la connexion, vérifiez suretha port 4433 est ouvert pour le trafic web.

## <a name="connect-to-office-365"></a>Connexion à Office 365

1. Dans Azure Sentinel, sélectionnez **collecte des données** puis cliquez sur le **Office 365** vignette.

2. Si vous le n'avez pas déjà activé, sous **connexion** utiliser le **activer** bouton pour activer la solution Office 365. Si elle a déjà été activé, il est identifié dans l’écran de connexion déjà activé.
1. Office 365 vous permet de diffuser des données à partir de plusieurs locataires Azure Sentinel. Pour chaque client que vous souhaitez vous connecter, ajoutez le locataire sous **connectent les locataires à Azure Sentinel**. 
1. Un écran d’Active Directory s’ouvre. Vous êtes invité à s’authentifier avec un utilisateur d’administrateur général sur chaque client que vous souhaitez vous connecter à Azure Sentinel et fournir des autorisations à Azure Sentinel pour lire ses journaux. 
5. Sous les journaux d’activité Stream Office 365, cliquez sur **sélectionnez** pour choisir les types de journaux que vous souhaitez diffuser en continu vers Azure Sentinel. Actuellement, Sentinel Azure prend en charge Exchange et SharePoint.

4. Cliquez sur **appliquer les modifications**.

3. Pour utiliser le schéma pertinent dans Analytique de journal pour les journaux d’Office 365, recherchez **OfficeActivity**.


## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter Office 365 à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [obtenez une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main [détecter des menaces avec Azure Sentinel](tutorial-detect-threats.md).

