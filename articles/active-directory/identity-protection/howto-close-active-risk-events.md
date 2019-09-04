---
title: Fermeture des détections de risques actives dans Azure Active Directory Identity Protection | Microsoft Docs
description: Découvrez les possibilités dont vous disposez pour fermer les détections de risques actives.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: d24ec94d0381fc2e79fdef97b6d525b7cec33fef
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126475"
---
# <a name="how-to-close-active-risk-detections"></a>Procédure : Fermer les détections de risques actives

Grâce aux [détections de risques](../reports-monitoring/concept-risk-events.md), Azure Active Directory détecte les indicateurs de comptes d’utilisateurs potentiellement compromis. En tant qu’administrateur, vous souhaitez fermer toutes les détections de risques afin que les utilisateurs affectés ne soient plus exposés.

Cet article vous présente les méthodes supplémentaires dont vous disposez pour fermer les détections de risques actives.

## <a name="options-to-close-risk-detections"></a>Méthodes de fermeture des détections de risques 

L’état d’une détection de risque est soit **actif**, soit **fermé**. Toutes les détections de risque actifs sont prises en compte dans le calcul d’une valeur appelée niveau de risque utilisateur. Le niveau de risque utilisateur est un indicateur (faible, moyen, élevé) de la probabilité qu’un compte ait été compromis. 

Pour fermer les détections de risques actives, vous disposez des méthodes suivantes :

- Obligation de réinitialisation de mot de passe avec une stratégie d’utilisateur à risque
- Réinitialisation manuelle du mot de passe
- Ignorer toutes les détections de risques 
- Fermeture manuelle de détections de risques spécifiques

## <a name="require-password-reset-with-a-user-risk-policy"></a>Obligation de réinitialisation de mot de passe avec une stratégie d’utilisateur à risque

En configurant la [stratégie d’accès conditionnel d’utilisateur à risque](howto-user-risk-policy.md), vous pouvez exiger une modification du mot de passe si un niveau de risque utilisateur spécifié a été automatiquement détecté. 

![Réinitialiser le mot de passe](./media/howto-close-active-risk-events/13.png)

Une réinitialisation de mot de passe ferme tous les événements à risque actifs de l’utilisateur associé et rétablit la sécurité de l’identité. L’utilisation d’une stratégie d’utilisateur à risque constitue la méthode recommandée pour la fermeture des détections de risques actives, car cette méthode est automatisée. Elle ne nécessite aucune interaction entre l’utilisateur affecté et le support technique ou un administrateur.

Toutefois, l’utilisation d’une stratégie d’utilisateur à risque n’est pas toujours possible. Cela s’applique notamment dans les cas suivants :

- utilisateurs non inscrits pour l’authentification multifacteur (MFA) ;
- utilisateurs avec des détections de risques actives qui ont été supprimées ;
- investigation révélant qu’une détection de risque signalée a été effectuée par l’utilisateur légitime.

## <a name="manual-password-reset"></a>Réinitialisation manuelle du mot de passe

Si l’obligation d’une réinitialisation de mot de passe à l’aide d’une stratégie d’utilisateur à risque est impossible, vous pouvez déclencher la fermeture de toutes les détections de risques par le biais d’une réinitialisation de mot de passe manuelle.

![Réinitialiser le mot de passe](./media/howto-close-active-risk-events/04.png)

La boîte de dialogue connexe fournit deux méthodes différentes pour réinitialiser le mot de passe :

![Réinitialiser le mot de passe](./media/howto-close-active-risk-events/05.png)

**Générer un mot de passe temporaire** : la génération d’un mot de passe temporaire vous permet de rétablir immédiatement la sécurité d’une identité. Cette méthode nécessite une interaction avec les utilisateurs affectés, car ces derniers ont besoin de connaître le mot de passe temporaire. Par exemple, vous pouvez envoyer le nouveau mot de passe temporaire à une autre adresse e-mail de l’utilisateur ou du responsable de l’utilisateur. Étant donné que le mot de passe est temporaire, l’utilisateur est invité à changer le mot de passe lors de sa prochaine connexion.

