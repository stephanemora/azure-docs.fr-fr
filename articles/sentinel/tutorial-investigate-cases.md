---
title: Examiner les cas avec la préversion d’Azure Sentinel | Microsoft Docs
description: Utilisez ce didacticiel pour apprendre à étudier les cas avec Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: a493cd67-dc70-4163-81b8-04a9bc0232ac
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/6/2019
ms.author: rkarlin
ms.openlocfilehash: d1da180d3b30b57ca2b69985bf3d0261b8d70c39
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65205518"
---
# <a name="tutorial-investigate-cases-with-azure-sentinel-preview"></a>Didacticiel : Examiner les cas avec la préversion d’Azure Sentinel

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ce didacticiel vous aide à détecter les menaces avec Azure Sentinel.

Une fois [connecté à vos sources de données](quickstart-onboard.md) à Azure Sentinel, vous souhaitez être averti en cas d’activité suspecte. Pour vous permettre de l’être, Azure Sentinel vous permet de créer des règles d’alerte avancées, qui génèrent des cas que vous pouvez attribuer et utiliser pour examiner en détail les anomalies et les menaces dans votre environnement. 

> [!div class="checklist"]
> * Créer des cas
> * Enquêter sur des cas
> * Répondre aux menaces

## <a name="investigate-cases"></a>Enquêter sur des cas

Un cas peut inclure plusieurs alertes. C’est une agrégation de toutes les preuves pertinentes pour une investigation spécifique. Un cas est créé en fonction des alertes que vous avez définies sur la page **Analytics**. Les propriétés relatives aux alertes, telles que l’état et la gravité sont définies au niveau du cas. Après avoir informé Azure Sentinel des types de menaces que vous recherchez et de comment les trouver, vous pouvez surveiller les menaces détectées en étudiant des cas. 

1. Sélectionnez **Cas**. La page **cas** vous permet de savoir combien de cas vous avez, combien sont ouverts, combien vous avez défini sur **En cours**, et combien sont fermés. Pour chaque cas, vous pouvez voir l’heure à laquelle il s’est produit et son état. Examinez la gravité de décider ce qui doit être géré en premier. Sur la page **cas**, cliquez sur l’onglet **Alertes** pour afficher toutes les alertes associées à un cas. Les entités que vous avez mappées précédemment comme faisant partie du cas peuvent être affichées dans l’onglet **Entités**.  Vous pouvez filtrer les cas selon les besoins, par état ou par gravité par exemple. Lorsque vous examinez l’onglet **Cas**, vous verrez les cas ouverts qui contiennent des alertes déclenchées par vos règles de détection définies dans **Analytics**. Dans la partie supérieure, vous verrez vos cas actifs, les nouveaux cas et les cas en cours de traitement. Vous pouvez également afficher une vue d’ensemble de tous les cas par ordre de gravité.

   ![Tableau de bord d’alerte](./media/tutorial-investigate-cases/cases.png)

2. Pour commencer une investigation, cliquez sur un cas spécifique. Sur la droite, vous pouvez voir des informations détaillées sur le cas, notamment sa gravité, un résumé du nombre d’entités impliquées (selon votre mappage). Chaque cas possède un ID unique. La gravité du cas est déterminée en fonction de l’alerte plus grave inclus dans le cas.  

1. Pour afficher plus de détails sur les alertes et les entités du cas, cliquez sur **Afficher les détails complets** sur la page du cas et passez en revue les onglets correspondants qui résument les informations du cas.  La vue complète du cas regroupe toutes les preuves dans l’alerte, les alertes associées et les entités.

1. Dans l’onglet **Alertes**, passez en revue l’alerte elle-même, le moment où elle a été déclenchée, de combien elle a dépassé les seuils que vous avez définis. Vous pouvez voir toutes les informations pertinentes sur l’alerte : la requête ayant déclenché l’alerte, le nombre de résultats retournés par la requête et la capacité d’exécuter des playbooks sur les alertes. Pour aller encore plus en détail dans le cas, cliquez sur le nombre d’accès. Cela ouvre la requête ayant généré les résultats et les résultats ayant déclenché l’alerte dans Log Analytics.

3. Dans l’onglet **Entités**, vous pouvez voir toutes les entités mappées comme faisant partie de la définition d’une règle d’alerte. 

4. Si vous investiguez activement un cas, il est judicieux de définir l’état du cas sur **En cours** jusqu’à ce que vous ayez fini de le traiter. Vous pouvez également fermer le cas, où **Fermé résolu** est l’état pour les cas indiquant qu’un incident a été traité, tandis que **Fermé ignoré** est l’état destiné aux cas ne nécessitant aucune prise en main. Des explications sont requises lors de la fermeture d’un cas.

5. Les cas peuvent être assignés à un utilisateur spécifique. Pour chaque cas, vous pouvez affecter un propriétaire, en définissant le champ **Propriétaire** du cas. Tous les cas sont créés en étant pas attribués. Vous pouvez aller dans les cas et faire un filtrage avec votre nom pour voir les cas qui vous sont assignés. 

5. Cliquez sur **Examiner** pour afficher le mappage d’investigation et la portée de la violation avec les étapes de correction. 



## <a name="respond-to-threats"></a>Répondre aux menaces

Azure Sentinel vous offre deux options principales pour répondre aux menaces à l’aide de playbooks. Vous pouvez définir un playbook pour qu’ils soit exécuté automatiquement lorsqu’une alerte est déclenchée, ou vous pouvez en exécuter un manuellement en réponse à une alerte.

- Lors de la configuration d’un playbook, vous pouvez faire qu’il s’exécute automatiquement lorsqu’une alerte est déclenchée. 

- Vous pouvez exécuter un playbook manuellement depuis l’intérieur de l’alerte, en cliquant sur **Afficher les playbooks** puis en sélectionnant un playbook à exécuter.




## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à commencer à examiner les cas en utilisant Azure Sentinel. Passez au didacticiel vous apprenant [comment répondre aux menaces à l’aide de playbooks automatisés](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Répondre aux menaces](tutorial-respond-threats-playbook.md) pour automatiser vos réponses aux menaces.

