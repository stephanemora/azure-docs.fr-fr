---
title: Notifications d’Azure Active Directory Identity Protection
description: Découvrez comment les notifications prennent en charge vos activités d’examen.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9090ca5b8057179b0cbef1d0a87ae563303ed2c1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85130430"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Notifications d’Azure Active Directory Identity Protection

Azure AD Identity Protection envoie deux types d’e-mails de notification automatisés pour vous aider à gérer le risque des utilisateurs et les détections de risques :

- E-mail Utilisateurs à risque détectés
- E-mail de synthèse hebdomadaire

Cet article vous offre une vue d’ensemble des deux e-mails de notification.

## <a name="users-at-risk-detected-email"></a>E-mail Utilisateurs à risque détectés

En cas de détection d’un compte à risque, Azure AD Identity Protection génère une alerte par e-mail dont l’objet est **Utilisateurs à risque détecté**. Cet e-mail inclut un lien vers le rapport **[Utilisateurs associés à un indicateur de risque](../reports-monitoring/concept-user-at-risk.md)** . En guise de bonne pratique, vous devez examiner immédiatement les utilisateurs à risque.

La configuration de cette alerte vous permet de spécifier à quel niveau de risque de l’utilisateur vous souhaitez que l’alerte soit générée. L’e-mail est généré lorsque le niveau de risque de l’utilisateur atteint le seuil que vous avez spécifié. Ainsi, si vous définissez une stratégie afin que le système envoie des alertes en cas de risque moyen pour l’utilisateur et si le score de risque de l’utilisateur de John atteint ce niveau de risque en raison d’une connexion en temps réel, vous recevrez un e-mail Utilisateurs à risque détectés. Si l’utilisateur fait l’objet de détections de risque ultérieures qui font que le calcul du niveau de risque de l’utilisateur correspond (ou est supérieur) au niveau de risque spécifié, vous recevrez d’autres e-mails Utilisateurs à risque détectés lorsque le score de risque de l’utilisateur sera recalculé. Par exemple, si un utilisateur passe à un risque moyen le 1er janvier, vous recevrez une notification par e-mail si vos paramètres sont réglés de manière à vous alerter en cas de risque moyen. Si ce même utilisateur fait ensuite l’objet d’une autre détection de risque le 5 janvier qui est également d’un risque moyen et que le score de risque de l’utilisateur est recalculé et reste moyen, vous recevrez une autre notification par e-mail. 

Toutefois, une nouvelle notification par e-mail est envoyée uniquement si le moment où la détection du risque a eu lieu (à l’origine de la modification du niveau de risque pour l’utilisateur) est plus récent que celui où le dernier e-mail a été envoyé. Par exemple, un utilisateur se connecte le 1er janvier à 5 h et il n’y a aucun risque en temps réel (c’est-à-dire qu’aucun e-mail n’est généré en raison de cette connexion). Dix minutes plus tard, à 5 h 10, le même utilisateur se connecte à nouveau et présente alors un risque en temps réel élevé, ce qui amène le niveau de risque de l’utilisateur à passer à un niveau élevé et entraîne l’envoi d’un e-mail. Puis, à 5 h 15, le score de risque hors connexion pour la connexion d’origine à 5 h passe à un risque élevé en raison du traitement des risques hors connexion. Un autre e-mail Utilisateur signalé à risque n’est pas envoyé, car l’heure de la première connexion est antérieure à la deuxième connexion à l’origine de la notification par e-mail.

Pour empêcher une surcharge d’e-mails, vous ne recevrez qu’un seul e-mail Utilisateurs à risque détectés dans un délai de cinq secondes. Cela signifie que si plusieurs utilisateurs passent au niveau de risque spécifié au cours de la même période de cinq secondes, nous regrouperons les messages et enverrons un seul e-mail pour représenter la modification du niveau de risque pour tous.

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

- Nouveaux utilisateurs à risque détectés
- Nouvelles connexions à risque détectées (en temps réel)
- Des liens vers les rapports connexes dans Identity Protection

![E-mail de synthèse hebdomadaire](./media/howto-identity-protection-configure-notifications/weekly-digest-email.png)

Par défaut, les destinataires incluent tous les administrateurs généraux. Les administrateurs généraux peuvent également ajouter d’autres administrateurs généraux, des administrateurs de la sécurité et des lecteurs Sécurité comme destinataires.

### <a name="configure-weekly-digest-email"></a>Configurer un courrier de synthèse hebdomadaire

En tant qu’administrateur, vous pouvez activer ou désactiver l’envoi d’un courrier de synthèse hebdomadaire et choisir les utilisateurs affectés à la réception de celui-ci.

Configurez le courrier de synthèse hebdomadaire dans le **portail Azure** sous **Azure Active Directory** > **Sécurité** > **Identity Protection** > **Synthèse hebdomadaire**.

## <a name="see-also"></a>Voir aussi

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
