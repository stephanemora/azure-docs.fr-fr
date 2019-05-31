---
title: Avancées de sécurité des données pour IaaS dans Azure Security Center | Microsoft Docs
description: " Découvrez comment activer la sécurité des données avancée pour IaaS dans Azure Security Center. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: monhaber
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2019
ms.author: monhaber
ms.openlocfilehash: e7420adfe1608df39ef72124817f1d6dadf07db8
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66400164"
---
# <a name="advanced-data-security-for-sql-servers-on-iaas"></a>Sécurité avancée des données pour les serveurs SQL sur IaaS
Sécurité avancée des données pour les serveurs SQL Server sur des Machines virtuelles Azure est un package unifié incluant des fonctionnalités de sécurité avancées SQL. Cela inclut actuellement les fonctionnalités pour les surfaces et atténuer les vulnérabilités potentielles de base de données et détecter les activités anormales pouvant indiquer une menace pour votre base de données. 

Cette offre pour les serveurs SQL de machines virtuelles Azure est basée sur la même technologie fondamentale utilisée dans le [package de base de données SQL Azure Advanced données sécurité](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security).


## <a name="overview"></a>Vue d'ensemble

Sécurité des données avancée fournit un ensemble de fonctionnalités avancées de sécurité SQL, composée d’évaluation des vulnérabilités et une Protection avancée contre les menaces.

* La fonctionnalité [Évaluation des vulnérabilités](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) est un service simple à configurer, qui vous permet de découvrir, suivre et de corriger des vulnérabilités de base de données potentielles. Il offre une visibilité de votre état de sécurité et inclut les étapes pour résoudre des problèmes de sécurité et améliorer votre fortifications de base de données.
* [Advanced Threat Protection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) détecte les activités anormales indiquant des tentatives inhabituelles ou potentiellement dangereuses visant à accéder ou à exploiter votre serveur SQL server. Il analyse votre base de données pour les activités suspectes et fournit des alertes de sécurité action orientée sur les modèles d’accès de base de données anormales en continu. Ces alertes fournissent les détails de l’activité suspecte et mesures recommandées pour examiner et atténuer la menace.

## <a name="get-started-with-advanced-data-security-for-sql-on-azure-vms"></a>Bien démarrer avec fonctions avancées de sécurité de données pour SQL sur des machines virtuelles Azure

Les étapes suivantes vous aident à démarrer avec fonctions avancées de sécurité de données pour SQL sur des machines virtuelles Azure.

### <a name="set-up-advanced-data-security-for-sql-on-azure-vms"></a>Configurer les fonctions avancées de sécurité de données pour SQL sur des machines virtuelles Azure

