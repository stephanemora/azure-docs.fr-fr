---
title: Examiner les alertes avec la préversion d’Azure Sentinel | Microsoft Docs
description: Utilisez ce didacticiel pour apprendre à examiner les alertes avec Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: b5fbc5ac-68b2-4024-9c1b-bd3cc41a66d0
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/20/2019
ms.author: rkarlin
ms.openlocfilehash: 2cc33a9ac55ae9e906d88b72476d4b5ee244d2c8
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780423"
---
# <a name="tutorial-detect-threats-with-azure-sentinel-preview"></a>Didacticiel : Détecter les menaces avec la préversion d’Azure Sentinel

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Après avoir [connecté vos sources de données](quickstart-onboard.md) à Azure Sentinel, vous souhaitez être averti en cas d’activité suspecte. Pour vous permettre de l’être, Azure Sentinel vous permet de créer des règles d’alerte avancées, qui génèrent des incidents que vous pouvez attribuer et utiliser pour examiner en détail les anomalies et les menaces dans votre environnement. 

Ce didacticiel vous aide à détecter les menaces avec Azure Sentinel.
> [!div class="checklist"]
> * Créer des règles de détection
> * Automatiser les réponses aux menaces

## <a name="create-detection-rules"></a>Créer des règles de détection

Pour examiner les incidents, vous devez d’abord créer des règles de détection. 

