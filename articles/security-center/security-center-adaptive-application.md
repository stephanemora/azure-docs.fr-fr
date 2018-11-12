---
title: Contrôles d’application adaptative dans Azure Security Center | Microsoft Docs
description: Ce document vous aide à utiliser les contrôles d’application adaptative dans Azure Security Center afin de mettre dans une liste verte des applications s’exécutant sur les machines virtuelles Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: 9268b8dd-a327-4e36-918e-0c0b711e99d2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: rkarlin
ms.openlocfilehash: ddf9c5e30a27a829a74ccf0985dce30a68f9bbb7
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51256644"
---
# <a name="adaptive-application-controls-in-azure-security-center"></a>Contrôles d’application adaptative dans Azure Security Center
Découvrez comment configurer le contrôle d’application dans Azure Security Center à l’aide de cette procédure pas à pas.

## <a name="what-are-adaptive-application-controls-in-security-center"></a>Que sont les contrôles d’application adaptative dans Azure Security Center ?
Les contrôles d’application adaptatifs sont une solution de mise en liste verte des applications de bout en bout automatisée et intelligente de Azure Security Center. Ils vous aident à contrôler les applications et à décider lesquelles peuvent s’exécuter sur vos machines virtuelles situées dans Azure. Cela vous permet notamment de renforcer vos machines virtuelles contre les programmes malveillants. Security Center utilise le machine learning pour analyser les applications en cours d’exécution sur votre machine virtuelle et exploite ces informations pour vous aider à appliquer les règles spécifiques de mise en liste verte. Cette fonctionnalité simplifie considérablement le processus de configuration et de gestion des stratégies de mise en liste verte des applications, ce qui vous permet de :

- Bloquer ou vous alerter si une application malveillante tente de s’exécuter, y compris celles pouvant être omises par les solutions de logiciels anti-programme malveillant.
- Respecter la stratégie de sécurité de votre organisation qui autorise uniquement l’utilisation de certains logiciels licenciés.
- Empêcher l’utilisation de logiciels indésirables sur votre environnement.
- Empêcher l’exécution des applications obsolètes et non prises en charge.
- Bloquer des outils logiciels spécifiques qui ne sont pas autorisés dans votre organisation.
- Autoriser le service informatique à contrôler l’accès aux données sensibles en utilisant l’application.

## <a name="how-to-enable-adaptive-application-controls"></a>Comment activer les contrôles d’application adaptative ?
Les contrôles d’application adaptative vous aident à définir un ensemble d’applications autorisées à s’exécuter sur des groupes configurés de machines virtuelles. Cette fonctionnalité est disponible uniquement pour les machines Windows (toutes les versions, Classic ou Azure Resource Manager). Vous pouvez suivre les étapes ci-dessous pour configurer la mise en liste verte des applications dans Security Center :

1. Ouvrez le tableau de bord **Security Center**.
2. Dans le volet de gauche, sélectionnez **Adaptive application controls** (Contrôles d’application adaptatifs) situé sous **Advanced cloud defense** (Défense de cloud avancée).

    ![Défense](./media/security-center-adaptive-application/security-center-adaptive-application-fig1-new.png)

La page **Adaptive application controls** (Contrôles d’application adaptatifs) apparaît.

![controls](./media/security-center-adaptive-application/security-center-adaptive-application-fig2.png)

La section **Groupes de machines virtuelles** contient trois onglets :

* **Configuré** : liste des groupes contenant les machines virtuelles qui ont été configurées avec le contrôle d’applications.
* **Recommandé** : liste des groupes pour lesquels le contrôle d’applications est recommandé. Security Center utilise le machine learning pour identifier les machines virtuelles potentiellement concernées par le contrôle d’application en fonction de leur capacité à exécuter les mêmes applications en continu.
* **Aucune recommandation** : liste des groupes contenant des machines virtuelles sans recommandation de contrôle d’applications. Par exemple, les machines virtuelles dont les applications sont toujours en cours de modification et qui n’ont pas atteint un état stable.

> [!NOTE]
> Security Center utilise un algorithme de clustering propriétaire pour créer des groupes de machines virtuelles s’assurant que les machines virtuelles similaires reçoivent la meilleure stratégie de contrôle d’application optimale recommandée.
>
>

