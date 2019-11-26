---
title: Créer des règles d’analytique personnalisées pour détecter les menaces suspectes avec Azure Sentinel | Microsoft Docs
description: Ce tutoriel vous montre comment créer des règles d’analytique personnalisées pour détecter les menaces suspectes avec Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 9c4ba09c7e3eca4482ed56b0b337124aeec5b838
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73928252"
---
# <a name="tutorial-create-custom-analytic-rules-to-detect-suspicious-threats"></a>Didacticiel : Créer des règles d’analytique personnalisées pour détecter les menaces suspectes

Après avoir [connecté vos sources de données](quickstart-onboard.md) à Azure Sentinel, vous pouvez créer des règles personnalisées qui effectuent des recherches dans votre environnement sur la base de critères spécifiques et qui génèrent des incidents lorsqu’elles trouvent des menaces correspondant aux critères. Vous pouvez ensuite examiner les incidents signalés. Ce tutoriel vous aide à créer des règles personnalisées pour détecter les menaces avec Azure Sentinel.

Ce didacticiel vous aide à détecter les menaces avec Azure Sentinel.
> [!div class="checklist"]
> * Créer des règles d’analytique
> * Automatiser les réponses aux menaces

## <a name="create-custom-analytic-rules"></a>Créer des règles d’analytique personnalisées

Vous pouvez créer des règles d’analytique personnalisées pour détecter plus facilement les types de menaces et d’anomalies suspectes dans votre environnement. En utilisant une règle, vous êtes sûr d’être immédiatement averti des menaces et ainsi de pouvoir les trier, les examiner et les corriger au plus vite.

1. Dans le portail Azure, sous Azure Sentinel, sélectionnez **Analytique**.

1. Dans la barre de menus du haut, sélectionne **+Créer**, puis **Règle de requête planifiée**. L’**Assistant de création de règle personnalisée** s’ouvre.

    ![Créer une requête planifiée](media/tutorial-detect-threats-custom/create-scheduled-query.png)

1. Sous l’onglet **Général**, indiquez un nom descriptif et entrez une description. Définissez la **Gravité d’alerte** si nécessaire. Quand vous créez la règle, vous pouvez aussi l’activer. La règle sera alors exécutée dès la fin du processus de création. Si vous créez la règle sans l’activer, elle est ajoutée à l’onglet **Règles actives**, d’où vous pouvez l’activer dès que vous en avez besoin.

    ![Démarrer la création d’une règle d’analytique personnalisée](media/tutorial-detect-threats-custom/general-tab.png)

1. Sous l’onglet**Paramètres**, vous pouvez soit écrire une requête directement, soit créer la requête dans Log Analytics, puis la coller dans le champ **Requête de recherche**. À mesure que vous configurez et modifiez votre requête, Azure Sentinel en simule les résultats dans la fenêtre **Aperçu des résultats**, sur la droite. Cela vous renseigne sur la quantité de données susceptibles d’être générées sur l’intervalle de temps spécifié pour l’alerte que vous êtes en train de créer. La quantité dépend de ce que vous avez défini pour la requête **Run query every** (Exécuter la requête tous/toutes les) et **Lookup data from the last** (Données de recherche du dernier/de la dernière). Si vous constatez qu’en moyenne votre requête déclencherait des alertes trop fréquemment, vous pouvez augmenter le nombre de résultats à une valeur supérieure à votre référence moyenne.

   ![Créer une requête dans Azure Sentinel](media/tutorial-detect-threats-custom/settings-tab.png)

   Voici un exemple de requête qui vous avertit quand un nombre anormal de ressources est créé dans Activité Azure.

    `AzureActivity
    \| where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
    \| where ActivityStatus == "Succeeded"
    \| make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller`

   > [!NOTE]
   > La requête peut comporter entre 1 et 10 000 caractères, et ne doit pas contenir « search \* » ou « union \* ».

    1. Sous **Query scheduling** (Planification de requête), définissez les paramètres suivants :

        1.  Définissez le paramètre **Run query every** (Exécuter la requête tous/toutes les) pour indiquer la fréquence d’exécution de la requête (au maximum toutes les cinq minutes et au minimum une fois par jour) dans **Frequency**.

        1.  Définissez le paramètre **Lookup data from the last** (Antériorité des données à rechercher) pour contrôler la fenêtre de temps qui détermine la quantité de données sur laquelle la requête doit s’exécuter (par exemple, elle peut s’exécuter toutes les heures, sur 60 minutes de données).

        1. Vous pouvez configurer Azure Sentinel pour **arrêter l’exécution de la requête après la génération d’une alerte** si vous souhaitez être notifié à chaque alerte générée. Vous devez définir la fenêtre (jusqu’à 24 heures) durant laquelle la requête doit s’arrêter.

    1. Définissez des conditions de déclencheur d’alerte sous **Alert trigger** (Déclencheur d’alerte). Sous **Entity mapping** (Mappage d’entités), vous pouvez mapper les colonnes de votre requête aux champs d’entité reconnus par Azure Sentinel. Pour chaque champ, mappez la colonne correspondante dans la requête que vous avez créée dans Log Analytics, au champ de l’entité appropriée. Chaque entité inclut plusieurs champs, comme SID et GUID. Vous pouvez mapper l’entité à n’importe quel champ, pas seulement à l’entité de niveau supérieur.

1.  Sous l’onglet **Automate responses** (Automatiser les réponses), sélectionnez les playbooks à exécuter automatiquement quand une alerte est générée par la règle personnalisée. Pour plus d’informations sur la création et l’automatisation de playbooks, consultez [Répondre aux menaces](tutorial-respond-threats-playbook.md).

    ![Automatiser la réponse aux menaces dans Azure Sentinel](media/tutorial-detect-threats-custom/response-automation-custom.png)

1. Sélectionnez **Review** (Vérifier) pour vérifier tous les paramètres de votre nouvelle règle d’alerte, puis sélectionnez **Create to initialize your alert rule** (Créer pour initialiser votre règle d’alerte).

   ![Vérifier votre requête personnalisée](media/tutorial-detect-threats-custom/review-tab.png)

1.  Une fois l’alerte créée, une règle personnalisée est ajoutée au tableau sous **Règles actives**. Dans cette liste, vous pouvez activer, désactiver ou supprimer chaque règle.

1.  Pour voir les résultats de vos règles d’alerte, accédez à la page **Incidents**, où vous pouvez ensuite trier,  [examiner les incidents](tutorial-investigate-cases.md) et corriger les menaces.


> [!NOTE]
> Les alertes générées dans Azure Sentinel sont consultables dans  [Microsoft Graph Security](https://aka.ms/securitygraphdocs). Pour plus d’informations, consultez la [documentation sur les alertes Microsoft Graph Security](https://aka.ms/graphsecurityreferencebetadocs).

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à prendre en main la détection des menaces en utilisant Azure Sentinel.

Pour automatiser vos réponses aux menaces, découvrez comment [Configurer des réponses automatisées aux menaces dans Azure Sentinel](tutorial-respond-threats-playbook.md).