> [!NOTE]
> Les alertes générées dans Azure Sentinel sont disponibles via [Microsoft Graph Security](https://aka.ms/securitygraphdocs). Reportez-vous à la [documentation sur les alertes Microsoft Graph Security](https://aka.ms/graphsecurityreferencebetadocs)pour obtenir des informations supplémentaires et connaître les partenaires d’intégration.

Les règles de détection sont basées sur les types de menaces et d’anomalies qui pourraient être suspectes dans votre environnement et pour lesquelles vous souhaitez être averti tout de suite, en vous assurant qu’elles sont exposées, examinées et corrigées. 

1. Dans le portail Azure, sous Azure Sentinel, sélectionnez **Analytique**.

   ![Analytics](./media/tutorial-detect-threats/alert-rules.png)

2. Dans la barre de menus supérieure, cliquez sur **+Ajouter**.  

   ![Créer une règle d’alerte](./media/tutorial-detect-threats/create-alert-rule.png)

3. Sous **Créer une règle d’alerte**, fournissez un nom descriptif et définissez le paramètre **Gravité** en fonction des besoins. 

4. Créez la requête dans Log Analytics et collez-la dans le champ **Set alert rule** (Définir une règle d’alerte). Voici un exemple de requête qui vous avertit quand un nombre anormal de ressources est créé dans Activité Azure.

        AzureActivity
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"
        | where ActivityStatus == "Succeeded"
        | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller

   > [!NOTE]
   > La longueur de la requête doit être comprise entre 1 et 10 000 caractères et qui ne peut pas contenir « search * » ni « union * ».


5. Dans la section **Mappage d’entités**, utilisez les champs sous **Type d’entité** pour mapper les colonnes dans votre requête aux champs d’entité reconnus par Azure Sentinel. Pour chaque champ, mappez la colonne concernée dans la requête que vous avez créée dans Log Analytics, au champ de l’entité appropriée. Sélectionnez le nom de la colonne appropriée sous la **propriété**. Chaque entité inclut plusieurs champs, par exemple SID, GUID, etc. Vous pouvez mapper l’entité à n’importe quel champ, pas seulement à l’entité de niveau supérieur.

6. Définissez des conditions de déclencheur d’alerte sous **Alert trigger** (Déclencheur d’alerte). Cela permet de définir les conditions qui déclenchent l’alerte. 

7. Définissez le paramètre **Fréquence** pour déterminer la fréquence d’exécution de la requête - fréquemment, toutes les 5 minutes ou rarement, une fois par jour. 

8. Définissez le paramètre **Période** pour contrôler la fenêtre de temps de la quantité de données sur laquelle la requête s’exécute - par exemple, elle peut s’exécuter toutes les heures sur 60 minutes de données.

9. Vous pouvez également définir le paramètre **Suppression**. Celui-ci est utile lorsque vous souhaitez arrêter le déclenchement d’alertes en double pour le même incident. De cette façon, vous pouvez arrêter le déclenchement d’alertes pendant une période donnée. Cela peut vous aider à éviter les alertes en double pour le même incident et vous permettre de supprimer les alertes consécutives pour une période donnée. Par exemple, si les paramètres **Alert scheduling** (Planification d’alerte) **Fréquence** sont définis sur 60 minutes et que le paramètre **Alert scheduling Period** (Période de planification d’alerte) est défini sur deux heures, et que les résultats de la requête ont dépassé le seuil défini, une alerte est déclenchée à deux reprises : une première fois quand le dépassement est détecté après les 60 dernières minutes et une seconde fois lors des premières 60 minutes des deux heures de l’échantillonnage des données. Si une alerte est déclenchée, nous vous recommandons de définir la suppression sur la quantité de temps définie dans la période d’alerte. Dans notre exemple, vous souhaitez définir la suppression pour 60 minutes, de façon que les alertes soient uniquement déclenchées pour les événements qui ont eu lieu au cours de la dernière heure.

8. Après avoir collé votre requête dans le champ **Set alert rule** (Définir une règle d’alerte), vous pouvez tout de suite voir une simulation de l’alerte sous **Logic alert simulation** (Simulation d’alerte logique) afin de comprendre quelle quantité de données sera générée après un intervalle de temps spécifique pour l’alerte que vous avez créée. Cela va dépendre de ce que vous définissez pour **Fréquence** et **Seuil**. Si vous voyez qu’en moyenne, votre alerte est déclenchée trop fréquemment, vous pouvez définir un nombre de résultats plus élevé, de façon qu’il soit au-dessus de votre référence moyenne.

9. Cliquez sur **Créer** pour initialiser votre règle d’alerte. Une fois que l’alerte est créée, un incident contenant l’alerte est créé. Vous pouvez voir les règles de détection définies sous forme de lignes dans l’onglet **Security Analytics** (Analyse de sécurité). Vous pouvez également voir le nombre de correspondances pour chaque règle - les alertes déclenchées. Dans cette liste, vous pouvez activer, désactiver ou supprimer chaque règle. Vous pouvez également faire un clic droit sur les points de suspension (...) à la fin de la ligne pour chaque alerte, afin de modifier, de désactiver, de cloner, d’afficher les correspondances ou de supprimer une règle. La page **	Analyse** est une galerie de toutes vos règles d’alerte actives, y compris des modèles que vous activez et des règles d’alerte que vous créez en fonction des modèles.

1. Les résultats des règles d’alerte peuvent être consultés sur la page **Incidents**, depuis laquelle vous pouvez trier, [examiner les incidents](tutorial-investigate-cases.md) et corriger les menaces.



## <a name="automate-threat-responses"></a>Automatiser les réponses aux menaces

Les équipes SIEM/SOC peuvent être régulièrement submergées d’alertes de sécurité. Le volume d’alertes générées est tellement important que les administrateurs de sécurité disponibles sont débordés. Il en résulte trop souvent des situations où de nombreuses alertes ne peuvent pas être examinées. L’organisation reste donc vulnérable aux attaques qui passent inaperçues. 

Beaucoup, voire la plupart, de ces alertes sont conformes à des modèles récurrents qui peuvent être traités par des actions de correction spécifiques et définies. Azure Sentinel vous permet déjà de définir votre correction en vous basant sur des playbooks. Il est également possible de définir l’automatisation en temps réel dans le cadre de la définition de votre playbook afin de pouvoir automatiser entièrement une réponse définie à des alertes de sécurité particulières. Grâce à l’automatisation complète des réponses de routine aux types d’alertes récurrents, les équipes de réponse peuvent réduire considérablement leur charge de travail, ce qui leur permet de se concentrer sur des alertes uniques, d’analyser des modèles et de repérer les menaces, entre autres.

Pour automatiser les réponses :

1. Choisissez l’alerte pour laquelle vous souhaitez automatiser la réponse.
1. Dans le menu de navigation de l’espace de travail Azure Sentinel, sélectionnez **Analytics**.
1. Sélectionnez l’alerte que vous souhaitez automatiser. 
1. Dans la page **Modifier la règle d’alerte**, sous **Automatisation en temps réel**, choisissez le **playbook déclenché** que vous souhaitez exécuter en cas d’alerte correspondante.
1. Sélectionnez **Enregistrer**.

   ![automatisation en temps réel](./media/tutorial-detect-threats/rt-configuration.png)


Par ailleurs, vous pouvez résoudre manuellement une erreur en exécutant un playbook depuis l’intérieur de l’alerte, en cliquant sur **Afficher les playbooks** puis en sélectionnant un playbook à exécuter. Pour apprendre à créer un playbook ou à modifier un playbook existant, voir [Working with playbooks in Azure Sentinel (Utilisation de playbooks dans Azure Sentinel)](tutorial-respond-threats-playbook.md).



## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à prendre en main la détection des menaces en utilisant Azure Sentinel. 

Pour apprendre à automatiser vos réponses aux menaces, consultez le didacticiel expliquant [comment répondre aux menaces à l’aide de playbooks automatisés](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Répondre aux menaces](tutorial-respond-threats-playbook.md) pour automatiser vos réponses aux menaces.

