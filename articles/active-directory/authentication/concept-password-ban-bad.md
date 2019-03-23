---
title: Interdits dynamiquement les mots de passe - Azure Active Directory
description: Interdire les mots de passe faibles dans votre environnement via des mots de passe interdits dynamiquement dans Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 795abcadb1cee7599665f7bc3b8e0d3e08a3da05
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369069"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>Éliminer les mots de passe incorrects de votre organisation

Les leaders du secteur vous recommandent de ne pas utiliser le même mot de passe à plusieurs endroits, de le rendre complexe et de ne pas utiliser de mots de passe simples, tels que 123. Comment les organisations s’assurent que leurs utilisateurs suivent cette recommandation ? Comment peuvent-ils s’assurer que les utilisateurs n’utilisent pas de mots de passe courants ou de mots de passe connus dans des cas de violation de données ?

## <a name="global-banned-password-list"></a>Liste globale de mots de passe interdits

Microsoft cherche toujours à garder une longueur d’avance sur les cybercriminels. Par conséquent, l’équipe Azure AD Identity Protection recherche en permanence les mots de passe couramment utilisés et compromis. Puis, ils bloquent ces mots de passe qui sont considérés comme étant trop courants dans ce que l’on appelle la liste globale de mots de passe interdits. Les cybercriminels utilisent également des stratégies semblables dans leurs attaques, par conséquent, Microsoft ne rend pas public le contenu de cette liste. Ces mots de passe vulnérables sont bloqués avant de devenir une véritable menace pour les clients de Microsoft. Pour plus d’informations sur les travaux en matière de sécurité actuels, consultez le [Rapport de renseignement sur la sécurité (SIR) de Microsoft](https://www.microsoft.com/security/operations/security-intelligence-report).

## <a name="custom-banned-password-list"></a>Liste personnalisée de mots de passe interdits

Il se peut que certaines organisations veulent pousser plus loin la sécurité en ajoutant leurs propres personnalisations à la liste de mots de passe interdits dans ce que Microsoft appelle la liste de mots de passe interdits personnalisée. Les clients d’entreprise, comme Contoso peuvent ensuite choisir de bloquer des variantes de leurs noms de marque, des conditions spécifiques de la société ou d’autres éléments.

La liste de mots de passe interdits personnalisée et la capacité à activer l’intégration d’Active Directory localement sont gérées via le portail Azure.

![Modifier la liste de mots de passe interdits personnalisé sous les méthodes d’authentification](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="on-premises-hybrid-scenarios"></a>Scénarios hybrides locaux

La protection des comptes cloud uniquement est utile, toutefois de nombreuses organisations conservent des scénarios hybrides incluant Windows Server Active Directory en local. Il est possible d’installer la protection de mot de passe Azure AD pour Windows Server Active Directory agents sur site étendre les listes de mots de passe interdits à votre infrastructure existante. Maintenant, les utilisateurs et les administrateurs qui modifient, définissent ou réinitialisent des mots de passe localement doivent se conformer à la même stratégie de mot de passe que les utilisateurs cloud uniquement.

## <a name="how-are-passwords-evaluated"></a>Évaluation des mots de passe

Chaque fois qu'un utilisateur modifie ou réinitialise son mot de passe, la force et la complexité du nouveau mot de passe sont évaluées à l'aide de la liste globale et de la liste personnalisée des mots de passe interdits (si cette dernière est configurée).

Même si le mot de passe d'un utilisateur contient un mot interdit, il peut être accepté s'il est globalement suffisamment fort. Un mot de passe nouvellement configuré passe par les étapes suivantes pour évaluer sa force globale et déterminer s'il doit être accepté ou rejeté.

### <a name="step-1-normalization"></a>Étape 1 : Normalisation

Un nouveau mot de passe est d'abord soumis à un processus de normalisation. Cela permet de mapper un petit ensemble de mots de passe interdits avec un ensemble beaucoup plus large de mots de passe potentiellement faibles.

La normalisation se décompose en deux parties.  Premièrement, toutes les majuscules sont remplacées par des minuscules.  Deuxièmement, des substitutions sont effectuées sur certains caractères communs, par exemple :  

| Lettre d'origine  | Lettre de substitution |
| --- | --- |
| « 0 »  | « o » |
| « 1 »  | « l » |
| « $ »  | « s » |
| '\@'  | « a » |

Exemple : supposons que le mot de passe « blank » soit interdit et qu'un utilisateur tente de remplacer son mot de passe par « Bl@nK ». Bien que « Bl@nk » ne soit pas spécifiquement interdit, le processus de normalisation le remplace par « blank », qui est un mot de passe interdit.

### <a name="step-2-check-if-password-is-considered-banned"></a>Étape 2 : Déterminer si le mot de passe est interdit

#### <a name="fuzzy-matching-behavior"></a>Correspondance approximative

La correspondance approximative est utilisée sur le mot de passe normalisé pour déterminer s'il contient un mot de passe figurant dans la liste globale ou la liste personnalisée des mots de passe interdits. Le processus de correspondance repose sur une distance de modification d'une (1) comparaison.  

Exemple : supposons que le mot de passe « abcdef » soit interdit et qu'un utilisateur tente de remplacer son mot de passe par l'un des suivants :

« abcdeg »    * (« f » de fin remplacé par « g »)* « abcdefg »   * (« g » ajouté à la fin)* « abcde »     * (« f » de fin supprimé)*

Aucun des mots de passe ci-dessus ne correspond précisément au mot de passe interdit « abcdef ». Toutefois, comme chaque exemple entre dans le cadre de la distance de modification de 1 par rapport au jeton interdit « abcdef », on considère qu'ils présentent tous une correspondance avec « abcdef ».

#### <a name="substring-matching-on-specific-terms"></a>Correspondance de sous-chaîne (sur des termes spécifiques)

La correspondance de sous-chaîne est utilisée sur le mot de passe normalisé pour rechercher le prénom et le nom de l'utilisateur, ainsi que le nom du locataire (notez que la correspondance du nom du locataire n'est pas utilisée lors de la validation des mots de passe sur un contrôleur de domaine Active Directory).

Exemple : supposons qu'un utilisateur, John Doe, souhaite réinitialiser son mot de passe en le remplaçant par « J0hn123fb ». Après la normalisation, ce mot de passe devient « john123fb ». La correspondance de sous-chaîne détecte que le mot de passe contient le prénom de l'utilisateur « John ». Même si « J0hn123fb » ne figure pas spécifiquement dans la liste des mots de passe interdits, la correspondance de sous-chaîne a trouvé « John » dans le mot de passe. Par conséquent, ce mot de passe est rejeté.

#### <a name="score-calculation"></a>Calcul du score

L'étape suivante consiste à détecter toutes les occurrences de mots de passe interdits dans le nouveau mot de passe normalisé de l'utilisateur. Ensuite :

1. Chaque mot de passe interdit trouvé dans le mot de passe d'un utilisateur reçoit un point.
2. Chaque caractère unique restant reçoit un point.
3. Un mot de passe doit recevoir au moins 5 points pour être accepté.

Pour les deux exemples suivants, nous partons du principe que Contoso utilise la protection par mot de passe Azure AD, que « contoso » figure dans sa liste personnalisée et que « blank » figure dans la liste globale.

Exemple : un utilisateur remplace son mot de passe par « C0ntos0Blank12 ».

Après la normalisation, ce mot de passe devient « contosoblank12 ». Le processus de correspondance détecte que ce mot de passe contient deux mots interdits : contoso et blank. Le score obtenu par ce mot de passe est le suivant :

[contoso] + [vide] + [1] + [2] = 4 points dans la mesure où ce mot de passe est sous 5 points, il est rejeté.

Exemple : un utilisateur remplace son mot de passe par « ContoS0Bl@nkf9! ».

Après la normalisation, ce mot de passe devient « contosoblankf9 ! ». Le processus de correspondance détecte que ce mot de passe contient deux mots interdits : contoso et blank. Le score obtenu par ce mot de passe est le suivant :

[contoso] + [blank] + [f] + [9] + [!] = 5 points. Dans la mesure où ce mot de passe a obtenu au moins 5 points, il est accepté.

   > [!IMPORTANT]
   > Veuillez noter que la liste globale et l'algorithme des mots de passe interdits peuvent à tout moment changer dans Azure, en fonction des analyses et des recherches en cours sur la sécurité. Pour le service d'agent DC local, les algorithmes mis à jour ne prendront effet qu'après la réinstallation du logiciel de l'agent DC.

## <a name="license-requirements"></a>Conditions de licence :

|   | Protection de mot de passe Azure AD avec liste globale de mots de passe interdits | Protection de mot de passe Azure AD avec liste personnalisée de mots de passe interdits|
| --- | --- | --- |
| Utilisateurs du cloud uniquement | Azure AD Gratuit | Azure AD Standard |
| Utilisateurs synchronisés à partir de Windows Server Active Directory en local | Azure AD Premium P1 ou P2 | Azure AD Premium P1 ou P2 |

Vous trouverez des informations de licence supplémentaires, notamment les prix, sur le [site de tarification Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-users-see"></a>Ce que voient les utilisateurs

Lorsqu’un utilisateur tente de réinitialiser un mot de passe pour le remplacer par un mot de passe interdit, le message d’erreur suivant s’affiche :

Malheureusement, votre mot de passe contient un mot, une expression ou un modèle qui rend votre mot de passe facile à deviner. Réessayez avec un autre mot de passe.

## <a name="next-steps"></a>Étapes suivantes

* [Configurer la liste de mots de passe interdits personnalisée](howto-password-ban-bad.md)
* [Activer les agents locaux de protection de mot de passe Azure AD](howto-password-ban-bad-on-premises-deploy.md)
