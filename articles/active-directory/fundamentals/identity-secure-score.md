---
title: Quel est le score sécurisé d’identité ? -Azure Active Directory
description: Comment vous pouvez utiliser le score d’identité sécurisée pour améliorer la sécurité de votre annuaire
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: tilarso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6cdff2305914ca6e4144f7784d1a60026a1d27c0
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65988669"
---
# <a name="what-is-the-identity-secure-score-in-azure-active-directory"></a>Qu’est-ce que le degré de sécurisation Identity Secure Score dans Azure Active Directory ?

Dans quelle mesure votre locataire Azure AD est-il sécurisé ? Si vous ne savez pas comment répondre à cette question, cet article explique comment le score sécurisé d’identité vous aide à surveiller et améliorer votre posture de sécurité d’identité.

## <a name="what-is-an-identity-secure-score"></a>Qu’est-ce qu’un degré de sécurisation Identity Secure Score ?

Le score sécurisé d’identité est le nombre compris entre 1 et 223 qui fonctionne comme un indicateur pour vous êtes comment alignée avec les recommandations de sécurité de Microsoft. Chaque action d’amélioration du produit dans score sécurisé identity est adaptée à votre configuration spécifique.  

![Degré de sécurisation](./media/identity-secure-score/identity-secure-score-overview.png)

Le degré de sécurisation vous aide à accomplir les tâches suivantes :

- Évaluer votre méthode de sécurité relative aux identités de façon objective
- Planifier les améliorations à apporter à la sécurité des identités
- Évaluer la réussite de vos améliorations

Vous pouvez accéder au degré de sécurisation et aux informations correspondantes dans le tableau de bord du degré de sécurisation Identity Secure Score. Ce tableau de bord présente les informations suivantes :

- Votre score sécurisé d’identité
- Un graphique de comparaison illustrant comment votre identité secure score compare à d’autres locataires dans le même secteur et de taille similaire
- Un graphique de tendance indiquant comment votre score sécurisé d’identité a changé au fil du temps
- Une liste des améliorations possibles

La mise en œuvre des actions d’amélioration vous offre les possibilités suivantes :

- Améliorer votre posture de sécurité et votre score
- Tirer parti les fonctionnalités disponibles pour votre organisation dans le cadre de vos investissements d’identité

## <a name="how-do-i-get-my-secure-score"></a>Comment faire pour obtenir mon degré de sécurisation ?

Le score d’identité sécurisée est disponible dans toutes les éditions d’Azure AD. Pour obtenir votre degré de sécurisation, accédez au [tableau de bord de présentation Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/IdentitySecureScore).

## <a name="how-does-it-work"></a>Comment cela fonctionne-t-il ?

Toutes les 48 heures, Azure examine votre configuration de sécurité et compare vos paramètres avec ceux des meilleures pratiques suggérées. Le résultat de cette évaluation, un nouveau score est calculé en fonction de votre répertoire. Il est possible que votre configuration de sécurité n’est pas alignée avec les méthodes recommandées et les actions d’amélioration du produit ne sont que partiellement remplies. Dans ces scénarios, vous seront remis une partie du score maximale disponible pour le contrôle.

Chaque suggestion est évaluée en fonction de votre configuration Azure AD. Si vous utilisez des produits tiers pour activer des meilleures pratiques recommandées, vous pouvez indiquer cette configuration dans les paramètres d’une action d’amélioration du produit. Vous avez également la possibilité de définir les recommandations sont ignorées si elles ne s’appliquent à votre environnement. Les suggestions ignorées ne sont pas prises en compte dans le calcul de votre degré de sécurisation.

![Ignorer ou marquer action tel qu’indiqué par des tiers](./media/identity-secure-score/identity-secure-score-ignore-or-third-party-reccomendations.png)

## <a name="how-does-it-help-me"></a>En quoi cela peut-il m’aider ?

Le degré de sécurisation vous aide à accomplir les tâches suivantes :

- Évaluer votre méthode de sécurité relative aux identités de façon objective
- Planifier les améliorations à apporter à la sécurité des identités
- Évaluer la réussite de vos améliorations

## <a name="what-you-should-know"></a>Ce que vous devez savoir

### <a name="who-can-use-the-identity-secure-score"></a>Qui peut utiliser le degré de sécurisation Identity Secure Score ?

Le degré de sécurisation Identity Secure Score peut être utilisé par les rôles suivants :

- Administrateur général
- Administrateur de la sécurité
- Lecteurs Sécurité

