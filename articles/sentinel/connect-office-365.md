---
title: Connecter des données Office 365 à Azure Sentinel en préversion | Microsoft Docs
description: Découvrez comment connecter des données Office 365 à Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: ff7c862e-2e23-4a28-bd18-f2924a30899d
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 6b1e167d26b5848238dd51bf9792f8316c33a385
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65205564"
---
# <a name="connect-data-from-office-365-logs"></a>Connecter des données de journaux Office 365

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Vous pouvez diffuser des journaux d’audit dans Azure Sentinel en un seul clic à partir d’[Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide). Vous pouvez diffuser des journaux d’audit dans un espace de travail unique d’Azure Sentinel à partir de plusieurs locataires. Le connecteur de journal d’activité Office 365 fournit des informations sur les activités de l’utilisateur en cours. Vous obtenez des informations sur plusieurs actions utilisateur, administrateur, système et de stratégie et d’événements d’Office 365. En connectant les journaux d’activité d’Office 365 dans Azure Sentinel, vous pouvez utiliser ces données pour afficher des tableaux de bord, créer des alertes personnalisées et améliorer votre processus d’investigation.


## <a name="prerequisites"></a>Prérequis

- Vous devez être un administrateur général ou un administrateur de la sécurité sur ce locataire
- Sur votre ordinateur, à partir duquel vous vous êtes connecté à Azure Sentinel pour créer la connexion, assurez-vous que le port 4433 est ouvert pour le trafic web.

## <a name="connect-to-office-365"></a>Connexion à Office 365

1. Dans Azure Sentinel, sélectionnez **Connecteurs de données** puis cliquez sur la vignette **Office 365**.

2. Si vous ne l’avez pas déjà fait, sous **connexion**, utilisez le bouton **Activer** pour activer la solution Office 365. Si elle a déjà été activée, elle est identifiée dans l’écran de connexion comme étant déjà activée.
1. Office 365 vous permet de diffuser des données dans Azure Sentinel à partir de plusieurs locataires. Pour chaque locataire auquel vous souhaitez vous connecter, ajoutez le locataire sous **Connect tenants to Azure Sentinel** (Connecter des locataires à Azure Sentinel). 
1. Un écran Active Directory s’ouvre. Vous êtes invité à vous authentifier avec un utilisateur administrateur général sur chaque locataire que vous souhaitez connecter à Azure Sentinel, et à fournir des autorisations sur Azure Sentinel pour lire ses journaux. 
5. Sous les journaux d’activité Office 365, cliquez sur **Sélectionner** pour choisir les types de journaux que vous souhaitez diffuser en continu dans Azure Sentinel. Azure Sentinel prend actuellement en charge Exchange et SharePoint.

4. Cliquez sur **Appliquer les modifications**.

3. Pour utiliser le schéma pertinent dans Log Analytics pour les journaux d’activité Office 365, recherchez **OfficeActivity**.


## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter Office 365 à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [obtenir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats.md).

