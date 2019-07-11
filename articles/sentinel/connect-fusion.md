---
title: Activer la fusion dans la préversion d’Azure Sentinel | Microsoft Docs
description: Découvrez comment activer la fusion dans Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 82becf50-6628-47e4-b3d7-18d7d72d505f
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 1e17afb2f06265975442c127baecc8cab75d63f7
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67611259"
---
# <a name="enable-fusion"></a>Activer la fusion

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Le Machine Learning dans Azure Sentinel est intégré dès le début. Nous avons conçu le système avec des innovations ML visant à améliorer la productivité des analystes de sécurité, des scientifiques des données de sécurité et des ingénieurs. L’innovation est la fusion Azure Sentinel spécialement conçue pour réduire les problèmes d’inattention.

La fusion se sert d’algorithmes d’apprentissage graphiques pour mettre en corrélation des millions d’activités anormales de fidélité inférieure à partir de différents produits tels qu’Azure AD Identity Protection et Microsoft Cloud App Security, et elle les combine en un nombre gérable de cas de sécurité intéressants.

## <a name="enable-fusion"></a>Activer la fusion

1. Dans le portail Azure, sélectionnez l’icône pour ouvrir Cloud Shell.
  ![Cloud Shell](./media/connect-fusion/cloud-shell.png)

2.  Sélectionnez PowerShell dans la fenêtre **Bienvenue dans Cloud Shell** qui s’ouvre en dessous.

3.  Choisissez l’abonnement sur lequel vous avez déployé Azure Sentinel, et **créez un stockage**.

4. Une fois authentifié et lorsque votre lecteur Azure est conçu, exécutez les commandes suivantes dans l’invite de commandes :

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=true --subscription "{Subscription Guid}"

## <a name="disable-fusion"></a>Désactiver la fusion

Suivez la procédure ci-dessus et exécutez la commande suivante :

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=false --subscription "{Subscription Guid}"

## <a name="view-the-status-of-fusion"></a>Afficher l’état de la fusion

            az resource show --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --subscription "{Subscription Guid}"


## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à activer la fusion dans Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats.md).