### <a name="configure-a-new-application-control-policy"></a>Configurer une nouvelle stratégie de contrôle des applications
1. Cliquez sur l’onglet **Recommandé** pour obtenir une liste des groupes disposant de recommandations de contrôle d’applications :

  ![Recommandé](./media/security-center-adaptive-application/security-center-adaptive-application-fig3.png)

  Cette liste comprend les éléments suivants :

  - **NOM** : nom de l’abonnement et du groupe
  - **MACHINES VIRTUELLES** : nombre de machines virtuelles dans le groupe
  - **ÉTAT** : état de la recommandation
  - **GRAVITÉ** : le niveau de gravité des recommandations

2. Cliquez sur un groupe pour ouvrir l’option **Créer des règles de contrôle d’applications**.

  ![Règles de contrôle d’application](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png)

3. Dans **Sélectionner les machines virtuelles**, examinez la liste des machines virtuelles recommandées et décochez celles pour lesquelles vous ne souhaitez pas appliquer de stratégie de mise en liste verte d’application. Ensuite, deux listes sont affichées :

  - **Applications recommandées** : liste des applications fréquentes sur les machines virtuelles de ce groupe et donc recommandées pour recevoir l’autorisation d’exécution.
  - **Plus d’applications** : liste des applications qui sont moins fréquentes sur les machines virtuelles de ce groupe, ou qui sont appelées Exploitables (voir ci-dessous) et recommandées pour un examen.

4. Examinez les applications dans chacune des listes, et décochez celles auxquelles vous ne souhaitez pas appliquer les règles. Chaque liste comprend les éléments suivants :

  - **NOM** : informations de certificat ou chemin d’accès complet de l’application
  - **TYPES DE FICHIERS** : type du fichier d’application. Cela peut être EXE, Script, MSI, ou toute autre permutation de ces types.
  - **EXPLOITABLE** : une icône d’avertissement indique si une application spécifique peut être utilisée par un attaquant pour ignorer une solution de mise en liste verte des applications. Nous vous recommandons de vérifier ces applications avant de les valider.
  - **UTILISATEURS** : utilisateurs recommandés pour recevoir l’autorisation d’exécution d’une application

5. Une fois que vous avez terminé vos sélections, sélectionnez **Créer**. <br>
Une fois que vous avez sélectionné Créer, Azure Security Center crée automatiquement les règles appropriées sur la solution de mise en liste verte des applications intégrées disponible sur les serveurs Windows (AppLocker).


> [!NOTE]
> - Security Center se base sur l’équivalent de deux semaines de données au minimum pour établir une ligne de base et remplir les recommandations uniques par groupe de machines virtuelles. Les nouveaux clients du niveau Standard de Security Center peuvent s’attendre à un comportement dans lequel leurs groupes de machines virtuelles apparaîtront d’abord sous l’onglet *Aucune recommandation*.
> - Les contrôles d’application adaptative du Security Center ne prennent pas en charge les machines virtuelles pour lesquelles une stratégie AppLocker est déjà activée par un objet de stratégie de groupe (GPO) ou une stratégie de sécurité locale.
> -  Afin d’assurer les meilleures pratiques de sécurité, Security Center essaie toujours de créer une règle d’éditeur pour les applications sélectionnées à autoriser. Si une application ne possède pas d’informations d’éditeur (l’application n’est pas signée), une règle de chemin d’accès est créée pour le chemin d’accès complet d’un fichier EXE spécifique.
>   

### <a name="editing-and-monitoring-a-group-configured-with-application-control"></a>Modifier et surveiller un groupe configuré avec le contrôle d’application

1. Pour modifier et surveiller un groupe configuré avec une stratégie de mise en liste verte des applications, revenez à la page **Contrôles d’applications adaptatifs** et sélectionnez **CONFIGURÉ** sous **Groupes de machines virtuelles** :

  ![Groupes](./media/security-center-adaptive-application/security-center-adaptive-application-fig5.png)

  Cette liste comprend les éléments suivants :

  - **Nom** : nom de l’abonnement et du groupe
  - **MACHINES VIRTUELLES** : nombre de machines virtuelles dans le groupe
  - **Mode** : le mode Audit enregistre les tentatives d’exécution des applications hors liste verte, et le mode Bloquer interdit l’exécution de ces applications
  - **Alertes** : toutes les violations en cours

2. Cliquez sur un groupe pour apporter des modifications à la page **Modifier la stratégie de contrôle d’application**.

  ![Protection](./media/security-center-adaptive-application/security-center-adaptive-application-fig6.png)

