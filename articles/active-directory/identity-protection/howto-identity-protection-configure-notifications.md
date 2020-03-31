---
title: Notifications d’Azure Active Directory Identity Protection
description: Découvrez comment les notifications prennent en charge vos activités d’examen.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c83aa6e476bbd898999fb6efe490c7847a809ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120126"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Notifications d’Azure Active Directory Identity Protection

Azure AD Identity Protection envoie deux types d’e-mails de notification automatisés pour vous aider à gérer le risque des utilisateurs et les détections de risques :

- E-mail Utilisateurs à risque détectés
- E-mail de synthèse hebdomadaire

Cet article vous offre une vue d’ensemble des deux e-mails de notification.

## <a name="users-at-risk-detected-email"></a>E-mail Utilisateurs à risque détectés

En cas de détection d’un compte à risque, Azure AD Identity Protection génère une alerte par e-mail dont l’objet est **Utilisateurs à risque détecté**. Cet e-mail inclut un lien vers le rapport **[Utilisateurs associés à un indicateur de risque](../reports-monitoring/concept-user-at-risk.md)** . En guise de bonne pratique, vous devez examiner immédiatement les utilisateurs à risque.

La configuration de cette alerte vous permet de spécifier à quel niveau de risque de l’utilisateur vous souhaitez que l’alerte soit générée. L’e-mail est généré lorsque le niveau de risque de l’utilisateur atteint la valeur que vous avez spécifiée ; toutefois, vous ne recevrez aucune nouvelle alerte Utilisateurs à risque détectés pour cet utilisateur une fois qu’il a atteint ce niveau de risque. Ainsi, si vous définissez une stratégie afin que le système envoie des alertes pour des utilisateurs à risques moyens, et que l’utilisateur John atteint ce niveau de risque, vous recevrez un e-mail Utilisateurs à risque détectés pour John, mais vous ne recevrez pas de deuxième alerte d’utilisateur à risque détecté si John atteint un niveau de risque élevé ou est associé à d’autres détections de risques.

![E-mail Utilisateurs à risque détectés](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>Configurer les alertes Utilisateurs à risque détectés

En tant qu’administrateur, vous pouvez définir les éléments suivants :

- **Le niveau de risque de l’utilisateur qui déclenche la génération de cet e-mail** : par défaut, le niveau de risque est défini sur « Élevé ».
- **Les destinataires de cet e-mail** : par défaut, les destinataires incluent tous les administrateurs généraux. Les administrateurs généraux peuvent également ajouter d’autres administrateurs généraux, des administrateurs de la sécurité et des lecteurs Sécurité comme destinataires.
   - Si vous le souhaitez, vous pouvez **ajouter des e-mails supplémentaires pour recevoir des notifications d’alerte**. Cette fonctionnalité est en préversion et les utilisateurs définis doivent disposer des autorisations appropriées pour afficher les rapports liés sur le portail Azure.

Configurez l’e-mail Utilisateurs à risque sur le **Portail Azure** sous **Azure Active Directory** > **Sécurité** > **Identity Protection** > **Alertes Utilisateurs à risque détectés**.

## <a name="weekly-digest-email"></a>E-mail de synthèse hebdomadaire

L’e-mail de synthèse hebdomadaire contient un récapitulatif des nouvelles détections de risques.  
Il inclut :

- Les utilisateurs à risque
- Activités suspectes
- Les vulnérabilités détectées
- Des liens vers les rapports connexes dans Identity Protection

![E-mail de synthèse hebdomadaire](./media/howto-identity-protection-configure-notifications/400.png)

Par défaut, les destinataires incluent tous les administrateurs généraux. Les administrateurs généraux peuvent également ajouter d’autres administrateurs généraux, des administrateurs de la sécurité et des lecteurs Sécurité comme destinataires.

### <a name="configure-weekly-digest-email"></a>Configurer un courrier de synthèse hebdomadaire

En tant qu’administrateur, vous pouvez activer ou désactiver l’envoi d’un courrier de synthèse hebdomadaire et choisir les utilisateurs affectés à la réception de celui-ci.

Configurez le courrier de synthèse hebdomadaire dans le **portail Azure** sous **Azure Active Directory** > **Sécurité** > **Identity Protection** > **Synthèse hebdomadaire**.

## <a name="see-also"></a>Voir aussi

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
