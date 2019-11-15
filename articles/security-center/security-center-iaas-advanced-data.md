---
title: Advanced Data Security pour IaaS dans Azure Security Center | Microsoft Docs
description: " Découvrez comment activer Advanced Data Security pour IaaS dans Azure Security Center. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 93e52b393db288f5b19afde4a31e08d0bb91b471
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73571571"
---
# <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Advanced Data Security pour SQL Server sur les machines virtuelles Azure (préversion)
Advanced Data Security pour les serveurs SQL Server est un package unifié de fonctionnalités de sécurité SQL avancées. Cette fonctionnalité en préversion inclut des fonctions qui permettent d’identifier et d’atténuer les vulnérabilités potentielles de votre base de données, et de détecter les activités anormales susceptibles de présenter des menaces pour votre base de données. 

Cette offre de sécurité pour les serveurs SQL de machines virtuelles Azure est basée sur la même technologie fondamentale que celle utilisée dans le [package Azure SQL Database Advanced Data Security](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security).


## <a name="overview"></a>Vue d'ensemble

Advanced Data Security fournit un ensemble de fonctionnalités de sécurité SQL avancées incluant l’Évaluation des vulnérabilités et la Protection avancée contre les menaces.

* La fonctionnalité [Évaluation des vulnérabilités](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) est un service simple à configurer, qui vous permet de découvrir, suivre et de corriger des vulnérabilités de base de données potentielles. Elle offre une visibilité sur votre état de sécurité et inclut des étapes pour résoudre les problèmes de sécurité et améliorer la protection de votre base de données.
* La fonctionnalité [Protection avancée contre les menaces](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) détecte les activités anormales indiquant des tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses de votre serveur SQL. Elle supervise en permanence votre base de données pour détecter des activités suspectes, et envoie des alertes de sécurité orientées action en cas de modèles d’accès anormaux à la base de données. Ces alertes fournissent des détails sur les activités suspectes et des mesures recommandées pour examiner et atténuer la menace.

## <a name="get-started-with-advanced-data-security-for-sql-on-azure-vms"></a>Bien démarrer avec Advanced Data Security pour SQL sur les machines virtuelles Azure

Les étapes suivantes vous aideront à bien démarrer avec Advanced Data Security pour SQL sur les machines virtuelles Azure en préversion publique.

### <a name="set-up-advanced-data-security-for-sql-on-azure-vms"></a>Configurer Advanced Data Security pour SQL sur les machines virtuelles Azure

Activez Advanced Data Security pour SQL Server sur les machines virtuelles au niveau de l’abonnement/l’espace de travail :
 
1. À partir de la barre latérale de Security Center, ouvrez **Tarification et paramètres**.

1. Sélectionnez l’abonnement ou l’espace de travail pour lequel vous souhaitez activer Advanced Data Security pour SQL Server sur les machines virtuelles Azure.

1. Activez l’option **SQL Server sur les machines virtuelles (préversion)** . 

    (Cliquez sur la capture d’écran pour la développer)

    [![Alertes et recommandations de Security Center comme elles sont affichées dans Windows Admin Center](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png)](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-large.png#lightbox)

    Advanced Data Security pour SQL Server est activé sur toutes les machines SQL Server qui sont connectées à l’espace de travail sélectionné ou à l’espace de travail par défaut de l’abonnement sélectionné.

    >[!NOTE]
    > La solution sera active après le premier redémarrage de SQL Server. 

Pour créer un espace de travail, suivez les instructions fournies dans [Créer un espace de travail Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

Pour connecter la machine hôte SQL Server à un espace de travail, suivez les instructions données dans [Connecter des ordinateurs Windows à Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows).


## <a name="set-up-email-notification-for-atp-alerts"></a>Configurer la notification par e-mail pour les alertes ATP 

Vous pouvez définir une liste de destinataires devant recevoir une notification par e-mail quand des alertes Security Center sont générées. L’e-mail contient un lien direct vers l’alerte dans Azure Security Center avec tous les détails pertinents. 

1. Accédez à **Security Center** > **Tarification & paramètres**, puis cliquez sur l’abonnement concerné.

    ![Paramètres d’abonnement](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. Dans le menu Paramètres, cliquez sur **Notifications par e-mail**. 
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

## <a name="advanced-threat-protection-for-sql-servers-on-azure-vms-alerts"></a>Alertes Advanced Threat Protection pour SQL Server sur les machines virtuelles Azure
Les alertes sont générées en cas de détection de tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses des serveurs SQL. Ces événements peuvent déclencher les alertes suivantes :

### <a name="anomalous-access-pattern-alerts-preview"></a>Alertes de modèle d’accès anormal (préversion)

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

### <a name="potential-sql-injection-attacks-supported-in-sql-server-2019"></a>Attaques par injection potentielle de code SQL (prise en charge dans SQL Server 2019)

* **Vulnérabilité par injection de code SQL** : cette alerte est déclenchée quand une application génère une instruction SQL défectueuse dans la base de données. Cette alerte peut éventuellement indiquer une vulnérabilité aux attaques par injection de code SQL. Causes potentielles :
    * Un défaut dans le code d’application qui crée l’instruction SQL défectueuse
    * Le code de l’application ou des procédures stockées qui n’assainissent pas l’entrée utilisateur lors de la création de l’instruction SQL défectueuse, qui peut être exploitée par les injections SQL
* **Injection potentielle de code SQL** : cette alerte est déclenchée en cas d’attaque active contre une vulnérabilité d’application identifiée sur l’injection SQL. Cela signifie que l’attaquant tente d’injecter des instructions SQL malveillantes en utilisant les procédures stockées ou le code d’application vulnérables.


### <a name="unsafe-command-supported-in-sql-server-2019"></a>Commande dangereuse (prise en charge dans SQL Server 2019)

* **Action potentiellement dangereuse** : cette alerte est déclenchée quand une commande à privilèges élevés et potentiellement dangereuse est exécutée. Causes potentielles :
    * Une commande qu’il est recommandé de désactiver pour une meilleure posture de sécurité est activée.
    * Un attaquant essaie d’exploiter ou de réaffecter des privilèges d’accès SQL.   


## <a name="explore-and-investigate-security-alerts"></a>Explorer et examiner les alertes de sécurité

Vous pouvez examiner vos alertes sur la sécurité des données dans la page des alertes de Security Center, sous l’onglet Sécurité de la ressource ou par le biais du lien direct fourni dans les e-mails d’alerte.

1. Pour examiner les alertes :

    * Dans Security Center, cliquez sur **Alertes de sécurité** dans la barre latérale et sélectionnez une alerte.
    * Dans l’étendue des ressources, ouvrez la page de la ressource appropriée et, dans la barre latérale, cliquez sur **Sécurité**. 

1. Les alertes sont conçues pour être autonomes, avec des étapes de correction détaillées et des informations d’investigation dans chacune d’elles. Vous pouvez approfondir vos investigations en utilisant d’autres fonctionnalités d’Azure Security Center et d’Azure Sentinel pour avoir une vue plus large :

    * Activez la fonctionnalité d’audit de SQL Server pour faire d’autres investigations. Si vous utilisez Azure Sentinel, chargez les journaux d’audit SQL des événements du journal de sécurité Windows pour profiter d’une expérience d’investigation enrichie.
    * Pour améliorer votre posture de sécurité, appliquez les recommandations concernant la machine hôte que Security Center indique dans chaque alerte. Vous réduirez ainsi les risques d’attaques futures. 


## <a name="next-steps"></a>Étapes suivantes

Pour des informations connexes, consultez l’article suivant :

- [Comment appliquer les recommandations](security-center-remediate-recommendations.md)