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
ms.openlocfilehash: 7d4f36be51591d6be2b4c42eb8a8950ab52a0258
ms.sourcegitcommit: f7998db5e6ba35cbf2a133174027dc8ccf8ce957
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/05/2019
ms.locfileid: "68782577"
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

Quand vous vous abonnez au niveau Standard dans Azure Security Center, Advanced Threat Protection est automatiquement configuré sur tous vos comptes de stockage. Vous pouvez activer ou désactiver Advanced Threat Protection pour vos comptes de stockage sous un abonnement spécifique comme suit :

1. Lancez **Azure Security Center** dans le [portail Azure](https://portal.azure.com).
1. Dans le menu principal, cliquez sur **Tarification et paramètres**.
1. Cliquez sur l’abonnement pour lequel vous voulez activer ou désactiver la protection contre les menaces pour ses comptes de stockage.

    ![Sélectionnez un abonnement](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-subscription.png)

1. Cliquez sur **Niveau tarifaire**.
1. Dans la section **Sélectionner le niveau tarifaire par type de ressource**, dans la ligne **Comptes de stockage**, cliquez sur **Activé** ou sur **Désactivé**.

    ![Activer ATP dans Security Center](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing2.png)
1. Cliquez sur **Enregistrer**.

### <a name="using-azure-resource-manager-templates"></a>Utilisation de modèles Azure Resource Manager

Utilisez un modèle Azure Resource Manager pour déployer un compte Stockage avec Advanced Threat Protection activé. Pour plus d’informations, consultez [Compte de stockage avec Advanced Threat Protection](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="using-azure-policy"></a>Utilisation d’Azure Policy

Utilisez Azure Policy pour activer Advanced Threat Protection sur les comptes de stockage sous un groupe de ressources ou abonnement spécifique.

1. Lancez la page **Azure Policy - Définitions**.

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

Les alertes sont générées en cas de détection de tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses des comptes de stockage. Pour obtenir la liste de ces alertes, consultez [Alertes de Stockage Azure](../../security-center/security-center-alerts-data-services.md#azure-storage).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les [Journaux dans les comptes de stockage Azure ](/rest/api/storageservices/About-Storage-Analytics-Logging)

* En savoir plus sur [Azure Security Center](../../security-center/security-center-intro.md)
