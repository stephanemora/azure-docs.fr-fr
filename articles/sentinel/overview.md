---
title: Qu’est-ce que la préversion d’Azure Sentinel ? | Microsoft Docs
description: Découvrez Azure Sentinel, ses fonctionnalités principales et son fonctionnement.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 10cce91a-421b-4959-acdf-7177d261f6f2
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 15cf770a372c9a1386dd0293abeac01fd3cacf63
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779743"
---
# <a name="what-is-azure-sentinel-preview"></a>Qu’est-ce que la préversion d’Azure Sentinel ?

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Microsoft Azure Sentinel est une solution de cloud native et évolutive de type **SIEM (Security Information and Event Management)** et **SOAR (Security Orchestrated Automated Response)** . Azure Sentinel assure une analyse de sécurité intelligente et fournit des informations sur les menaces dans l’ensemble de l’entreprise. Elle constitue une solution unique pour la détection des alertes, la visibilité des menaces, la chasse proactive et la réponse face aux menaces. 

Azure Sentinel est votre observatoire global sur l’ensemble de l’entreprise et atténue le stress dû à des attaques de plus en plus sophistiquées, à l’augmentation des volumes d’alertes et à la durée de résolution.

- **Collectez des données à l’échelle du cloud** sur l’ensemble des utilisateurs, appareils, applications et infrastructures, tant locaux que dans de multiples clouds. 

- **Détectez les menaces non détectées précédemment** et réduisez les faux positifs en vous appuyant sur l’analytique et les systèmes de renseignement incomparables sur les menaces fournis par Microsoft. 

- **Investiguez les menaces à l’aide de l’intelligence artificielle** et recherchez les activités suspectes à grande échelle en profitant des années de travail que Microsoft a consacrées à la cybersécurité. 

- **Répondez aux incidents rapidement** avec une orchestration et une automatisation intégrées des tâches courantes.


![Fonctionnalités principales d’Azure Sentinel](./media/overview/core-capabilities.png)

S’appuyant sur la gamme complète des services Azure existants, Azure Sentinel intègre de façon native les éléments de base comme Log Analytics et Logic Apps. Azure Sentinel enrichit vos opérations d’investigation et de détection avec l’intelligence artificielle et fournit les flux d’informations sur les menaces venant de Microsoft et vous permet de mettre en place votre propre intelligence face aux menaces. 

 
## <a name="connect-to-all-your-data"></a>Connexion à toutes vos données

Pour intégrer Azure Sentinel, vous devez d’abord [vous connecter à vos sources de sécurité](connect-data-sources.md). Azure Sentinel est fourni avec plusieurs connecteurs pour les solutions Microsoft, disponibles par défaut et offrant une intégration en temps réel, y compris les solutions Microsoft Threat Protection, et les sources Microsoft 365, dont Office 365, Azure AD, Azure ATP et Microsoft Cloud App Security, et bien plus encore. En outre, il existe des connecteurs intégrés pour la connexion aux écosystèmes de sécurité élargis pour les solutions non Microsoft. Vous pouvez également utiliser le format d’événement commun, Syslog ou l’API REST pour connecter vos sources de données à Azure Sentinel.  

![Collecteurs de données](./media/collect-data/collect-data-page.png)

## <a name="dashboards"></a>Tableaux de bord

