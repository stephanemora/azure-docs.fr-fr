---
title: Advanced Data Security pour IaaS dans Azure Security Center | Microsoft Docs
description: " Découvrez comment activer Advanced Data Security pour IaaS dans Azure Security Center. "
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
ms.author: v-mohabe
ms.openlocfilehash: 0b83575baa2221f0b502abbf919654492c7ab6cf
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295754"
---
# <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-public-preview"></a>Advanced Data Security pour les serveurs SQL Server sur Machines virtuelles Azure (préversion publique)
Advanced Data Security pour les serveurs SQL Server est un package unifié de fonctionnalités de sécurité SQL avancées. Actuellement en préversion publique, il inclut des fonctionnalités permettant de découvrir et d’atténuer les vulnérabilités de votre base de données, et de détecter les activités anormales susceptibles d’indiquer une menace ciblant votre base de données. 

Cette offre de sécurité pour les serveurs SQL de machines virtuelles Azure est basée sur la même technologie fondamentale que celle utilisée dans le [package Azure SQL Database Advanced Data Security](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security).


## <a name="overview"></a>Vue d'ensemble

Advanced Data Security fournit un ensemble de fonctionnalités de sécurité SQL avancées incluant l’Évaluation des vulnérabilités et la Protection avancée contre les menaces.

* La fonctionnalité [Évaluation des vulnérabilités](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) est un service simple à configurer, qui vous permet de découvrir, suivre et de corriger des vulnérabilités de base de données potentielles. Elle offre une visibilité sur votre état de sécurité et inclut des étapes pour résoudre les problèmes de sécurité et améliorer la protection de votre base de données.
* La fonctionnalité [Protection avancée contre les menaces](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) détecte les activités anormales indiquant des tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses de votre serveur SQL. Elle supervise en permanence votre base de données pour détecter des activités suspectes, et envoie des alertes de sécurité orientées action en cas de modèles d’accès anormaux à la base de données. Ces alertes fournissent des détails sur les activités suspectes et des mesures recommandées pour examiner et atténuer la menace.

## <a name="get-started-with-advanced-data-security-for-sql-on-azure-vms"></a>Bien démarrer avec Advanced Data Security pour SQL sur les machines virtuelles Azure

Les étapes suivantes vous aideront à bien démarrer avec Advanced Data Security pour SQL sur les machines virtuelles Azure en préversion publique.

### <a name="set-up-advanced-data-security-for-sql-on-azure-vms"></a>Configurer Advanced Data Security pour SQL sur des machines virtuelles Azure

