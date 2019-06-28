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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65153967"
---
# <a name="advanced-threat-protection-for-azure-storage"></a>Advanced Threat Protection pour le stockage Azure

Advanced Threat Protection pour Stockage Azure offre une couche supplémentaire de sécurité intelligente qui détecte les tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses des comptes de stockage. Cette couche de protection vous permet de traiter efficacement les menaces sans pour autant être un expert en sécurité ni avoir besoin de gérer des systèmes de supervision de la sécurité. 

Les alertes de sécurité sont déclenchées lorsque des anomalies se produisent dans l’activité.  Ces alertes de sécurité sont intégrées avec [Azure Security Center](https://azure.microsoft.com/services/security-center/) et sont également envoyées par e-mail aux administrateurs d’abonnement, avec les détails des activités suspectes et des recommandations sur la façon d’examiner et de corriger les menaces.

> [!NOTE]
> * Advanced Threat Protection pour Stockage Azure n’est actuellement disponible que pour le Stockage d’objets blob.
> * Pour plus d’informations sur la tarification, y compris une version d’évaluation gratuite de 30 jours, consultez la [page de tarification d’Azure Security Center]( https://azure.microsoft.com/pricing/details/security-center/).
> * La fonctionnalité ATP pour Stockage Azure n’est actuellement pas disponible dans les régions de cloud souverain et Azure Government.

Advanced Threat Protection pour Stockage Azure ingère les journaux de diagnostic relatives aux requêtes de lecture, d’écriture et de suppression envoyées au Stockage Blob en vue de détecter les menaces potentielles. Pour examiner les alertes d’Advanced Threat Protection, vous pouvez afficher l’activité de stockage associée à l’aide de la journalisation Storage Analytics. Pour plus d’informations, consultez [Configurer la journalisation Storage Analytics](storage-monitor-storage-account.md#configure-logging).

## <a name="set-up-advanced-threat-protection"></a>Configurer Advanced Threat Protection 

### <a name="using-the-portal"></a>Utiliser le portail

1. Lancez le portail Azure sur [https://portal.azure.com](https://portal.azure.com/).

2. Accédez à la page de configuration du compte de stockage Azure que vous voulez protéger. Dans la page **Paramètres**, sélectionnez **Protection avancée contre les menaces**.

3. Dans le panneau de configuration **Protection avancée contre les menaces**
    * Activez (**ON**) l’option *Protection contre les menaces* avancée
    * Cliquez sur **Enregistrer** pour enregistrer la stratégie de protection avancée contre les menaces, nouvelle ou mise à jour. (Les prix dans l’image sont uniquement fournis à titre d’exemple.)

![Activer la protection avancée contre les menaces dans le stockage Azure](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="using-azure-security-center"></a>Utilisation du Centre de sécurité Azure
Lorsque vous vous abonnez au niveau Standard dans Azure Security Center, Advanced Threat Protection est configuré sur vos comptes de stockage. Pour plus d’informations, consultez [Passer au niveau tarifaire Standard de Security Center pour une sécurité renforcée](https://docs.microsoft.com/azure/security-center/security-center-pricing). (Les prix dans l’image sont uniquement fournis à titre d’exemple.)

![Niveau Standard dans ASC](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing.png)

### <a name="using-azure-resource-manager-templates"></a>Utilisation de modèles Azure Resource Manager

Utilisez un modèle Azure Resource Manager pour déployer un compte Stockage avec Advanced Threat Protection activé.
Pour plus d’informations, consultez [Compte de stockage avec Advanced Threat Protection](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="using-azure-policy"></a>Utilisation d’Azure Policy

Utilisez Azure Policy pour activer Advanced Threat Protection sur les comptes de stockage sous un groupe de ressources ou abonnement spécifique.

1. Lancez la page Azure **Policy - Définitions**.

1. Recherchez la stratégie **Déployer Advanced Threat Protection sur les comptes de stockage**.

     ![Recherche de stratégie](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)
  
1. Sélectionnez un groupe de ressources ou un abonnement Azure.

    ![Sélectionnez Abonnement ou Groupe](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. Attribuez la stratégie.

    ![Page Définitions de stratégies](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="using-rest-api"></a>Utilisation de l'API REST
Utilisez les commandes de l’API Rest pour créer, mettre à jour ou obtenir le paramètre Advanced Threat Protection pour un compte de stockage spécifique.

* [Advanced Threat Protection - Créer](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [Advanced Threat Protection - Obtenir](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="using-azure-powershell"></a>Utilisation de Microsoft Azure PowerShell

Utilisez les cmdlets PowerShell suivantes :

  * [Activer Advanced Threat Protection](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
  * [Obtenir Advanced Threat Protection](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
  * [Désactiver Advanced Threat Protection](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

## <a name="explore-security-anomalies"></a>Explorer les anomalies de sécurité

Quand des anomalies d’activité du stockage sont détectées, vous recevez une notification par e-mail qui contient des informations sur l’événement de sécurité suspect. Les détails de l’événement fournis sont les suivants :

* Nature de l’anomalie
* Nom du compte de stockage
* Heure de l’événement
* Type de stockage
* Causes potentielles 
* Procédure d’investigation
* Procédure de correction


L’e-mail fournit également des détails sur les causes possibles et les actions recommandées pour examiner et atténuer la menace potentielle.

![E-mail d’alerte envoyé par la protection avancée contre les menaces dans le stockage Azure](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

Vous pouvez examiner et gérer vos alertes de sécurité actuelles à partir de la [vignette Alertes de sécurité](../../security-center/security-center-managing-and-responding-alerts.md#managing-security-alerts) dans Azure Security Center. Cliquez sur une alerte pour afficher les détails et les actions recommandées pour examiner la menace actuelle et atténuer les menaces futures.

![E-mail d’alerte envoyé par la protection avancée contre les menaces dans le stockage Azure](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="protection-alerts"></a>Alertes de collection

Les alertes sont générées en cas de détection de tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses des comptes de stockage. Ces événements peuvent déclencher les alertes suivantes :

### <a name="anomalous-access-pattern-alerts"></a>Alertes de modèle d’accès anormal

* **Accès à partir d’un emplacement inhabituel** : Cette alerte est déclenchée en cas de changement dans le modèle d’accès à un compte de stockage. Cela peut être le cas, par exemple, quand quelqu’un a accédé à un compte de stockage à partir d’un emplacement géographique inhabituel.
Causes potentielles :
   * Un attaquant a accédé à votre compte de stockage
   * Un utilisateur légitime a accédé à votre compte de stockage à partir d’un nouvel emplacement
 
* **Anomalie d’application** : Cette alerte indique qu’une application inhabituelle a accédé à ce compte de stockage. Causes potentielles :
   * Un attaquant a accédé à votre compte de stockage à l’aide d’une nouvelle application.
   * Un utilisateur légitime a utilisé une nouvelle application/un nouveau navigateur pour accéder à votre compte de stockage.

* **Accès anonyme** : Cette alerte indique un changement dans le modèle d’accès à un compte de stockage. Par exemple, ce compte a fait l’objet d’un accès anonyme (c’est-à-dire sans authentification), ce qui est inattendu comparé au modèle d’accès récent à ce compte.
Causes potentielles :
   * Un attaquant a exploité l’accès en lecture public à un conteneur.
   * Un utilisateur ou une application légitime a utilisé un accès en lecture public à un conteneur.

### <a name="anomalous-extractupload-alerts"></a>Alertes d’extraction/de chargement anormal(e)

* **Exfiltration de données** : Cette alerte indique qu’une quantité anormalement élevée de données a été extraite par rapport à l’activité récente sur ce conteneur de stockage. Causes potentielles :
   * Un attaquant a extrait une grande quantité de données à partir d’un conteneur. (Par exemple : exfiltration/divulgation de données, transfert non autorisé de données)
   * Un utilisateur ou une application légitime a extrait une quantité inhabituelle de données à partir d’un conteneur. (Par exemple : activité de maintenance)

* **Suppression inattendue** : Cette alerte indique qu’une ou plusieurs opérations de suppression inattendues se sont produites dans un compte de stockage, par rapport à l’activité récente sur ce compte. Causes potentielles :
   * Un attaquant a supprimé des données à partir de votre compte de stockage.
   * Un utilisateur légitime a effectué une suppression inhabituelle.

* **Chargement de package de service cloud Azure** : Cette alerte indique qu’un package de service cloud Azure (fichier .cspkg) a été chargé dans un compte de stockage de façon inhabituelle, par rapport à l’activité récente sur ce compte. Causes potentielles : 
   * Un attaquant se prépare à déployer un code malveillant à partir de votre compte de stockage vers un service cloud Azure.
   * Un utilisateur légitime prépare un déploiement de service légitime.

### <a name="suspicious-storage-activities-alerts"></a>Alertes d’activités suspectes de stockage

* **Modification d’autorisation d’accès** : Cette alerte indique que les autorisations d’accès de ce conteneur de stockage ont été modifiées de façon inhabituelle. Causes potentielles : 
   * Un attaquant a modifié les autorisations du conteneur pour en affaiblir la sécurité.
   * Un utilisateur légitime a modifié les autorisations du conteneur.

* **Inspection d’accès** : Cette alerte indique que les autorisations d’accès d’un compte de stockage ont été inspectées de façon inhabituelle, par rapport à l’activité récente sur ce compte. Causes potentielles : 
   * Un attaquant a effectué une reconnaissance en vue d’une attaque future.
   * Un utilisateur légitime a effectué une maintenance sur le compte de stockage.

* **Exploration des données** : Cette alerte indique que les objets blob ou les conteneurs dans un compte de stockage ont été énumérés de manière inhabituelle, par rapport à l’activité récente sur ce compte. Causes potentielles : 
   * Un attaquant a effectué une reconnaissance en vue d’une attaque future.
   * Un utilisateur ou une logique d’application légitime a exploré les données au sein du compte de stockage.






## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les [Journaux dans les comptes de stockage Azure ](/rest/api/storageservices/About-Storage-Analytics-Logging)

* En savoir plus sur [Azure Security Center](../../security-center/security-center-intro.md)
