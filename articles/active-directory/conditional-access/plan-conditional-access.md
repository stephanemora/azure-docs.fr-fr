---
title: Planifier des stratégies d’accès conditionnel dans Azure Active Directory | Microsoft Docs
description: Dans cet article, vous découvrez comment planifier des stratégies d’accès conditionnel pour Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: martincoetzer
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1c75d5022432a9a57b30aabec4dd2c4f76f2f29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671828"
---
# <a name="how-to-plan-your-conditional-access-deployment-in-azure-active-directory"></a>Procédure : Planifier votre déploiement d’accès conditionnel Azure Active Directory

La planification de votre déploiement d’accès conditionnel est essentielle pour obtenir la stratégie d’accès dont vous avez besoin pour les applications et ressources de votre organisation. L’étape qui doit vous prendre le plus de temps dans la phase de planification de votre déploiement est la conception des différentes stratégies dont vous avez besoin pour accorder ou bloquer l’accès à vos utilisateurs dans les conditions que vous choisissez. Ce document décrit les étapes à suivre pour implémenter des stratégies d’accès conditionnel sécurisées et efficaces. Avant de commencer, vous devez bien comprendre le fonctionnement de l’[accès conditionnel](overview.md) et quand vous devez l’utiliser.

## <a name="what-you-should-know"></a>Ce que vous devez savoir

L’accès conditionnel est comme une infrastructure qui vous permet de contrôler l’accès aux applications et aux ressources de votre organisation, au lieu d’une fonctionnalité autonome. Par conséquent, certains paramètres d’accès conditionnel nécessitent la configuration de fonctionnalités supplémentaires. Par exemple, vous pouvez configurer une stratégie qui répond à un [niveau de risque de connexion](../identity-protection/howto-identity-protection-configure-risk-policies.md) spécifique. Toutefois, une stratégie basée sur un niveau de risque de connexion nécessite l’activation d’[Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md).

Si des fonctionnalités supplémentaires sont nécessaires, vous devez peut-être également obtenir les licences associées. Par exemple, alors que l’accès conditionnel est une fonctionnalité d’Azure AD Premium P1, Identity Protection nécessite une licence Azure AD Premium P2.

Il existe deux types de stratégies d’accès conditionnel : de référence et standard. Une [stratégie de référence](baseline-protection.md) est une stratégie d’accès conditionnel prédéfinie. L’objectif de ces stratégies est d’avoir au moins le niveau de référence de sécurité activé. Stratégies de référence. Les stratégies de référence sont disponibles dans toutes les éditions d’Azure AD et fournissent seulement des options de personnalisation limitées. Si un scénario nécessite plus de souplesse, désactivez la stratégie de référence et implémentez vos exigences dans une stratégie standard personnalisée.

Dans une stratégie d’accès conditionnel standard, vous pouvez personnaliser tous les paramètres pour ajuster la stratégie à vos exigences commerciales. Les stratégies standard nécessitent une licence Azure AD Premium P1.

>[!NOTE]
> Nous vous recommandons d’utiliser une stratégie d’accès conditionnel en fonction de l’appareil d’Azure AD pour obtenir la meilleure mise en œuvre après l’authentification initiale de l’appareil. Ceci comprend la fermeture des sessions si l’appareil devient non conforme et le flux de code d’appareil.

## <a name="draft-policies"></a>Rédiger des stratégies

L’accès conditionnel Azure Active Directory vous permet d’atteindre un niveau supérieur de protection de vos applications cloud. Dans ce nouveau niveau, l’accès à une application cloud est basé sur l’évaluation d’une stratégie dynamique et non sur une configuration d’accès statique. Avec une stratégie d’accès conditionnel, vous définissez une réponse (**faire cela**) sur une condition d’accès (**quand cela se produit**).

![Raison et réponse](./media/plan-conditional-access/10.png)

Définissez chaque stratégie d’accès conditionnel à implémenter à l’aide de ce modèle de planification. Exercice de planification :

- Il vous permet de schématiser les réponses et les conditions de chaque stratégie.
- Il produit un catalogue de stratégies d’accès conditionnel bien documenté pour votre organisation. 

Vous pouvez utiliser votre catalogue pour évaluer si votre implémentation de stratégie reflète les exigences métier de votre organisation. 

Utilisez l’exemple de modèle suivant pour créer des stratégies d’accès conditionnel pour votre organisation :

