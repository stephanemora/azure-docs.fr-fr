---
title: Qu’est-ce qu’Enterprise State Roaming dans Azure Active Directory ? | Microsoft Docs
description: Enterprise State Roaming fournit aux utilisateurs une expérience unifiée sur leurs appareils Windows et réduit le temps nécessaire à la configuration d’un nouveau périphérique.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5b60970592180a2353860369e637d4b9a9bb8f9
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481904"
---
# <a name="what-is-enterprise-state-roaming"></a>Qu’est-ce qu’Enterprise State Roaming ?

Avec Windows 10, les utilisateurs [Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) ont la possibilité de synchroniser en toute sécurité leurs paramètres utilisateur et les données de paramètres d’application dans le cloud. Enterprise State Roaming fournit aux utilisateurs une expérience unifiée sur leurs appareils Windows et réduit le temps nécessaire à la configuration d’un nouveau périphérique. Enterprise State Roaming fonctionne comme la [synchronisation des paramètres de consommateur](https://go.microsoft.com/fwlink/?linkid=2015135) présentée dans Windows 8. En outre, Enterprise State Roaming offre :

* **Séparation des données d’entreprise des données client** : les organisations maîtrisent leurs données, et les données d’entreprise ne sont pas mélangées dans le compte cloud client ou aucune donnée client ne figure dans le compte cloud d’entreprise.
* **Sécurité renforcée** : les données sont automatiquement chiffrées avant de quitter l’appareil Windows 10 de l’utilisateur à l’aide d’Azure Rights Management (Azure RMS) et les données restent chiffrées au repos dans le cloud. Tout le contenu reste chiffré au repos dans le cloud, à l’exception des espaces de noms tels que les noms de paramètres et les noms d’application Windows.  
* **Meilleure gestion et meilleure surveillance** : vous offre plus de contrôle et de visibilité sur qui synchronise les paramètres dans votre organisation et sur quels appareils à travers l’intégration du portail Azure AD. 

Enterprise State Roaming est disponible dans plusieurs régions Azure. Pour consulter la liste à jour des régions disponibles, voir la page [Régions Azure - Services par région](https://azure.microsoft.com/regions/#services) sous Azure Active Directory.

| Article | Description |
| --- | --- |
| [Activer Enterprise State Roaming dans Azure Active Directory](enterprise-state-roaming-enable.md) |Enterprise State Roaming est disponible pour toute organisation ayant souscrit un abonnement premium à Azure Active Directory (Azure AD). Pour plus de détails sur l’obtention d’un abonnement Azure AD, consultez la page [du produit Azure AD](https://azure.microsoft.com/services/active-directory) . |
| [FAQ sur l’itinérance des paramètres et des données](enterprise-state-roaming-faqs.md) |Cette rubrique répond à certaines questions que les administrateurs informatiques peuvent se poser sur les paramètres et la synchronisation des données d’application. |
| [Paramètres de stratégie de groupe et de MDM pour la synchronisation des paramètres](enterprise-state-roaming-group-policy-settings.md) |Windows 10 fournit les paramètres de stratégie de groupe et de gestion d’appareil mobile (MDM) pour limiter la synchronisation des paramètres. |
| [Référence des paramètres d’itinérance Windows 10](enterprise-state-roaming-windows-settings-reference.md) |Voici une liste complète de tous les paramètres destinés à l’itinérance et/ou à la sauvegarde dans Windows 10. |
| [Dépannage](enterprise-state-roaming-troubleshooting.md) |Cette rubrique présente certaines étapes de base pour la résolution des problèmes et contient une liste de problèmes connus. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’activation d’Enterprise State Roaming, consultez [Activer Enterprise State Roaming](enterprise-state-roaming-enable.md).