### <a name="how-are-controls-scored"></a>Comment les contrôles sont notées ?

Les contrôles peuvent être évaluées de deux manières. Certaines sont évaluées de façon binaire : vous obtenez 100 % du score de si vous disposez de la fonctionnalité ou paramètre configuré selon notre recommandation. Autres scores sont calculés sous forme de pourcentage de la configuration de total. Par exemple, si la recommandation d’amélioration du produit indique que vous obtiendrez 30 points si vous protégez tous vos utilisateurs avec l’authentification Multifacteur et il vous suffit 5 de 100 utilisateurs total protégés, vous recevra un score partiels environ 2 points (5 protégé / nombre total de 100 * 30 pts maximales = 2 score partielle pts) .

### <a name="what-does-not-scored-mean"></a>Que signifie la mention [Not Scored] (Degré non calculé) ?

Les actions accompagnées de la mention [Not Scored] (Degré non calculé) sont les actions que vous pouvez mettre en œuvre dans votre organisation, mais dont le degré de sécurisation n’est pas calculé, car elles ne sont pas (encore) intégrées à l’outil. Par conséquent, vous pouvez toujours améliorer votre sécurité, mais vous n’obtenez pas les points correspondant aux actions mises en œuvre pour le moment.

### <a name="how-often-is-my-score-updated"></a>Quelle est la fréquence de mise à jour de mon degré de sécurisation ?

Le degré de sécurisation est calculé une fois par jour (vers 1 h PST). Si vous apportez une modification à une action évaluée, le degré de sécurisation est automatiquement mis à jour le lendemain. La répercussion d’une modification dans le degré de sécurisation peut prendre jusqu’à 48 heures.

### <a name="my-score-changed-how-do-i-figure-out-why"></a>Mon degré de sécurisation a changé. Comment savoir pourquoi ?

Rendez-vous sur le [centre de sécurité Microsoft 365](https://security.microsoft.com/), où vous trouverez votre score sécurisée Microsoft complète. Vous pouvez facilement voir toutes les modifications à votre score sécurisé, passez en revue les modifications détaillées sur l’onglet Historique.

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>Le score sécurisé mesure mon risque d’obtention de violation ?

Non. Le score sécurisé ne signifie pas une mesure absolue de la probabilité que vous êtes obtenir pas respectée. Il indique dans quelle mesure vous appliquez des fonctionnalités qui peuvent atténuer le risque de violation. Aucun service ne peut garantir que vous ne serez pas compromise, et le score sécurisé ne doit pas être interprété comme une garantie de quelque manière.

### <a name="how-should-i-interpret-my-score"></a>Comment dois-je interpréter mon degré de sécurisation ?

Vous obtenez des points lorsque vous configurez des fonctionnalités de sécurité suggérées ou mettez en œuvre des tâches liées à la sécurité (par exemple, la lecture de rapports). Certaines actions sont évaluées dans le cadre d’une mise en œuvre partielle, par exemple lorsque vous activez l’authentification multifacteur (MFA) pour vos utilisateurs. Votre score sécurisé est directement représentant des services de sécurité Microsoft que vous utilisez. N’oubliez pas que la sécurité doit être équilibrée avec facilité d’utilisation. Tous les contrôles de sécurité ont une forme d’impact sur l’utilisateur. Les contrôles ayant un faible impact sur l’utilisateur doivent avoir peu d’effet (voire aucun) sur les opérations quotidiennes de vos utilisateurs.

Pour afficher votre historique de score, rendez-vous sur le [centre de sécurité Microsoft 365](https://security.microsoft.com/) et passez en revue votre score est sécurisé Microsoft. Vous pouvez examiner les modifications à votre ensemble score sécurisé être en cliquant sur Afficher l’historique. Choisissez une date spécifique pour déterminer les contrôles qui ont été activés ce jour et les points que vous avez gagnés pour chacun d’eux.

### <a name="how-does-the-identity-secure-score-relate-to-the-office-365-secure-score"></a>Quel est le lien entre les degrés de sécurisation Identity Secure Score et Office 365 Secure Score ?

Le [Microsoft secure score](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score) contient cinq catégories de score et de contrôle distincte :

- Identité
- Données
- Appareils
- Infrastructure
- Applications

Le score d’identité sécurisée représente la partie de l’identité du score sécurisé Microsoft. Ce chevauchement signifie que vos recommandations pour l’identité secure score et le score d’identité dans Microsoft est les mêmes.

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur le score sécurisée Microsoft](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score)
