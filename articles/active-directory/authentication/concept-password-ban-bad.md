---
title: Mots de passe interdits dynamiquement - Azure Active Directory
description: Interdire les mots de passe faibles dans votre environnement via des mots de passe interdits dynamiquement dans Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef127d120b32f5344bce0f68d79f48401087f0ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79231013"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>Éliminer les mots de passe incorrects de votre organisation

Les leaders du secteur vous recommandent de ne pas utiliser le même mot de passe à plusieurs endroits, de le rendre complexe et de ne pas utiliser des mots de passe simples comme « MotDePasse123 ». Comment les organisations s’assurent-elles que leurs utilisateurs respectent cette bonne pratique ? Comment peuvent-elles s’assurer que les utilisateurs n’utilisent pas des mots de passe faibles ou même des variantes de ceux-ci ?

Pour doter vos utilisateurs de mots de passe plus forts, il convient dans un premier temps de leur donner des conseils. Les conseils de Microsoft sur ce sujet se trouvent sur le lien suivant :

[Conseils pour le choix du mot de passe Microsoft](https://www.microsoft.com/research/publication/password-guidance)

S’il est important de donner de bons conseils, cela n’empêchera pas beaucoup d’utilisateurs de continuer à choisir des mots de passe faibles. Azure AD Password Protection protège votre organisation en détectant et bloquant les mots de passe faibles connus et leurs variantes, mais aussi en bloquant les termes faibles propres à votre organisation qui ont été éventuellement ajoutés.

Pour plus d’informations sur les travaux en matière de sécurité actuels, consultez le [Rapport de renseignement sur la sécurité (SIR) de Microsoft](https://www.microsoft.com/security/operations/security-intelligence-report).

## <a name="global-banned-password-list"></a>Liste globale de mots de passe interdits

L’équipe Azure AD Identity Protection analyse en permanence les données de télémétrie de sécurité Azure AD à la recherche de mots de passe faibles couramment utilisés ou compromis, ou plus spécifiquement, de termes élémentaires faibles qui servent souvent de base aux mots de passe faibles. Quand des termes faibles sont détectés, ils sont ajoutés à la liste globale des mots de passe interdits. Le contenu de la liste globale des mots de passe interdits n’est pas basé sur une source de données externe. Cette liste est entièrement basée sur les résultats permanents de l’analyse et de la télémétrie de sécurité Azure AD.

Chaque fois qu’un mot de passe est changé ou réinitialisé pour un utilisateur de locataire Azure AD, la version actuelle de la liste globale des mots de passe interdits est utilisée comme entrée clé au moment de vérifier la force du mot de passe. Cette vérification permet à tous les clients Azure AD de disposer de mots de passe plus forts.

> [!NOTE]
> Sachant que les cybercriminels utilisent des stratégies similaires dans leurs attaques, Microsoft ne publie pas le contenu de cette liste dans le domaine public.

## <a name="custom-banned-password-list"></a>Liste personnalisée de mots de passe interdits

Certaines organisations peuvent souhaiter améliorer la sécurité d’un cran en ajoutant leurs propres personnalisations à la liste globale des mots de passe interdits dans ce que Microsoft appelle la liste personnalisée des mots de passe interdits. Microsoft préconise que les termes ajoutés à cette liste soient pour l’essentiel spécifiques à l’organisation, par exemple :

- Noms de marques
- Noms de produits
- Lieux (par exemple, le siège social de l’entreprise)
- Termes internes spécifiques à l’entreprise
- Abréviations qui ont une signification spécifique dans l’entreprise

Une fois ajoutés à la liste personnalisée des mots de passe interdits, ces termes sont combinés à ceux de la liste globale des mots de passe interdits pendant la vérification des mots de passe.

> [!NOTE]
> La liste des mots de passe interdits personnalisée est limitée à un maximum de 1 000 termes. Elle n’est pas conçue pour bloquer des listes de mots de passe extrêmement longues. Pour tirer pleinement parti des avantages de la liste personnalisée des mots de passe interdits, Microsoft recommande dans un premier temps d’examiner et de comprendre l’algorithme d’évaluation des mots de passe (consultez [Comment les mots de passe sont évalués](concept-password-ban-bad.md#how-are-passwords-evaluated)), avant d’ajouter de nouveaux termes à la liste personnalisée des mots de passe interdits. Comprendre le fonctionnement de l’algorithme permettra à votre entreprise de détecter et bloquer efficacement un grand nombre de mots de passe faibles ainsi que leurs variantes.

Prenons l’exemple d’un client nommé « Contoso », dont le siège est situé à Londres, et qui fabrique un produit nommé « Widget ». Pour ce client, il serait peu productif et peu sûr de chercher à bloquer des variantes spécifiques de ces termes, par exemple :

- "Contoso!1"
- "Contoso@London"
- "ContosoWidget"
- "!Contoso"
- "LondonHQ"
- ... etc.

Au lieu de cela, il serait nettement plus efficace et sûr de bloquer uniquement les termes de base clés :

- "Contoso"
- "Londres"
- "Widget"

L’algorithme de vérification des mots de passe bloque automatiquement les variantes et les combinaisons faibles de ces termes.

La liste de mots de passe interdits personnalisée et la capacité à activer l’intégration d’Active Directory localement sont gérées via le portail Azure.

![Modifier la liste de mots de passe interdits personnalisée sous Méthodes d’authentification](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>Attaques par pulvérisation de mot de passe et listes de mots de passe compromis tierces

L’un des principaux avantages d’Azure AD Password Protection est qu’il vous aide à vous défendre contre les attaques par pulvérisation de mot de passe. La plupart des attaques par pulvérisation de mot de passe ne cherchent pas à répéter trop souvent une attaque sur un même compte, car ce comportement augmente considérablement les chances de détection, que ce soit par le verrouillage du compte ou par d’autres moyens. La majorité des attaques par pulvérisation de mot de passe envoient un petit nombre de mots de passe faibles connus à chaque compte d’une entreprise. Cette technique permet à l’attaquant de repérer rapidement un compte facile à compromettre tout en évitant les seuils de détection potentiels.

Azure AD Password Protection a été conçu pour bloquer efficacement tous les mots de passe faibles connus que les attaques par pulvérisation de mot de passe sont susceptibles d’utiliser, sur la base des données de télémétrie de sécurité réelles auxquelles Azure AD a accès.  Microsoft a connaissance de sites web tiers qui énumèrent des millions de mots de passe compromis à la suite de violations de sécurité connues publiquement. Il est courant que les produits de vérification de mots de passe tiers s’appuient sur la comparaison en force brute par rapport à ces millions de mots de passe. D’après Microsoft, ces techniques ne constituent pas le meilleur moyen d’améliorer la force globale des mots de passe en raison des stratégies généralement utilisées par les attaquants par pulvérisation de mot de passe.

> [!NOTE]
> La liste globale des mots de passe interdits de Microsoft ne repose en rien sur des sources de données tierces, pas même sur des listes de mots de passe compromis.

Même si la liste globale des mots de passe interdits de Microsoft s’avère plus petite que certaines listes tierces massives, ses effets sur la sécurité sont amplifiés par le fait qu’elle est tirée de données de télémétrie de sécurité réelles portant sur des attaques par pulvérisation de mot de passe qui se sont réellement produites, mais aussi par le fait que l’algorithme de vérification des mots de passe de Microsoft utilise des techniques de correspondance approximative intelligentes. Au final, elle détectera efficacement des millions de mots de passe faibles connus et empêchera leur utilisation dans votre entreprise. Les clients qui choisissent d’ajouter des termes propres à leur organisation à la liste personnalisée des mots de passe interdits profitent eux aussi des avantages de cet algorithme.

Vous trouverez des informations complémentaires sur les problèmes de sécurité liés aux mots de passe en lisant l’article [Your Pa$$word doesn't matter](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984).

## <a name="on-premises-hybrid-scenarios"></a>Scénarios hybrides locaux

La protection des comptes cloud uniquement est utile, toutefois de nombreuses organisations conservent des scénarios hybrides incluant Windows Server Active Directory en local. Les avantages en matière de sécurité d’Azure AD Password Protection peuvent aussi être étendus à votre environnement Windows Server Active Directory via l’installation d’agents locaux. Dès lors, les utilisateurs et les administrateurs qui changent ou réinitialisent leur mot de passe dans Active Directory doivent se conformer à la même stratégie de mot de passe que celle qui s’applique aux utilisateurs cloud uniquement.

## <a name="how-are-passwords-evaluated"></a>Évaluation des mots de passe

Chaque fois qu’un utilisateur change ou réinitialise son mot de passe, la force et la complexité du nouveau mot de passe sont vérifiées par rapport à la liste combinée des termes des listes globale et personnalisée des mots de passe interdits (si cette dernière est configurée).

Même si le mot de passe d'un utilisateur contient un mot interdit, il peut être accepté s'il est globalement suffisamment fort. Un mot de passe nouvellement configuré passe par les étapes suivantes pour évaluer sa force globale et déterminer s'il doit être accepté ou rejeté.

### <a name="step-1-normalization"></a>Étape 1 : Normalisation

Un nouveau mot de passe est d'abord soumis à un processus de normalisation. Cette technique permet de mapper un petit ensemble de mots de passe interdits à un ensemble beaucoup plus large de mots de passe potentiellement faibles.

La normalisation se décompose en deux parties.  Premièrement, toutes les majuscules sont remplacées par des minuscules.  Deuxièmement, des substitutions sont effectuées sur certains caractères communs, par exemple :  

| Lettre d'origine  | Lettre de substitution |
| --- | --- |
| « 0 »  | « o » |
| « 1 »  | « l » |
| « $ »  | « s » |
| '\@'  | « a » |

Exemple : supposons que le mot de passe « blank » soit interdit et qu'un utilisateur tente de remplacer son mot de passe par « Bl@nK ». Bien que « Bl@nk » ne soit pas spécifiquement interdit, le processus de normalisation le remplace par « blank », qui est un mot de passe interdit.

### <a name="step-2-check-if-password-is-considered-banned"></a>Étape 2 : Déterminer si le mot de passe est interdit

#### <a name="fuzzy-matching-behavior"></a>Correspondance approximative

La correspondance approximative est utilisée sur le mot de passe normalisé pour déterminer s'il contient un mot de passe figurant dans la liste globale ou la liste personnalisée des mots de passe interdits. Le processus de correspondance repose sur une distance de modification d'une (1) comparaison.  

Exemple : supposons que le mot de passe « abcdef » soit interdit et qu'un utilisateur tente de remplacer son mot de passe par l'un des suivants :

« abcdeg »    *(« f » de fin remplacé par « g »)* « abcdefg »   *(« g » ajouté à la fin)* « abcde »     *(« f » de fin supprimé)*

Aucun des mots de passe ci-dessus ne correspond précisément au mot de passe interdit « abcdef ». Cependant, comme chaque exemple entre dans le cadre de la distance de modification de 1 par rapport au terme interdit « abcdef », ils sont tous considérés comme étant une correspondance de « abcdef ».

#### <a name="substring-matching-on-specific-terms"></a>Correspondance de sous-chaîne (sur des termes spécifiques)

La correspondance de sous-chaîne est utilisée sur le mot de passe normalisé pour rechercher le prénom et le nom de l'utilisateur, ainsi que le nom du locataire (notez que la correspondance du nom du locataire n'est pas utilisée lors de la validation des mots de passe sur un contrôleur de domaine Active Directory).

Exemple : supposons qu’un utilisateur, Pol, souhaite réinitialiser son mot de passe en le remplaçant par « P0l123fb ». Après la normalisation, ce mot de passe devient « pol123fb ». La correspondance de substring détecte que le mot de passe contient le prénom de l’utilisateur « Pol ». Même si « P0l123fb » ne figure pas spécifiquement dans la liste des mots de passe interdits, la correspondance de substring a trouvé « Pol » dans le mot de passe. Par conséquent, ce mot de passe est rejeté.

#### <a name="score-calculation"></a>Calcul du score

L'étape suivante consiste à détecter toutes les occurrences de mots de passe interdits dans le nouveau mot de passe normalisé de l'utilisateur. Ensuite :

1. Chaque mot de passe interdit trouvé dans le mot de passe d'un utilisateur reçoit un point.
2. Chaque caractère unique restant reçoit un point.
3. Un mot de passe doit recevoir au moins cinq (5) points pour être accepté.

Pour les deux exemples suivants, nous partons du principe que Contoso utilise la protection par mot de passe Azure AD, que « contoso » figure dans sa liste personnalisée et que « blank » figure dans la liste globale.

Exemple : un utilisateur remplace son mot de passe par « C0ntos0Blank12 ».

Après la normalisation, ce mot de passe devient « contosoblank12 ». Le processus de correspondance détecte que ce mot de passe contient deux mots interdits : contoso et blank. Le score obtenu par ce mot de passe est le suivant :

[contoso] + [blank] + [1] + [2] = 4 points. Dans la mesure où ce mot de passe a obtenu moins de cinq (5) points, il est rejeté.

Exemple : un utilisateur remplace son mot de passe par « ContoS0Bl@nkf9! ».

Après la normalisation, ce mot de passe devient « contosoblankf9 ! ». Le processus de correspondance détecte que ce mot de passe contient deux mots interdits : contoso et blank. Le score obtenu par ce mot de passe est le suivant :

[contoso] + [blank] + [f] + [9] + [!] = 5 points. Dans la mesure où ce mot de passe a obtenu au moins cinq (5) points, il est accepté.

   > [!IMPORTANT]
   > Veuillez noter que la liste globale et l'algorithme des mots de passe interdits peuvent à tout moment changer dans Azure, en fonction des analyses et des recherches en cours sur la sécurité. Pour le service d’agent DC local, les algorithmes mis à jour ne prennent effet qu’après la réinstallation du logiciel de l’agent DC.

## <a name="license-requirements"></a>Conditions de licence :

|   | Protection de mot de passe Azure AD avec liste globale de mots de passe interdits | Protection de mot de passe Azure AD avec liste personnalisée de mots de passe interdits|
| --- | --- | --- |
| Utilisateurs du cloud uniquement | Azure AD Gratuit | Azure AD Premium P1 ou P2 |
| Utilisateurs synchronisés à partir de Windows Server Active Directory en local | Azure AD Premium P1 ou P2 | Azure AD Premium P1 ou P2 |

> [!NOTE]
> Les utilisateurs Windows Server Active Directory locaux qui ne sont pas synchronisés avec Azure Active Directory bénéficient également des avantages de la protection par mot de passe Azure AD basée sur les licences existantes pour les utilisateurs synchronisés.

Vous trouverez des informations de licence supplémentaires, notamment les prix, sur le [site de tarification Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-users-see"></a>Ce que voient les utilisateurs

Lorsqu’un utilisateur tente de réinitialiser un mot de passe pour le remplacer par un mot de passe interdit, le message d’erreur suivant s’affiche :

Malheureusement, votre mot de passe contient un mot, une expression ou un modèle qui rend votre mot de passe facile à deviner. Réessayez avec un autre mot de passe.

## <a name="next-steps"></a>Étapes suivantes

- [Configurer la liste de mots de passe interdits personnalisée](howto-password-ban-bad.md)
- [Activer les agents locaux de protection de mot de passe Azure AD](howto-password-ban-bad-on-premises-deploy.md)