**Avant de commencer** : Vous avez besoin d’un espace de travail Log Analytics pour stocker les journaux de sécurité en cours d’analyse. Si vous n’en avez pas, vous pouvez en créer un facilement, comme expliqué dans [Créer un espace de travail Log Analytics dans le portail Azure](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

1. Connectez la machine virtuelle qui héberge le serveur SQL à l’espace de travail Log Analytics. Pour obtenir des instructions, consultez [Connecter des ordinateurs Windows à Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows).

1. À partir de la Place de marché Azure, accédez à la [solution SQL Advanced Data Security](https://ms.portal.azure.com/#create/Microsoft.SQLAdvancedDataSecurity).
(Vous pouvez la trouver à l’aide de l’option de recherche de la Place de marché, comme illustré dans l’image suivante.) La page **SQL Advanced Data Security** s’ouvre.

    ![Advanced Data Security pour IaaS](./media/security-center-advanced-iaas-data/sql-advanced-data-security.png)

1. Cliquez sur **Créer**. Les espaces de travail s’affichent.

    ![Advanced Data Security - Créer](./media/security-center-advanced-iaas-data/sql-advanced-data-create.png)

1. Sélectionnez l’espace de travail à utiliser et cliquez sur **Créer**.

   ![Sélectionner un espace de travail](./media/security-center-advanced-iaas-data/sql-workspace.png)

1. Redémarrez le [serveur SQL de la machine virtuelle](https://docs.microsoft.com/sql/database-engine/configure-windows/start-stop-pause-resume-restart-sql-server-services?view=sql-server-2017).


## <a name="explore-and-investigate-security-alerts"></a>Explorer et examiner les alertes de sécurité

Vous pouvez afficher et gérer vos alertes de sécurité actuelles.

1. Cliquez sur **Security Center** > **Alertes de sécurité**, puis cliquez sur une alerte.

    ![Rechercher une alerte](./media/security-center-advanced-iaas-data/find-alert.png)

1. Dans la colonne **Ressource attaquée**, cliquez sur une ressource qui a été attaquée.

1. Pour afficher les détails de l’alerte et les actions nécessaires afin d’examiner les menaces actuelles et de répondre aux menaces ultérieures, faites défiler la page **Informations générales** puis, dans la section **Étapes de correction** , cliquez sur le lien **Étapes d’investigation**.

    ![Étapes de correction](./media/security-center-advanced-iaas-data/remediation-steps.png)

1. Pour afficher les journaux associés au déclenchement de l’alerte, accédez à **Espaces de travail Log Analytics** et effectuez les étapes suivantes :

     > [!NOTE]
     > Si **Espaces de travail Log Analytics** n’apparaît pas dans le menu gauche, cliquez sur **Tous les services**, puis recherchez **Espaces de travail Log Analytics**.

    1. Vérifiez que les colonnes **Niveau tarifaire** et **ID d’espace de travail** sont visibles. (**Espaces de travail Log Analytics** > **Modifier les colonnes**, ajoutez **Niveau tarifaire** et **ID d’espace de travail**.)

     ![Modifier les colonnes](./media/security-center-advanced-iaas-data/edit-columns.png)

    1. Cliquez sur l’espace de travail qui a les journaux d’alerte.

    1. Dans le menu **Général**, cliquez sur **Journaux**.

    1. Cliquez sur le œil en regard de la table **SQLAdvancedThreatProtection**. La liste des journaux s’affiche.

     ![Voir les journaux](./media/security-center-advanced-iaas-data/view-logs.png)

## <a name="set-up-email-notification-for-atp-alerts"></a>Configurer la notification par e-mail pour les alertes ATP 

Vous pouvez définir une liste de destinataires devant recevoir une notification par e-mail quand des alertes ASC sont générées. L’e-mail contient un lien direct vers l’alerte dans Azure Security Center avec tous les détails pertinents. 

1. Accédez à **Security Center** > **Tarification & paramètres**, puis cliquez sur l’abonnement concerné.

    ![Paramètres d’abonnement](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. Dans le menu **Paramètres**, cliquez sur **Notifications par e-mail**. 
1. Dans la zone de texte **Adresse e-mail**, entrez les adresses e-mail devant recevoir les notifications. Vous pouvez entrer plusieurs adresses e-mail en les séparant par une virgule (,).  Par exemple admin1@mycompany.com,admin2@mycompany.com,admin3@mycompany.com.

      ![Paramètres de courrier électronique](./media/security-center-advanced-iaas-data/email-settings.png)

1. Dans les paramètres de **Notifications par e-mail**, définissez les options suivantes :
  
    * **Envoyer une notification par e-mail pour les alertes à gravité élevée** : Au lieu d’envoyer des e-mails pour toutes les alertes, en envoyer uniquement pour les alertes à gravité élevée.
    * **Envoyer également un e-mail aux propriétaires de l’abonnement** :  Envoyer des notifications également aux propriétaires des abonnements.

1. En haut de l’écran **Notifications par e-mail**, cliquez sur **Enregistrer**.

  > [!NOTE]
  > Veillez à cliquer sur **Enregistrer** avant de fermer la fenêtre, sinon les nouveaux paramètres de **Notifications par e-mail** ne seront pas enregistrés.

## <a name="explore-vulnerability-assessment-reports"></a>Explorer les rapports d’évaluation des vulnérabilités

Le tableau de bord Évaluation des vulnérabilités fournit une vue d’ensemble des résultats de votre évaluation sur vos bases de données. Vous pouvez afficher la distribution des bases de données en fonction de la version de SQL Server, une synthèse des bases de données ayant échoué ou satisfait aux tests, et une synthèse générale des tests ayant échoué en fonction de la distribution des risques.

Vous pouvez afficher vos rapports et résultats d’Évaluation des vulnérabilités directement à partir de Log Analytics.

1. Accédez à votre espace de travail Log Analytics avec la solution Advanced Data Security.
1. Accédez à **Solutions** et sélectionnez la solution **Évaluation des vulnérabilités SQL**.
1. Dans le volet **Résumé**, cliquez sur **Afficher le résumé** et sélectionnez votre **rapport d’évaluation des vulnérabilités SQL**.

    ![Rapport d’évaluation SQL](./media/security-center-advanced-iaas-data/ads-sql-server-1.png)

    Le tableau de bord de rapport se charge. Vérifiez que la fenêtre de temps est définie au moins sur **Les 7 derniers jours**, car les analyses d’évaluation des vulnérabilités sont exécutées sur vos bases de données selon une planification fixe une fois tous les sept jours.

    ![Définir Les 7 derniers jours](./media/security-center-advanced-iaas-data/ads-sql-server-2.png)

1. Pour afficher davantage de détails, cliquez sur l’un des éléments du tableau de bord. Par exemple :

   1. Cliquez sur une vérification de vulnérabilité dans la section **Synthèse des vérifications ayant échoué** pour afficher un tableau Log Analytics contenant les résultats de cette vérification parmi toutes les bases de données. Celles qui ont des résultats sont listées en premier.

   1. Ensuite, cliquez pour afficher les détails de chaque vulnérabilité, notamment la description de la vulnérabilité, son impact, l’état, les risques associés et les résultats réels sur cette base de données. Vous pouvez également voir la requête réelle qui a été exécutée pour effectuer cette vérification, ainsi que les informations de correction pour résoudre cette vulnérabilité.

    ![Sélectionner un espace de travail](./media/security-center-advanced-iaas-data/ads-sql-server-3.png)

    ![Sélectionner un espace de travail](./media/security-center-advanced-iaas-data/ads-sql-server-4.png)

1. Vous pouvez exécuter n’importe quelle requête Log Analytics sur vos données de résultats d’évaluation des vulnérabilités, afin de segmenter et découper les données en fonction de vos besoins.

## <a name="advanced-threat-protection-for-sql-servers-on-azure-vms-alerts"></a>Alertes Advanced Threat Protection pour les serveurs SQL sur les machines virtuelles Azure
Les alertes sont générées en cas de détection de tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses des serveurs SQL. Ces événements peuvent déclencher les alertes suivantes :

### <a name="anomalous-access-pattern-alerts-supported-in-public-preview"></a>Alertes de modèle d’accès anormal (prises en charge dans la préversion publique)

* **Accès à partir d’un emplacement inhabituel :** cette alerte est déclenchée en cas de changement du modèle d’accès au serveur SQL, quand un utilisateur s’est connecté au serveur SQL à partir d’un emplacement géographique inhabituel. Causes potentielles :
     * Un attaquant ou un ancien employé malveillant a accédé à votre serveur SQL Server.
     * Un utilisateur légitime a accédé à votre serveur SQL à partir d’un nouvel emplacement.
* **Accès à partir d’une application potentiellement dangereuse** : cette alerte est déclenchée quand une application potentiellement dangereuse est utilisée pour accéder à la base de données. Causes potentielles :
     * Un attaquant a tenté d’accéder à votre serveur SQL à l’aide d’outils d’attaque courants.
     * Des tests d’intrusion légitimes sont en cours.
* **Accès à partir d’une entité de sécurité inconnue** : cette alerte est déclenchée en cas de modification du modèle d’accès au serveur SQL, quand un utilisateur s’est connecté au serveur SQL à l’aide d’un principal inhabituel (utilisateur SQL). Causes potentielles :
     * Un attaquant ou un ancien employé malveillant a accédé à votre serveur SQL Server. 
     * Un utilisateur légitime a accédé à votre serveur SQL avec un nouveau principal.
* **Informations d’identification SQL par force brute** : cette alerte est déclenchée quand il existe un nombre anormalement élevé d’échecs de connexion avec des informations d’identification différentes. Causes potentielles :
     * Un attaquant a tenté d’accéder à votre serveur SQL à l’aide de force brute.
     * Des tests d’intrusion légitimes sont en cours.

### <a name="potential-sql-injection-attacks-coming"></a>Attaques par injection SQL potentielles (à venir)

* **Vulnérabilité par injection de code SQL** : cette alerte est déclenchée quand une application génère une instruction SQL défectueuse dans la base de données. Cette alerte peut éventuellement indiquer une vulnérabilité aux attaques par injection de code SQL. Causes potentielles :
     * Un défaut dans le code d’application qui crée l’instruction SQL défectueuse
     * Le code de l’application ou des procédures stockées qui n’assainissent pas l’entrée utilisateur lors de la création de l’instruction SQL défectueuse, qui peut être exploitée par les injections SQL
* **Injection potentielle de code SQL** : cette alerte est déclenchée en cas d’attaque active contre une vulnérabilité d’application identifiée sur l’injection SQL. Cela signifie que l’attaquant tente d’injecter des instructions SQL malveillantes en utilisant les procédures stockées ou le code d’application vulnérables.
