---
title: Activer Enterprise State Roaming dans Azure Active Directory | Microsoft Docs
description: Foire aux questions sur les paramètres Enterprise State Roaming sur les appareils Windows. Enterprise State Roaming fournit aux utilisateurs une expérience unifiée sur leurs appareils Windows et réduit le temps nécessaire à la configuration d’un nouveau périphérique.
services: active-directory
keywords: enterprise state roaming, cloud windows, comment activer enterprise state roaming
documentationcenter: ''
author: tanning
manager: daveba
editor: curtand
ms.subservice: devices
ms.assetid: f71d66fd-7f9e-45eb-9cfe-5d989870f8a4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: joflore
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7e665f3124296392c8138f4f2bd3632b56e53de
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65230933"
---
# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>Activer Enterprise State Roaming dans Azure Active Directory
Enterprise State Roaming est disponible pour toute organisation disposant d’une licence Azure AD Premium ou Enterprise Mobility + Security (EMS). Pour plus d’informations sur l’obtention d’un abonnement Azure AD, consultez la page [du produit Azure AD](https://azure.microsoft.com/services/active-directory) .

Lorsque vous activez Enterprise State Roaming, votre organisation reçoit automatiquement une licence gratuite à utilisation limitée pour le service de protection Azure Rights Management d’Azure Information Protection. Cet abonnement gratuit est limité au chiffrage et au déchiffrage des paramètres d’entreprise et des données d’application synchronisés par Enterprise State Roaming. Vous devez avoir [un abonnement payant](https://azure.microsoft.com/pricing/details/information-protection/) pour utiliser toutes les fonctionnalités du service Azure Rights Management.

## <a name="to-enable-enterprise-state-roaming"></a>Activer Enterprise State Roaming

1. Connectez-vous au [Centre d’administration Azure AD](https://aad.portal.azure.com/).

1. Sélectionnez **Azure Active Directory** &gt; **Appareils** &gt; **Enterprise State Roaming**.

1. Sélectionnez **Les utilisateurs peuvent synchroniser les paramètres et les données d’application entre les appareils**. Pour plus d’informations, consultez [Comment configurer les paramètres de l’appareil](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).
  
   ![Image du paramètre d’appareil «Les utilisateurs peuvent synchroniser les paramètres et les données d’application sur différents appareils»](./media/enterprise-state-roaming-enable/device-settings.png)
  
Pour qu’un appareil Windows 10 utilise le service Enterprise State Roaming, l’appareil doit s’authentifier à l’aide d’une identité Azure AD. Pour les appareils joints à Azure AD, l’identité de connexion principale de l’utilisateur est l’identité Azure AD. Ainsi, aucune configuration supplémentaire n’est requise. Pour les appareils qui utilisent un répertoire Active Directory local, l’administrateur informatique doit [configurer les appareils hybrides joints à Azure AD](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-manual-steps). 

## <a name="data-storage"></a>Stockage des données
Les données Enterprise State Roaming sont hébergées dans une ou plusieurs [régions Azure](https://azure.microsoft.com/regions/) qui correspondent le plus à la valeur de pays/région définie dans l’instance Azure Active Directory. Les données Enterprise State Roaming sont partitionnées selon les trois principales régions géographiques : Amérique du Nord, EMEA et Asie-Pacifique. Les données Enterprise State Roaming pour le client sont situées localement dans la région géographique, et ne sont pas répliquées entre régions.  Exemple :

Valeur Pays/région | Emplacement de l’hébergement des données
---------------------|-------------------------
Une pays/région EMEA comme France ou Zambie | Une ou plusieurs régions Azure en Europe 
Un pays/région Amérique du Nord comme États-Unis ou Canada | Une ou plusieurs régions Azure aux États-Unis
Une pays/région APAC comme Australie ou Nouvelle-Zélande | Une ou plusieurs régions Azure en Asie
Régions Amérique du Sud et Antarctique | Une ou plusieurs régions Azure aux États-Unis

La valeur de pays/région est définie dans le cadre du processus de création d’un répertoire Azure AD et ne peut pas être modifiée par la suite. Pour plus d’informations sur votre emplacement de stockage des données, soumettez un ticket au [support technique Azure](https://azure.microsoft.com/support/options/).

## <a name="view-per-user-device-sync-status"></a>Afficher l’état de synchronisation d’appareil par utilisateur
Procédez comme suit pour afficher un rapport d’état de synchronisation d’appareil par utilisateur.

1. Connectez-vous au [Centre d’administration Azure AD](https://aad.portal.azure.com/).

1. Sélectionnez **Azure Active Directory** &gt; **Utilisateurs** &gt; **Tous les utilisateurs**.

1. Sélectionnez l’utilisateur, puis **Appareils**.

1. Sous **Afficher**, sélectionnez **Paramètres de synchronisation des appareils et données d’application** pour afficher l’état de synchronisation.
  
   ![image du paramètre de synchronisation d’appareils et des données](./media/enterprise-state-roaming-enable/sync-status.png)
  
1. Si des appareils se synchronisent pour cet utilisateur, vous les verrez comme illustré ici.
  
   ![image de la colonne de synchronisation d’appareils et des données](./media/enterprise-state-roaming-enable/device-status-row.png)

## <a name="data-retention"></a>Conservation des données
Les données synchronisées vers le cloud Microsoft à l’aide d’Enterprise State Roaming sont conservées jusqu’à leur suppression manuelle ou bien jusqu’à ce qu’elles soient considérées comme obsolètes. 

### <a name="explicit-deletion"></a>Suppression explicite
La suppression explicite est lorsqu’un administrateur Azure supprime un utilisateur ou un répertoire, ou qu’il demande explicitement que les données soient supprimées.

* **Suppression d’un utilisateur** : Quand un utilisateur est supprimé dans Azure AD, ses données d’itinérance sont supprimées après 90 à 180 jours. 
* **Suppression d’un annuaire** : La suppression de tout un annuaire dans Azure AD est une opération à effet immédiat. Toutes les données de paramètres associées à ce répertoire sont supprimées après 90 à 180 jours. 
* **Suppression à la demande** : Si l’administrateur Azure AD veut supprimer manuellement les données d’un utilisateur spécifique ou des données de paramètres, il peut soumettre un ticket au [support technique Azure](https://azure.microsoft.com/support/). 

### <a name="stale-data-deletion"></a>Suppression des données obsolètes
Les données qui n’ont pas été utilisées depuis un an (« la période de rétention ») sont considérées comme obsolètes et peuvent être supprimées du cloud Microsoft. La période de rétention est susceptible de changer, mais ne sera pas inférieure à 90 jours. Il peut s’agir d’un ensemble spécifique de paramètres Windows/d’application ou de tous les paramètres d’un utilisateur. Exemple :

* Si une collection de paramètres spécifique n’est utilisée par aucun appareil (par exemple, si une application est supprimée de l’appareil ou qu’un groupe de paramètres tels que « Thème » est désactivé pour tous les appareils d’un utilisateur), elle devient obsolète à la fin de la période de rétention et peut être supprimée. 
* Si un utilisateur a désactivé la synchronisation des paramètres sur tous ses appareils, aucune donnée de paramètres n’est utilisée. De plus, toutes les données de paramètres pour cet utilisateur deviennent obsolètes et peuvent être supprimées à la fin de la période de rétention. 
* Si l’administrateur du répertoire Azure AD désactive Enterprise State Roaming pour l’ensemble du répertoire, la synchronisation des paramètres cessera pour tous les utilisateurs du répertoire. De plus, toutes les données de paramètres de tous les utilisateurs deviennent obsolètes et peuvent être supprimées à la fin de la période de rétention. 

### <a name="deleted-data-recovery"></a>Récupération de données supprimées
La stratégie de rétention de données n’est pas configurable. Les données définitivement supprimées ne sont pas récupérables. Toutefois, les données de paramètres sont supprimées uniquement du cloud Microsoft, pas de l’appareil de l’utilisateur final. Si un appareil se reconnecte au service Enterprise State Roaming, les paramètres sont alors à nouveau synchronisés et stockés dans le cloud Microsoft.

## <a name="next-steps"></a>Étapes suivantes

* [Présentation d’Enterprise State Roaming](enterprise-state-roaming-overview.md)
* [FAQ sur l’itinérance des paramètres et des données](enterprise-state-roaming-faqs.md)
* [Paramètres de stratégie de groupe et de MDM pour la synchronisation des paramètres](enterprise-state-roaming-group-policy-settings.md)
* [Référence des paramètres d’itinérance Windows 10](enterprise-state-roaming-windows-settings-reference.md)
* [Dépannage](enterprise-state-roaming-troubleshooting.md)