|Quand *cela* se produit :|Faire *cela* :|
|-|-|
|Une tentative d’accès est effectuée :<br>- Sur une application cloud *<br>- Par des utilisateurs et des groupes*<br>Au moyen de :<br>- Condition 1 (par exemple, en dehors du réseau d’entreprise)<br>- Condition 2 (par exemple, les plateformes d’appareils)|Bloquer l’accès à l’application|
|Une tentative d’accès est effectuée :<br>- Sur une application cloud *<br>- Par des utilisateurs et des groupes*<br>Au moyen de :<br>- Condition 1 (par exemple, en dehors du réseau d’entreprise)<br>- Condition 2 (par exemple, les plateformes d’appareils)|Accorder l’accès avec (AND) :<br>- Exigence 1 (par exemple, MFA)<br>- Exigence 2 (par exemple, conformité des appareils)|
|Une tentative d’accès est effectuée :<br>- Sur une application cloud *<br>- Par des utilisateurs et des groupes*<br>Au moyen de :<br>- Condition 1 (par exemple, en dehors du réseau d’entreprise)<br>- Condition 2 (par exemple, les plateformes d’appareils)|Accorder l’accès avec (OR) :<br>- Exigence 1 (par exemple, MFA)<br>- Exigence 2 (par exemple, conformité des appareils)|

Au minimum, **quand cela se produit** définit l’entité de sécurité (**qui**) qui tente d’accéder à une application cloud (**quoi**). Si nécessaire, vous pouvez également ajouter **comment** une tentative d’accès est effectuée. Dans l’accès conditionnel, les éléments qui définissent les variables « qui », « quoi » et « comment » sont appelées des conditions. Pour plus d’informations, consultez [Que sont les conditions dans l’accès conditionnel Azure Active Directory ?](concept-conditional-access-conditions.md) 

Avec **faire cela**, vous définissez la réponse de votre stratégie à une condition d’accès. Dans votre réponse, vous bloquez ou accordez l’accès avec des exigences supplémentaires, par exemple, l’authentification multifacteur (MFA). Pour une vue d’ensemble complète, consultez [Que sont les contrôles d’accès dans l’accès conditionnel Azure Active Directory ?](controls.md)  

Une stratégie d’accès conditionnel combine des conditions à des contrôles d’accès.

![Raison et réponse](./media/plan-conditional-access/51.png)

