---
title: Tableau de bord Azure Defender et ses fonctionnalités
description: En savoir plus sur les fonctionnalités du tableau de bord Azure Defender.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 16379380fc35bb2355c496dc857e9de3b41347f9
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096902"
---
# <a name="the-azure-defender-dashboard"></a>Le tableau de bord Azure Defender

Le tableau de bord Azure Defender fournit :

- Visibilité de la couverture d’Azure Defender sur les différents types de ressources
- Liens pour configurer les fonctionnalités de protection avancée contre les menaces
- L’état d’intégration et l’installation de l’agent
- Alertes de détection des menaces Azure Defender 

Pour accéder au tableau de bord Azure Defender, sélectionnez **Azure Defender** dans la section Sécurité du cloud du menu de Security Center.

## <a name="whats-shown-on-the-dashboard"></a>Éléments affichés dans le tableau de bord

:::image type="content" source="./media/azure-defender-dashboard/sample-defender-dashboard-numbered.png" alt-text="Exemple de tableau de bord Azure Defender" lightbox="./media/azure-defender-dashboard/sample-defender-dashboard-numbered.png":::

Le tableau de bord inclut les sections suivantes :

1. **Couverture d’Azure Defender** : ici, vous pouvez voir les types de ressources figurant dans votre abonnement et éligibles pour la protection par Azure Defender. Le cas échéant, vous aurez également la possibilité de procéder à une mise à niveau. Si vous souhaitez mettre à niveau toutes les ressources éligibles possibles, sélectionnez **Tout mettre à niveau**.

2. **Alertes de sécurité** : quand Azure Defender détecte une menace dans n’importe quelle zone de votre environnement, il génère une alerte. Ces alertes décrivent les détails des ressources affectées, les étapes de correction suggérées et, dans certains cas, l’option permettant de déclencher une application logique en réponse. Sélectionnez n’importe quel emplacement dans ce graphique pour ouvrir la **page d’alertes de sécurité**.

3. **Protection avancée** : Azure Defender comprend de nombreuses fonctionnalités de protection avancée contre les menaces pour les machines virtuelles, les bases de données SQL, les conteneurs, les applications web, votre réseau, et bien plus encore. Dans cette section de protection avancée, vous pouvez voir l’état des ressources dans les abonnements sélectionnés pour chacune de ces protections. Sélectionnez l’une d’entre elles pour accéder directement à la zone de configuration de ce type de protection.

4. **Insights** : ce volet déroulant de nouvelles, de lectures suggérées et d’alertes de haute priorité fournit à Security Center des informations sur les questions de sécurité qui sont pertinentes pour vous et votre abonnement. Qu’il s’agisse d’une liste de CVE de gravité élevée détectés sur vos machines virtuelles par un outil d’analyse des vulnérabilités, ou d’un nouveau billet de blog d’un membre de l’équipe Security Center, vous le trouverez ici dans le volet Insights de votre **tableau de bord Azure Defender**.




## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert le tableau de bord Azure Defender. 

Pour plus d’informations sur Azure Defender, consultez [Présentation d’Azure Defender](azure-defender.md)

> [!div class="nextstepaction"]
> [Activer Azure Defender](enable-azure-defender.md)