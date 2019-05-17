---
title: Fournir des commentaires sur les événements à risque dans Azure AD Identity Protection - Azure Active Directory
description: Comment et pourquoi devez vous fournir des commentaires sur les événements à risque Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66d53590e89afb1a903b22ff60e32871a1502ada
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827904"
---
# <a name="how-to-give-risk-feedback-in-azure-ad-identity-protection"></a>Procédure : Envoyer des commentaires risque dans Azure AD Identity Protection

Azure AD Identity Protection vous permet d’envoyer des commentaires sur son évaluation des risques. Le document suivant répertorie les scénarios où vous souhaitez envoyer vos commentaires sur l’évaluation des risques du service Azure AD Identity Protection et comment nous incorporons il.

## <a name="what-is-a-detection"></a>Qu’est une détection ?

Une détection de la Protection d’identité est un indicateur d’activité suspecte à partir d’un point de vue du risque identity. Ces activités suspectes sont appelées événements à risque. Ces détections basée sur l’identité peuvent être basées sur l’heuristique, l’apprentissage automatique ou peuvent provenir de produits de partenaires. Ces détections sont utilisées pour déterminer le risque de connexion et les risques de l’utilisateur,

* Risque de l’utilisateur représente la probabilité de qu'une identité est compromise.
* Risque de connexion représente la probabilité d’une connexion à est compromise (par exemple, l’authentification dans n’est pas autorisée par le propriétaire de l’identité).

## <a name="why-should-i-give-risk-feedback-to-azure-ads-risk-assessments"></a>Pourquoi dois-je fournir des commentaires à risque pour l’évaluation des risques d’Azure AD ? 

Il existe plusieurs raisons peuvent expliquer pourquoi vous devez donner les commentaires de risque d’Azure AD :

1. **Vous avez trouvé évaluation des risques utilisateur ou de connexion d’Azure AD incorrect**. Par exemple, une connexion indiqué dans les rapports 'Connexions risquées' a sans gravité et toutes les détections lors de cette connexion ont été faux positifs.
1. **Vous validé les utilisateur d’Azure AD ou évaluation du risque de connexion était correcte**. Par exemple, une connexion indiqué dans les rapports 'Connexions risquées' a en effet nuisible et vous souhaitez savoir que toutes les détections lors de cette connexion étaient vrais positifs de Azure AD.
1. **Vous mis à jour le risque sur cet utilisateur en dehors d’Azure AD Identity Protection** et que vous souhaitez le niveau de risque de l’utilisateur à mettre à jour.

## <a name="how-does-azure-ad-use-my-risk-feedback"></a>Comment Azure AD n’utilise pas mes commentaires risque ?

Azure AD utilise vos commentaires pour mettre à jour le risque de l’utilisateur et/ou de connexion sous-jacent. Ces commentaires vous aide à sécuriser l’utilisateur final. Par exemple, après avoir confirmé la que connexion est compromis, Azure AD immédiatement augmente les risques de l’utilisateur et d’agrégation risque de connexion (risque de pas en temps réel) à haute. Si cet utilisateur est inclu dans votre stratégie de risque utilisateur pour forcer les utilisateurs à risque élevé en toute sécurité réinitialiser leurs mots de passe, l’utilisateur sera automatiquement corrige de lui-même la prochaine fois qu’ils connectez-vous.

## <a name="how-should-i-give-risk-feedback-and-what-happens-under-the-hood"></a>Comment dois-je donner des risques des commentaires et ce qui se passe sous le capot ?

Voici les scénarios et les mécanismes de commentaires de risque à Azure AD.

