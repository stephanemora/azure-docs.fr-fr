---
title: Advanced Threat Protection pour le stockage Azure
description: Configurez la protection avancée contre les menaces dans le stockage Azure pour détecter les anomalies dans l’activité de votre compte et vous alerter sur les tentatives d’accès à votre compte potentiellement dangereuses.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/03/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: ed263fec271801ac9c46bbc3125c71ca15b9a330
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153967"
---
# <a name="advanced-threat-protection-for-azure-storage"></a>Advanced Threat Protection pour le stockage Azure

Advanced Threat Protection pour Stockage Azure offre une couche supplémentaire de sécurité intelligente qui détecte les tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses des comptes de stockage. Cette couche de protection vous permet de traiter efficacement les menaces sans pour autant être un expert en sécurité ni avoir besoin de gérer des systèmes de supervision de la sécurité. 

Alertes de sécurité sont déclenchés lorsque des anomalies dans l’activité se produisent.  Ces alertes de sécurité sont intégrées avec [Azure Security Center](https://azure.microsoft.com/services/security-center/)et sont également envoyées par courrier électronique aux administrateurs d’abonnement, avec les détails des activités suspectes et des recommandations sur la façon d’examiner et corriger les menaces.

> [!NOTE]
> * Protection avancée contre les menaces pour le stockage Azure est actuellement disponible uniquement pour le stockage d’objets Blob.
> * Pour plus d’informations, y compris une version d’évaluation gratuite de 30 jours, consultez le [page de tarification Azure Security Center]( https://azure.microsoft.com/pricing/details/security-center/).
> * ATP pour la fonctionnalité de stockage Azure n’est actuellement pas disponible dans Azure government et régions de cloud souverain.

Protection avancée contre les menaces pour le stockage Azure reçoit des journaux de diagnostic de requêtes de lecture, écriture et delete pour le stockage d’objets Blob pour la détection des menaces. Pour examiner les alertes à partir de la protection contre les menaces avancées, vous pouvez afficher l’activité de stockage associé à l’aide de la journalisation du stockage Analytique. Pour plus d’informations, consultez Comment [configurer la journalisation du stockage Analytique](storage-monitor-storage-account.md#configure-logging).

## <a name="set-up-advanced-threat-protection"></a>Configurer la protection avancée contre les menaces 

### <a name="using-the-portal"></a>Utiliser le portail

1. Lancez le portail Azure sur [https://portal.azure.com](https://portal.azure.com/).

2. Accédez à la page de configuration du compte de stockage Azure que vous voulez protéger. Dans la page **Paramètres**, sélectionnez **Protection avancée contre les menaces**.

3. Dans le panneau de configuration **Protection avancée contre les menaces**
    * Activez (**ON**) l’option *Protection contre les menaces* avancée
    * Cliquez sur **Enregistrer** pour enregistrer la stratégie de protection avancée contre les menaces, nouvelle ou mise à jour. (Les prix de l’image sont par exemple uniquement à des fins).

![Activer la protection avancée contre les menaces dans le stockage Azure](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="using-azure-security-center"></a>Utilisation du Centre de sécurité Azure
Lorsque vous vous abonnez au niveau Standard dans Azure Security Center, Advanced Threat Protection est configurée sur vos comptes de stockage. Pour plus d’informations, consultez [mise à niveau vers le niveau Standard de Security Center pour renforcer la sécurité](https://docs.microsoft.com/azure/security-center/security-center-pricing). (Les prix de l’image sont par exemple uniquement à des fins).

![Niveau standard dans ASC](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing.png)

### <a name="using-azure-resource-manager-templates"></a>Utilisation de modèles Azure Resource Manager

Utilisez un modèle Azure Resource Manager pour déployer un compte de stockage Azure-Protection avancée contre les menaces est activé.
Pour plus d’informations, consultez [compte de stockage avec Advanced Threat Protection](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="using-azure-policy"></a>À l’aide de la stratégie Azure

Utiliser une stratégie de Azure pour activer-Protection avancée contre les menaces sur les comptes de stockage sous un groupe de ressources ou abonnement spécifique.

1. Déjeuner Azure **Policy - définitions** page.

1. Recherchez le **déployer Advanced Threat Protection sur les comptes de stockage** stratégie.

     ![Stratégie de recherche](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)
  
1. Sélectionnez un groupe de ressources ou abonnement Azure.

    ![Sélectionnez l’abonnement ou groupe](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. Affectez la stratégie.

    ![Page de définitions de stratégie](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="using-rest-api"></a>Utilisation de l'API REST
Utilisez les commandes de l’API Rest pour créer, mettre à jour ou obtenir le paramètre-Protection avancée contre les menaces pour un compte de stockage spécifique.

* [Advanced Threat Protection - créer](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [Advanced Threat Protection - obtenir](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="using-azure-powershell"></a>Utilisation de Microsoft Azure PowerShell

Utilisez les applets de commande PowerShell suivante :

  * [Activer la Protection avancée contre les menaces](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
  * [Bénéficiez de la Protection contre les menaces avancées](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
  * [Désactiver la Protection avancée contre les menaces](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

## <a name="explore-security-anomalies"></a>Explorez les anomalies de sécurité

Quand des anomalies d’activité du stockage sont détectées, vous recevez une notification par e-mail qui contient des informations sur l’événement de sécurité suspect. Les détails de l’événement fournis sont les suivants :

* La nature de l’anomalie
* Nom du compte de stockage
* L’heure de l’événement
* Le type de stockage
* Les causes potentielles 
* La procédure d’investigation
* Les étapes de correction


L’e-mail fournit également des détails sur les causes possibles et les actions recommandées pour examiner et atténuer la menace potentielle.

![E-mail d’alerte envoyé par la protection avancée contre les menaces dans le stockage Azure](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

Vous pouvez examiner et gérer vos alertes de sécurité actuelles à partir de la [vignette Alertes de sécurité](../../security-center/security-center-managing-and-responding-alerts.md#managing-security-alerts) dans Azure Security Center. Cliquez sur une alerte pour afficher les détails et les actions recommandées pour examiner la menace actuelle et atténuer les menaces futures.

![E-mail d’alerte envoyé par la protection avancée contre les menaces dans le stockage Azure](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="protection-alerts"></a>Alertes de collection

Les alertes sont générées en cas de détection de tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses des comptes de stockage. Ces événements peuvent déclencher les alertes suivantes :

### <a name="anomalous-access-pattern-alerts"></a>Alertes de modèle d’accès anormaux

* **Accès à partir de l’emplacement inhabituel**: Cette alerte est déclenchée lorsqu’il existe une modification du modèle d’accès à un compte de stockage. Cela peut être le cas, par exemple, quand quelqu’un a accédé à un compte de stockage à partir d’un emplacement géographique inhabituel.
Causes possibles :
   * Un attaquant a accédé à votre compte de stockage
   * Un utilisateur légitime a accédé à votre compte de stockage à partir d’un nouvel emplacement
 
* **Application anomalies**: Cette alerte indique qu’une application inhabituel a accédé à ce compte de stockage. Causes possibles :
   * Un attaquant a accédé à votre compte de stockage à l’aide d’une nouvelle application.
   * Un utilisateur légitime a utilisé un nouveau/navigateur de l’application à accéder à votre compte de stockage.

* **L’accès anonyme**: Cette alerte indique qu’il existe une modification du modèle d’accès à un compte de stockage. Par exemple, ce compte a été accessibles de manière anonyme (c'est-à-dire sans aucune authentification), qui est inattendu comparé au modèle d’accès récents sur ce compte.
Causes possibles :
   * Une personne malveillante a exploité l’accès en lecture public pour un conteneur.
   * Un utilisateur légitime ou une application a utilisé un accès en lecture public pour un conteneur.

### <a name="anomalous-extractupload-alerts"></a>Alertes d’extraction/chargement anormales

* **Exfiltration de données**: Cette alerte indique qu’une quantité anormalement élevée de données a été extrait par rapport à l’activité récente sur ce conteneur de stockage. Causes possibles :
   * Une personne malveillante a extrait une grande quantité de données à partir d’un conteneur. (Par exemple : exfiltration/divulgation de données, le transfert non autorisé de données)
   * Un utilisateur légitime ou une application a extrait un nombre inhabituel de données à partir d’un conteneur. (Par exemple : activité de maintenance)

* **Suppression inattendue**: Cette alerte indique qu’une ou plusieurs opérations de suppression inattendue s’est produite dans un compte de stockage, par rapport à l’activité récente sur ce compte. Causes possibles :
   * Une personne malveillante a supprimé des données à partir de votre compte de stockage.
   * Un utilisateur légitime a effectué une suppression inhabituel.

* **Télécharger le package de Service Cloud Azure**: Cette alerte indique qu’un package de Service Cloud Azure (fichier .cspkg) a été chargé dans un compte de stockage de façon inhabituelle, par rapport à l’activité récente sur ce compte. Causes possibles : 
   * Une personne malveillante a été préparation au déploiement d’un code malveillant à partir de votre compte de stockage à un service cloud Azure.
   * Préparation d’un utilisateur légitime pour un déploiement de service légitime.

### <a name="suspicious-storage-activities-alerts"></a>Alertes d’activités suspectes de stockage

* **Modification d’autorisation d’accès**: Cette alerte indique que les autorisations d’accès de ce conteneur de stockage ont été modifiées de façon inhabituelle. Causes possibles : 
   * Une personne malveillante a modifié les autorisations de conteneur pour assouplir sa sécurité.
   * Un utilisateur légitime a modifié les autorisations du conteneur.

* **Accéder à l’Inspection**: Cette alerte indique que les autorisations d’accès d’un compte de stockage ont été inspectées de façon inhabituelle, par rapport à l’activité récente sur ce compte. Causes possibles : 
   * Une personne malveillante a effectué la reconnaissance d’une attaque future.
   * Un utilisateur légitime a effectué la maintenance sur le compte de stockage.

* **Exploration des données**: Cette alerte indique que les objets BLOB ou des conteneurs dans un compte de stockage ont été énumérés de manière inhabituelle, par rapport à l’activité récente sur ce compte. Causes possibles : 
   * Une personne malveillante a effectué la reconnaissance d’une attaque future.
   * Un utilisateur légitime ou une logique d’application a exploré les données au sein du compte de stockage.






## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur [journaux dans les comptes de stockage Azure](/rest/api/storageservices/About-Storage-Analytics-Logging)

* En savoir plus sur [Azure Security Center](../../security-center/security-center-intro.md)
