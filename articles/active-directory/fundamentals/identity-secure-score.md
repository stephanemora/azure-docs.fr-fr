---
title: Qu’est-ce que le degré de sécurisation Identity Secure Score dans Azure AD ? - préversion | Microsoft Docs
description: Découvrez comment utiliser le degré de sécurisation Identity Secure Score pour améliorer la méthode de sécurité de votre locataire Azure AD.
services: active-directory
keywords: Identity Secure Score, Azure AD, sécuriser l’accès aux ressources de l’entreprise
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: fundamentals
ms.topic: overview
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: a9bb22cae2e9fcd607a81d442f4fb51f12c43454
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47224135"
---
# <a name="what-is-the-identity-secure-score-in-azure-ad---preview"></a>Qu’est-ce que le degré de sécurisation Identity Secure Score dans Azure AD ? - préversion

Dans quelle mesure votre locataire Azure AD est-il sécurisé ? Si vous ne savez pas comment répondre à cette question, lisez cet article pour savoir comment le degré de sécurisation Identity Secure Score vous aide à surveiller et améliorer votre méthode de sécurité relative aux identités. 

## <a name="what-is-an-identity-secure-score"></a>Qu’est-ce qu’un degré de sécurisation Identity Secure Score ?

Le degré de sécurisation Identity Secure Score est un nombre compris entre 1 et 248 qui indique dans quelle mesure vous respectez les suggestions de meilleures pratiques de Microsoft en matière de sécurité.


![Degré de sécurisation](./media/identity-secure-score/01.png)



Le degré de sécurisation vous aide à accomplir les tâches suivantes :

- Évaluer votre méthode de sécurité relative aux identités de façon objective

- Planifier les améliorations à apporter à la sécurité des identités

- Évaluer la réussite de vos améliorations 


Vous pouvez accéder au degré de sécurisation et aux informations correspondantes dans le tableau de bord du degré de sécurisation Identity Secure Score. Ce tableau de bord présente les informations suivantes :

- Votre degré de sécurisation

    ![Degré de sécurisation](./media/identity-secure-score/02.png)

- Un graphique de comparaison

    ![Degré de sécurisation](./media/identity-secure-score/03.png)

- Un graphique de tendance

    ![Degré de sécurisation](./media/identity-secure-score/04.png)

- Une liste des meilleures pratiques relatives à la sécurité des identités 

    ![Degré de sécurisation](./media/identity-secure-score/05.png)


La mise en œuvre des actions d’amélioration vous offre les possibilités suivantes :

- Améliorer votre méthode de sécurité et votre degré de sécurisation
 
- Tirer parti des fonctionnalités d’identité de Microsoft 



## <a name="how-do-i-get-my-secure-score"></a>Comment faire pour obtenir mon degré de sécurisation ?

Le degré de sécurisation Identity Secure Score est disponible avec toutes les éditions d’Azure AD.

Pour obtenir votre degré de sécurisation, accédez au [tableau de bord de présentation Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/IdentitySecureScore).



## <a name="how-does-it-work"></a>Comment cela fonctionne-t-il ?

Toutes les 48 heures, Azure examine votre configuration de sécurité et compare vos paramètres avec ceux des meilleures pratiques suggérées. Selon le résultat de cette évaluation, un nouveau degré de sécurisation est calculé pour votre locataire. Ainsi, la répercussion d’une modification apportée à la configuration dans le degré de sécurisation peut prendre jusqu’à 48 heures. 

Chaque suggestion est évaluée en fonction de votre configuration Azure AD. Si vous utilisez des produits tiers pour mettre en œuvre une suggestion de meilleure pratique, vous pouvez l’indiquer dans les paramètres d’une action d’amélioration.

![Degré de sécurisation](./media/identity-secure-score/07.png)


En outre, vous avez la possibilité de définir les suggestions à ignorer si elles ne s’appliquent à votre environnement. Les suggestions ignorées ne sont pas prises en compte dans le calcul de votre degré de sécurisation. 
 
![Degré de sécurisation](./media/identity-secure-score/06.png)



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

