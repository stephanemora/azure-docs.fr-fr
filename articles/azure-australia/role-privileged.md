---
title: Contrôle d’accès en fonction du rôle et Privileged Identity Management dans Azure Australie
description: Conseils sur la mise en œuvre des contrôles d’accès en fonction du rôle et de Privileged Identity Management dans les régions australiennes pour répondre aux exigences particulières de la politique, des réglementations et de la législation du gouvernement australien.
author: Galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: e2a94f82e4830bd1e9c96039f5ef8fe6546b0d0b
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982677"
---
# <a name="role-based-access-control-rbac-and-privileged-identity-management-pim"></a>Contrôle d’accès en fonction du rôle(RBAC) et Privileged Identity Management (PIM)

La gestion des privilèges Administrateur est une étape essentielle pour garantir la sécurité dans tout environnement informatique. La restriction des privilèges Administrateur par l’utilisation de modèles de sécurité de privilège minimum est requise par [ACSC ISM](https://acsc.gov.au/infosec/ism/index.htm) et fait partie de la liste des suggestions de sécurité [d’ACSC Essential 8](https://www.acsc.gov.au/infosec/mitigationstrategies.htm).

Microsoft fournit une suite de contrôles permettant d’implémenter l’accès Juste-à-temps et Just-Enough dans Microsoft Azure. La compréhension de ces contrôles est essentielle pour renforcer la posture de sécurité dans le Cloud. Ce guide fournit une vue d’ensemble des contrôles ainsi que des considérations clés en matière de conception lors de leur implémentation.

## <a name="role-based-access-control-rbac"></a>Contrôle d’accès en fonction du rôle

Le contrôle d’accès en fonction du rôle joue un rôle déterminant dans la gestion de l’accès à toutes les ressources dans Microsoft Azure et dans la gestion des Azure Active Directory (Azure AD). Le contrôle d’accès en fonction du rôle et un certain nombre de fonctionnalités complémentaires disponibles dans Azure^peuvent être implémentés. Cet article se penche sur l’implémentation d’un contrôle d’accès en fonction du rôle efficace en utilisant les groupes d’administration d’Azure, Azure Active Directory et Privileged Identity Management (PIM).

À un niveau élevé, l’implémentation du contrôle d’accès en fonction du rôle requiert trois composants :

![Vue d’ensemble du contrôle d’accès en fonction du rôle](media/rbac-overview.png)

* **Principaux de sécurité** : Un principal de sécurité peut être un utilisateur, un groupe, des [principaux de service](https://docs.microsoft.com/en-us/azure/active-directory/develop/app-objects-and-service-principals) ou une [identité managée](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). Les principaux de sécurité doivent disposer de privilèges en utilisant mes groupes d’Azure Active Directory.

* **Définitions de rôles** : Une définition de rôle, ou simplement rôle, est une collection d’autorisations. Ces autorisations définissent les opérations qui peuvent être effectuées par les principaux de sécurité attribués à la définition de rôle. Cette fonctionnalité est fournie par les rôles de ressources Azure et les rôles d’administrateur d’Azure Active Directory. Azure est fourni avec un ensemble de rôles intégrés (lien) qui peuvent être complétés par des rôles personnalisés.

* **Étendue** : L’étendue correspond à l’ensemble de ressources Azure auquel s’applique une définition de rôle. Les rôles Azure peuvent être attribués aux ressources Azure à l’aide des groupes d’administration d’Azure.

Ces trois composants combinés accordent aux principaux de sécurité l’accès indiqué dans les définitions de rôles pour toutes les ressources se trouvant dans l’étendue du groupe d’administration d’Azure. C’est ce qui s’appelle une attribution de rôle. Plusieurs définitions de rôle peuvent être attribuées à un principal de sécurité, et plusieurs principaux de sécurité peuvent être attribués à une seule étendue.

### <a name="azure-active-directory-groups"></a>Groupes Azure Active Directory

Lorsque vous attribuez des privilèges à des personnes ou à des équipes, l’attribution doit, dans la mesure du possible, être liée à un groupe Azure Active Directory Group. Elle ne doit pas être directement attribuée à l’utilisateur en question. Il s’agit de la même pratique recommandée héritée des implémentations Active Directory locales. Dans la mesure du possible, les groupes d’Azure Active Directory doivent être créés par équipe, en complément de la structure logique des groupes d’administration d’Azure que vous avez créé.

Dans un scénario cloud hybride, les groupes de sécurité Windows Server Active Directory locaux peuvent être synchronisés avec votre instance Azure Active Directory. Si vous avez déjà implémenté le contrôle d’accès en fonction du rôle localement à l’aide de ces groupes de sécurité Windows Server Active Directory, ils peuvent être utilisés pour implémenter RBAC pour vos ressources Azure après leur synchronisation. Dans le cas contraire, votre environnement cloud peut être considéré comme une ardoise vierge permettant de concevoir et d’implémenter un plan de gestion des privilèges solide centré autour de votre implémentation d’Azure Active Directory.

### <a name="azure-resource-roles-versus-azure-active-directory-administrator-roles"></a>Rôles de ressources Azure ou rôles d’administrateur Azure Active Directory

Microsoft Azure offre un large éventail de rôles intégrés pour les [ressources Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) et [l’administration Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles). Ces deux types de rôles offrent un accès granulaire particuliers pour les ressources Azure ou les administrateurs d’Azure AD. Il est important de noter que les rôles de ressources Azure ne peuvent pas être utilisés pour fournir un accès administrateur à Azure AD et les rôles Azure AD rôles ne fournissent pas d’accès particulier aux ressources Azure.

Voici quelques exemples de types d’accès pouvant être attribués à une ressource Azure à l’aide d’un rôle intégré :

* Autoriser un utilisateur à gérer des machines virtuelles dans un abonnement et un autre utilisateur à gérer des réseaux virtuels
* Permettre à un groupe d’administrateurs de base de données de gérer des bases de données SQL dans un abonnement
* Permettre à un utilisateur à gérer toutes les ressources dans un groupe de ressources, telles que des machines virtuelles, des sites Web et des sous-réseaux
* Permettre à une application d’accéder à toutes les ressources d’un groupe de ressources

Voici quelques exemples de types d’accès pouvant être attribués à l’administration Azure AD :

* Autoriser les membres du support technique de réinitialiser les mots de passe utilisateur
* Autoriser le personnel à inviter des utilisateurs externes sur une instance de Azure AD pour une B2B Collaboration
* Autoriser le personnel administratif a disposer d’un accès en lecture pour effectuer des rapports de connexion et d’audit
* Autoriser le personnel à gérer tous les utilisateurs et les groupes, y compris à réinitialiser les mots de passe.

Il est important de prendre le temps de comprendre la liste complète des actions autorisées fournies par un rôle intégré pour s’assurer qu’aucun accès non autorisé ne soit accordé. La liste des rôles intégrés et de l’accès qu’ils octroient change constamment. Vous pouvez obtenir plus d’informations sur la liste complète des rôles et leurs définitions en consultant la documentation grâce au lien ci-dessus ou en utilisant le cmdlet d’Azure PowerShell :

```PowerShell
PS C:\> Get-AzRoleDefinition

Name             : AcrDelete
Id               : <<RoleID>>
IsCustom         : False
Description      : acr delete
Actions          : {Microsoft.ContainerRegistry/registries/artifacts/delete}
NotActions       : {}
DataActions      : {}
NotDataActions   : {}
AssignableScopes : {/}
...
```

ou la commande Azure CLI :

```bash
PS C:\> az role definition list
[
  {
    "assignableScopes": [
      "/"
    ],
    "description": "acr delete",
    "id": "/subscriptions/49b12d1b-4030-431c-8448-39056021c4ab/providers/Microsoft.Authorization/roleDefinitions/c2f4ef07-c644-48eb-af81-4b1b4947fb11",
    "name": "c2f4ef07-c644-48eb-af81-4b1b4947fb11",
    "permissions": [
      {
        "actions": [
          "Microsoft.ContainerRegistry/registries/artifacts/delete"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "AcrDelete",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  },
...
```

Il est également possible de créer des rôles de ressources Azure personnalisés si besoin. Ces rôles personnalisés peuvent être créés dans le Portail Azure via PowerShell ou Azure CLI. Lors de la création de rôles personnalisés, il est essentiel de s’assurer que l’objectif du rôle est unique et que sa fonction n’est pas déjà fournie par un rôle de ressource Azure existant. Cela permet de réduire la complexité de la gestion continue et d’éviter que les principaux de sécurité ne reçoivent des privilèges inutiles. Par exemple, la création d’un rôle de ressource Azure personnalisé qui se situe entre les rôles de ressources Azure intégrés, « contributeur de machines virtuelles » et « connexion de l’administrateur aux machines virtuelles ».

Le rôle personnalisé peut être basé sur le rôle contributeur existant, qui accorde l’accès suivant :

| Ressource Azure | Niveau d’accès |
| --- | --- |
| Virtual Machines | Gestion autorisée mais accès non accordé |
| Réseau virtuel attaché à la machine virtuelle | Accès impossible |
| Stockage attaché à la machine virtuelle | Accès impossible |
|

Le rôle personnalisé pourrait conserver cet accès de base, mais il accorde aux utilisateurs désignés des privilèges de base supplémentaires permettant de modifier la configuration réseau des machines virtuelles.

Les rôles de ressources Azure ont également la possibilité d’être attribués par les groupes d’administration d’Azure.

### <a name="azure-management-groups"></a>Groupes d'administration Azure

Les groupes d’administration d’Azure peuvent être utilisés par une organisation pour gérer l’attribution de rôles à tous les abonnements et à leurs ressources dans une location Azure. Les groupes d’administration d’Azure sont conçus pour vous permettre de créer des hiérarchies de gestion, y compris la possibilité de mapper la structure hiérarchique de votre organisation dans Azure. La création d’unités opérationnelles et organisationnelles en tant qu’entités logiques distinctes permet d’appliquer des autorisations au sein d’une organisation en fonction des exigences particulières de chaque équipe. Les groupes d’administration d’Azure peuvent être utilisés pour définir une hiérarchie de gestion pouvant avoir jusqu’à six niveaux.

![Groupes d'administration](media/management-groups.png)

Les groupes d’administration d’Azure sont mappés à des abonnements Azure dans une location Azure. Cela permet à une organisation de séparer les ressources Azure appartenant à des unités opérationnelles particulières et de fournir un niveau de contrôle granulaire sur la gestion des coûts et de l’attribution des privilèges.

## <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

Microsoft a implémenté Juste-à-temps (JIT) et Just-Enough-Access (JEA) grâce à Azure Privileged Identity Management. Ce service permet au personnel administratif de contrôler, de gérer et de surveiller l’accès privilégié aux ressources Azure. PIM permet aux principaux de sécurité d’être « éligibles » pour un rôle par le personnel administratif, ce qui permet aux utilisateurs de demander l’activation du rôle grâce au Portail Azure ou aux cmdlets PowerShell. Par défaut, l’attribution de rôle peut être activée pour une durée comprise entre 1 et 72 heures. Si nécessaire, l’utilisateur peut demander une extension de son attribution de rôle et il existe bien une option permettant de rendre cette l’attribution permanente. De plus, l’obligation d’authentification multifacteur peut être appliquée lorsque les utilisateurs demandent l’activation de leurs rôles éligibles. Lorsque la période allouée pour l’activation du rôle expire, le principal de sécurité ne dispose plus de l’accès privilégié accordé par le rôle.

L’utilisation de PIM empêche les problèmes d’affectation de privilèges les plus courants qui peuvent se produire dans les environnements qui n’utilisent pas l’accès juste-à-temps ou qui n’effectuent pas des audits de routine pour l’attribution de privilèges. L’un des problèmes les plus fréquent est l’oubli de l’affectation de privilèges élevés, ces derniers étant donc conservés même après la fin de la tâche ayant nécessité l’octroi de privilèges élevés. Un autre problème est la prolifération de privilèges élevés au sein d’un environnement par le clonage de l’accès attribué à un principal de sécurité lors de la configuration d’autres principaux de sécurité similaires.

## <a name="key-design-considerations"></a>Considérations clés en matière de conception

Lors de la conception d’une stratégie de contrôle d’accès en fonction du rôle dans le but de mettre en œuvre un module de sécurité de privilèges minimum, les exigences de sécurité suivantes doivent être prises en compte :

* Les demandes d’accès privilégié sont validées
* Les privilèges d’administrateur sont limités à l’accès minimal requis pour effectuer des tâches précises
* Les privilèges d’administrateur sont limités à la durée minimale requise pour effectuer des tâches précises
* Les privilèges administrateur accordés font l’objet d’examens réguliers

Le processus de conception d’une stratégie de contrôle d’accès en fonction du rôle nécessite une analyse détaillée des fonctions d’entreprise pour comprendre la différence d’accès entre les différents rôles d’entreprise, ainsi que le type et la fréquence du travail nécessitant des privilèges élevés. La différence de fonction entre un opérateur de sauvegarde, un administrateur de sécurité et un auditeur nécessite différents niveaux d’accès à différents moments, avec différents niveaux de révision continue.

## <a name="validate-requests-for-access"></a>Valider les demandes d’accès

Les privilèges élevés doivent être formellement approuvés. Pour ce faire, il faut mettre en place un processus d’approbation et confier au personnel compétent la tâche de valider toutes les demandes de privilèges supplémentaires et de s’assurer qu’elles sont légitimes. Privileged Identity Management propose plusieurs options pour approuver l’attribution de rôle. Une demande d’activation de rôle peut être configurée de manière à autoriser l’approbation automatique ou la contrôler, nécessitant ainsi que des approbateurs désignés examinent et approuvent manuellement toutes les demandes d’activation de rôle. Les demandes d’activation peuvent également être configurées de manière à exiger que des informations supplémentaires soient incluses avec la demande d’activation, telles que les numéros de tickets.

### <a name="restrict-privilege-based-on-duties"></a>Restreindre les privilèges en fonction des tâches

La restriction du niveau de privilège accordé aux principaux de sécurité est essentielle, car lorsque les affectations de privilèges sont trop nombreuses, elles constituent souvent un vecteur d’attaque de sécurité informatique. Les types de ressources managées et les équipes compétentes doivent être évalués afin que le niveau minimal de privilèges requis pour les tâches quotidiennes puisse être attribué. Les privilèges supplémentaires qui ne se limitent pas à ceux requis pour les tâches quotidiennes doivent être accordés uniquement pour la période de temps nécessaire à l’exécution d’une tâche précise. Par exemple, fournir un accès « contributeur » à l’administrateur d’un client, mais lui permettre de demander des autorisations « propriétaire » pour une ressource Azure en rapport avec une tâche précise nécessitant un accès global temporaire.

Cela permet de s’assurer que chaque administrateur individuel dispose d’un accès élevé pour une coutre durée uniquement. Le respect de ces pratiques réduit la surface d’attaque globale de l’infrastructure informatique de toute organisation.

### <a name="regular-evaluation-of-administrative-privilege"></a>Évaluation régulière des privilèges Administrateur

Il est essentiel que les principaux de sécurité d’un environnement fassent régulièrement l’objet d’un audit pour s’assurer que le niveau de privilège est actuellement attribué est approprié. Microsoft Azure fournit un certain nombre de moyens permettant d’auditer et d’évaluer les privilèges attribués aux principaux de sécurité Azure. Privileged Identity Management autorise le personnel administratif à effectuer régulièrement des « révisions d’accès » pour les rôles accordés aux principaux de sécurité. Une révision d’accès peut être effectuée pour auditer l’attribution de rôle de ressource Azure et l’attribution de rôle administrateur d’Azure Active Directory. Une révision d’accès peut être configurée avec les propriétés suivantes :

* **nom de révision, date de début et date de fin** : Les révisions doivent être configurées de manière à être suffisamment longues pour que les utilisateurs désignés puissent les effectuer.

* **Rôle à réviser**: Chaque révision d’accès se concentre sur un rôle Azure unique.

* **Réviseurs sélectionnés** : Il existe trois options pour effectuer une révision. Vous pouvez affecter la révision à quelqu’un d’autre, vous pouvez la faire vous-même ou vous pouvez demander à chaque utilisateur de réviser son propre accès.

* **Exiger des utilisateurs qu’ils fournissent un motif d’accès** : Les utilisateurs peuvent être tenus d’indiquer la raison du maintien de leur niveau de privilège lorsqu’ils effectuent la révision d’accès.

La progression des révisions d’accès en attente peut être surveillée à tout moment via un tableau de bord dans le Portail Azure. L’accès au rôle en cours de révision reste inchangé tant que la révision d’accès n’est pas terminée. Il est également possible [d’auditer](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-how-to-use-audit-log) toutes les attributions d’utilisateurs et les activations PIM au cours d’une période donnée.

## <a name="next-steps"></a>Étapes suivantes

Consultez l’article sur la [Surveillance du système dans Azure Australie](system-monitor.md).
