---
title: Déterminer à quel moment un utilisateur spécifique pourra accéder à une application | Microsoft Docs
description: Comment déterminer à quel moment un utilisateur très important pourra accéder à une application que vous avez configurée pour l’approvisionnement des utilisateurs avec Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c6ad7e305958131c4f544dfa2022e7471e9adac
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147102"
---
# <a name="check-the-status-of-user-provisioning"></a>Vérifier l’état de l’approvisionnement d’utilisateurs

Le service d’approvisionnement d’Azure AD exécute un cycle d’approvisionnement initial sur le système source et le système cible, suivi de cycles incrémentiels périodiques. Lorsque vous configurez l’approvisionnement d’une application, vous pouvez vérifier l’état actuel du service d’approvisionnement et voir quand un utilisateur sera en mesure d’accéder à une application.

## <a name="view-the-provisioning-progress-bar"></a>Afficher la barre de progression de l’approvisionnement

 Sur la page **Approvisionnement** d’une application, vous pouvez afficher l’état du service d’approvisionnement d’Azure AD. La section **État actuel** en bas de la page indique si le cycle a démarré l’approvisionnement de comptes d’utilisateurs. Vous pouvez surveiller la progression du cycle, voir combien d’utilisateurs et de groupes ont été approvisionnés et combien de rôles sont créés.