### <a name="what-does-not-scored-mean"></a>Que signifie la mention [Not Scored] (Degré non calculé) ?

Les actions accompagnées de la mention [Not Scored] (Degré non calculé) sont les actions que vous pouvez mettre en œuvre dans votre organisation, mais dont le degré de sécurisation n’est pas calculé, car elles ne sont pas (encore) intégrées à l’outil. Par conséquent, vous pouvez toujours améliorer votre sécurité, mais vous n’obtenez pas les points correspondant aux actions mises en œuvre pour le moment.

### <a name="how-often-is-my-score-updated"></a>Quelle est la fréquence de mise à jour de mon degré de sécurisation ?

Le degré de sécurisation est calculé une fois par jour (vers 1 h PST). Si vous apportez une modification à une action évaluée, le degré de sécurisation est automatiquement mis à jour le lendemain. La répercussion d’une modification dans le degré de sécurisation peut prendre jusqu’à 48 heures.


### <a name="my-score-changed-how-do-i-figure-out-why"></a>Mon degré de sécurisation a changé. Comment savoir pourquoi ?

Sur la page de l’analyseur du degré de sécurisation du [portail de degré de sécurisation](https://securescore.microsoft.com/#!/score), cliquez sur un point de données pour un jour spécifique, puis faites défiler la page vers le bas pour faire apparaître les actions terminées et incomplètes pour ce jour et ainsi examiner ce qui a changé.

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>Le degré de sécurisation mesure-t-il le risque de violation ?

Non. Le degré de sécurisation ne fournit pas une mesure absolue de la probabilité d’être victime d’une violation. Il indique dans quelle mesure vous appliquez des fonctionnalités qui peuvent atténuer le risque de violation. Aucun service ne peut garantir que vous ne serez pas victime d’une violation. Le degré de sécurisation ne doit en aucun cas être interprété comme une garantie.

### <a name="how-should-i-interpret-my-score"></a>Comment dois-je interpréter mon degré de sécurisation ?

Vous obtenez des points lorsque vous configurez des fonctionnalités de sécurité suggérées ou mettez en œuvre des tâches liées à la sécurité (par exemple, la lecture de rapports). Certaines actions sont évaluées dans le cadre d’une mise en œuvre partielle, par exemple lorsque vous activez l’authentification multifacteur (MFA) pour vos utilisateurs. Votre degré de sécurisation représente de façon directe les services de sécurité Microsoft que vous utilisez. N’oubliez pas que vous devez toujours trouver le juste équilibre entre sécurité et facilité d’utilisation. Tous les contrôles de sécurité ont une forme d’impact sur l’utilisateur. Les contrôles ayant un faible impact sur l’utilisateur doivent avoir peu d’effet (voire aucun) sur les opérations quotidiennes de vos utilisateurs.

Pour afficher l’historique de votre degré de sécurisation, accédez à la page de l’analyseur du degré de sécurisation du [portail de degré de sécurisation](https://securescore.microsoft.com/#!/score). Choisissez une date spécifique pour déterminer les contrôles qui ont été activés ce jour et les points que vous avez gagnés pour chacun d’eux.


### <a name="how-does-the-identity-secure-score-relate-to-the-office-365-secure-score"></a>Quel est le lien entre les degrés de sécurisation Identity Secure Score et Office 365 Secure Score ? 

Le [degré de sécurisation Office 365 Secure Score](https://docs.microsoft.com/office365/securitycompliance/office-365-secure-score) va bientôt migrer vers un agrégat de cinq degrés différents :

- Identité

- Données

- Appareils

- Infrastructure

- Applications

Le degré de sécurisation Identity Secure Score représente la partie identité du degré de sécurisation Office 365 Secure Score. Ainsi, vos suggestions relatives au degré de sécurisation Identity Secure Score et au degré de sécurisation des identités dans Office 365 sont les mêmes. 


## <a name="next-steps"></a>Étapes suivantes

Cliquez sur [ici](https://www.youtube.com/watch?v=jzfpDJ9Kg-A) pour voir une vidéo sur le degré de sécurisation Office 365 Secure Score.
 
