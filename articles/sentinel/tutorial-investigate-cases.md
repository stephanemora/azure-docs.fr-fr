---
title: Examiner les incidents avec la préversion d’Azure Sentinel | Microsoft Docs
description: Utilisez ce tutoriel pour apprendre à examiner les alertes avec Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: a493cd67-dc70-4163-81b8-04a9bc0232ac
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/6/2019
ms.author: rkarlin
ms.openlocfilehash: bad3fddd6caf7e6eb455e59280f181c787b95a4e
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780398"
---
# <a name="tutorial-investigate-incidents-with-azure-sentinel-preview"></a>Didacticiel : Examiner les incidents avec la préversion d’Azure Sentinel

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ce didacticiel vous aide à détecter les menaces avec Azure Sentinel.

Après avoir [connecté vos sources de données](quickstart-onboard.md) à Azure Sentinel, vous souhaitez être averti en cas d’activité suspecte. Pour vous permettre de l’être, Azure Sentinel vous permet de créer des règles d’alerte avancées, qui génèrent des incidents que vous pouvez attribuer et utiliser pour examiner en détail les anomalies et les menaces dans votre environnement. 

> [!div class="checklist"]
> * Créer des incidents
> * Investiguer les incidents
> * Répondre aux menaces

## <a name="investigate-incidents"></a>Investiguer les incidents

un incident peut inclure plusieurs alertes. C’est une agrégation de toutes les preuves pertinentes pour une investigation spécifique. un incident est créé en fonction des alertes que vous avez définies sur la page **Analytics**. Les propriétés relatives aux alertes, telles que l’état et la gravité sont définies au niveau de l’incident. Après avoir informé Azure Sentinel des types de menaces que vous recherchez et de comment les trouver, vous pouvez surveiller les menaces détectées en étudiant des incidents. 

1. Sélectionnez **Incidents**. La page **Incidents** vous permet de connaître le nombre d’incidents, le nombre d’incidents ouverts, le nombre d’incidents définis sur **En cours** et le nombre d’incidents clos. Pour chaque incident, vous pouvez voir l’heure à laquelle il s’est produit et son état. Examinez la gravité de décider ce qui doit être géré en premier. Sur la page **Incidents**, cliquez sur l’onglet **Alertes** pour afficher toutes les alertes associées à un incident. Les entités que vous avez mappées précédemment comme faisant partie de l’incident peuvent être affichées dans l’onglet **Entités**.  Vous pouvez filtrer les incidents selon les besoins, par état ou par gravité par exemple. Lorsque vous examinez l’onglet **Incidents**, vous verrez les incidents ouverts qui contiennent des alertes déclenchées par vos règles de détection définies dans **Analytics**. Dans la partie supérieure, vous verrez vos incidents actifs, les nouveaux incidents et les incidents en cours. Vous pouvez également voir une vue d’ensemble de tous vos incidents par gravité.

   ![Tableau de bord d’alerte](./media/tutorial-investigate-cases/cases.png)

2. Pour commencer une investigation, cliquez sur un incident spécifique. Sur la droite, vous pouvez voir des informations détaillées sur l’incident, notamment sa gravité, le résumé du nombre d’entités impliquées (en fonction de votre mappage). Chaque incident a un ID unique. La gravité de l’incident est déterminée en fonction de l’alerte la plus grave incluse dans l’incident.  

1. Pour afficher plus de détails sur les alertes et les entités de l’incident, cliquez sur **Afficher tous les détails** dans la page incident et passez en revue les onglets pertinents qui résument les informations sur l’incident.  L’affichage complet des incidents regroupe toutes les preuves dans l’alerte, les alertes et les entités associées.

1. Dans l’onglet **Alertes**, passez en revue l’alerte elle-même, le moment où elle a été déclenchée, de combien elle a dépassé les seuils que vous avez définis. Vous pouvez voir toutes les informations pertinentes sur l’alerte : la requête ayant déclenché l’alerte, le nombre de résultats retournés par la requête et la capacité d’exécuter des playbooks sur les alertes. Pour aller encore plus en détail dans l’incident, cliquez sur le nombre d’accès. Cela ouvre la requête ayant généré les résultats et les résultats ayant déclenché l’alerte dans Log Analytics.

3. Dans l’onglet **Entités**, vous pouvez voir toutes les entités mappées comme faisant partie de la définition d’une règle d’alerte. 

4. Si vous étudiez activement un incident, il est judicieux de définir l’état de l’incident sur **En cours** jusqu’à ce que vous le fermiez. Vous pouvez également fermer l’incident, où **Fermé résolu** est l’état pour les incidents indiquant qu’un incident a été traité, tandis que **Fermé ignoré** est l’état destiné aux incidents ne nécessitant aucun traitement. Des explications sont requises lors de la fermeture d’un incident.

5. Les incidents peuvent être assignés à un utilisateur spécifique. Pour chaque incident, vous pouvez affecter un propriétaire, en définissant le champ **Propriétaire** du cas. Tous les incidents ne sont pas assignés lorsqu’ils sont créés. Vous pouvez aller dans les incidents et faire un filtrage avec votre nom pour voir les incidents qui vous sont assignés. 

5. Cliquez sur **Examiner** pour afficher le mappage d’investigation et la portée de la violation avec les étapes de correction. 



## <a name="respond-to-threats"></a>Répondre aux menaces

Azure Sentinel vous offre deux options principales pour répondre aux menaces à l’aide de playbooks. Vous pouvez définir un playbook pour qu’ils soit exécuté automatiquement lorsqu’une alerte est déclenchée, ou vous pouvez en exécuter un manuellement en réponse à une alerte.

- Lors de la configuration d’un playbook, vous pouvez faire qu’il s’exécute automatiquement lorsqu’une alerte est déclenchée. 

- Vous pouvez exécuter un playbook manuellement depuis l’intérieur de l’alerte, en cliquant sur **Afficher les playbooks** puis en sélectionnant un playbook à exécuter.




## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à commencer à examiner les incidents à l’aide d’Azure Sentinel. Passez au didacticiel vous apprenant [comment répondre aux menaces à l’aide de playbooks automatisés](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Répondre aux menaces](tutorial-respond-threats-playbook.md) pour automatiser vos réponses aux menaces.

