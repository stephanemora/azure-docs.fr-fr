---
title: Créer une révision d’accès pour les membres d’un groupe ou des utilisateurs ayant accès à une application avec Azure AD| Microsoft Docs
description: Découvrez comment créer une révision d’accès pour les membres d’un groupe ou des utilisateurs ayant accès à une application.
services: active-directory
author: markwahl-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2018
ms.author: billmath
ms.openlocfilehash: 784a461421420af403a43f944d6f63aef3ccc152
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195605"
---
# <a name="create-an-access-review-of-group-members-or-application-access-with-azure-ad"></a>Créer une révision d’accès des membres du groupe ou un accès aux applications avec Azure AD

Les attributions d’accès deviennent « obsolètes » lorsque les utilisateurs bénéficient d’un accès dont ils n’ont plus besoin. Afin de réduire les risques associés aux affectations d’accès par état, les administrateurs peuvent utiliser Azure Active Directory (Azure AD) pour créer une révision d’accès des membres du groupe ou des utilisateurs affectés à une application. La création de révisions d’accès récurrentes peut vous faire gagner du temps. Si vous devez régulièrement effectuer des révisions d’accès pour savoir qui a accès à une application ou qui sont les membres d’un groupe, vous pouvez définir la fréquence de ces révisions. Pour plus d’informations sur ces scénarios, voir [Gérer l’accès des utilisateurs](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md) et [Gérer l’accès des invités](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md). 

## <a name="create-an-access-review"></a>Créer une révision d’accès

1. En tant qu’administrateur général, accédez à la [page des révisions d’accès](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) et sélectionnez **Programmes**.

2. Sélectionnez le programme qui contient le contrôle de révision d’accès que vous souhaitez créer. Le **programme par défaut »** est toujours présent, mais vous pouvez aussi créer un autre programme. Par exemple, vous pouvez choisir de disposer d’un programme pour chaque initiative de conformité ou objectif de l’entreprise.

3. Dans le programme, sélectionnez **Contrôles**, puis sélectionnez **Ajouter** pour ajouter un contrôle.

4. Nommez la révision d’accès. Si vous le souhaitez, vous pouvez fournir une description de cette révision. Les réviseurs ont accès au nom et à la description de la révision.

5. Définissez la date de début. Par défaut, une révision d’accès se produit une seule fois, démarre le jour même de sa création et s’arrête un mois plus tard. Vous pouvez modifier les dates de début et de fin pour que la révision d’accès démarre ultérieurement et dure le nombre de jours souhaité.

6. Pour rendre une révision d’accès récurrente, configurez une fréquence hebdomadaire, mensuelle, trimestrielle ou annuelle, puis utilisez le curseur ou la zone de texte pour définir le nombre de jours que chaque révision d’une série doit rester ouverte pour permettre aux réviseurs d’y ajouter des commentaires. Par exemple, la durée maximale d’une révision mensuelle est de 27 jours, afin d’éviter le chevauchement des révisions. 

7.  Les séries de révisions d’accès récurrentes peuvent se terminer de trois manières : elles peuvent s’exécuter de façon continue pour démarrer les révisions indéfiniment, s’exécuter jusqu’à une date spécifique, ou s’exécuter jusqu’à ce qu’un nombre défini d’occurrences se soient produites. L’administrateur général peut arrêter la série après sa création en modifiant la date définie dans les paramètres.

8. Les révisions d’accès peuvent porter sur les membres d’un groupe ou sur les utilisateurs qui ont été assignés à une application. Vous pouvez affiner davantage la révision d’accès pour passer en revue uniquement les utilisateurs invités qui sont membres (ou assignés à l’application) au lieu d’examiner tous les utilisateurs qui sont membres ou qui ont accès à l’application.

9. Sélectionnez une ou plusieurs personnes pour passer en revue tous les utilisateurs concernés. Sinon, vous pouvez choisir de faire en sorte que les membres vérifient leur propre accès. Si la ressource est un groupe, vous pouvez demander aux propriétaires de groupe d’effectuer la révision. Vous pouvez également demander à ce que les réviseurs fournissent un motif lorsqu’ils approuvent l’accès.