**Avant de commencer**: Vous avez besoin d’un espace de travail Analytique de journal pour stocker les journaux de sécurité en cours d’analyse. Si vous n’en avez pas, vous pouvez en créer un simple, comme expliqué dans [créer un espace de travail Analytique de journal dans le portail Azure](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

1. Connectez la machine virtuelle qui héberge le serveur SQL à l’espace de travail Analytique de journal. Pour obtenir des instructions, consultez [ordinateurs Windows de se connecter à Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows).

1. À partir de la place de marché Azure, accédez à la [solution de fonctions avancées de sécurité de données SQL](https://ms.portal.azure.com/#create/Microsoft.SQLAdvancedDataSecurity).
(Vous pouvez trouver à l’aide de l’option de recherche de la place de marché, comme voir dans l’image suivante.) Le **fonctions avancées de sécurité de données SQL** page s’ouvre.

    ![Sécurité des données avancée pour IaaS](./media/security-center-advanced-iaas-data/sql-advanced-data-security.png)

1. Cliquez sur **Créer**. Les lieux de travail est affichés.

    ![Créer de la sécurité des données avancées](./media/security-center-advanced-iaas-data/sql-advanced-data-create.png)

1. Sélectionnez l’espace de travail à utiliser et cliquez sur **créer**.

   ![Sélectionner un espace de travail](./media/security-center-advanced-iaas-data/sql-workspace.png)

1. Redémarrez le [SQL server de l’ordinateur virtuel](https://docs.microsoft.com/sql/database-engine/configure-windows/start-stop-pause-resume-restart-sql-server-services?view=sql-server-2017).


## <a name="explore-and-investigate-security-alerts"></a>Explorer et examiner les alertes de sécurité

Vous pouvez afficher et gérer vos alertes de sécurité actuel.

1. Cliquez sur **Security Center** > **alertes de sécurité**, puis cliquez sur une alerte.

    ![Localisation de l’alerte](./media/security-center-advanced-iaas-data/find-alert.png)

1. À partir de la **ressource attaquée** colonne, cliquez sur une ressource qui a été attaquée.

1. Pour afficher les détails de l’alerte et les actions pour examiner les menaces actuelles et de réponses aux menaces futures, faites défiler la **des informations générales** page, puis, dans le **étapes de correction** section, cliquez sur le  **ÉTAPES d’INVESTIGATION** lien.

    ![Étapes de correction](./media/security-center-advanced-iaas-data/remediation-steps.png)

1. Pour afficher les journaux qui sont associés le déclenchement de l’alerte, accédez à **espaces de travail analytique Log** et procédez comme suit :

     > [!NOTE]
     > Si **espaces de travail analytique Log** n’apparaissent dans le menu de gauche, cliquez sur **tous les services**, puis recherchez **espaces de travail analytique Log**.

    1. Vérifiez que les colonnes affichent les **niveau tarifaire** et **WorkspaceID** colonnes. (**Espaces de travail analytique log** > **modifier les colonnes**, ajouter **niveau tarifaire** et **WorkspaceID**.)

     ![Modifier les colonnes](./media/security-center-advanced-iaas-data/edit-columns.png)

    1. Cliquez sur l’espace de travail qui dispose des journaux d’alerte.

    1. Sous le **général** menu, cliquez sur **journaux**

    1. Cliquez sur le œil en regard **SQLAdvancedThreatProtection** table. Les journaux sont répertoriés.

     ![Afficher les journaux](./media/security-center-advanced-iaas-data/view-logs.png)

## <a name="set-up-email-notification-for-atp-alerts"></a>Configurer la Notification par courrier électronique pour les alertes d’ATP 

Vous pouvez définir une liste de destinataires pour recevoir une notification par e-mail lors de la génération d’alertes ASC. L’e-mail contient un lien direct vers l’alerte dans Azure Security Center avec tous les détails pertinents. 

1. Accédez à **Security Center** > **stratégie de sécurité** et dans la ligne de l’abonnement approprié, cliquez sur **modifier les paramètres >** .

    ![Paramètres d’abonnement](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. À partir de la **paramètres** menu, cliquez sur **notifications par courrier électronique**. 
1. Dans le **adresse de messagerie** texte, entrez les adresses de messagerie pour recevoir les notifications. Vous pouvez entrer plusieurs adresses de messagerie en séparant les adresses e-mail par une virgule (,).  Par exemple admin1@mycompany.com,admin2@mycompany.com,admin3@mycompany.com

      ![Paramètres de courrier électronique](./media/security-center-advanced-iaas-data/email-settings.png)

1. Dans le **notification par courrier électronique** paramètres, définissez les options suivantes :
  
    * **Envoyer la notification par courrier électronique pour les alertes de gravité élevée**: Au lieu d’envoyer des e-mails pour toutes les alertes, envoyer uniquement pour les alertes de gravité élevée.
    * **Également envoyer des notifications par courrier électronique aux propriétaires d’abonnement**:  Envoyer des notifications aux propriétaires d’abonnements trop.

1. En haut de la **notifications par courrier électronique** , cliquez sur **enregistrer**.

  > [!NOTE]
  > Veillez à cliquer sur **enregistrer** avant de fermer la fenêtre ou le nouveau **notification par courrier électronique** paramètres ne seront pas enregistrés.

## <a name="explore-vulnerability-assessment-reports"></a>Explorer des rapports d’évaluation de vulnérabilité

Le tableau de bord évaluation des vulnérabilités fournit une vue d’ensemble de résultats de votre évaluation sur vos bases de données. Vous pouvez afficher la distribution des bases de données en fonction de la version de SQL Server, ainsi qu’un résumé du basculement par rapport à la transmission des bases de données et une synthèse générale des vérifications en fonction de la distribution des risques en échec.

Vous pouvez afficher vos résultats d’évaluation de vulnérabilité et les rapports directement à partir de l’Analytique de journal.

1. Accédez à votre espace de travail Analytique de journal avec la solution de sécurité avancée des données.
1. Accédez à **Solutions** et sélectionnez le **évaluation des vulnérabilités SQL** solution.
1. Dans le **Résumé** volet, cliquez sur **afficher le résumé** et sélectionnez votre **rapport d’évaluation de vulnérabilité de SQL**.

    ![Rapport d’évaluation SQL](./media/security-center-advanced-iaas-data/ads-sql-server-1.png)

    Charge de rapport de tableau de bord. Assurez-vous que la fenêtre de temps est définie au moins au **7 derniers jours** dans la mesure où les analyses d’évaluation de vulnérabilité sont exécutés sur vos bases de données selon une planification fixe une fois par 7 jours.

    ![Définir des 7 derniers jours](./media/security-center-advanced-iaas-data/ads-sql-server-2.png)

1. Pour descendre pour plus d’informations, cliquez sur un des éléments du tableau de bord. Exemple :

   1. Cliquez sur une vérification d’une vulnérabilité dans le **échec vérifie Résumé** section pour afficher une table de journal Analytique avec les résultats de cette vérification sur toutes les bases de données. Ceux qui ont des résultats est répertoriés en premier.

   1. Ensuite, cliquez sur Afficher les détails pour chaque vulnérabilité, y compris la description de la vulnérabilité et impact, l’état, les risques associés et les résultats réels sur cette base de données. Vous pouvez également voir la requête réelle qui a été exécutée pour effectuer cette vérification et les informations de mise à jour pour résoudre cette vulnérabilité.

    ![Sélectionner un espace de travail](./media/security-center-advanced-iaas-data/ads-sql-server-3.png)

    ![Sélectionner un espace de travail](./media/security-center-advanced-iaas-data/ads-sql-server-4.png)

1. Vous pouvez exécuter des requêtes d’Analytique de journal sur vos données de résultats d’évaluation des vulnérabilités, pour segmenter et découper les données selon vos besoins.

## <a name="advanced-threat-protection-for-sql-servers-on-azure-vms-alerts"></a>Avancées de Protection contre les menaces pour les serveurs SQL sur les alertes de machines virtuelles Azure
Alertes sont générées par les tentatives inhabituelles ou potentiellement dangereuses visant à accéder ou à exploiter les serveurs SQL. Ces événements peuvent déclencher les alertes suivantes :

### <a name="anomalous-access-pattern-alerts"></a>Alertes de modèle d’accès anormaux

* **Accès à partir de l’emplacement inhabituel :** cette alerte est déclenchée en cas de changement du modèle d’accès au serveur SQL, quand un utilisateur s’est connecté au serveur SQL à partir d’un emplacement géographique inhabituel. Causes possibles :
     * Un attaquant ou ancienne base de données l’emploient malveillant a accédé à votre serveur SQL Server.
     * Un utilisateur légitime a accédé à votre serveur SQL à partir d’un nouvel emplacement.
* **Accès à partir d’une application potentiellement dangereuse**: son alerte est déclenchée lorsqu’une application potentiellement dangereux est utilisée pour accéder à la base de données. Causes possibles :
     * Un attaquant tentant de violation de votre SQL à l’aide des outils d’attaque courants.
     * Un légitime des tests d’intrusion en action.
* **Accès à partir d’une entité de sécurité inconnue** : cette alerte est déclenchée en cas de modification du modèle d’accès au serveur SQL, quand un utilisateur s’est connecté au serveur SQL à l’aide d’un principal inhabituel (utilisateur SQL). Causes possibles :
     * Un attaquant ou ancienne base de données l’emploient malveillant a accédé à votre serveur SQL Server. 
     * Un utilisateur légitime a accédé à votre serveur SQL à partir d’avec une nouvelle entité de sécurité.
* **Informations d’identification SQL par force brute** : cette alerte est déclenchée quand il existe un nombre anormalement élevé d’échecs de connexion avec des informations d’identification différentes. Causes possibles :
     * Un attaquant tentant de violation de votre SQL à l’aide de force brute.
     * Un légitime des tests d’intrusion en action.

### <a name="potential-sql-injection-attacks-coming"></a>Attaques par Injection SQL potentielles (entrée)

* **Vulnérabilité par injection de code SQL** : cette alerte est déclenchée quand une application génère une instruction SQL défectueuse dans la base de données. Cette alerte peut éventuellement indiquer une vulnérabilité aux attaques par injection de code SQL. Causes possibles :
     * Un défaut dans le code d’application qui crée l’instruction SQL défectueuse
     * Le code de l’application ou des procédures stockées qui n’assainissent pas l’entrée utilisateur lors de la création de l’instruction SQL défectueuse, qui peut être exploitée par les injections SQL
* **Injection potentielle de code SQL** : cette alerte est déclenchée en cas d’attaque active contre une vulnérabilité d’application identifiée sur l’injection SQL. Cela signifie que l’attaquant tente d’injecter des instructions SQL malveillantes en utilisant les procédures stockées ou le code d’application vulnérables.
