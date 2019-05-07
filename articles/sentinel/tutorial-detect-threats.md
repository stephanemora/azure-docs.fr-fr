---
title: Examiner les alertes avec la version préliminaire d’Azure Sentinel | Microsoft Docs
description: Utilisez ce didacticiel pour apprendre à examiner les alertes avec Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: b5fbc5ac-68b2-4024-9c1b-bd3cc41a66d0
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2019
ms.author: rkarlin
ms.openlocfilehash: 319ec5d09a6daddb5c1fc36f680ee6d0d856e337
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205427"
---
# <a name="tutorial-detect-threats-with-azure-sentinel-preview"></a>Didacticiel : Détectez les menaces avec la version préliminaire d’Azure Sentinel

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Après avoir [vos sources de données connectées](quickstart-onboard.md) à Azure Sentinel, vous souhaitez être averti en cas de quelque chose suspectes. Pour vous permettre de procéder, Sentinel Azure vous permet de que créer des avancées règles d’alerte, qui génèrent des cas que vous pouvez affecter et les utiliser pour examiner profondément les anomalies et menaces dans votre environnement. 

Ce didacticiel vous aide à détecter les menaces avec Azure Sentinel.
> [!div class="checklist"]
> * Créer des règles de détection
> * Répondre aux menaces

## <a name="create-detection-rules"></a>Créer des règles de détection

Pour examiner les cas, vous devez d’abord créer des règles de détection. 

> [!NOTE]
> Alertes générées dans Azure Sentinel sont disponibles via [Microsoft Graph Security](https://aka.ms/securitygraphdocs). Reportez-vous à la [documentation sur les alertes Microsoft Graph Security](https://aka.ms/graphsecurityreferencebetadocs) des détails supplémentaires et des partenaires d’intégration.

Règles de détection sont basées sur les types de menaces et anomalies qui pourraient être suspectes dans votre environnement que vous souhaitez savoir sur tout de suite, en vous assurant qu’ils sont exposés, examinés et mis à jour. 

1. Dans le portail Azure sous Azure Sentinel, sélectionnez **Analytique**.

   ![Analytics](./media/tutorial-detect-threats/alert-rules.png)

2. Dans la barre de menus supérieure, cliquez sur **+ ajouter**.  

   ![Créer une règle d’alerte](./media/tutorial-detect-threats/create-alert-rule.png)

3. Sous **créer une règle d’alerte**, fournissez un nom descriptif et définir le **gravité** en fonction des besoins. 

4. Créer la requête dans Log Analytique et collez-la dans la **ensemble une règle d’alerte** champ. Voici un exemple de requête qui serait vous alerte quand un nombre anormal de ressources est créé dans activité Azure.

        AzureActivity
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"
        | where ActivityStatus == "Succeeded"
        | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller

5. Dans le **mappage d’entité** section, utilisez les champs sous **type d’entité** pour mapper les colonnes dans votre requête pour les champs d’entité reconnues par Azure Sentinel. Pour chaque champ, mappez la colonne concernée dans la requête que vous avez créé dans le journal Analytique, pour le champ d’entité appropriée. Sélectionnez le nom de la colonne appropriée sous le **propriété**. Chaque entité inclut plusieurs champs, par exemple SID, GUID, etc. Vous pouvez mapper l’entité selon l’un des champs, pas seulement l’entité de niveau supérieure.

6. Définir des conditions de déclenchement d’alerte sous **de déclenchement d’alerte**. Définit les conditions qui déclenchent l’alerte. 

7. Définir le **fréquence** pour la fréquence à laquelle la requête est exécutée - comme fréquemment toutes les 5 minutes ou très rarement une fois par jour. 

8. Définir le **période** pour contrôler la fenêtre de temps pour la quantité de données la requête s’exécute sur - par exemple, il peut s’exécuter toutes les heures sur 60 minutes de données.

9. Vous pouvez également définir le **Suppression**. La suppression est utile lorsque vous souhaitez arrêter les alertes en double à partir de déclenchement pour le même incident. De cette façon, vous pouvez arrêter les alertes de déclenchement pendant une période spécifique. Cela peut vous aider à éviter les alertes en double pour le même incident et vous permet de supprimer les alertes consécutives pour une période donnée. Par exemple, si le **planification d’alerte** **fréquence** est définie sur 60 minutes et le **période de planification d’alerte** est fixée à deux heures, et les résultats de requête dépassé défini seuil, il déclenche une alerte à deux reprises, une fois quand il est tout d’abord détectée au cours des 60 dernières minutes, et à nouveau lorsqu’il est le premier des 60 dernières minutes des 2-heures de données échantillonnées. Nous recommandons que si une alerte est déclenchée, la suppression doit être la durée définie dans la période d’alerte. Dans notre exemple, vous souhaiterez définie suppression pendant 60 minutes, afin que les alertes sont déclenchées uniquement pour les événements qui se sont produites pendant l’heure la plus récente.

8. Une fois que vous collez votre requête dans le **ensemble une règle d’alerte** champ, vous pouvez immédiatement voir une simulation de l’alerte sous **simulation alerte logique** afin que vous puissiez obtenir la présentation de la quantité de données sera généré sur un intervalle de temps spécifique de l’alerte que vous avez créé. Cela dépend de ce que vous définissez pour **fréquence** et **seuil**. Si vous voyez qu’en moyenne, votre alerte est déclenchée trop fréquemment, vous pouvez définir le nombre de résultats supérieur afin qu’il soit au-dessus de votre ligne de base de la moyenne.

9. Cliquez sur **créer** pour initialiser votre règle d’alerte. Une fois que l’alerte est créée, un incident est créé qui contient l’alerte. Vous pouvez voir les règles de détection défini sous forme de lignes dans le **Analytique de sécurité** onglet. Vous pouvez également voir le nombre de correspondances pour chaque règle - les alertes déclenchées. Dans cette liste, que vous pouvez activer, désactiver ou supprimer chaque règle. Vous pouvez également droit-sélectionnez les points de suspension (...) à la fin de la ligne pour chaque alerte à modifier, désactiver, cloner, afficher les correspondances ou supprimer une règle. Le **Analytique** page est une galerie de toutes vos règles d’alerte actives, y compris les modèles vous activez et vous créez en fonction des modèles de règles d’alerte.

1. Les résultats les règles d’alerte peuvent être consultés dans le **cas** page, où vous pouvez trier, [examiner les cas](tutorial-investigate-cases.md), et contrer les menaces.



## <a name="respond-to-threats"></a>Répondre aux menaces

Sentinel Azure vous offre deux options principales pour répondre aux menaces à l’aide de règles. Vous pouvez définir un playbook pour exécuter automatiquement lorsqu’une alerte est déclenchée, ou vous pouvez exécuter manuellement un manuel en réponse à une alerte.

- Définir un playbook pour exécuter automatiquement lorsqu’une alerte est déclenchée lorsque vous configurez le playbook. 

- Exécuter manuellement un manuel à partir d’à l’intérieur de l’alerte, en cliquant sur **afficher playbooks** , puis en sélectionnant un playbook à exécuter.




## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris comment commencer à détecter des menaces à l’aide d’Azure Sentinel. 

Pour apprendre à automatiser vos réponses aux menaces, [comment répondre aux menaces à l’aide de règles automatisées](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Répondre aux menaces](tutorial-respond-threats-playbook.md) pour automatiser vos réponses aux menaces.

