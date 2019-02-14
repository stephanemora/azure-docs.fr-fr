---
title: Fermeture des événements à risque actifs dans Azure Active Directory Identity Protection | Microsoft Docs
description: Découvrez les possibilités dont vous disposez pour fermer les événements à risque actifs.
services: active-directory
keywords: azure active directory identity protection, cloud app discovery, gestion d’applications, sécurité, risque, niveau de risque, vulnérabilité, stratégie de sécurité
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/24/2018
ms.author: markvi
ms.reviewer: raluthra
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50448cc5d4fe6714aa0cd29216209506eccf7a7c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56201616"
---
# <a name="how-to-close-active-risk-events"></a>Procédure : Fermer les événements à risque actifs

Grâce aux [événements à risque](../reports-monitoring/concept-risk-events.md), Azure Active Directory détecte les indicateurs de comptes d’utilisateurs potentiellement compromis. En tant qu’administrateur, vous souhaitez fermer tous les événements à risque afin que les utilisateurs affectés ne soient plus exposés.

Cet article vous présente les méthodes supplémentaires dont vous disposez pour fermer les événements à risque actifs.

## <a name="options-to-close-risk-events"></a>Méthodes de fermeture des événements à risque 

L’état d’un événement à risque est soit **actif**, soit **fermé**. Tous les événements à risque actifs sont pris en compte dans le calcul d’une valeur appelée niveau de risque utilisateur. Le niveau de risque utilisateur est un indicateur (faible, moyen, élevé) de la probabilité qu’un compte ait été compromis. 

Pour fermer les événements à risque actifs, vous disposez des méthodes suivantes :

- Obligation de réinitialisation de mot de passe avec une stratégie d’utilisateur à risque

- Réinitialisation manuelle du mot de passe
 
- Ignorance de tous les événements à risque 

- Fermeture manuelle d’événements à risque spécifiques



## <a name="require-password-reset-with-a-user-risk-policy"></a>Obligation de réinitialisation de mot de passe avec une stratégie d’utilisateur à risque

En configurant la [stratégie d’accès conditionnel d’utilisateur à risque](howto-user-risk-policy.md), vous pouvez exiger une modification du mot de passe si un niveau de risque utilisateur spécifié a été automatiquement détecté. 

![Réinitialiser le mot de passe](./media/howto-close-active-risk-events/13.png)

Une réinitialisation de mot de passe ferme tous les événements à risque actifs de l’utilisateur associé et rétablit la sécurité de l’identité. L’utilisation d’une stratégie d’utilisateur à risque constitue la méthode recommandée pour la fermeture des événements à risque actifs, car cette méthode est automatisée. Elle ne nécessite aucune interaction entre l’utilisateur affecté et le support technique ou un administrateur.

Toutefois, l’utilisation d’une stratégie d’utilisateur à risque n’est pas toujours possible. Cela s’applique notamment dans les cas suivants :

- utilisateurs non inscrits pour l’authentification multifacteur (MFA) ;
- utilisateurs avec des événements à risque actifs qui ont été supprimés ;
- investigation révélant qu’un événement à risque signalé a été effectué par l’utilisateur légitime.


## <a name="manual-password-reset"></a>Réinitialisation manuelle du mot de passe

Si l’obligation d’une réinitialisation de mot de passe à l’aide d’une stratégie d’utilisateur à risque est impossible, vous pouvez déclencher la fermeture de tous les événements à risque par le biais d’une réinitialisation de mot de passe manuelle.

![Réinitialiser le mot de passe](./media/howto-close-active-risk-events/04.png)


La boîte de dialogue connexe fournit deux méthodes différentes pour réinitialiser le mot de passe :

![Réinitialiser le mot de passe](./media/howto-close-active-risk-events/05.png)