Après la connexion des sources de données, vous pouvez choisir parmi une galerie de [tableaux de bord créés par des experts](quickstart-get-visibility.md#dashboards) qui mettent en avant les informations provenant de vos sources de données. Chaque tableau de bord est entièrement personnalisable : vous pouvez ajouter votre propre logique ou modifier les requêtes ou vous pouvez créer entièrement un tableau de bord.

Les tableaux de bord offrent une visualisation interactive à avec l’analytique avancée pour aider vos analystes de sécurité à bénéficier d’une meilleure compréhension de ce qui se passe pendant une attaque. Les outils d’investigation permettent de vous immerger dans n’importe quel domaine, à partir de n’importe quel type de données, pour développer rapidement le contexte de la menace. 

![Tableaux de bord](./media/overview/dashboards.png)

## <a name="analytics"></a>Analytics

Pour vous aider à réduire le niveau de bruit et à réduire le nombre d’alertes que vous devez examiner, Azure Sentinel utilise l’[analytique pour mettre en corrélation les alertes et les incidents](tutorial-detect-threats.md). Les **incidents** sont des groupes d’alertes liées qui, prises ensemble, constituent une menace exploitable que vous pouvez examiner et résoudre. Utilisez les règles de corrélation intégrées telles quelles ou utilisez-les comme point de départ pour créer vos propres règles. Azure Sentinel fournit également des règles d’apprentissage machine pour mettre définir le comportement de votre réseau et rechercher les possibles anomalies sur vos ressources. Ces analyses connectent ensuite les informations en transformant les alertes basse fidélité sur différentes entités en incidents de sécurité potentiels de haute fidélité.

![Cas](./media/overview/cases.png)

## <a name="user-analytics"></a>Analytique des utilisateurs

Avec l’intégration native de l’apprentissage machine et l’[analytique utilisateur](user-analytics.md), Azure Sentinel peut contribuer à détecter rapidement les menaces. Azure Sentinel s’intègre parfaitement à Azure Advanced Threat Protection pour analyser le comportement des utilisateurs et hiérarchiser les utilisateurs que vous devez examiner en premier, en fonction des alertes et des habitudes suspectes sur Azure Sentinel et Microsoft 365.

![Analytique des utilisateurs](./media/overview/user-analytics.png)


## <a name="security-automation--orchestration"></a>Automatisation et orchestration de la sécurité

Automatisez les tâches courantes et [simplifiez l’orchestration de la sécurité avec des playbooks](tutorial-respond-threats-playbook.md) qui s’intègrent aux services Azure et à vos outils. Basée sur Azure Logic Apps, la solution d’automatisation et d’orchestration d’Azure Sentinel offre une architecture hautement extensible qui permet une automatisation capable d’évoluer avec les nouvelles technologies et les nouvelles menaces. Pour créer des playbooks avec Azure Logic Apps, vous pouvez faire votre choix dans une galerie croissante de playbooks intégrés. Ceux-ci incluent [plus de 200 connecteurs](https://docs.microsoft.com/azure/connectors/apis-list) pour différents services comme Azure Functions. Ces connecteurs vous permettent d’appliquer une logique personnalisée dans votre code, ServiceNow, Jira, Zendesk, les requêtes HTTP, Microsoft Teams, Slack, Windows Defender ATP et Cloud App Security.

Par exemple, si vous utilisez le système de tickets ServiceNow, vous pouvez utiliser les outils fournis pour utiliser Azure Logic Apps et automatiser vos flux de travail et ouvrir un ticket dans ServiceNow chaque fois qu’un événement en particulier est détecté.

![Playbooks](./media/tutorial-respond-threats-playbook/logic-app.png)



## <a name="investigation"></a>Investigation

Les outils d’[investigation approfondie](tutorial-investigate-cases.md) d’Azure Sentinel vous aident à comprendre l’étendue et à identifier la cause racine d’une menace de sécurité potentielle. Vous pouvez choisir une entité sur le graphique interactif pour poser des questions sur une entité spécifique et approfondir cette entité et ses connexions pour arriver à la cause racine de la menace. 

![Investigation](./media/overview/investigation.png)


## <a name="hunting"></a>Chasse

Utilisez les [puissants outils de recherche et de requête](hunting.md) d’Azure Sentinel, basés sur l’infrastructure MITRE, et qui vous permettent de façon proactive de rechercher les menaces de sécurité sur toutes les sources de données de votre organisation avant même le déclenchement d’une alerte. Une fois que vous avez découvert quelle requête de recherche fournit des informations d’importance sur les attaques possibles, vous pouvez également créer des règles de détection personnalisées basées sur cette requête et communiquer ces informations sous forme d’alertes au personnel en charge de la gestion des incidents de sécurité. Lors de la recherche, vous pouvez créer des signets pour les événements intéressants, ce qui vous permet d’y revenir plus tard, de les partager avec d’autres utilisateurs et de les regrouper avec d’autres événements en corrélation pour créer un dossier d’investigation solide.

![Chasse](./media/overview/hunting.png)

## <a name="community"></a>Communauté

La communauté Azure Sentinel est une ressource importante pour l’automatisation et la détection des menaces. Nos analystes de sécurité Microsoft créent et ajoutent constamment de nouveaux tableaux de bord, de nouveaux playbooks, de nouvelles requêtes de recherche, etc. et les publient dans la communauté pour que vous puissiez les utiliser dans votre environnement. Vous pouvez télécharger l’exemple de contenu dans le [référentiel](https://aka.ms/asicommunity) GitHub de la communauté pour créer des tableaux de bord personnalisés, des requêtes de recherche, des notebooks et des playbooks pour Azure Sentinel. 

![Communauté](./media/overview/community.png)

## <a name="next-steps"></a>Étapes suivantes

- Pour commencer à utiliser Azure Sentinel, vous devez disposer d’un abonnement à Microsoft Azure. Si vous n’avez pas d’abonnement, vous pouvez vous inscrire à un [essai gratuit](https://azure.microsoft.com/free/).
- Découvrez comment [intégrer vos données à Azure Sentinel](quickstart-onboard.md) et [obtenir une visibilité de vos données et des menaces potentielles](quickstart-get-visibility.md).
