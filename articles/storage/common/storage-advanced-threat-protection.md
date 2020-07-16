---
title: Configurer la protection avancée contre les menaces
titleSuffix: Azure Storage
description: Configurez la protection avancée contre les menaces pour Stockage Azure de façon à détecter les anomalies dans l’activité de votre compte et à être notifié en cas de tentatives d’accès potentiellement dangereuses à votre compte.
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: tamram
ms.reviewer: ozgun
ms.openlocfilehash: 3069ee020d5f127eb0bdb8cbaf251cd3f3cef8d9
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86118412"
---
# <a name="configure-advanced-threat-protection-for-azure-storage"></a>Configurer la protection avancée contre les menaces pour Stockage Azure

La protection avancée contre les menaces pour Stockage Azure offre une couche supplémentaire de veille de sécurité qui détecte les tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses des comptes de stockage. Cette couche de protection vous permet de traiter les menaces sans pour autant être un expert en sécurité ni avoir besoin de gérer des systèmes de supervision de la sécurité.

Les alertes de sécurité sont déclenchées lorsque des anomalies se produisent dans l’activité. Ces alertes de sécurité sont intégrées avec [Azure Security Center](https://azure.microsoft.com/services/security-center/) et sont également envoyées par e-mail aux administrateurs d’abonnement, avec les détails des activités suspectes et des recommandations sur la façon d’examiner et de corriger les menaces.

Le service ingère les journaux de ressources des demandes de lecture, d’écriture et de suppression adressées au Stockage Blob et à Azure Files (préversion) en vue de détecter les menaces. Pour examiner les alertes de la protection avancée contre les menaces, vous pouvez afficher l’activité de stockage associée à l’aide de la journalisation Storage Analytics. Pour plus d’informations, consultez **Configuration de la journalisation** dans [Surveillance d’un compte de stockage dans le portail Azure](storage-monitor-storage-account.md#configure-logging).

## <a name="availability"></a>Disponibilité

Advanced Threat Protection pour Stockage Azure est actuellement disponible pour Stockage Blob, Azure Files (préversion) et Azure Data Lake Storage Gen2 (préversion). Les types de compte qui prennent en charge Advanced Threat Protection incluent les comptes à usage général v2, les objets blob de blocs et les comptes Stockage Blob. Advanced Threat Protection est disponible dans tous les clouds publics et US Government, mais pas dans d’autres régions de cloud souverain ou de cloud Azure Government.

Les comptes avec des espaces de noms hiérarchiques activés pour Data Lake Storage prennent en charge les transactions à l’aide des API Stockage Blob Azure et des API Data Lake Storage. Les partages de fichiers Azure prennent en charge les transactions via SMB.

Pour plus d’informations sur la tarification, y compris une version d’évaluation gratuite de 30 jours, consultez la [page de tarification d’Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="set-up-advanced-threat-protection"></a>Configurer la protection avancée contre les menaces

Vous pouvez configurer la protection avancée contre les menaces de plusieurs façons, qui sont décrites dans les sections suivantes.

### <a name="azure-security-center"></a>[Centre de sécurité Azure](#tab/azure-security-center)

Quand vous vous abonnez au niveau Standard dans Azure Security Center, la protection avancée contre les menaces est automatiquement configurée sur tous vos comptes de stockage. Vous pouvez activer ou désactiver la protection avancée contre les menaces pour vos comptes de stockage sous un abonnement spécifique comme suit :

1. Lancez **Azure Security Center** dans le [portail Azure](https://portal.azure.com).
1. Dans le menu principal, cliquez sur **Tarification et paramètres**.
1. Cliquez sur l’abonnement pour lequel vous voulez activer ou désactiver la protection contre les menaces pour ses comptes de stockage.

    ![Sélectionner un abonnement](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-subscription.png)

1. Cliquez sur **Niveau tarifaire**.
1. Dans la section **Sélectionner le niveau tarifaire par type de ressource**, dans la ligne **Comptes de stockage**, cliquez sur **Activé** ou sur **Désactivé**.

    ![Activer ATP dans Security Center](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing2.png)
1. Cliquez sur **Enregistrer**.

### <a name="portal"></a>[Portail](#tab/azure-portal)

1. Lancez le [portail Azure](https://portal.azure.com/).
1. Accédez à votre compte Stockage Azure. Sous **Paramètres**, sélectionnez **Sécurité avancée**.
1. Sélectionnez le lien **Paramètres** dans la page de configuration de la sécurité avancée.
1. Définissez **Sécurité avancée** sur **ACTIVÉ**.
1. Cliquez sur **Enregistrer** pour enregistrer la stratégie nouvelle ou mise à jour.

    ![Activer la protection avancée contre les menaces dans le stockage Azure](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="template"></a>[Modèle](#tab/template)

Utilisez un modèle Azure Resource Manager pour déployer un compte Stockage Azure avec la protection avancée contre les menaces activée. Pour plus d’informations, consultez [Compte de stockage avec la protection avancée contre les menaces](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

Utilisez Azure Policy pour activer la protection avancée contre les menaces dans les comptes de stockage sous un abonnement ou un groupe de ressources spécifiques.

1. Lancez la page **Azure Policy - Définitions**.

1. Recherchez la stratégie **Déployer Advanced Threat Protection sur les comptes de stockage**.

     ![Recherche de stratégie](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)

1. Sélectionnez un groupe de ressources ou un abonnement Azure.

    ![Sélectionnez Abonnement ou Groupe](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. Attribuez la stratégie.

    ![Page Définitions de stratégies](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="rest-api"></a>[REST API](#tab/rest-api)

Utilisez les commandes de l’API Rest pour créer, mettre à jour ou obtenir le paramètre de protection avancée contre les menaces pour un compte de stockage spécifique.

* [Protection avancée contre les menaces – Créer](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [Protection avancée contre les menaces – Obtenir](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Utilisez les cmdlets PowerShell suivantes :

* [Activer la protection avancée contre les menaces](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
* [Obtenir la protection avancée contre les menaces](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
* [Désactiver la protection avancée contre les menaces](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

---

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

Vous pouvez examiner et gérer vos alertes de sécurité actuelles à partir de la [vignette Alertes de sécurité](../../security-center/security-center-managing-and-responding-alerts.md) dans Azure Security Center. Cliquez sur une alerte pour afficher les détails et les actions recommandées pour examiner la menace actuelle et atténuer les menaces futures.

![E-mail d’alerte envoyé par la protection avancée contre les menaces dans le stockage Azure](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="security-alerts"></a>Alertes de sécurité

Les alertes sont générées en cas de détection de tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses des comptes de stockage. Pour obtenir la liste des alertes relatives au Stockage Azure, consultez la section **Stockage** dans [Protection contre les menaces pour les services de données dans Azure Security Center](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurestorage).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les [Journaux dans les comptes de stockage Azure ](/rest/api/storageservices/About-Storage-Analytics-Logging)
* En savoir plus sur [Azure Security Center](../../security-center/security-center-intro.md)
