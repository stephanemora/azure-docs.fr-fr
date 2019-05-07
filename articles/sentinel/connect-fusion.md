---
title: Activer la fusion Sentinel version préliminaire d’Azure | Microsoft Docs
description: Découvrez comment activer la fusion dans Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 82becf50-6628-47e4-b3d7-18d7d72d505f
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: bf9a78006d13614a73a3fccfc57f28ce850456d9
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65204467"
---
# <a name="enable-fusion"></a>Activer la fusion

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Machine Learning dans Azure Sentinel est intégrée dès le début. Nous avons réfléchie conçu le système avec des innovations ML visant à améliorer la productivité des analystes de sécurité, les scientifiques des données de sécurité et les ingénieurs. Une telle innovation est Qu'azure Sentinel Fusion conçu en particulier pour réduire la fatigue alerte.

Fusion utilise des algorithmes d’apprentissage graphique sous tension pour mettre en corrélation entre des millions d’activités anormales d’inférieur fidélité à partir de différents produits tels qu’Azure AD Identity Protection et Microsoft Cloud App Security, de les combiner en un nombre gérable de incidents de sécurité intéressants.

## <a name="enable-fusion"></a>Activer la fusion

1. Dans le portail Azure, sélectionnez l’icône pour ouvrir Cloud Shell.
  ![Cloud Shell](./media/connect-fusion/cloud-shell.png)

2.  Dans le **Bienvenue dans Cloud Shell** windows qui s’ouvre en dessous, sélectionnez PowerShell.

3.  Choisissez l’abonnement sur lequel vous avez déployé Azure Sentinel, et **créer stockage**.

4. Une fois que vous êtes authentifié et que votre disque Azure est généré, à l’invite de commandes, exécutez les commandes suivantes :

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=true --subscription "{Subscription Guid}"

## <a name="disable-fusion"></a>Désactiver la fusion

Suivez la même procédure que ci-dessus et exécutez la commande suivante :

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=false --subscription "{Subscription Guid}"

## <a name="view-the-status-of-fusion"></a>Afficher l’état de la fusion

            az resource show --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --subscription "{Subscription Guid}"


## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à activer la Fusion dans Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [obtenez une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main [détecter des menaces avec Azure Sentinel](tutorial-detect-threats.md).

