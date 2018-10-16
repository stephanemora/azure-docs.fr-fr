---
title: Résolution des problèmes liés à l’appartenance dynamique des groupes | Microsoft Docs
description: Conseils pour résoudre les problèmes d’appartenance dynamique à des groupes dans Azure AD.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 09/11/2018
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: e189fb8b2bc5079d1560d3b7a54fea2db7366fe7
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46293965"
---
# <a name="troubleshooting-dynamic-memberships-for-groups"></a>Résolution des problèmes liés à l’appartenance dynamique à des groupes

**J’ai configuré une règle sur un groupe, mais aucune appartenance n’est mise à jour dans le groupe**<br/>Vérifiez les valeurs des attributs d’utilisateur sur la règle : des utilisateurs satisfont-ils à la règle ? Si tout semble correct, attendez quelque temps avant que le groupe se remplisse. Selon la taille de votre client, cela peut prendre jusqu'à 24 heures pour que le groupe soit rempli la première fois ou après une modification de la règle.

**J’ai configuré une règle, mais les membres existants sur celle-ci sont à présent supprimés**<br/>Ce comportement est normal. Lors de l’activation ou de la modification d’une règle, les membres existants du groupe sont supprimés. Les utilisateurs provenant de l’évaluation de la règle sont ajoutés en tant que membres du groupe.

**Je ne vois pas instantanément de changement d’appartenance quand j’ajoute ou quand je modifie une règle. Pourquoi ?**<br/>L’évaluation de l’appartenance dédiée est effectuée régulièrement dans le cadre d’un processus asynchrone exécuté en arrière-plan. La durée de l’opération est déterminée par le nombre d’utilisateurs dans votre annuaire et par la taille du groupe créé à la suite de la règle. Généralement, les annuaires contenant peu d’utilisateurs constatent les changements d’appartenance au groupe en moins de quelques minutes. Le renseignement des annuaires contenant de nombreux utilisateurs peut prendre plus de 30 minutes.

**J’ai rencontré une erreur de traitement de règle**<br/>Le tableau suivant répertorie les erreurs de règle d’appartenance dynamique courantes et la façon de les corriger.

| Erreur d’analyse de la règle | Utilisation incorrecte | Utilisation corrigée |
| --- | --- | --- |
| Erreur : attribut non pris en charge. |(user.invalidProperty -eq "Value") |(user.department -eq "value")<br/><br/>Assurez-vous que l’attribut se trouve dans la [liste des propriétés prises en charge](groups-dynamic-membership.md#supported-properties). |
| Erreur : l’opérateur n’est pas pris en charge sur l’attribut. |(user.accountEnabled -contains true) |(user.accountEnabled - eq true)<br/><br/>L’opérateur utilisé n’est pas pris en charge pour le type de propriété (dans cet exemple,-contains ne peut pas être utilisé avec le type booléen). Utilisez les opérateurs corrects pour le type de propriété. |
| Erreur : erreur de compilation de la requête. | 1. (user.department -eq "Sales") (user.department -eq "Marketing")<br>2. (user.userPrincipalName -match "*@domain.ext") | 1. Opérateur manquant. Utilisez -and ou -or pour associer les prédicats<br>(user.department -eq "Sales") -or (user.department -eq "Marketing")<br>2. Erreur dans l’expression régulière utilisée avec -match<br>(user.userPrincipalName -match ".*@domain.ext")<br>ou bien : (user.userPrincipalName -match "@domain.ext$") |

## <a name="next-steps"></a>Étapes suivantes

Ces articles fournissent des informations supplémentaires sur Azure Active Directory.

* [Gestion de l’accès aux ressources avec les groupes Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Gestion des applications dans Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Qu’est-ce qu’Azure Active Directory ?](../fundamentals/active-directory-whatis.md)
* [Intégration des identités locales dans Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
