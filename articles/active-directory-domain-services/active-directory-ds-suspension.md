---
title: 'Azure Active Directory Domain Services : domaines suspendus | Microsoft Docs'
description: Suspension et suppression des domaines managés
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: ergreenl
ms.openlocfilehash: 6448e592db8024d911831b0b3634dabb4ed3b101
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47063113"
---
# <a name="suspended-domains"></a>Domaines suspendus
Quand Azure AD Domain Services (Azure AD DS) ne parvient pas à mettre en service un domaine managé pendant une longue période, ça met ce domaine en état de suspension. Cet article explique pourquoi les domaines managés sont suspendus et comment remédier à la suspension d’un domaine.


## <a name="states-your-managed-domain-can-be-in"></a>Différents états des domaines managés

![Chronologie de la suspension des domaines](media\active-directory-domain-services-suspension\suspension-timeline.PNG)

Le graphique ci-dessus décrit les états possibles d’un domaine managé Azure AD DS.

### <a name="running-state"></a>État « En cours d’exécution »
Un domaine managé qui est configuré correctement et qui fonctionne régulièrement a l’état **En cours d’exécution**.

**À quoi s’attendre ?**
* Microsoft peut surveiller régulièrement l’intégrité de votre domaine managé.
* Les contrôleurs de domaine de votre domaine managé sont corrigés et mis à jour régulièrement.
* Les modifications effectuées dans Azure Active Directory sont régulièrement synchronisées avec votre domaine managé.
* Des sauvegardes de votre domaine managé sont effectuées régulièrement.


### <a name="needs-attention-state"></a>État « Doit être surveillé »
Un domaine managé passe à l’état **Doit être surveillé** si un ou plusieurs problèmes doivent être résolus par l’administrateur. La page d’intégrité de votre domaine managé répertorie les alertes liées à cet état. 

Par exemple, si vous avez configuré un groupe de sécurité réseau restrictif pour votre réseau virtuel, Microsoft peut être incapable de mettre à jour et de surveiller votre domaine managé. Cette configuration non valide déclenche une alerte qui met votre domaine géré dans l’état « Doit être surveillé ».

Chaque alerte est associée à une procédure de résolution. Certaines alertes sont temporaires et sont résolues automatiquement par le service. Vous pouvez résoudre d’autres alertes en suivant les instructions fournies dans la procédure de résolution associée à l’alerte. Pour résoudre certaines alertes critiques, vous devez contacter le support Microsoft afin d’obtenir une résolution.

Pour plus d’informations, consultez [Résolution des alertes liées aux domaines managés](active-directory-ds-troubleshoot-alerts.md).

**À quoi s’attendre ?**

Dans certains cas (par exemple, si vous avez une configuration réseau non valide), les contrôleurs de domaine de votre domaine managé peuvent être inaccessibles. Lorsque votre domaine managé est dans l’état « Doit être surveillé », Microsoft ne peut pas garantir qu’il sera surveillé, corrigé, mis à jour et sauvegardé régulièrement.

* L’état de votre domaine managé n’est pas sain et la surveillance continue de l’intégrité peut s’arrêter jusqu’à la résolution de l’alerte.
* Les contrôleurs de domaine de votre domaine managé peuvent ne pas être corrigés et mis à jour.
* Les modifications effectuées dans Azure Active Directory peuvent ne pas être synchronisées avec votre domaine managé.
* Des sauvegardes de votre domaine managé peuvent être effectuées, si cela est possible.
* Si vous résolvez les alertes qui impactent votre domaine managé, vous pourrez peut-être le restaurer dans l’état « En cours d’exécution ».
* Les alertes critiques sont déclenchées en cas de problèmes de configuration où Microsoft est incapable d’atteindre vos contrôleurs de domaine. Si ces alertes ne sont pas résolues dans les quinze jours, votre domaine managé est placé à l’état « Suspendu ».


### <a name="the-suspended-state"></a>État « Suspendu »
Un domaine managé est placé à l’état **Suspendu** dans les cas suivants :

