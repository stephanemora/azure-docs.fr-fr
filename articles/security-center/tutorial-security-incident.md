---
title: Didacticiel sur la réponse aux incidents - Azure Security Center
description: Dans ce tutoriel, vous apprenez à trier les alertes de sécurité, à déterminer la cause racine et l’étendue d’un incident, et à effectuer des recherches dans les données de sécurité.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 181e3695-cbb8-4b4e-96e9-c4396754862f
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2018
ms.author: memildin
ms.openlocfilehash: 49e2af8fd2d71e1e5103f5337d5c7104c2904322
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/27/2019
ms.locfileid: "74559200"
---
# <a name="tutorial-respond-to-security-incidents"></a>Didacticiel : Répondre à des incidents de sécurité
Security center analyse continuellement vos charges de travail de cloud hybride à l’aide d’analyses avancées et d’informations sur les menaces pour vous avertir des activités malveillantes. En outre, vous pouvez intégrer des alertes à partir d’autres produits et services de sécurité dans Security Center et créer des alertes personnalisées en fonction de vos propres indicateurs ou sources d’informations. Une fois une alerte générée, une action rapide est nécessaire pour examiner et résoudre la situation. Ce didacticiel vous apprendra à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Trier les alertes de sécurité
> * Examiner de plus près pour déterminer la cause racine et l’étendue d’un incident de sécurité
> * Rechercher des données de sécurité pour faciliter les recherches

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis
Pour parcourir les fonctionnalités traitées dans ce didacticiel, vous devez avoir accès au niveau tarifaire Standard de Security Center. Vous pouvez essayer Security Center Standard gratuitement. Pour en savoir plus, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/security-center/). Le démarrage rapide [Intégrer votre abonnement Azure à Security Center Standard](security-center-get-started.md) vous guide dans la mise à niveau vers le plan Standard.

## <a name="scenario"></a>Scénario
Contoso a récemment migré une partie de ses ressources locales dans Azure, notamment certaines bases de données SQL et charges de travail métier reposant sur des machines virtuelles. L’équipe principale de réponse aux incidents de sécurité informatique (CSIRT) de Contoso rencontre aujourd’hui des difficultés pour étudier les problèmes de sécurité car les renseignements sur la sécurité ne sont pas intégrés à leurs outils actuels de réponse aux incidents. Cette absence d’intégration pose un problème durant l’étape de détection (trop de faux positifs) et durant les étapes d’évaluation et de diagnostic. Dans le cadre de la migration, l’équipe a décidé d’opter pour Security Center pour l’aider à résoudre ce problème.

La première phase de cette migration s’est terminée après l’intégration de toutes les ressources et l’application de toutes les recommandations de sécurité d’Azure Security Center. L’équipe CSIRT de Contoso est le point central pour la gestion des incidents de sécurité informatique. Elle se compose d’un groupe de personnes chargées de traiter les incidents de sécurité. Les membres de l’équipe ont des responsabilités clairement définies pour s’assurer que tous les domaines d’action sont couverts.

Pour les besoins de ce scénario, nous allons nous concentrer sur les rôles des personnes suivantes, qui font partie de l’équipe CSIRT de Contoso :

![Cycle de vie de la réponse aux incidents](./media/tutorial-security-incident/security-center-incident-response.png)

Judy travaille aux opérations de sécurité. Ses responsabilités incluent :

* La surveillance et l’élimination en continu des menaces de sécurité.
* La remontée des problèmes au propriétaire des charges de travail de cloud ou à l’analyste de la sécurité en fonction des besoins.

Sam est analyste sécurité et ses responsabilités incluent :

* L’examen des attaques.
* La correction des alertes.
* La collaboration avec les propriétaires des charges de travail pour déterminer et appliquer des mesures de correction.

Comme vous pouvez le voir, Judy et Sam ont des responsabilités différentes, et ils doivent travailler ensemble en se partageant les informations d’Azure Security Center.

## <a name="triage-security-alerts"></a>Trier les alertes de sécurité
Security Center fournit une vue unifiée de toutes les alertes de sécurité. Les alertes de sécurité sont classées en fonction de leur gravité et les alertes liées sont combinées dans un seul incident de sécurité lorsque c’est possible. Lorsque vous triez les alertes et les incidents, vous devez :

- Fermer les alertes pour lesquelles aucune action supplémentaire n’est nécessaire, par exemple, si c’est un faux positif
- Agir pour corriger les attaques connues, par exemple empêcher le trafic réseau venant d’une adresse IP malveillante
- Déterminer quelles alertes nécessitent un examen supplémentaire


1. Dans le menu principal de Security Center sous **DETECTION**, sélectionnez **Alertes de sécurité** :

   ![Alertes de sécurité](./media/tutorial-security-incident/tutorial-security-incident-fig1.png)

2. Dans la liste des alertes, cliquez sur un incident de sécurité, qui est un ensemble d’alertes, pour en savoir plus sur cet incident. **Incident de sécurité détecté** s’ouvre.

   ![Incident de sécurité](./media/tutorial-security-incident/tutorial-security-incident-fig2.png)

