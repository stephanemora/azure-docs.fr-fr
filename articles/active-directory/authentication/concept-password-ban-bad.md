---
title: Mots de passe interdits dynamiquement dans Azure AD
description: Interdire les mots de passe faibles dans votre environnement via des mots de passe interdits dynamiquement dans Azure AD
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: rogoya
ms.openlocfilehash: dfeacb266d6aa6a43e49a39bd19c9699ef65ce82
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/19/2018
ms.locfileid: "39162012"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>Éliminer les mots de passe incorrects de votre organisation

|     |
| --- |
| La protection de mot de passe Azure AD et la liste de mots de passe interdits personnalisée sont des fonctionnalités en préversion publique d’Azure Active Directory. Pour plus d’informations sur les préversions, consultez [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) (Conditions d’utilisation supplémentaires des préversions Microsoft Azure).|
|     |

Les leaders du secteur vous recommandent de ne pas utiliser le même mot de passe à plusieurs endroits, de le rendre complexe et de ne pas utiliser de mots de passe simples, tels que 123. Comment les organisations s’assurent que leurs utilisateurs suivent cette recommandation ? Comment peuvent-ils s’assurer que les utilisateurs n’utilisent pas de mots de passe courants ou de mots de passe connus dans des cas de violation de données ?

## <a name="global-banned-password-list"></a>Liste globale de mots de passe interdits

Microsoft cherche toujours à garder une longueur d’avance sur les cybercriminels. Par conséquent, l’équipe Azure AD Identity Protection recherche en permanence les mots de passe couramment utilisés et compromis. Puis, ils bloquent ces mots de passe qui sont considérés comme étant trop courants dans ce que l’on appelle la liste globale de mots de passe interdits. Les cybercriminels utilisent également des stratégies semblables dans leurs attaques, par conséquent, Microsoft ne rend pas public le contenu de cette liste. Ces mots de passe vulnérables sont bloqués avant de devenir une véritable menace pour les clients de Microsoft. Pour plus d’informations sur les travaux en matière de sécurité actuels, consultez le [Rapport de renseignement sur la sécurité (SIR) de Microsoft](https://www.microsoft.com/security/intelligence-report).

## <a name="preview-custom-banned-password-list"></a>Aperçu : liste personnalisée de mots de passe interdits

Il se peut que certaines organisations veulent pousser plus loin la sécurité en ajoutant leurs propres personnalisations à la liste de mots de passe interdits dans ce que Microsoft appelle la liste de mots de passe interdits personnalisée. Les clients d’entreprise, comme Contoso peuvent ensuite choisir de bloquer des variantes de leurs noms de marque, des conditions spécifiques de la société ou d’autres éléments.

La liste de mots de passe interdits personnalisée et la capacité à activer l’intégration d’Active Directory localement sont gérées via le portail Azure.

![Modifier la liste de mots de passe interdits personnalisée sous Méthodes d’authentification dans le portail Azure](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="on-premises-hybrid-scenarios"></a>Scénarios hybrides locaux

La protection des comptes cloud uniquement est utile, toutefois de nombreuses organisations conservent des scénarios hybrides incluant Windows Server Active Directory en local. Il est possible d’installer la protection de mot de passe Azure AD pour les agents Windows Server Active Directory (préversion) en local pour étendre les listes de mots de passe interdits à votre infrastructure existante. Maintenant, les utilisateurs et les administrateurs qui modifient, définissent ou réinitialisent des mots de passe localement doivent se conformer à la même stratégie de mot de passe que les utilisateurs cloud uniquement.

## <a name="how-does-the-banned-password-list-work"></a>Fonctionnement de la liste de mots de passe interdits

Une correspondance des mots de passe avec ceux de la liste des mots de passe interdits est établie par conversion en minuscules de la chaîne et comparaison de celle-ci avec les mots de passe interdits connus au sein d’une instance de modification de 1 avec correspondance approximative.

Exemple : le mot de passe est bloqué pour une organisation
   - Un utilisateur tente de définir son mot de passe sur P@ssword. Celui-ci est converti en « mot de passe » et est bloqué dans la mesure où il s’agit d’une variante du mot de passe bloqué.
   - Un administrateur tente de définir un mot de passe utilisateur sur « Password123! » Celui-ci est converti en « password123! » Celui-ci est bloqué, dans la mesure où il s’agit d’une variante du mot de passe.

Chaque fois qu’un utilisateur réinitialise ou modifie son mot de passe Azure AD, celui-ci transite par ce processus pour confirmer qu’il ne figure pas dans la liste de mots de passe interdits. Cette vérification est incluse dans les scénarios hybrides utilisant la réinitialisation de mot de passe en libre-service, la synchronisation du hachage de mot de passe et l’authentification directe.

## <a name="license-requirements"></a>Conditions de licence :

Les avantages de la liste globale de mots de passe interdits s’appliquent à tous les utilisateurs d’Azure Active Directory (Azure AD).

La liste personnalisée de mots de passe interdits requiert des licences Azure AD Basic.

La protection de mot de passe Azure AD pour Windows Server Active Directory requiert des licences Azure AD Premium. 

Vous trouverez des informations de licence supplémentaires, notamment les prix, sur le [site de tarification Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-users-see"></a>Ce que voient les utilisateurs

Lorsqu’un utilisateur tente de réinitialiser un mot de passe pour le remplacer par un mot de passe interdit, le message d’erreur suivant s’affiche :

Malheureusement, votre mot de passe contient un mot, une expression ou un modèle qui rend votre mot de passe facile à deviner. Réessayez avec un autre mot de passe.

## <a name="next-steps"></a>Étapes suivantes

* [Configurer la liste de mots de passe interdits personnalisée](howto-password-ban-bad.md)
* [Activer les agents locaux de protection de mot de passe Azure AD](howto-password-ban-bad-on-premises.md)