10. Si vous souhaitez appliquer manuellement les résultats lorsque la révision se termine, cliquez sur **Démarrer**.  Sinon, la section suivante explique comment configurer l’application automatique de la révision.

### <a name="configuring-an-access-review-with-auto-apply"></a>Configuration de l’application automatique de la révision d’accès

1.  Développez le menu des paramètres de saisie semi-automatique, puis activez l’application automatique des résultats à la ressource. 

2.  Si des utilisateurs n’ont pas fait l’objet d’une révision pendant la période définie, vous pouvez suivre les recommandations du système (si celles-ci sont activées) concernant le refus ou l’approbation de l’accès continu des utilisateurs. Vous pouvez aussi maintenir leur accès tel qu’il est ou le supprimer. Cela n’aura pas d’impact sur les utilisateurs qui ont fait l’objet d’une révision manuelle. Si la décision du réviseur final est de refuser l’accès, l’accès de l’utilisateur sera supprimé.

3.  Pour activer l’option permettant de suivre les recommandations en l’absence d’une réponse des réviseurs, vous devez activer l’affichage des recommandations dans les paramètres avancés.
 
4.  Enfin, cliquez sur **Démarrer**.

En fonction de vos sélections dans les paramètres de saisie semi-automatique, l’application automatique est exécutée après la date de fin de la révision ou lorsque vous arrêtez manuellement la révision. La révision passe alors de l’état Terminé à divers états intermédiaires, tels que Application en cours, pour arriver enfin à l’état Appliqué. Les utilisateurs dont l’accès est refusé doivent perdre leur appartenance au groupe ou leur affectation d’applications au bout de quelques minutes.


## <a name="manage-the-access-review"></a>Gestion de la révision d’accès

Par défaut, Azure AD envoie un e-mail aux réviseurs peu de temps après le démarrage de la révision. Si vous ne souhaitez pas qu’Azure AD envoie cet e-mail, veillez à informer les réviseurs qu’une révision d’accès leur a été assignée. Vous pouvez leur afficher des instructions relatives à la [révision d’accès](active-directory-azure-ad-controls-perform-access-review.md). Si votre révision s’adresse à des invités qui doivent réviser leur propre accès, donnez-leur des instructions sur la [façon de réviser son propre accès](active-directory-azure-ad-controls-perform-access-review.md).

Si certains réviseurs sont invités, ces derniers sont uniquement notifiés par e-mail s’ils ont déjà accepté leur invitation.

Pour gérer une série de révisions d’accès, accédez à la révision d’accès dans **Contrôles**. Vous y voyez les occurrences à venir dans les révisions planifiées, pour lesquelles vous pouvez modifier la date de fin, ou ajouter/supprimer des réviseurs en conséquence. 

Vous pouvez suivre la progression des révisions des réviseurs dans le tableau de bord Azure AD, dans la section des **Révisions d’accès**. Aucun droit d’accès n’est modifié dans le répertoire avant que [la révision ne soit terminée](active-directory-azure-ad-controls-complete-access-review.md).

## <a name="next-steps"></a>Étapes suivantes

Lorsqu’une révision d’accès a démarré, Azure AD envoie automatiquement aux réviseurs un e-mail les invitant à réviser l’accès. Si un utilisateur n’a pas reçu d’e-mail, vous pouvez lui envoyer les instructions sur la [façon de réviser un accès](active-directory-azure-ad-controls-perform-access-review.md). 

S’il s’agit d’une révision unique, une fois la période de révision d’accès terminée, ou si l’administrateur interrompt la révision d’accès, suivez les étapes décrivant la [réalisation d’une révision d’accès](active-directory-azure-ad-controls-complete-access-review.md) pour voir et appliquer les résultats.  

S’il s’agit d’une série de révisions, accédez à **l’historique des révisions** dans la page de la série de révisions d’accès pour sélectionner une révision d’accès déjà terminée.  Les révisions à venir sont répertoriées sous **Révision prévue**, où vous pouvez modifier la durée de la révision, et ajouter ou supprimer des réviseurs pour chaque révision.


