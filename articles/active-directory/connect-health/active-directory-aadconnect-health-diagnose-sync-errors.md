---
title: Azure AD Connect Health - Diagnostiquer des erreurs de synchronisation d’attribut en double | Microsoft Docs
description: Ce document décrit le processus de diagnostic des erreurs de synchronisation d’attribut en double et le correctif potentiel des scénarios d’objets orphelins directement depuis le portail Azure.
services: active-directory
documentationcenter: ''
author: zhiweiw
manager: maheshu
editor: billmath
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: zhiweiw
ms.openlocfilehash: 0a345fd3443fb33d6f5912c8a04677091e20ecc8
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34305597"
---
# <a name="duplicate-attribute-sync-errors-diagnosis-and-remediation"></a>Diagnostic et remède des erreurs de synchronisation d’attribut en double 

## <a name="overview"></a>Vue d'ensemble
Allant plus loin dans la mise en évidence des erreurs de synchronisation, Azure Active Directory Connect Health permet la correction en libre-service des erreurs de synchronisation d’attribut en double et des objets orphelins d’Azure AD. L’avantage clé de la fonctionnalité de diagnostic :
- Fournir la procédure de diagnostic pour limiter les scénarios d’erreurs de synchronisation d’attribut en double et indiquer des solutions spécifiques
- Appliquer le correctif pour les scénarios dédiés d’Azure AD pour résoudre l’erreur en seul clic
- Aucune mise à niveau ou configuration n’est nécessaire pour activer cette fonctionnalité.
Obtenir plus de détails sur la [résilience en double](https://aka.ms/dupattributeresdocs) de Azure AD.

## <a name="problems"></a>Problèmes
### <a name="common-scenario"></a>Scénario courant
Lorsque les erreurs de synchronisation **QuarantinedAttributeValueMustBeUnique** et **AttributeValueMustBeUnique** se produisent, il est courant de voir des conflits de nom d’utilisateur principal ou d’adresses proxy dans Azure AD. Vous pouvez résoudre les erreurs de synchronisation en mettant à jour l’objet source en conflit depuis le côté local. L’erreur de synchronisation sera résolue après la synchronisation suivante. Par exemple, l’image ci-dessous indique que deux utilisateurs rencontrent des conflits de leur UserPrincipalName comme *Joe.J@contoso.com*. Les objets en conflit sont mis en quarantaine dans Azure AD. 

![Diagnostiquer le scénario courant d’erreur de synchronisation](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixCommonCase.png)

### <a name="orphaned-object-scenario"></a>Scénario d’objet orphelin
Parfois, vous pouvez trouver qu'un utilisateur perd l’ancre source. La suppression de l’objet source s’est produite sur l’AD local, mais la modification du signal de suppression n’a jamais été synchronisée avec Azure AD. Plusieurs raisons peuvent en être la cause, comme un problème de moteur de synchronisation ou de migration de domaine. Lorsque le même objet a été restauré ou recréé, l’utilisateur logique existant doit correspondre à l’utilisateur à synchroniser à partir de l’ancre Source. Pour un utilisateur existant en tant qu’objet cloud uniquement, vous pouvez également voir un utilisateur en conflit synchronisé avec Azure AD et qui ne peut pas être mis en correspondance avec l’objet existant lors de la synchronisation. Il n’existe aucun moyen direct pour remapper l’ancre Source. En savoir plus sur le [KB existant](https://support.microsoft.com/help/2647098). Par exemple, l’objet existant dans Azure AD préserve la licence de Jacques. Un objet qui vient d’être synchronisé avec une ancre Source différente a engendré un état d’attribut en double dans Azure AD. Les modifications de Jacques sur l’AD local ne pourront pas être appliquées à l’utilisateur d’origine de Jacques (objet existant) dans Azure AD.  

![Diagnostiquer une erreur de synchronisation (scénario d’objet orphelin)](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)

## <a name="diagnostic-and-troubleshooting-steps-in-connect-health"></a>Étapes de diagnostic et de dépannage dans Connect Health 
La fonctionnalité de diagnostic prend en charge les objets utilisateur avec les attributs dupliqués suivants :

| Nom de l'attribut | Types d’erreur de synchronisation|
| ------------------ | -----------------|
| UserPrincipalName | QuarantinedAttributeValueMustBeUnique ou AttributeValueMustBeUnique | 
| ProxyAddresses | QuarantinedAttributeValueMustBeUnique ou AttributeValueMustBeUnique | 
| SipProxyAddress | AttributeValueMustBeUnique | 
| OnPremiseSecurityIdentifier |  AttributeValueMustBeUnique |

>[!IMPORTANT]
> Il requiert une autorisation **Administrateur global** ou **Contributeur** dans les paramètres RBAC pour pouvoir accéder à cette fonctionnalité. 
>

En suivant les étapes à partir du portail Azure, vous pourrez affiner les détails de l’erreur de synchronisation et fournir des solutions plus précises :

![Étapes de diagnostic d’erreur de synchronisation](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixSteps.png)

À partir du portail Azure, vous serez en mesure de suivre quelques étapes pour identifier des scénarios de réparation spécifiques :  
1.  Dans la colonne d’état du diagnostic, l’état s’affiche s’il existe un flux potentiel de résolution de problèmes pour cibler l’erreur et essayer de la corriger directement depuis Azure Active Directory.
| Statut | Qu’est-ce que cela signifie ? |
| ------------------ | -----------------|
| Non commencé | Vous n’avez pas visité ce processus de diagnostic. Selon le résultat du diagnostic, il existe un moyen possible de résoudre l’erreur de synchronisation directement à partir du portail. |
| Correction manuelle requise | L’erreur ne correspond pas aux critères du correctif disponible à partir du portail. Le cas peut être que (1) les types d’objets en conflit ne sont pas des utilisateurs, (2) vous avez déjà parcouru les étapes de diagnostic et aucune résolution n’est disponible à partir du portail. Dans ce cas, le correctif du côté local sera l’une des solutions. [En savoir plus sur le correctif local](https://support.microsoft.com/help/2647098) | 
| En attente de synchronisation | Un correctif a été appliqué. En attente du prochain cycle de synchronisation pour effacer l’erreur. |
>[!IMPORTANT]
> La colonne d’état de diagnostic est réinitialisée après chaque cycle de synchronisation. 
>

2.  En cliquant sur le bouton **Diagnostiquer** dans le panneau des détails de l’erreur, vous devrez répondre à quelques questions et identifier les détails de l’erreur de synchronisation. Répondre aux questions aidera à identifier le cas du scénario de l’objet orphelin. 

3.  S’il existe un bouton **Fermer** à la fin du diagnostic, cela signifie qu’il n’existe aucune solution rapide disponible à partir du portail, basée sur les réponses données. Reportez-vous à la solution décrite dans la dernière étape. La correction locale sera toujours une solution. Après avoir cliqué sur le bouton Fermer, l’état de l’erreur de synchronisation actuelle passera à **Correction manuelle requise**. L’état reste identique lors du cycle de synchronisation en cours.

4.  Une fois que le cas de l’objet orphelin est identifié, vous serez en mesure de résoudre les erreurs de synchronisation d’attribut en double directement à partir du portail. Cliquez sur le bouton **Appliquer le correctif** pour déclencher le processus. L’état de l’erreur de synchronisation en cours sera mis à jour pour devenir **En attente de synchronisation**.

5.  Après le cycle de synchronisation suivant, l’erreur doit être supprimée de la liste.

## <a name="how-to-answer-the-diagnosis-questions"></a>Comment répondre aux questions de diagnostic 
### <a name="does-the-user-exist-in-your-on-premises-active-directory"></a>Est-ce que l’utilisateur existe dans votre Active Directory local ?

La question tente d’identifier l’objet source d’un utilisateur existant à partir d’un Active Directory local.  
1.  Vérifiez si votre Active Directory contient un objet portant le UserPrincipalName indiqué. Si Non, répondez Non.
2.  Si Oui, vérifiez si l’objet est encore concerné par la synchronisation.  
  - Recherche dans l'espace connecteur Microsoft Azure Active Directory avec le nom de domaine.
  - Si l’objet a été trouvé avec l’état **Ajout en attente**, répondez Non. Azure AD Connect n’est pas en mesure de connecter l’objet à l’objet AD approprié.
  - Si l’objet n’est pas trouvé, répondez Oui.

> En prenant le diagramme suivant, par exemple, la question tente d’identifier si *Joe Jackson* existe toujours dans l’Active Directory local.
Pour le **scénario courant**, les deux utilisateurs *Joe Johnson* et *Joe Jackson* seront présents dans votre Active Directory local. Les objets mis en quarantaine sont deux utilisateurs différents.

![Diagnostiquer le scénario courant d’erreur de synchronisation](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixCommonCase.png)

> Pour le **scénario de l’objet orphelin**, un seul utilisateur (*Joe Johnson*) sera présent dans le Active Directory local :

![Diagnostiquer une erreur de synchronisation (scénario d’objet orphelin)](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)

### <a name="do-both-these-accounts-belong-to-the-same-user"></a>Ces deux comptes appartiennent-ils au même utilisateur ?
La question vérifie si l’utilisateur entrant en conflit et l’objet utilisateur existant dans Azure AD appartiennent au même utilisateur.  
1.  L’objet en conflit est synchronisé avec Azure Active Directory. Comparez l’objet à partir de son :  
  - Nom d’affichage
  - Nom d’utilisateur principal
  - ID objet
2.  En cas d’échec de leur comparaison, vérifiez que votre Active Directory contient des objets portant les UserPrincipalNames indiqués. Répondez NON si les deux sont trouvés.  

> Dans le cas ci-dessous, les deux objets appartiennent au même utilisateur (*Joe Johnson*).

![Diagnostiquer une erreur de synchronisation (scénario d’objet orphelin)](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)


## <a name="what-happened-after-fix-is-applied-for-orphaned-object-scenario"></a>Que s’est-il passé une fois le correctif appliqué pour le scénario de l’objet orphelin
Selon les réponses aux questions déclenchées, vous serez en mesure de voir le bouton **Appliquer un correctif** lorsqu’un correctif est disponible à partir d’Azure AD. Dans ce cas, l’objet local est synchronisé avec un objet Azure Active AD inattendu. Les deux objets sont mappés à l’aide de l’ancre Source. Le changement appliqué entraînera des opérations telles que :
- La mise à jour de l’ancre Source à l’objet correct dans Azure AD.
- La suppression de l’objet en conflit dans Azure AD, s’il est présent.

![Diagnostiquer l’erreur de synchronisation après la correction](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixAfterFix.png)

>[!IMPORTANT]
> Le changement Appliquer le correctif s’applique uniquement aux cas de l’objet orphelin.
>

Après les étapes ci-dessus, l’utilisateur sera en mesure d’accéder à la ressource d’origine, qui est le lien vers l’objet existant. La valeur **État de diagnostic** dans la liste sera mise à jour avec l’état **En attente de synchronisation**. L’erreur de synchronisation sera résolue après la synchronisation suivante. Connect Health n’affichera plus l’erreur de synchronisation résolue dans la liste. 


## <a name="faq"></a>Forum Aux Questions
 -  Que se passe-t-il en cas d’échec de l’exécution de l’application ?  
Si l’exécution échoue, il est possible qu’Azure AD Connect fasse l’objet d’une erreur d’exportation. Actualisez la page du portail et recommencez après la synchronisation suivante. Le cycle de synchronisation par défaut est de 30 minutes. 

 -  Que se passe-t-il si l’**objet existant** est l’objet à supprimer ?  
Si l’objet existant doit être supprimé, le processus n’implique pas de changement de l’ancre Source. Vous devez être en mesure de résoudre cela depuis votre AD local.  

 -  Quelle est l’autorisation dont doit disposer l’utilisateur pour pouvoir appliquer le correctif ?  
L’administrateur global ou le collaborateur à partir des paramètres RBAC aura l’autorisation d’accès au diagnostic et au processus de dépannage.

 -  Est-ce que je dois configurer AAD Connect ou mettre à jour l’agent Azure AD Connect Health pour cette fonctionnalité ?  
Non, le processus de diagnostic est une fonctionnalité entièrement basée sur le cloud.

 -  Si l’objet existant est supprimé de façon réversible, est-ce que le processus de diagnostic restaurera l’état actif de l’objet ?  
Non, le correctif mettra à jour uniquement l’attribut d’objet Ancre Source. 