| Scénario | Comment envoyer des commentaires ? | Que se passe-t-il sous le capot ? | Notes |
| --- | --- | --- | --- |
| **Connectez-vous ne pas compromis (faux positif)** <br> Les rapports « Connexions risquées » montre une connexion à risque sign-in [état de risque = menacée] mais que la connexion n’est pas compromise. | Sélectionnez la connexion, puis cliquez sur « Confirmer connectez-vous safe ». | Azure AD déplacera le d’agrégation risque de connexion sur none [état de risque = Confirmed sécurisé ; (Agrégat) de niveau de risque =-] et permet d’inverser son impact sur le risque de l’utilisateur. | Actuellement, l’option « Confirmer connectez-vous safe » est uniquement disponible dans les rapports « Connexions à risque ». |
| **Connectez-vous compromis (vrai positif sans gravité)** <br> Les rapports « Connexions risquées » montre une connexion à risque sign-in [risque d’état = menacée] avec un risque faible [(agrégat) de niveau de risque = faible] et cette connexion dans a été effectivement compromis. | Sélectionnez la connexion et cliquez sur « Confirmer connectez-vous compromis ». | Azure AD à haute déplacera le d’agrégation risque de connexion et le risque de l’utilisateur [état de risque = Confirmed compromise ; Niveau de risque = High]. | Actuellement, l’option 'Confirmer connectez-vous compromis' est uniquement disponible dans les rapports « Connexions à risque ». |
| **Utilisateur compromis (vrai positif sans gravité)** <br> Rapport des « Utilisateurs à risque » montre un utilisateur à risque [risque d’état = menacée] avec un risque faible [niveau de risque = faible] et que l’utilisateur a été effectivement compromis. | Sélectionnez l’utilisateur, puis cliquez sur « Confirmer utilisateur compromis ». | Azure AD déplacera le risque d’utilisateur élevé [état de risque = Confirmed compromise ; Niveau de risque = High] et ajoute une nouvelle détection « Administrateur confirmé utilisateur compromis ». | Actuellement, l’option « User confirmer compromis » est uniquement disponible dans les rapports « Utilisateurs à risque ». <br> La détection « Administrateur confirmé utilisateur compromis » est indiqué dans l’onglet « Événements à risque ne pas liés à une connexion à » dans le rapport « Utilisateurs à risque ». |
| **Utilisateur mis à jour en dehors d’Azure AD Identity Protection (vrai positif + Remediated)** <br> Rapport de « Utilisateurs à risque » indique un utilisateur à risque, et j’ai corrigé par la suite de l’utilisateur en dehors d’Azure AD Identity Protection. | 1. Sélectionnez l’utilisateur et cliquez sur « Confirmer utilisateur compromis ». (Ce processus confirme à Azure AD que l’utilisateur a été effectivement compromis.) <br> 2. Attendez que « niveau de risque » l’utilisateur pour accéder à haute. (Cette laisse Azure AD le temps nécessaire pour suivre les commentaires ci-dessus pour le moteur de risque.) <br> 3. Sélectionnez l’utilisateur, puis cliquez sur « Ignorer le risque de l’utilisateur ». (Ce processus confirme à Azure AD que l’utilisateur est compromis n’est plus.) |  Azure AD déplace le risque de l’utilisateur à aucun [état de risque = ignoré ; Niveau de risque =-] et ferme le risque sur toutes les existants connexions présentant un risque actif. | En cliquant sur le risque de l’utilisateur Dismiss fermera tous les risques liés sur l’utilisateur au-delà de connexions. Cette action ne peut pas être annulée. |
| **Utilisateur ne pas compromis (faux positif)** <br> Rapport de « Utilisateurs à risque » indique à l’utilisateur à risque, mais l’utilisateur n’est pas compromise. | Sélectionnez l’utilisateur, puis cliquez sur « Ignorer le risque de l’utilisateur ». (Ce processus confirme à Azure AD que l’utilisateur n’est pas compromise.) | Azure AD déplace le risque de l’utilisateur à aucun [état de risque = ignoré ; Niveau de risque =-]. | En cliquant sur le risque de l’utilisateur Dismiss fermera tous les risques liés sur l’utilisateur au-delà de connexions. Cette action ne peut pas être annulée. |
| Fermer le risque de l’utilisateur, mais je ne sais pas si l’utilisateur est compromis / sécurisé. | Sélectionnez l’utilisateur, puis cliquez sur « Ignorer le risque de l’utilisateur ». (Ce processus confirme à Azure AD que l’utilisateur est compromis n’est plus.) | Azure AD déplace le risque de l’utilisateur à aucun [état de risque = ignoré ; Niveau de risque =-]. | En cliquant sur le risque de l’utilisateur Dismiss fermera tous les risques liés sur l’utilisateur au-delà de connexions. Cette action ne peut pas être annulée. Nous vous recommandons corriger l’utilisateur en cliquant sur « Réinitialiser le mot de passe » ou demandez à l’utilisateur de réinitialiser/modifier en toute sécurité leurs informations d’identification. |

Commentaires sur les événements à risque dans Identity Protection sont traité en mode hors connexion et peuvent prendre un certain temps pour mettre à jour. Le risque de traitement de la colonne d’état fournit l’état actuel du traitement de vos commentaires.

![État de traitement de rapport de l’utilisateur à risque à risque](./media/howto-provide-risk-event-feedback/risky-users-provide-feedback.png)

## <a name="next-steps"></a>Étapes suivantes

[Référencent des événements à risque Azure Active Directory Identity Protection](risk-events-reference.md)