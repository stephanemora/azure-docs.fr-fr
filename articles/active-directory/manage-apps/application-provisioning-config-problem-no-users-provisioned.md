---
title: Aucun utilisateur n’est approvisionné dans une application de la galerie Azure AD | Microsoft Docs
description: Résolution des problèmes courants rencontrés lorsque les utilisateurs ne figurent pas dans une application de la galerie Azure AD que vous avez configurée pour l’approvisionnement d’utilisateurs avec Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6e74ad04f10865a830d27c1814be10eeff3ad59
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60443213"
---
# <a name="no-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Aucun utilisateur n’est approvisionné dans une application de la galerie Azure AD
Une fois que l’approvisionnement automatique a été configuré pour une application (avec notamment la vérification des informations d’identification de l’application fournies à Azure AD pour la connexion à l’application), les utilisateurs et/ou groupes sont approvisionnés sur l’application. L’approvisionnement est déterminé par les éléments suivants :

-   Quels utilisateurs et groupes ont été **affectés** à l’application. Pour plus d’informations sur l’affectation, consultez [Affecter un utilisateur ou un groupe à une application d’entreprise dans Azure Active Directory](assign-user-or-group-access-portal.md).
-   Les **mappages d’attributs** sont-ils activés et configurés pour synchroniser les attributs valides d’Azure AD avec l’application. Pour plus de détails sur les mappages d’attributs, consultez [Personnalisation des mappages d’attributs d’approvisionnement d’utilisateurs pour les applications SaaS dans Azure Active Directory](customize-application-attributes.md).
-   Un **filtre d’étendue** permet-il de filtrer les utilisateurs en fonction de valeurs d’attribut spécifiques. Pour plus d’informations sur les filtres d’étendue, consultez [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](define-conditional-rules-for-provisioning-user-accounts.md).
  
Si vous constatez que les utilisateurs ne sont pas approvisionnés, consultez les journaux d’audit dans Azure AD. Recherchez les entrées de journal d’un utilisateur spécifique.

Les journaux d’audit d’approvisionnement sont accessibles dans le portail Azure, sous l’onglet **Azure Active Directory &gt; Applications Entreprise &gt; \[Nom de l’application\] &gt; Journaux d’audit**. Filtrez les journaux sur la catégorie **Approvisionnement de comptes** pour afficher uniquement les événements d’approvisionnement de cette application. Vous pouvez rechercher des utilisateurs en fonction de l’ID de correspondance qui a été configuré pour eux dans les mappages d’attributs. Par exemple, si vous avez configuré le « nom d’utilisateur principal » ou « l’adresse e-mail » en tant qu’attribut correspondant côté Azure AD, et si l’utilisateur qui n’est pas approvisionné a la valeur « audrey@contoso.com », recherchez les journaux d’audit correspondant à « audrey@contoso.com » et passez en revue les entrées renvoyées.

Les journaux d’audit d’approvisionnement enregistrent toutes les opérations effectuées par le service d’approvisionnement, y compris l’interrogation d’Azure AD concernant les utilisateurs qui se trouvent dans l’étendue de l’approvisionnement, l’interrogation de l’application cible concernant l’existence de ces utilisateurs, et la comparaison des objets utilisateur du système. Ajoutez ensuite, mettez à jour ou désactivez le compte d’utilisateur dans le système cible en fonction de cette comparaison.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Problèmes généraux d’approvisionnement à prendre en compte
Voici une liste des problèmes généraux que vous pouvez explorer si vous savez par où commencer.

- [Le service d’approvisionnement ne semble pas démarrer](#provisioning-service-does-not-appear-to-start)
- [Les journaux d’audit indiquent que les utilisateurs sont ignorés et non approvisionnés, bien qu’ils soient affectés](#audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Le service d’approvisionnement ne semble pas démarrer
Si vous définissez le paramètre **État de la configuration** sur **Activé** dans la section **Azure Active Directory &gt; Applications Entreprise &gt; \[Nom de l’application\] &gt;Approvisionnement** du portail Azure. Cependant, si aucune autre information d’état n’est affichée sur cette page après de nouveaux chargements, il est probable que le service est en cours d’exécution mais qu’il n’a pas encore terminé la synchronisation initiale. Vérifiez les **journaux d’audit** décrits ci-dessus pour déterminer les opérations effectuées par le service, et la présence éventuelle d’erreurs.

>[!NOTE]
>Une synchronisation initiale peut prendre de 20 minutes à plusieurs heures, en fonction de la taille de l’annuaire Azure AD et du nombre d’utilisateurs présents dans l’étendue de l’approvisionnement. Les synchronisations qui suivent la synchronisation initiale sont plus rapides, car le service d’approvisionnement stocke les filigranes qui représentent l’état des deux systèmes après la synchronisation initiale. La synchronisation initiale améliore les performances des synchronisations suivantes.
>


## <a name="audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Les journaux d’audit indiquent que les utilisateurs sont ignorés et non approvisionnés, bien qu’ils soient affectés

Lorsqu’un utilisateur apparaît comme « ignoré » dans les journaux d’audit, il est essentiel de consulter les détails du message du journal d’activité pour en déterminer la raison. Voici les raisons les plus courantes et les solutions correspondantes :

- **Un filtre d’étendue a été configuré** **et exclut l’utilisateur en fonction d’une valeur d’attribut**. Pour plus d’informations sur les filtres d’étendue, consultez [Filtres d’étendue](define-conditional-rules-for-provisioning-user-accounts.md).
- **L’utilisateur n’est pas « autorisé de manière effective ».** Ce message d’erreur indique un problème concernant l’enregistrement d’affectation d’utilisateurs stocké dans Azure AD. Pour résoudre ce problème, supprimez l’assignation de l’utilisateur (ou du groupe) de l’application, puis réassignez-le. Pour plus d’informations sur l’assignation, consultez [Affecter un utilisateur ou un groupe à une application d’entreprise dans Azure Active Directory](assign-user-or-group-access-portal.md).
- **Un attribut requis manque ou n’est pas indiqué pour un utilisateur.** Lors de la configuration de l’approvisionnement, il est important de vérifier et configurer les mappages d’attributs et les workflows qui définissent les propriétés de l’utilisateur (ou du groupe) passant d’Azure AD à l’application. Cette configuration inclut la définition d’une « propriété correspondante » réservée à l’identification et à la mise en correspondance des utilisateurs/groupes entre les deux systèmes. Pour plus de détails sur ce processus important, consultez [Personnalisation des mappages d’attributs d’approvisionnement d’utilisateurs pour les applications SaaS dans Azure Active Directory](customize-application-attributes.md).
- **Mappage d’attributs pour les groupes :** Approvisionnement du nom du groupe et des détails du groupe, en plus des membres, si la prise en charge est effective pour certaines applications. Vous pouvez activer ou désactiver cette fonctionnalité en activant ou désactivant la **mappage** pour les objets de groupe affichés dans l’onglet **Approvisionnement**. Si les groupes d’approvisionnement sont activés, veillez à passer en revue les mappages d’attributs afin de vous assurer qu’un champ approprié est utilisé pour l’« ID correspondant ». L’ID correspondant peut être le nom d’affichage ou l’alias de messagerie. Le groupe et ses membres ne sont pas approvisionnés si la propriété correspondante est vide ou n’est pas renseignée pour un groupe dans Azure AD.

## <a name="next-steps"></a>Étapes suivantes

[Synchronisation Azure AD Connect : présentation du provisionnement déclaratif](../hybrid/concept-azure-ad-connect-sync-declarative-provisioning.md)