**Obliger l’utilisateur à réinitialiser le mot de passe** : le fait de contraindre les utilisateurs à réinitialiser les mots de passe entraîne une récupération automatique qui ne nécessite aucun contact avec le support technique ou un administrateur. À l’instar d’une stratégie d’utilisateur à risque, cette méthode s’applique uniquement aux utilisateurs qui sont inscrits pour l’authentification multifacteur. Pour les utilisateurs non encore inscrits pour l’authentification multifacteur, cette option n’est pas disponible.

## <a name="dismiss-all-risk-detections"></a>Ignorer toutes les détections de risques

Si une réinitialisation de mot de passe est impossible, vous pouvez choisir d’ignorer toutes les détections de risques. 

![Réinitialiser le mot de passe](./media/howto-close-active-risk-events/03.png)

Lorsque vous cliquez sur **Ignorer tous les événements**, tous les événements sont fermés et l’utilisateur concerné n’est plus exposé. Toutefois, étant donné que cette méthode n’a pas d’incidence sur le mot de passe existant, elle ne rétablit pas la sécurité de l’identité associée. Le cas d’usage par défaut de cette méthode est un utilisateur supprimé avec des détections de risques actives. 

## <a name="close-individual-risk-detections-manually"></a>Fermeture manuelle de détections de risques spécifiques

Vous pouvez fermer manuellement des détections de risques spécifiques. La fermeture manuelle de détections de risques vous permet de réduire le niveau de risque utilisateur. En règle générale, les détections de risques sont fermées manuellement en réponse à une investigation associée, par exemple, lorsqu’une discussion avec un utilisateur révèle qu’une détection de risque active n’est plus requise. 
 
Lorsque vous fermez des détections de risques manuellement, vous pouvez choisir d’exécuter l’une des actions ci-après pour modifier l’état d’une détection de risque :

![Actions](./media/howto-close-active-risk-events/06.png)

- **Résoudre** : si après avoir examiné une détection de risque, vous avez pris une mesure de correction appropriée en dehors d’Identity Protection et pensez que la détection de risque doit être considérée comme fermée, marquez l’événement comme résolu. Les événements résolus définiront l’état de détection de risque sur Fermé et la détection de risque n’entre plus dans le calcul du risque d’un utilisateur.
- **Marquer comme faux positif** : dans certains cas, il se peut qu’une détection de risque soit signalée à tort en tant que telle. Vous pouvez réduire le nombre de ces détections de risques en les marquant comme faux positifs. Vous aiderez ainsi les algorithmes d’apprentissage automatique à améliorer la classification des événements similaires par la suite. L’état des événements marqués comme faux positifs est défini sur Fermé, et ces événements n’entrent plus dans le calcul du risque utilisateur.
- **Ignorer** : si vous n’avez pris aucune mesure de correction, mais que vous souhaitez que la détection de risque soit supprimée de la liste active, vous pouvez choisir de l’ignorer. Son état sera alors défini sur Fermé. Les événements ignorés n’entrent plus dans le calcul du risque d’un utilisateur. Cette option doit uniquement être utilisée dans des circonstances inhabituelles.
- **Réactiver** : les détections de risques qui ont été fermées manuellement (par le biais de l’action Résoudre, Marquer comme faux positif ou Ignorer) peuvent être réactivées et reprendre ainsi l’état Actif. Les détections de risques réactivées contribuent au calcul du niveau de risque d’un utilisateur. Les détections de risques fermées à l’aide d’une mesure de correction (telle qu’une réinitialisation de mot de passe sécurisée) ne peuvent pas être réactivées.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir une vue d’ensemble du service Azure AD Identity Protection, consultez l’article de [présentation d’Azure AD Identity Protection](overview.md).
