---
title: Notifications d’Azure Active Directory Identity Protection | Microsoft Docs
description: Découvrez comment les notifications prennent en charge vos activités d’examen.
services: active-directory
keywords: azure active directory identity protection, cloud app discovery, gestion d’applications, sécurité, risque, niveau de risque, vulnérabilité, stratégie de sécurité
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 65ca79b9-4da1-4d5b-bebd-eda776cc32c7
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0065ec03695ee977133ae2ec43aafba7d5bfff78
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65784342"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Notifications d’Azure Active Directory Identity Protection

Azure AD Identity Protection envoie deux types d’e-mails de notification automatisés pour vous aider à gérer le risque des utilisateurs et les événements à risque :

- E-mail Utilisateurs à risque détectés
- E-mail de synthèse hebdomadaire

Cet article vous offre une vue d’ensemble des deux e-mails de notification.


## <a name="users-at-risk-detected-email"></a>E-mail Utilisateurs à risque détectés

En cas de détection d’un compte à risque, Azure AD Identity Protection génère une alerte par e-mail dont l’objet est **Utilisateurs à risque détecté**. Cet e-mail inclut un lien vers le rapport **[Utilisateurs associés à un indicateur de risque](../reports-monitoring/concept-user-at-risk.md)** . En guise de bonne pratique, vous devez examiner immédiatement les utilisateurs à risque.

La configuration de cette alerte vous permet de spécifier à quel niveau de risque de l’utilisateur vous souhaitez que l’alerte soit générée. L’e-mail est généré lorsque le niveau de risque de l’utilisateur atteint la valeur que vous avez spécifiée ; toutefois, vous ne recevrez aucune nouvelle alerte Utilisateurs à risque détectés pour cet utilisateur une fois qu’il a atteint ce niveau de risque. Ainsi, si vous définissez une stratégie afin que le système envoie des alertes pour des utilisateurs à risques moyens, et que l’utilisateur John atteint ce niveau de risque, vous recevrez un e-mail Utilisateurs à risque détectés pour John, mais vous ne recevrez pas de deuxième alerte si John atteint le niveau de risque élevé ou est associé à d’autres événements à risque.

![E-mail Utilisateurs à risque détectés](./media/notifications/01.png)


### <a name="configuration"></a>Configuration

En tant qu’administrateur, vous pouvez définir les éléments suivants :

- **Le niveau de risque de l’utilisateur qui déclenche la génération de cet e-mail** : par défaut, le niveau de risque est défini sur « Élevé ».
- **Les destinataires de cet e-mail** : par défaut, les destinataires incluent tous les administrateurs généraux. Les administrateurs généraux peuvent également ajouter d’autres administrateurs généraux, des administrateurs de la sécurité et des lecteurs Sécurité comme destinataires.  


Pour ouvrir la boîte de dialogue connexe, cliquez sur **Alertes** dans la section **Paramètres** de la page **Identity Protection**.

![E-mail Utilisateurs à risque détectés](./media/notifications/05.png)


## <a name="weekly-digest-email"></a>E-mail de synthèse hebdomadaire

L’e-mail de synthèse hebdomadaire contient un récapitulatif des nouveaux événements à risque.  
Il inclut :

- Les utilisateurs à risque

- Activités suspectes

- Les vulnérabilités détectées

- Des liens vers les rapports connexes dans Identity Protection

    ![Correction](./media/notifications/400.png "Correction")

### <a name="configuration"></a>Configuration

En tant qu’administrateur, vous pouvez désactiver l’envoi de l’e-mail de synthèse hebdomadaire.

![Risques des utilisateurs](./media/notifications/62.png "Risques des utilisateurs")

Pour ouvrir la boîte de dialogue connexe, cliquez sur **Synthèse hebdomadaire** dans la section **Paramètres** de la page **Identity Protection**.

![E-mail Utilisateurs à risque détectés](./media/notifications/04.png)


## <a name="see-also"></a>Voir aussi

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