3. Dans cet écran, la description de l’incident de sécurité est affichée au-dessus, ainsi que la liste des alertes faisant parties de l’incident. Cliquez sur l’alerte sur laquelle vous souhaitez approfondir vos recherches et obtenir plus d’informations.

   ![Incident de sécurité](./media/tutorial-security-incident/tutorial-security-incident-fig3.png)

   Le type d’alerte peut varier, lisez [Présentation des alertes de sécurité dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) pour plus d’informations sur le type d’alerte, ainsi que les étapes de correction possibles. Pour les alertes qui peuvent être ignorées en toute sécurité, vous pouvez cliquer avec le bouton droit sur l’alerte et sélectionnez l’option **Ignorer** :

   ![Alerte](./media/tutorial-security-incident/tutorial-security-incident-fig4.png)

4. Si la cause première et la portée de l’activité malveillante sont inconnues, passez à l’étape suivante pour approfondir vos recherches.

## <a name="investigate-an-alert-or-incident"></a>Examiner une alerte ou un incident
1. Sur la page **Alerte de sécurité**, cliquez sur le bouton **Démarrer l’investigation** (si vous avez déjà démarré, le nom devient **Poursuivre l’investigation**).

   ![Investigation](./media/tutorial-security-incident/tutorial-security-incident-fig5.png)

   Le mappage d’investigation est une représentation graphique des entités connectées à cette alerte ou cet incident de sécurité. En cliquant sur une entité du mappage, les informations sur cette entité font apparaitre de nouvelles entités et le mappage se développe. Les propriétés de l’entité sélectionnée dans le mappage sont mises en surbrillance dans le volet situé à droite de la page. Les informations disponibles sur chaque onglet varient en fonction de l’entité sélectionnée. Pendant le processus d’investigation, passez en revue toutes les informations pertinentes pour mieux comprendre le mouvement de l’attaquant.

2. Si vous avez besoin de plus de preuves, ou si vous devez examiner plus en détail des entités trouvées lors de l’analyse, passez à l’étape suivante.

## <a name="search-data-for-investigation"></a>Rechercher des données pour l’investigation

Vous pouvez utiliser les fonctionnalités de recherche dans Security Center pour rechercher plus de preuves des systèmes compromis et obtenir plus de détails sur les entités faisant parties de l’investigation.

Pour effectuer une recherche, ouvrez le tableau de bord **Security Center**, cliquez sur **Recherche** dans le volet de navigation de gauche, sélectionnez l’espace de travail contenant les entités sur lesquelles vous souhaitez effectuer des recherches, saisissez la requête de recherche, et cliquez sur le bouton de recherche.

## <a name="clean-up-resources"></a>Supprimer des ressources
D’autres guides de démarrage rapide et didacticiels de cette collection reposent sur ce guide. Si vous envisagez de suivre les didacticiels et guides de démarrage rapide suivants, conservez le niveau Standard et gardez l’approvisionnement automatique activé. Dans le cas contraire, ou si vous voulez revenir au niveau Gratuit :

1. Revenez au menu principal de Security Center et sélectionnez **Stratégie de sécurité**.
2. Sélectionnez la stratégie ou l’abonnement pour lequel vous voulez revenir au niveau Gratuit. La fenêtre **Stratégie de sécurité** s’ouvre.
3. Dans **COMPOSANTS DE LA STRATÉGIE**, sélectionnez **Niveau tarifaire**.
4. Sélectionnez **Gratuit** pour modifier l’abonnement et passer du niveau Standard au niveau Gratuit.
5. Sélectionnez **Enregistrer**.

Si vous voulez désactiver l’approvisionnement automatique :

1. Revenez au menu principal de Security Center et sélectionnez **Stratégie de sécurité**.
2. Sélectionnez l’abonnement pour lequel vous souhaitez désactiver l’approvisionnement automatique.
3. Dans **Stratégie de sécurité : collecte de données**, sélectionnez **Désactivé** sous **Intégration** pour désactiver l’approvisionnement automatique.
4. Sélectionnez **Enregistrer**.

>[!NOTE]
> La désactivation de l’approvisionnement automatique ne supprime pas Microsoft Monitoring Agent des machines virtuelles Azure sur lesquelles l’agent a été approvisionné. La désactivation de l’approvisionnement automatique limite la surveillance de la sécurité pour vos ressources.
>

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris les fonctionnalités de Security Center à utiliser pour répondre à un incident de sécurité, telles que :

> [!div class="checklist"]
> * Un incident de sécurité qui est une agrégation d’alertes associées pour une ressource
> * Un mappage d’investigation est une représentation graphique des entités connectées à cette alerte ou cet incident de sécurité
> * Les fonctionnalités de recherche afin de rechercher plus de preuves des systèmes compromis

Pour en savoir plus sur les fonctionnalités d’investigation de Security Center, consultez :

> [!div class="nextstepaction"]
> [Investiguer les incidents et les alertes](security-center-investigation.md)
