---
title: Paramètres de stratégie de groupe et de MDM | Microsoft Docs
description: Fournit des informations sur les paramètres de stratégie de groupe et de gestion des appareils mobiles (MDM) qui doivent être utilisés sur les appareils d’entreprise. Ces stratégies sont appliquées à l’ensemble de l’appareil de l’utilisateur.
services: active-directory
keywords: quels sont les paramètres de stratégie de groupe et de MDM pour Enterprise State Roaming, Enterprise State Roaming, cloud windows
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: curtand
ms.subservice: devices
ms.assetid: 6471a9b3-8dd4-4237-89d1-bfbeca9f8252
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: joflore
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f1b23862edeabbf8e03d4589fa3f22a3fab9f72
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60353157"
---
# <a name="group-policy-and-mdm-settings"></a>Paramètres de stratégie de groupe et de gestion des appareils mobiles
Ces paramètres de stratégie de groupe et de gestion des appareils mobiles (MDM) doivent être utilisés sur des appareils d’entreprise uniquement, car ces stratégies sont appliquées à l’ensemble de l’appareil de l’utilisateur. Appliquer une stratégie de MDM pour désactiver la synchronisation des paramètres pour un appareil personnel appartenant à un utilisateur aura un impact négatif sur l’utilisation de cet appareil. En outre, les autres comptes d’utilisateurs de l’appareil seront eux aussi affectés par la stratégie.

Les entreprises qui souhaitent gérer l’itinérance pour les appareils personnels (non gérés) peuvent utiliser le portail Azure pour activer ou désactiver l’itinérance, plutôt que de recourir à une stratégie de groupe ou à la MDM.
Les tableaux suivants décrivent les paramètres de stratégie disponibles.

## <a name="mdm-settings"></a>Paramètres de MDM
Les paramètres de stratégie de MDM s’appliquent à Windows 10 et à Windows 10 Mobile.  La prise en charge Windows 10 Mobile existe uniquement pour l’itinérance basée sur compte Microsoft via le compte OneDrive de l’utilisateur.  Veuillez consulter [Appareils et systèmes d’extrémité](enterprise-state-roaming-windows-settings-reference.md) pour plus de détails sur les appareils pris en charge pour la synchronisation basée sur Azure AD.

| Nom | Description |
| --- | --- |
| Autoriser la connexion de comptes Microsoft |Permet aux utilisateurs de s’authentifier sur l’appareil à l’aide d’un compte Microsoft |
| Autoriser la synchronisation de mes paramètres |Permet l’itinérance pour les paramètres de Windows et les données d’application. Désactiver cette stratégie désactive la synchronisation, ainsi que les sauvegardes sur les appareils mobiles |

## <a name="group-policy-settings"></a>Paramètres de stratégie de groupe
Les paramètres de stratégie de groupe s’appliquent aux appareils Windows 10 joints à un domaine Active Directory. Le tableau inclut également les paramètres hérités qui apparaissent pour gérer les paramètres de synchronisation, mais qui ne fonctionnent pas pour Enterprise State Roaming pour Windows 10, avec la notation « Ne pas utiliser » dans la description.

Ces paramètres se trouvent dans : `Computer Configuration > Administrative Templates > Windows Components > Sync your settings` 

| Nom | Description |
| --- | --- |
| Comptes : Bloquer des comptes Microsoft |Ce paramètre de stratégie empêche les utilisateurs d’ajouter de nouveaux comptes Microsoft sur l’ordinateur |
| Ne pas synchroniser |Empêche l’itinérance des paramètres Windows et des données d’application des utilisateurs |
| Ne pas synchroniser les options personnalisées |Désactive la synchronisation du groupe Thèmes |
| Ne pas synchroniser les paramètres du navigateur |Désactive la synchronisation du groupe Internet Explorer |
| Ne pas synchroniser les mots de passe |Désactive la synchronisation du groupe Mots de passe |
| Ne pas synchroniser les autres paramètres Windows |Désactive la synchronisation du groupe Autres paramètres Windows |
| Ne pas synchroniser la personnalisation du Bureau |N’utilisez pas cette fonction ; aucun effet |
| Ne pas synchroniser en cas de connexion limitée |Désactive l’itinérance en cas de connexion limitée, par exemple la 3G mobile |
| Ne pas synchroniser les applications |N’utilisez pas cette fonction ; aucun effet |
| Ne pas synchroniser les paramètres d’application |Désactive l’itinérance des données d’application |
| Ne pas synchroniser les paramètres de démarrage |N’utilisez pas cette fonction ; aucun effet |

## <a name="next-steps"></a>Étapes suivantes

Pour une vue d’ensemble, consultez [Vue d’ensemble d’Enterprise State Roaming](enterprise-state-roaming-overview.md).