Lorsque vous configurez pour la première fois l’approvisionnement automatique, la section **État actuel** au bas de la page affiche l’état initial du cycle d’approvisionnement. Cette section est mise à jour à chaque exécution d’un cycle incrémentiel. Les détails suivants sont affichés :
- Le type de cycle d’approvisionnement (initial ou incrémentiel) en cours d’exécution ou qui a été complété.
- Une **barre de progression** qui montre l’accomplissement de l’approvisionnement en pourcentage. Le pourcentage reflète le nombre de pages approvisionnées. Remarque : chaque page peut contenir plusieurs utilisateurs ou groupes. Le pourcentage ne reflète donc pas directement le nombre d’utilisateurs, de groupes ou de rôles approvisionnés.
- Un bouton **Actualiser** que vous pouvez utiliser pour mettre à jour la vue.
- Le nombre d’**Utilisateurs** et de **Groupes** approvisionnés, et le nombre de rôles créés. Pendant le cycle initial, le nombre d’**Utilisateurs** augmente de 1 lorsqu’un utilisateur est créé ou mis à jour, et diminue de 1 lorsqu’un utilisateur est supprimé. Pendant un cycle incrémentiel, les mises à jour d’utilisateurs n’affectent pas le nombre d’**Utilisateurs**. Le nombre ne change que lorsque des utilisateurs sont créés ou supprimés.
- Un lien **Afficher les journaux d’audit**, qui ouvre les journaux d’audit Azure AD pour plus d’informations sur toutes les opérations exécutées par le service d’approvisionnement d’utilisateurs, dont l’état d’approvisionnement d’utilisateurs individuels (voir la section [Utiliser des journaux d’audit](#use-audit-logs-to-check-a-users-provisioning-status) ci-dessous).

À la fin d’un cycle d’approvisionnement, la section **Statistiques à ce jour** affiche le nombre cumulatif d’utilisateurs et de groupes qui ont été approvisionnés à ce jour, ainsi que la date d’achèvement et la durée du dernier cycle. L’**ID d’activité** identifie uniquement le cycle d’approvisionnement le plus récent. L’**ID de tâche** est un identificateur unique de la tâche d’approvisionnement et est spécifique à l’application dans votre client.

La progression de l’approvisionnement peut être consultée dans le portail Azure, sous l’onglet **Azure Active Directory &gt; Applications Entreprise &gt;\[Nom de l’application\]&gt;Approvisionnement**.

![Barre de progression sur la page de l’approvisionnement](media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-audit-logs-to-check-a-users-provisioning-status"></a>Utiliser des journaux d’audit pour vérifier l’état de l’approvisionnement d’utilisateurs

Pour consulter l’état de l’approvisionnement d’un utilisateur spécifique, consultez les journaux d’audit dans Azure AD. Toutes les opérations effectuées par le service d’attribution d’utilisateurs sont enregistrées dans les journaux d’audit Azure AD. Cela comprend toutes les opérations de lecture et d’écriture effectuées sur les systèmes sources et cibles, ainsi que les données utilisateur qui ont été lues ou écrites à chaque opération.

Les journaux d’audit d’approvisionnement sont accessibles dans le portail Azure, sous l’onglet **Azure Active Directory &gt; Applications Entreprise &gt;\[Nom de l’application\]&gt; Journaux d’audit**. Filtrez les journaux d’activité sur la catégorie **Approvisionnement des comptes** pour afficher uniquement les événements d’approvisionnement de cette application. Vous pouvez rechercher des utilisateurs en fonction de l’ID de correspondance qui a été configuré pour eux dans les mappages d’attributs. 

Par exemple, si vous avez configuré le nom d’utilisateur principal ou l’adresse e-mail en tant qu’attribut correspondant côté Azure AD, et si l’utilisateur qui n’est pas approvisionné a la valeur « audrey@contoso.com », recherchez les journaux d’audit correspondant à « audrey@contoso.com » et passez en revue les entrées renvoyées.

Les journaux d’audit d’approvisionnement enregistrent toutes les opérations effectuées par le service d’approvisionnement, y compris :

* Interrogation d’Azure AD concernant les utilisateurs assignés qui se trouvent dans l’étendue de l’approvisionnement
* Interrogation de l’application cible concernant l’existence de ces utilisateurs
* Comparaison des objets utilisateur du système
* Ajout, mise à jour ou désactivation du compte d’utilisateur dans le système cible en fonction de la comparaison

Pour plus d’informations sur la lecture des journaux d’audit dans le portail Azure, consultez le [guide de création de rapports sur le provisionnement](check-status-user-account-provisioning.md).

## <a name="how-long-will-it-take-to-provision-users"></a>Combien de temps faut-il pour approvisionner des utilisateurs ?
Lorsque vous utilisez le provisionnement automatique des utilisateurs avec une application, Azure AD provisionne et met à jour automatiquement les comptes d’utilisateur dans une application selon différents éléments comme l’[affectation d’utilisateurs et de groupes](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) et selon un intervalle régulier planifié (généralement toutes les 40 minutes).

Le temps d’approvisionnement d’un utilisateur donné dépend principalement de si votre tâche d’approvisionnement exécute une synchronisation initiale ou incrémentielle.

- Pour les **synchronisations initiales**, le temps nécessaire dépend de plusieurs facteurs, notamment le nombre d’utilisateurs et de groupes dans la portée pour l’approvisionnement, ainsi que le nombre total d’utilisateurs et de groupe dans le système source. La première synchronisation entre Azure AD et une application peut prendre de 20 minutes à plusieurs heures, en fonction de la taille de l’annuaire Azure AD et du nombre d’utilisateurs dans l’étendue de l’approvisionnement. Une liste complète des facteurs affectant les performances de la synchronisation initiale est présentée plus loin dans cette section.

- Pour les **synchronisations incrémentielles** qui suivent les synchronisations initiales, les durées des tâches ont tendance à être plus rapides (10 minutes, par exemple) car le service d’approvisionnement stocke les filigranes qui représentent l’état des deux systèmes après la synchronisation initiale, ce qui améliore les performances des synchronisations suivantes. Le temps nécessaire dépend du nombre de modifications détectées dans ce cycle d’approvisionnement. S’il existe moins de 5 000 utilisateurs ou les changements d’appartenance au groupe, le travail peut finir dans un cycle d’approvisionnement incrémentiel. 

Le tableau suivant récapitule les temps de synchronisation des scénarios d’approvisionnement courants. Dans ces scénarios, le système source est Azure AD et le système cible est une application SaaS. Les temps de synchronisation sont dérivés d’une analyse statistique des travaux de synchronisation pour les applications SaaS ServiceNow, Workplace, Salesforce et G Suite.


| Configuration d’étendue | Utilisateurs, groupes et membres dans l’étendue | Temps de la synchronisation initiale | Temps de la synchronisation incrémentielle |
| -------- | -------- | -------- | -------- |
| Synchroniser les utilisateurs et groupes assignés uniquement |  < 1 000 |  < 30 minutes | < 30 minutes |
| Synchroniser les utilisateurs et groupes assignés uniquement |  1 000 - 10 000 | 142 - 708 minutes | < 30 minutes |
| Synchroniser les utilisateurs et groupes assignés uniquement |   10 000 - 100 000 | 1 170 - 2 340 minutes | < 30 minutes |
| Synchroniser l'ensemble des utilisateurs et groupes dans Azure AD |  < 1 000 | < 30 minutes  | < 30 minutes |
| Synchroniser l'ensemble des utilisateurs et groupes dans Azure AD |  1 000 - 10 000 | < 30 - 120 minutes | < 30 minutes |
| Synchroniser l'ensemble des utilisateurs et groupes dans Azure AD |  10 000 - 100 000  | 713 - 1 425 minutes | < 30 minutes |
| Synchroniser l’ensemble des utilisateurs dans Azure AD|  < 1 000  | < 30 minutes | < 30 minutes |
| Synchroniser l’ensemble des utilisateurs dans Azure AD | 1 000 - 10 000  | 43 - 86 minutes | < 30 minutes |


Pour la configuration **Synchroniser l’utilisateur et les groupes affectés uniquement**, vous pouvez utiliser les formules suivantes pour déterminer les temps minimum et maximum attendus pour la **synchronisation initiale** :

    Minimum minutes =  0.01 x [Number of assigned users, groups, and group members]
    Maximum minutes = 0.08 x [Number of assigned users, groups, and group members] 
    
Résumé des facteurs qui influencent le temps nécessaire pour terminer une **synchronisation initiale**  :

- Nombre total d'utilisateurs et de groupes concernés par l'approvisionnement.

- Nombre total d'utilisateurs, de groupes et de membres de groupe présents dans le système source (Azure AD).

- Si les utilisateurs dans l’étendue pour l’approvisionnement sont mis en correspondance ou non avec des utilisateurs existants dans l’application cible, ou doivent être créés la première fois. Les travaux de synchronisation pour lesquels tous les utilisateurs sont créés pour la première fois prennent environ *deux fois plus* de temps que les travaux de synchronisation pour lesquels tous les utilisateurs sont mis en correspondance avec des utilisateurs existants.

- Nombre d’erreurs dans les [journaux d’audit](check-status-user-account-provisioning.md). Les performances sont ralenties s'il y a beaucoup d'erreurs et que le service d'approvisionnement est passé en quarantaine.    

- Limites de taux de requêtes et limitation implémentées par le système cible. Certains systèmes cible implémentent des limites de taux de requêtes et une limitation qui peuvent affecter les performances lors d’opérations de synchronisation de grande envergure. Dans ces conditions, une application qui reçoit trop rapidement un trop grand nombre de requêtes risque d’afficher un taux de réponse plus faible ou d’interrompre la connexion. Pour améliorer les performances, le connecteur doit ajuster ce taux en évitant d’envoyer les requêtes d’application plus rapidement que ce que l’application est capable de traiter. Les connecteurs d’approvisionnement intégrés à Microsoft permettent d’effectuer cet ajustement. 

- Le nombre et la taille des groupes affectés. La synchronisation des groupes affectés prend plus de temps que la synchronisation des utilisateurs. Le nombre et la taille des groupes affectés ont un impact sur les performances. Si une application comporte des [mappages activés pour la synchronisation des objets de groupe](customize-application-attributes.md#editing-group-attribute-mappings), les propriétés de groupe, telles que les noms de groupe et les appartenances, sont synchronisées en plus des utilisateurs. Ces synchronisations supplémentaires prendront plus temps que la seule synchronisation des objets utilisateur.

## <a name="next-steps"></a>Étapes suivantes
[Automatisation de l’approvisionnement et de l’annulation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning)