**Générer un mot de passe temporaire** : la génération d’un mot de passe temporaire vous permet de rétablir immédiatement la sécurité d’une identité. Cette méthode nécessite une interaction avec les utilisateurs affectés, car ces derniers ont besoin de connaître le mot de passe temporaire. Par exemple, vous pouvez envoyer le nouveau mot de passe temporaire à une autre adresse e-mail de l’utilisateur ou du responsable de l’utilisateur. Étant donné que le mot de passe est temporaire, l’utilisateur est invité à changer le mot de passe lors de sa prochaine connexion.


**Obliger l’utilisateur à réinitialiser le mot de passe** : le fait de contraindre les utilisateurs à réinitialiser les mots de passe entraîne une récupération automatique qui ne nécessite aucun contact avec le support technique ou un administrateur. À l’instar d’une stratégie d’utilisateur à risque, cette méthode s’applique uniquement aux utilisateurs qui sont inscrits pour l’authentification multifacteur. Pour les utilisateurs non encore inscrits pour l’authentification multifacteur, cette option n’est pas disponible.


## <a name="dismiss-all-risk-events"></a>Ignorance de tous les événements à risque

Si une réinitialisation de mot de passe est impossible, vous pouvez choisir d’ignorer tous les événements à risque. 

![Réinitialiser le mot de passe](./media/howto-close-active-risk-events/03.png)

Lorsque vous cliquez sur **Ignorer tous les événements**, tous les événements sont fermés et l’utilisateur concerné n’est plus exposé. Toutefois, étant donné que cette méthode n’a pas d’incidence sur le mot de passe existant, elle ne rétablit pas la sécurité de l’identité associée. Le cas d’usage par défaut de cette méthode est un utilisateur supprimé avec des événements à risque actifs. 


## <a name="close-individual-risk-events-manually"></a>Fermeture manuelle d’événements à risque spécifiques

Vous pouvez fermer manuellement des événements à risque individuels. La fermeture manuelle d’événements à risque vous permet de réduire le niveau de risque utilisateur. En règle générale, les événements à risque sont fermés manuellement en réponse à une investigation associée, par exemple, lorsqu’une discussion avec un utilisateur révèle qu’un événement à risque actif n’est plus requis. 
 
Lorsque vous fermez des événements à risque manuellement, vous pouvez choisir d’exécuter l’une des actions ci-après pour modifier l’état d’un événement à risque :

![Actions](./media/howto-close-active-risk-events/06.png)

- **Résoudre** : si après avoir examiné un événement à risque, vous avez pris une mesure de correction appropriée en dehors d’Identity Protection et pensez que l’événement à risque doit être considéré comme fermé, marquez l’événement comme résolu. L’état des événements à risque résolus est défini sur Fermé et ces événements n’entrent plus dans le calcul du risque d’un utilisateur.

- **Marquer comme faux positif** : dans certains cas, il se peut qu’un événement à risque soit signalé à tort en tant que tel. Vous pouvez réduire le nombre de ces événements en les marquant comme faux positifs. Vous aiderez ainsi les algorithmes d’apprentissage automatique à améliorer la classification des événements similaires par la suite. L’état des événements marqués comme faux positifs est défini sur Fermé, et ces événements n’entrent plus dans le calcul du risque utilisateur.

- **Ignorer** : si vous n’avez pris aucune mesure de correction, mais que vous souhaitez que l’événement à risque soit supprimé de la liste active, vous pouvez choisir de l’ignorer. Son état sera alors défini sur Fermé. Les événements ignorés n’entrent plus dans le calcul du risque d’un utilisateur. Cette option doit uniquement être utilisée dans des circonstances inhabituelles.

- **Réactiver** : les événements à risque qui ont été fermés manuellement (par le biais de l’action Résoudre, Marquer comme faux positif ou Ignorer) peuvent être réactivés et reprendre ainsi l’état Actif. Les événements à risque réactivés contribuent au calcul du niveau de risque d’un utilisateur. Les événements à risque fermés à l’aide d’une mesure de correction (telle qu’une réinitialisation de mot de passe sécurisée) ne peuvent pas être réactivés.
  

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir une vue d’ensemble du service Azure AD Identity Protection, consultez l’article de [présentation d’Azure AD Identity Protection](overview.md).
