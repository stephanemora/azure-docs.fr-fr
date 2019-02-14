---
title: Améliorer la précision de la détection dans Azure Active Directory Identity Protection (version actualisée) | Microsoft Docs
description: Améliorer la précision de la détection dans Azure Active Directory Identity Protection (version actualisée).
services: active-directory
keywords: azure active directory identity protection, cloud app discovery, gestion d’applications, sécurité, risque, niveau de risque, vulnérabilité, stratégie de sécurité
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.author: markvi
ms.reviewer: raluthra
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1385442ace852c310ca6796da9294ca57c797690
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56211034"
---
# <a name="how-to-improve-the-detection-accuracy"></a>Procédure : Améliorer la précision de la détection 

Identity Protection fournit des mécanismes d’envoi de commentaires à Azure AD sur les détections de risques dans votre environnement. Pour fournir des commentaires, vous pouvez vérifier l’état de l’utilisateur à risque détecté ou de l’événement de connexion. Microsoft utilise ces commentaires pour agir sur les détections de risques actuelles et améliorer la précision des détections futures. 


## <a name="what-is-detection"></a>Qu’est-ce que la détection ?

La détection est le processus d’identification des activités suspectes liées à vos comptes d’utilisateur. Les activités suspectes qu’Azure AD est en mesure de détecter sont appelées un [événement à risque](../reports-monitoring/concept-risk-events.md). Le processus de détection est basé sur des algorithmes de Machine Learning automatiques et une méthode heuristique pour détecter les événements à risque pour les utilisateurs.

Les résultats de la détection sont utilisés pour déterminer si des utilisateurs et des connexions sont à risque. 


## <a name="how-can-i-improve-the-detection-accuracy"></a>Comment puis-je améliorer la précision de la détection ?

Étant donné que la détection est un processus automatisé, il est possible qu’Azure AD signale des faux positifs. Vous pouvez améliorer la précision de la détection en fournissant des commentaires à Azure AD sur les résultats de la détection.

Trois méthodes permettent d’améliorer la précision de la détection : confirmer une connexion compromise, confirmer une connexion sécurisée et ignorer le risque lié à l’utilisateur. Vous pouvez le faire à partir des rapports suivants :

- **Rapports sur les connexions à risque** : dans le rapport sur les connexions à risque, vous pouvez vérifier si les connexions sont sécurisées ou compromises

- **Rapport sur les utilisateurs à risque** : dans le rapport sur les utilisateurs à risque, vous pouvez ignorer le risque lié à l’utilisateur 

Vos commentaires sont traités par Azure AD afin d’améliorer la précision des résultats de la détection. En règle générale, vous fournissez des commentaires dans le cadre de l’examen d’un risque de connexion ou de l’utilisateur. Pour plus d’informations, consultez [Examiner les utilisateurs et les connexions à risque](howto-investigate-risky-users-signins.md).


## <a name="confirm-compromised"></a>Confirmer comme étant compromise

En confirmant un événement de connexion comme étant compromis, vous signalez à Azure AD que la connexion n’a pas été autorisée par le propriétaire de l’identité. Lorsque vous sélectionnez « Confirmer comme étant compromise », Azure AD :

- Augmente le risque de l’utilisateur affecté au niveau élevé.

- Aide à optimiser le Machine Learning qui détecte les événements à risque
 
- Prend des mesures supplémentaires pour mieux protéger votre organisation



Pour confirmer une connexion compromise :

- **Le rapport sur les connexions à risque** : cette option vous permet de confirmer une connexion compromise pour un ou plusieurs événements de connexion.

    ![Ignorer le risque lié à l’utilisateur](./media/howto-improve-detection-accuracy/07.png)

- **La vue Détails du rapport sur les connexions à risque** : cette option vous permet de confirmer un compte compromis pour l’événement de connexion sélectionné dans le rapport de connexions à risque. 

    ![Ignorer le risque lié à l’utilisateur](./media/howto-improve-detection-accuracy/04.png)


 
## <a name="confirm-safe"></a>Confirmer comme étant sécurisée


En confirmant un événement de connexion comme étant sécurisé, vous signalez à Azure AD que la connexion **a été** autorisée par le propriétaire de l’identité concerné. Lorsque vous sélectionnez « Confirmer comme étant sécurisée », Azure AD :

- Rétablit la contribution de risque utilisateur des connexions sélectionnées

- Ferme les événements à risque sous-jacents

- Aide à optimiser le Machine Learning qui détecte les événements à risque

- Prend des mesures supplémentaires pour mieux protéger votre organisation
 

Pour confirmer une connexion sécurisée dans :

- **Le rapport sur les connexions à risque** : cette option vous permet de confirmer une connexion sécurisée pour un ou plusieurs événements de connexion.

    ![Ignorer le risque lié à l’utilisateur](./media/howto-improve-detection-accuracy/08.png)

- **La vue Détails du rapport sur les connexions à risque** : cette option vous permet de confirmer une connexion sécurisée pour l’événement de connexion sélectionné dans le rapport de connexions à risque. 

    ![Ignorer le risque lié à l’utilisateur](./media/howto-improve-detection-accuracy/05.png)




## <a name="dismiss-user-risk"></a>Ignorer le risque lié à l’utilisateur

Si vous avez déjà pris des mesures de correction pour un risque utilisateur, ou si vous pensez qu’il s’agit de faux positifs, vous pouvez ignorer un risque de l’utilisateur. Ignorez un risque de l’utilisateur pour rétablir l’utilisateur à un état non à risque. Toutes les connexions à risque et tous les événements à risque antérieurs pour l’utilisateur sélectionné seront ignorés.


Vous pouvez ignorer le risque de l’utilisateur signalé dans :

- **Le rapport sur les utilisateurs à risque** : cette option vous permet d’ignorer le risque utilisateur pour un ou plusieurs utilisateurs sélectionnés.

    ![Ignorer le risque lié à l’utilisateur](./media/howto-improve-detection-accuracy/02.png)

- **La vue Détails** : cette option vous permet d’ignorer le risque utilisateur pour l’utilisateur sélectionné dans le rapport sur les utilisateurs à risque. 

    ![Ignorer le risque lié à l’utilisateur](./media/howto-improve-detection-accuracy/01.png)


**Bon à savoir :**

- Vous ne pouvez pas annuler cette action.

- L’exécution de cette action peut prendre quelques minutes, c’est pourquoi vous ne devez pas renvoyer votre requête.

- Vous ne pouvez effectuer cette action que si Active Directory gère les informations d’identification de l’utilisateur. 



## <a name="best-practices"></a>Bonnes pratiques

Ignorer un risque de l’utilisateur est une méthode permettant de le débloquer s’il a été bloqué par la stratégie de risque de l’utilisateur et qu’il ne peut pas se corriger de lui-même du fait que la réinitialisation de mot de passe et/ou la MFA n’est pas activée. Dans ce cas, il est préférable de s’assurer que l’utilisateur s’inscrive à la réinitialisation de mot de passe et à la MFA pour que les événements à risque futurs puissent être automatiquement corrigés.


## <a name="next-steps"></a>Étapes suivantes

Pour obtenir une vue d’ensemble du service Azure AD Identity Protection, consultez l’article de [présentation d’Azure AD Identity Protection](overview-v2.md).


