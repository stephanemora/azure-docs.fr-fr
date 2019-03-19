---
title: Examiner les cas avec la version préliminaire d’Azure Sentinel | Microsoft Docs
description: Utilisez ce didacticiel pour apprendre à étudier le cas avec Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a493cd67-dc70-4163-81b8-04a9bc0232ac
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/6/2019
ms.author: rkarlin
ms.openlocfilehash: 6b3357ec06c89645b9c41e9efdb582a18af40672
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58074673"
---
# <a name="tutorial-investigate-cases-with-azure-sentinel-preview"></a>Tutoriel : Examiner les cas avec la version préliminaire d’Azure Sentinel

> [!IMPORTANT]
> Sentinel Azure est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ce didacticiel vous aide à détecter les menaces avec Azure Sentinel.

Après avoir [vos sources de données connectées](quickstart-onboard.md) à Azure Sentinel, vous souhaitez être averti en cas de quelque chose suspectes. Pour vous permettre de procéder, Sentinel Azure vous permet de que créer des avancées règles d’alerte, qui génèrent des cas que vous pouvez affecter et les utiliser pour examiner profondément les anomalies et menaces dans votre environnement. 

> [!div class="checklist"]
> * Créer des cas
> * Enquêter sur des cas
> * Répondre aux menaces

## <a name="investigate-cases"></a>Enquêter sur des cas

Un cas peut inclure plusieurs alertes. Il est une agrégation de toutes les preuves pertinentes pour une investigation spécifique. Un incident est créé en fonction des alertes que vous avez définies dans le **Analytique** page. Les propriétés relatives aux alertes, telles que le statut et de gravité sont définies au niveau du cas. Une fois que vous informer Azure Sentinel quels types de menaces que vous recherchez et pour obtenir ces informations, vous pouvez surveiller les menaces sont détectées en étudiant les cas. 

1. Sélectionnez **cas**. Le **cas** page vous permet de savoir combien de cas vous avez, combien sont ouvrir, combien vous avez défini à **en cours d’exécution**, et combien est fermés. Pour chaque cas, vous pouvez voir l’heure et l’état de la casse. Examinez la gravité de décider ce qu’il faut gérer le premier. Dans le **cas** , cliquez sur le **alertes** onglet pour afficher toutes les alertes qui sont associées à un incident. Les entités que vous avez mappés précédemment comme partie de la casse peut être affiché dans le **entités** onglet.  Vous pouvez filtrer les cas en fonction des besoins, par exemple par état ou gravité. Lorsque vous examinez le **cas** onglet, vous verrez les incidents ouverts qui contiennent les alertes déclenchées par les règles de détection définis dans **Analytique**. Dans la partie supérieure, vous verrez vos cas actifs, les nouveaux cas et dans les cas de progression. Vous pouvez également voir une vue d’ensemble de tous les cas votre par gravité.

   ![Tableau de bord alerte](./media/tutorial-investigate-cases/cases.png)

2. Pour commencer une investigation, cliquez sur un cas spécifique. Sur la droite, vous pouvez voir des informations détaillées pour le cas notamment sa gravité, résumée du nombre d’entités impliquées (selon votre mappage). Chaque cas possède un ID unique. La gravité de l’incident est déterminée en fonction de l’alerte plus graves inclus dans le cas.  

1. Pour afficher plus de détails sur les alertes et les entités dans le cas, cliquez sur **afficher les détails complets** dans le cas de page et passez en revue les onglets correspondants qui résument les informations d’incident.  Vue des cas complète regroupe toutes les preuves dans l’alerte, les alertes associées et des entités.

1. Dans le **alertes** onglet, passez en revue l’alerte elle-même - quand elle a été déclenchée et par combien il a dépassé les seuils que vous définissez. Vous pouvez voir toutes les informations pertinentes sur l’alerte : la requête qui a déclenché l’alerte, le nombre de résultats retournés par la requête et la capacité d’exécuter des playbooks sur les alertes. Pour Explorer le bas encore plus loin dans le cas, cliquez sur le nombre d’accès. La requête qui a généré les résultats et les résultats qui a déclenché l’alerte dans Log Analytique s’ouvre.

3. Dans le **entités** onglet, vous pouvez voir toutes les entités que vous avez mappé en tant que partie de la définition de règle d’alerte. 

4. Si vous recherchez activement un cas, il est judicieux de définir l’état du cas **en cours d’exécution** jusqu'à ce que vous le fermiez. Vous pouvez également fermer le cas, où **fermé résolu** est l’état pour les cas qui indiquent qu’un incident a été géré, tandis que **fermé ignorées** est l’état pour les cas qui ne nécessitent pas de gestion. Les explications sont requises expliquant le raisonnement de fermeture d’un cas.

5. Cas peuvent être affectés à un utilisateur spécifique. Pour chaque cas, vous pouvez affecter un propriétaire, en définissant le cas **propriétaire** champ. Tous les cas début, en tant que non attribués. Vous pouvez aller dans les cas et filtrer par votre nom pour voir tous les cas que vous possédez. 

5. Cliquez sur **examiner** pour afficher le mappage d’investigation et de la portée de la violation avec les étapes de correction. 



## <a name="respond-to-threats"></a>Répondre aux menaces

Sentinel Azure vous offre deux options principales pour répondre aux menaces à l’aide de règles. Vous pouvez définir un playbook pour exécuter automatiquement lorsqu’une alerte est déclenchée, ou vous pouvez exécuter manuellement un manuel en réponse à une alerte.

- Vous pouvez définir un playbook pour exécuter automatiquement lorsqu’une alerte est déclenchée lorsque vous configurez le playbook. 

- Vous pouvez exécuter manuellement un manuel à partir d’à l’intérieur de l’alerte, en cliquant sur **afficher playbooks** , puis en sélectionnant un playbook à exécuter.




## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris comment commencer à examiner les cas à l’aide d’Azure Sentinel. Passez au didacticiel pour [comment répondre aux menaces à l’aide de règles automatisées](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Répondre aux menaces](tutorial-respond-threats-playbook.md) pour automatiser vos réponses aux menaces.

