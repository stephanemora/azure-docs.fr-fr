---
title: Correction des problèmes d’appartenance pour les groupes - Azure Active Directory | Microsoft Docs
description: Conseils pour résoudre les problèmes d’appartenance dynamique à des groupes dans Azure AD.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0594d99874ea9bb83673013a9a03272edcd8ce0b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57897671"
---
# <a name="troubleshoot-and-resolve-groups-issues"></a>Dépanner et résoudre des problèmes de groupes

## <a name="troubleshooting-group-creation-issues"></a>Résolution des problèmes de création de groupe
**J’ai désactivé la création du groupe de sécurité dans le portail Azure, mais les groupes peuvent toujours être créés via Powershell** le **utilisateur peut créer des groupes de sécurité dans les portails Azure** définissant dans les contrôles de portail Azure ou non non-administrateur les utilisateurs peuvent créer des groupes de sécurité dans le volet d’accès ou le portail Azure. Il ne contrôle pas la création du groupe de sécurité par le biais de Powershell.

Pour désactiver la création du groupe pour les utilisateurs non-administrateurs dans Powershell :
1. Vérifiez que les utilisateurs non-administrateurs sont autorisés à créer des groupes :
   
   ```
   PS C:\> Get-MsolCompanyInformation | fl UsersPermissionToCreateGroupsEnabled
   ```
  
2. Si la commande retourne `UsersPermissionToCreateGroupsEnabled : True`, les utilisateurs non-administrateurs peuvent créer des groupes. Pour désactiver cette fonctionnalité :
  
   ``` 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```

<br/>**J’ai reçu un groupes maximal autorisé d’erreur lorsque vous tentez de créer un groupe dynamique dans Powershell**<br/>
Si vous recevez un message dans Powershell indiquant _des stratégies de groupe dynamique atteinte du nombre de groupes d’autorisé max_, cela signifie que vous avez atteint la limite maximale pour les groupes dynamiques dans votre client. Le nombre maximal de groupes dynamiques par locataire est de 5 000.

Pour créer les nouveaux groupes dynamiques, vous devez tout d’abord de supprimer des groupes dynamiques existants. Il n’existe aucun moyen pour augmenter la limite.

## <a name="troubleshooting-dynamic-memberships-for-groups"></a>Résolution des problèmes liés à l’appartenance dynamique à des groupes

**J’ai configuré une règle sur un groupe, mais aucune appartenance n’est mise à jour dans le groupe**<br/>
1. Vérifiez les valeurs d’attributs d’utilisateur ou appareil dans la règle. Vérifiez que les utilisateurs qui satisfont la règle. Pour les appareils, vérifiez les propriétés de l’appareil pour garantir des attributs synchronisés contiennent les valeurs attendues.<br/>
2. Vérifier l’appartenance de l’état du traitement pour vérifier si elle est terminée. Vous pouvez vérifier le [l’appartenance de l’état de traitement](groups-create-rule.md#check-processing-status-for-a-rule) et date de la mise à jour de la dernière sur le **vue d’ensemble** page pour le groupe.

Si tout semble correct, attendez quelque temps avant que le groupe se remplisse. Selon la taille de votre client, cela peut prendre jusqu'à 24 heures pour que le groupe soit rempli la première fois ou après une modification de la règle.

**J’ai configuré une règle, mais les membres existants sur celle-ci sont à présent supprimés**<br/>Ce comportement est normal. Lors de l’activation ou de la modification d’une règle, les membres existants du groupe sont supprimés. Les utilisateurs provenant de l’évaluation de la règle sont ajoutés en tant que membres du groupe.

**Je ne vois pas instantanément de changement d’appartenance quand j’ajoute ou quand je modifie une règle. Pourquoi ?**<br/>L’évaluation de l’appartenance dédiée est effectuée régulièrement dans le cadre d’un processus asynchrone exécuté en arrière-plan. La durée de l’opération est déterminée par le nombre d’utilisateurs dans votre annuaire et par la taille du groupe créé à la suite de la règle. Généralement, les annuaires contenant peu d’utilisateurs constatent les changements d’appartenance au groupe en moins de quelques minutes. Le renseignement des annuaires contenant de nombreux utilisateurs peut prendre plus de 30 minutes.

**Comment puis-je forcer le groupe à traiter maintenant ?**<br/>
Actuellement, il n’existe aucun moyen pour déclencher automatiquement le groupe à traiter à la demande. Toutefois, vous pouvez déclencher manuellement le retraitement en mettant à jour la règle d’adhésion pour ajouter un espace blanc à la fin.  

**J’ai rencontré une erreur de traitement de règle**<br/>Le tableau suivant répertorie les erreurs de règle d’appartenance dynamique courantes et la façon de les corriger.

| Erreur d’analyse de la règle | Utilisation incorrecte | Utilisation corrigée |
| --- | --- | --- |
| Error: attribut non pris en charge. |(user.invalidProperty -eq "Value") |(user.department -eq "value")<br/><br/>Assurez-vous que l’attribut se trouve dans la [liste des propriétés prises en charge](groups-dynamic-membership.md#supported-properties). |
| Error: l’opérateur n’est pas pris en charge sur l’attribut. |(user.accountEnabled -contains true) |(user.accountEnabled - eq true)<br/><br/>L’opérateur utilisé n’est pas pris en charge pour le type de propriété (dans cet exemple,-contains ne peut pas être utilisé avec le type booléen). Utilisez les opérateurs corrects pour le type de propriété. |
| Error: erreur de compilation de la requête. | 1. (user.department -eq "Sales") (user.department -eq "Marketing")<br>2. (user.userPrincipalName -match "*@domain.ext") | 1. Opérateur manquant. Utilisez -and ou -or pour associer les prédicats<br>(user.department -eq "Sales") -or (user.department -eq "Marketing")<br>2. Erreur dans l’expression régulière utilisée avec -match<br>(user.userPrincipalName -match ".*@domain.ext")<br>ou bien : (user.userPrincipalName -match "@domain.ext$") |

## <a name="next-steps"></a>Étapes suivantes

Ces articles fournissent des informations supplémentaires sur Azure Active Directory.

* [Gestion de l’accès aux ressources avec les groupes Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Gestion des applications dans Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Qu’est-ce qu’Azure Active Directory ?](../fundamentals/active-directory-whatis.md)
* [Intégration des identités locales dans Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