* Une ou plusieurs alertes critiques n’ont pas été résolues au bout de 15 jours. Les alertes critiques peuvent être dues à une configuration incorrecte qui bloque l’accès aux ressources dont a besoin Azure AD DS.
    * Par exemple, l’alerte [AADDS104 : erreur réseau](active-directory-ds-troubleshoot-nsg.md) n’a pas été résolue pendant plus de 15 jours dans le domaine managé.
* Il y a un problème de facturation avec votre abonnement Azure ou votre abonnement Azure a expiré.

Les domaines managés sont suspendus quand Microsoft ne parvient pas à gérer, surveiller, corriger ou sauvegarder le domaine en continu.

**À quoi s’attendre ?**
* Les contrôleurs de domaine de votre domaine managé sont déprovisionnés et ne sont plus accessibles dans le réseau virtuel.
* L’accès LDAP sécurisé à votre domaine managé via Internet (s’il est activé) cesse de fonctionner.
* Vous remarquez des échecs lors de l’authentification au domaine managé, lors de la connexion aux machines virtuelles jointes à un domaine ou lors des connexions via LDAP/LDAPS.
* Les sauvegardes de votre domaine managé ne sont plus effectuées.
* La synchronisation avec Azure AD cesse.

Après avoir résolu l’alerte, votre domaine managé va à l’état « Suspendu ». Puis vous devrez contacter le support technique.
L’équipe du support ne pourra restaurer votre domaine managé uniquement s’il existe une sauvegarde datant de moins de 30 jours.

Le domaine managé reste à l’état Suspendu pendant 15 jours seulement. Pour récupérer votre domaine managé, Microsoft vous recommande de résoudre les alertes critiques immédiatement.


### <a name="deleted-state"></a>État « Supprimé »
Un domaine managé qui reste à l’état « Suspendu » pendant plus de 15 jours passe à l’état **Supprimé**.

**À quoi s’attendre ?**
* Toutes les ressources et sauvegardes du domaine managé sont supprimées.
* Dans ce cas, vous ne pouvez pas restaurer le domaine managé et devez créer un domaine managé afin de pouvoir utiliser Azure AD DS.
* Lorsque le domaine managé est supprimé, vous n’êtes plus facturé pour celui-ci.


## <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>Comment savoir si votre domaine managé a été suspendu ?
Vous voyez une [alerte](active-directory-ds-troubleshoot-alerts.md) dans la page d’intégrité Azure AD DS du portail Azure, qui indique que le domaine est suspendu. L’état du domaine indique également « Suspendu ».


## <a name="restore-a-suspended-domain"></a>Restaurer un domaine suspendu
Pour restaurer un domaine à l’état « Suspendu », effectuez les étapes suivantes :

1. Allez sur la [page Azure Active Directory Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) dans le portail Azure.
2. Sélectionnez le domaine managé.
3. Dans le volet gauche, sélectionnez **Intégrité**.
4. Sélectionner l’alerte. L’ID de l’alerte sera AADDS503 ou AADDS504, selon la cause de la suspension.
5. Sélectionnez le lien de résolution qui est fourni dans l’alerte. Pour résoudre l’alerte, procédez comme suit.

Votre domaine managé peut uniquement être restauré à la date de la dernière sauvegarde. La date de la dernière sauvegarde est affichée dans la page Intégrité de votre domaine managé. Toutes les modifications apportées après la dernière sauvegarde ne pourront pas être restaurées. Les sauvegardes de domaine managé sont stockées pendant 30 jours. Les sauvegardes datant de plus de 30 jours sont supprimées.


## <a name="next-steps"></a>Étapes suivantes
- [Résoudre les alertes sur votre domaine managé](active-directory-ds-troubleshoot-alerts.md)
- [En savoir plus sur Azure Active Directory Domain Services](active-directory-ds-overview.md)
- [Contacter l’équipe produit](active-directory-ds-contact-us.md)

## <a name="contact-us"></a>Nous contacter
Contactez l’équipe produit des Services de domaine Azure Active Directory pour [partager vos commentaires ou pour obtenir de l’aide](active-directory-ds-contact-us.md).