Pour plus d’informations, consultez [Que faut-il pour faire fonctionner une stratégie ?](best-practices.md#whats-required-to-make-a-policy-work).

À ce stade, vous décidez d’une convention de nommage pour vos stratégies. La convention de nommage vous permet de rechercher des stratégies et de comprendre à quoi elles servent sans les ouvrir dans le portail d’administration Azure. Le nom de votre stratégie doit montrer :

- Un numéro de séquence
- L’application cloud à laquelle elle s’applique
- La réponse
- L’objet auquel elle s’applique (qui)
- Quand elle s’applique (le cas échéant)
 
![Convention de nommage](./media/plan-conditional-access/11.png)

Si un nom descriptif vous aide à maintenir une vue d’ensemble de votre implémentation de l’accès conditionnel, le numéro de séquence vous est aussi utile lorsque vous devez référencer une stratégie dans une conversation. Par exemple, si vous parlez à un collègue administrateur au téléphone, vous pouvez lui demander d’ouvrir une stratégie EM063 pour résoudre un problème.

Par exemple, le nom suivant indique que la stratégie nécessite MFA pour les utilisateurs du marketing sur des réseaux externes utilisant l’application Dynamics CRP :

`CA01 - Dynamics CRP: Require MFA For marketing When on external networks`

En plus de vos stratégies actives, il est préférable d’implémenter également des stratégies désactivées qui agissent comme des [contrôles d’accès résilients secondaires dans des scénarios d’urgence/de panne](../authentication/concept-resilient-controls.md). Votre standard de nommage pour les stratégies d’urgence doit inclure quelques éléments supplémentaires : 

- `ENABLE IN EMERGENCY` au début, pour faire ressortir le nom au milieu des autres stratégies.
- Le nom d’interruption auquel elle doit s’appliquer.
- Un numéro de séquence de classement pour aider l’administrateur à savoir dans quel ordre les stratégies doivent être activées. 

Par exemple, le nom suivant indique que cette stratégie est la première de quatre stratégies à activer en cas d’interruption de l’authentification multifacteur :

`EM01 - ENABLE IN EMERGENCY, MFA Disruption[1/4] - Exchange SharePoint: Require hybrid Azure AD join For VIP users`

## <a name="plan-policies"></a>Planifier des stratégies

Quand vous planifiez votre solution de stratégie d’accès conditionnel, déterminez si vous devez créer des stratégies pour obtenir les résultats suivants. 

### <a name="block-access"></a>Bloquer l’accès

L’option permettant de bloquer l’accès est puissante, car elle :

- Prévaut sur toutes les autres attributions d’un utilisateur
- A la possibilité d’empêcher l’ensemble de votre organisation de se connecter à votre locataire
 
Si vous voulez bloquer l’accès pour tous les utilisateurs, vous devez exclure au moins un utilisateur (en général, les comptes d’accès d’urgence) de la stratégie. Pour plus d’informations, consultez [Sélectionner des utilisateurs et des groupes](block-legacy-authentication.md#select-users-and-cloud-apps).  

### <a name="require-mfa"></a>Exiger une authentification multifacteur

Pour simplifier l’expérience de connexion de vos utilisateurs, vous pouvez autoriser ceux-ci à se connecter à vos applications cloud à l’aide d’un nom d’utilisateur et d’un mot de passe. Toutefois, en règle générale, il y a toujours des scénarios pour lesquels il vaut mieux exiger une forme plus robuste de vérification de compte. Avec une stratégie d’accès conditionnel, vous pouvez limiter le besoin d’authentification multifacteur à certains scénarios. 

Les cas d’utilisation courants pour lesquels exiger l’authentification multifacteur sont les accès :

- [Par les administrateurs](howto-baseline-protect-administrators.md)
- [À des applications spécifiques](app-based-mfa.md) 
- [À partir d’emplacements réseau non fiables](untrusted-networks.md).

### <a name="respond-to-potentially-compromised-accounts"></a>Répondre aux comptes potentiellement compromis

Avec des stratégies d’accès conditionnel, vous pouvez implémenter des réponses automatiques aux connexions d’identités potentiellement compromises. La probabilité qu’un compte soit compromis est exprimée sous forme de niveaux de risque. Il existe deux niveaux de risque calculés par Identity Protection : risque de connexion et risque d’utilisateur. Pour implémenter une réponse à un risque de connexion, vous avez deux options :

- [La condition de risque de connexion](concept-conditional-access-conditions.md#sign-in-risk) dans la stratégie d’accès conditionnel
- [La stratégie de risque de connexion](../identity-protection/howto-sign-in-risk-policy.md) dans Identity Protection 

Traiter le risque de connexion comme une condition est la méthode préférée, car elle vous donne plus d’options de personnalisation.

Le niveau de risque d’utilisateur est disponible uniquement sous forme de [stratégie de risque d’utilisateur](../identity-protection/howto-user-risk-policy.md) dans Identity Protection. 

Pour plus d’informations, consultez [Qu’est-ce qu’Azure Active Directory Identity Protection ?](../identity-protection/overview.md) 

### <a name="require-managed-devices"></a>Exiger des appareils gérés

L’augmentation du nombre d’appareils pris en charge pour accéder aux ressources cloud permet d’améliorer la productivité de vos utilisateurs. Toutefois, il est probable que vous ne souhaitiez pas que certaines ressources de votre environnement soient accessibles par des appareils dont le niveau de protection est inconnu. Pour ce type de ressources, vous devez exiger que les utilisateurs y accèdent uniquement à l’aide d’un appareil géré. Pour plus d’informations, consultez le [Guide pratique pour exiger des appareils managés et accéder aux applications cloud avec l’accès conditionnel](require-managed-devices.md). 

### <a name="require-approved-client-apps"></a>Exiger des applications client approuvées

Une des premières décisions que vous devez prendre pour les scénarios BYOD (apportez votre propre appareil) est de déterminer si vous devez gérer tout l’appareil ou seulement les données qui s’y trouvent. Vos employés utilisent des appareils mobiles pour des tâches à la fois personnelles et professionnelles. Tout en veillant à ce que vos employés restent productifs, vous voulez aussi éviter toute perte de données. Avec l’accès conditionnel Azure Active Directory (Azure AD), vous pouvez restreindre l’accès à vos applications cloud à des applications clientes approuvées capables de protéger vos données d’entreprise. Pour plus d’informations, consultez [Comment exiger des applications clientes approuvées pour accéder aux applications cloud avec l’accès conditionnel](app-based-conditional-access.md).

### <a name="block-legacy-authentication"></a>Bloquer l’authentification héritée

Azure AD prend en charge plusieurs protocoles d’authentification et d’autorisation parmi ceux les plus couramment utilisés, notamment l’authentification héritée. Comment pouvez-vous empêcher les applications utilisant l’authentification héritée d’accéder aux ressources de votre locataire ? Il est recommandé de les bloquer à l’aide d’une stratégie d’accès conditionnel, tout simplement. Si nécessaire, vous pouvez autoriser uniquement certains utilisateurs et des emplacements réseau spécifiques à utiliser les applications s’appuyant sur l’authentification héritée. Pour plus d’informations, consultez [Comment bloquer l’authentification héritée sur Azure AD avec l’accès conditionnel](block-legacy-authentication.md).

## <a name="test-your-policy"></a>Tester votre stratégie

Avant de déployer une stratégie en production, vous devez la tester pour vérifier qu’elle se comporte comme prévu.

1. Créer des utilisateurs de test
1. Créer un plan de test
1. Configurer la stratégie
1. Évaluer une connexion simulée
1. Tester votre stratégie
1. Nettoyage

### <a name="create-test-users"></a>Créer des utilisateurs de test

Pour tester une stratégie, créez un ensemble d’utilisateurs semblables aux utilisateurs dans votre environnement. La création d’utilisateurs de test vous permet de vérifier que vos stratégies fonctionnent comme prévu avant d’impacter les utilisateurs réels et de potentiellement interrompre leur accès aux applications et ressources. 

Certaines organisations ont des locataires de test dans ce but. Toutefois, il peut être difficile de recréer toutes les conditions et les applications dans un locataire de test pour tester intégralement le résultat d’une stratégie. 

### <a name="create-a-test-plan"></a>Créer un plan de test

Le plan de test est important pour comparer les résultats attendus et les résultats réels. Vous devez toujours avoir un objectif avant de tester quelque chose. Le tableau suivant décrit des exemples de cas de test. Ajustez les scénarios et les résultats attendus en fonction de la configuration de vos stratégies d’accès conditionnel.

|Stratégie |Scénario |Résultat attendu | Résultats |
|---|---|---|---|
|[Exiger l’authentification multifacteur en dehors du bureau](/azure/active-directory/conditional-access/untrusted-networks)|L’utilisateur autorisé se connecte à l’*application* quand il est dans un emplacement approuvé ou au bureau|L’utilisateur n’est pas invité à utiliser l’authentification multifacteur| |
|[Exiger l’authentification multifacteur en dehors du bureau](/azure/active-directory/conditional-access/untrusted-networks)|L’utilisateur autorisé se connecte à l’*application* quand il n’est pas dans un emplacement approuvé ou au bureau|L’utilisateur est invité à utiliser l’authentification multifacteur et peut se connecter| |
|[Exiger l’authentification multifacteur (pour les administrateurs)](/azure/active-directory/conditional-access/howto-baseline-protect-administrators)|L’administrateur général se connecte à l’*application*|L’administrateur est invité à utiliser l’authentification multifacteur| |
|[Connexions risquées](/azure/active-directory/identity-protection/howto-sign-in-risk-policy)|L’utilisateur se connecte à l’*application* à l’aide d’un [navigateur Tor](/azure/active-directory/active-directory-identityprotection-playbook)|L’administrateur est invité à utiliser l’authentification multifacteur| |
|[Gestion des appareils](/azure/active-directory/conditional-access/require-managed-devices)|L’utilisateur autorisé tente de se connecter à partir d’un appareil autorisé|Accès accordé| |
|[Gestion des appareils](/azure/active-directory/conditional-access/require-managed-devices)|L’utilisateur autorisé tente de se connecter à partir d’un appareil non autorisé|Accès bloqué| |
|[Changement de mot de passe pour les utilisateurs à risque](/azure/active-directory/identity-protection/howto-user-risk-policy)|L’utilisateur autorisé tente de se connecter avec des informations d’identification compromises (connexion à haut risque)|L’utilisateur est invité à changer le mot de passe ou l’accès est bloqué selon votre stratégie| |

### <a name="configure-the-policy"></a>Configurer la stratégie

La gestion des stratégies d’accès conditionnel est une tâche manuelle. Dans le Portail Azure, vous pouvez gérer toutes vos stratégies d’accès conditionnel dans un même emplacement central : la page d’accès conditionnel. Pour accéder et modifier la page d’accès conditionnel, accédez à la section **Sécurité** dans le volet de navigation d’**Active Directory**. 

![Accès conditionnel](media/plan-conditional-access/03.png)

Pour en savoir plus sur la création des stratégies d’accès conditionnel, consultez [Exiger l’authentification multifacteur pour des applications spécifiques avec l’accès conditionnel Azure Active Directory](app-based-mfa.md). Ce guide de démarrage rapide vous permet de :

- Vous familiariser avec l’interface utilisateur.
- Obtenez une première impression du fonctionnement de l’accès conditionnel. 

### <a name="evaluate-a-simulated-sign-in"></a>Évaluer une connexion simulée

À présent que vous avez configuré votre stratégie d’accès conditionnel, vous souhaitez probablement savoir s’il fonctionne comme prévu. Dans un premier temps, utilisez [l’outil de stratégie d’accès conditionnel What If](what-if-tool.md) pour simuler une connexion de votre utilisateur de test. La simulation évalue l’impact cette connexion sur vos stratégies et génère un rapport de simulation.

>[!NOTE]
> Bien que l’exécution simulée vous donne une idée de l’impact d’une stratégie d’accès conditionnel, elle ne remplace pas une série de tests réelle.

### <a name="test-your-policy"></a>Tester votre stratégie

Exécutez des cas de test en fonction de votre plan de test. Dans cette étape, vous exécutez un test de bout en bout de chaque stratégie pour vos utilisateurs de test, pour vérifier que chaque stratégie se comporte correctement. Utilisez les scénarios créés ci-dessus pour exécuter chaque test.

Veillez à tester aussi les critères d’exclusion d’une stratégie. Par exemple, vous pouvez exclure un utilisateur ou un groupe d’une stratégie qui exige l’authentification multifacteur. Testez si les utilisateurs exclus sont invités à utiliser l’authentification multifacteur, car l’association d’autres stratégies peut exiger l’authentification multifacteur pour ces utilisateurs.

### <a name="cleanup"></a>Nettoyage

La procédure de nettoyage utilise les étapes suivantes :

1. Désactiver la tâche.
1. Supprimer les utilisateurs et groupes attribués.
1. Supprimer les utilisateurs de test.  

## <a name="move-to-production"></a>Passer en production

Lorsque de nouvelles stratégies sont prêtes pour votre environnement, déployez-les en phases :

- Indiquez aux utilisateurs finaux les changements internes.
- Commencez par un petit ensemble d’utilisateurs et vérifiez que la stratégie se comporte comme prévu.
- Quand vous étendez une stratégie à davantage d’utilisateurs, continuez à exclure tous les administrateurs. De cette façon, au moins une personne a accès à la stratégie si un changement est nécessaire.
- Appliquez une stratégie à tous les utilisateurs uniquement si c’est nécessaire.

Une bonne pratique est de créer au moins un compte d’utilisateur qui est :

- dédié à l’administration des stratégies ;
- exclu de toutes vos stratégies.

## <a name="rollback-steps"></a>Étapes de restauration

Si vous devez restaurer les stratégies que vous venez d’implémenter, utilisez une ou plusieurs des options suivantes :

1. **Désactiver la stratégie** : La désactivation d’une stratégie garantit qu’elle ne s’applique pas quand un utilisateur tente de se connecter. Vous pouvez toujours revenir en arrière et activer la stratégie quand vous voulez l’utiliser.

   ![Désactiver une stratégie](media/plan-conditional-access/07.png)

1. **Exclure un utilisateur/groupe d’une stratégie** : Si un utilisateur ne peut pas accéder à l’application, vous pouvez l’exclure de la stratégie

   ![Exclure des utilisateurs](media/plan-conditional-access/08.png)

   > [!NOTE]
   > Cette option doit être utilisée avec parcimonie, uniquement si l’utilisateur est approuvé. L’utilisateur doit être rajouté dans la stratégie ou le groupe dès que possible.

1. **Supprimer la stratégie** : Si la stratégie n’est plus nécessaire, supprimez-la.

## <a name="next-steps"></a>Étapes suivantes

Consultez [Documentation sur l’accès conditionnel Azure AD](index.yml) pour obtenir une vue d’ensemble des informations disponibles.