3. Dans **Mode de Protection**, vous pouvez sélectionner une des options suivantes :

  - **Audit** : dans ce mode, la solution de contrôle d’application n’applique pas les règles et effectue uniquement les audits de l’activité des machines virtuelles protégées. Ce mode est recommandé pour les scénarios dont vous souhaitez tout d’abord observer le comportement global avant de bloquer l’exécution d’une application dans la machine virtuelle cible.
  - **Appliquer** : dans ce mode, la solution de contrôle d’application applique les règles, puis s’assure que les applications n’ayant pas d’autorisation soient bloquées.

   > [!NOTE]
   > Comme nous l’avons indiqué précédemment, une nouvelle stratégie de contrôle d’application est toujours configurée par défaut dans le mode *Audit*. 
   >

4. Dans **Extension de stratégie**, vous pouvez ajouter les chemins d’accès d’application que vous souhaitez autoriser. Une fois que vous ajoutez ces chemins d’accès, Security Center met à jour la stratégie de mise en liste verte d’applications sur les machines virtuelles au sein du groupe de machines virtuelles sélectionnées et crée les règles appropriées pour ces applications, en plus des règles déjà en place.

5. Passez en revue les violations en cours répertoriées dans la section **Alertes récentes**. Cliquez sur chaque ligne pour être redirigé vers la page **Alertes** dans Azure Security Center et afficher toutes les alertes qui ont été détectées par Azure Security Center sur les machines virtuelles associées.
  - **Alertes** : toutes les violations enregistrées.
  - **Nombre de machines virtuelles** : le nombre de machines virtuelles avec ce type d’alerte.

6. Sous **Règles de mise en liste verte de l’éditeur**, **Règles de mise en liste verte des chemins d’accès**, et **Règles de mise en liste verte de hachage**, vous pouvez voir les règles de mise en liste verte des applications actuellement configurées sur les machines virtuelles au sein d’un groupe, en fonction du type de regroupement de règles. Pour chaque règle, vous pouvez découvrir :

  - **Règle** : les paramètres spécifiques en fonction desquels une application est examinée par AppLocker pour déterminer si une application est autorisée à s’exécuter.
  - **Type de fichier** : types de fichiers couverts par une règle spécifique. Il peut s’agir d’un des éléments suivants : EXE, Script, MSI ou toute autre permutation de ces types de fichiers.
  - **Utilisateurs** : nom ou nombre d’utilisateurs autorisés à exécuter une application couverte par une règle de mise en liste verte des applications.

   ![Règles de mise en liste verte](./media/security-center-adaptive-application/security-center-adaptive-application-fig9.png)

7. Cliquez sur les trois points à la fin de chaque ligne si vous souhaitez supprimer une règle spécifique ou modifier les utilisateurs autorisés.

8. Après avoir apporté des modifications à une stratégie de **contrôles d’application adaptatifs**, cliquez sur **Enregistrer**.

### <a name="not-recommended-list"></a>Liste non recommandée

Security Center ne recommande des stratégies de mise en liste verte des applications que pour les machines virtuelles exécutant un ensemble stable d’applications. Les recommandations ne sont pas créées si les applications sur les machines virtuelles associées sont en cours de modification.

![Recommandation](./media/security-center-adaptive-application/security-center-adaptive-application-fig11.png)

La liste contient :
- **NOM** : nom de l’abonnement et du groupe
- **MACHINES VIRTUELLES** : nombre de machines virtuelles dans le groupe

Azure Security Center vous permet également de définir une stratégie de mise en liste verte des applications sur des groupes non recommandés de machines virtuelles. Suivez les mêmes principes que ceux indiqués précédemment, pour configurer une stratégie de mise en liste verte des applications sur ces groupes.


## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez découvert comment utiliser les contrôles d’application adaptative dans Azure Security Center afin d’inclure dans une liste verte des applications s’exécutant sur les machines virtuelles Azure. Pour plus d’informations sur le Centre de sécurité Azure, consultez les rubriques suivantes :

* [Gestion et résolution des alertes de sécurité dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Apprenez à gérer les alertes et à répondre aux incidents de sécurité dans Security Center.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md). découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Présentation des alertes de sécurité dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). En savoir plus sur les différents types d’alertes de sécurité.
* [Guide de résolution des problèmes d’Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Apprenez à résoudre les problèmes fréquents dans Azure Security Center.
* [FAQ du Centre de sécurité Azure](security-center-faq.md). forum aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](https://blogs.msdn.com/b/azuresecurity/). accédez à des billets de blog sur la sécurité et la conformité Azure.
