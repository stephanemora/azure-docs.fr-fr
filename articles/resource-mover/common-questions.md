---
title: Questions courantes sur Azure Resource Mover
description: Découvrez les réponses aux questions courantes sur Azure Resource Mover.
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: conceptual
ms.date: 02/21/2021
ms.author: raynew
ms.openlocfilehash: e900250aea84b4a9c9112fa54632a2be8b9cb49c
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102564269"
---
# <a name="common-questions"></a>Questions courantes

Cet article répond à des questions courantes sur [Azure Resource Mover](overview.md).


## <a name="moving-across-regions"></a>Déplacement entre les régions

### <a name="can-i-move-resources-across-any-regions"></a>Puis-je déplacer des ressources d’une région à une autre ?

Actuellement, vous pouvez déplacer des ressources de n’importe quelle région publique source vers n’importe quelle région publique cible, selon les [types de ressources disponibles dans la région en question](https://azure.microsoft.com/global-infrastructure/services/). Le déplacement des ressources dans les régions Azure Government n’est pas pris en charge actuellement.

### <a name="what-regions-are-currently-supported"></a>Quelles sont les régions actuellement prises en charge ?

Azure Resource Mover est actuellement disponible comme suit :

**Support** | **Détails**
--- | ---
Prise en charge du déplacement | Les ressources Azure prises en charge pour le déplacement avec Resource Mover peuvent être déplacées d’une région publique vers une autre région publique.
Prise en charge de métadonnées |  Les régions prises en charge pour le stockage des métadonnées sur les machines à déplacer incluent USA Est 2, Europe Nord, Asie Sud-Est, Japon Est, Royaume-Uni Sud et Australie Est comme régions de métadonnées. <br/><br/> Le déplacement de ressources dans la région Azure Chine est également pris en charge avec la région de métadonnées Chine Nord 2.

### <a name="what-resources-can-i-move-across-regions-using-resource-mover"></a>Quelles ressources puis-je déplacer d’une région à l’autre à l’aide de Resource Mover ?

À l’aide de Resource Mover, vous pouvez actuellement déplacer les ressources suivantes d’une région à l’autre :

- Machines virtuelles Azure et disques associés
- Cartes réseau
- Groupes à haute disponibilité 
- Réseaux virtuels Azure 
- Adresses IP publiques
- Groupes de sécurité réseau (NSG)
- Équilibreurs de charge internes et publics 
- Bases de données Azure SQL et pools élastiques

### <a name="can-i-move-disks-across-regions"></a>Est-ce que je peux déplacer des disques entre les régions ?

Vous ne pouvez pas sélectionner des disques comme ressources à déplacer entre des régions. Les disques sont cependant déplacés lors d’un déplacement d’une machine virtuelle.

### <a name="what-does-it-mean-to-move-a-resource-group"></a>Que signifie déplacer un groupe de ressources ?

Quand une ressource est sélectionnée pour être déplacée, le groupe de ressources correspondant est automatiquement ajouté pour être déplacé. De cette façon, la ressource de destination peut être placée dans un groupe de ressources. Vous pouvez choisir de personnaliser et de fournir un groupe de ressources existant une fois qu’il a été ajouté pour être déplacé. Le déplacement d’un groupe de ressources ne signifie pas que toutes les ressources du groupe de ressources source sont déplacées.

### <a name="can-i-move-resources-across-subscriptions-when-i-move-them-across-regions"></a>Puis-je déplacer des ressources d’un abonnement à l’autre lorsque je les déplace d’une région à l’autre ?

Vous pouvez modifier l’abonnement après avoir déplacé les ressources vers la région de destination. [En savoir plus](../azure-resource-manager/management/move-resource-group-and-subscription.md) sur le déplacement de ressources vers un autre abonnement. 

### <a name="does-azure-resource-mover-store-customer-data"></a>Le service Azure Resource Mover stocke-t-il les données des clients ? 
Non. Le service Azure Resource Mover ne stocke pas les données des clients. Il stocke uniquement les informations de métadonnées qui facilitent le suivi et la progression des ressources déplacées.

### <a name="where-is-the-metadata-for-moving-across-regions-stored"></a>Où sont stockées les métadonnées permettant d’effectuer un déplacement d’une région à l’autre ?

Elles sont stockées dans une base de données [Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) et dans [Stockage Blob Azure](../storage/common/storage-service-encryption.md) au sein d’un abonnement Microsoft. Actuellement, les métadonnées sont stockées dans les régions USA Est 2 et Europe Nord. Nous allons étendre cette couverture à d’autres régions. Cela ne vous empêche pas de déplacer des ressources dans toutes les régions publiques.

### <a name="is-the-collected-metadata-encrypted"></a>Les métadonnées collectées sont-elles chiffrées ?

Oui, aussi bien en transit qu’au repos.
- Pendant le transit, les métadonnées sont envoyées en toute sécurité au service Resource Mover via Internet à l’aide du protocole HTTPS.
- Une fois dans le stockage, les métadonnées sont chiffrées.

### <a name="how-is-managed-identity-used-in-resource-mover"></a>Comment l’identité managée est-elle utilisée dans Resource Mover ?

L’[identité managée](../active-directory/managed-identities-azure-resources/overview.md) (anciennement identité du service géré, MSI) fournit des services Azure avec une identité managée automatiquement dans Azure AD.
- Resource Mover utilise une identité managée afin de pouvoir accéder aux abonnements Azure pour déplacer les ressources d’une région à l’autre.
- Une collection de déplacements a besoin d’une identité affectée par le système, avec un accès à l’abonnement qui contient les ressources que vous déplacez.

- Si vous déplacez des ressources d’une région à l’autre dans le portail, ce processus se produit automatiquement.
- Si vous déplacez des ressources à l’aide de PowerShell, vous exécutez des cmdlets pour attribuer une identité affectée par le système à la collection, puis vous attribuez un rôle avec les autorisations d’abonnement appropriées au principal d’identité. 

### <a name="what-managed-identity-permissions-does-resource-mover-need"></a>De quelles autorisations d’identité managée a besoin Resource Mover ? 

L’identité managée Azure Resource Mover nécessite au moins les autorisations suivantes : 

- Autorisation d’écrire/de créer des ressources dans un abonnement utilisateur, disponible avec le rôle *Contributeur*. 
- Autorisation de créer des attributions de rôles. L’autorisation de créer des attributions de rôles existe dans le rôle *Propriétaire*, le rôle *Administrateur des accès utilisateur* ou un rôle personnalisé avec *l’autorisation Microsoft.Authorization/role assignments/write* attribuée. Cette autorisation n’est pas nécessaire si l’identité managée de la ressource de partage de données est déjà autorisée à accéder au magasin de données Azure. 
 
Lorsque vous ajoutez des ressources dans le hub Azure Resource Mover dans le portail, les autorisations sont gérées automatiquement tant que l’utilisateur dispose des autorisations décrites ci-dessus. Si vous ajoutez des ressources avec PowerShell, vous devez attribuer les autorisations manuellement.

> [!IMPORTANT]
> Nous vous recommandons vivement de ne pas modifier ni de supprimer les attributions de rôles d’identité. 

### <a name="what-if-i-dont-have-permissions-to-assign-role-identity"></a>Que faire si je n’ai pas l’autorisation d’attribuer une identité de rôle ?

Il existe plusieurs raisons pour lesquelles vous n’avez peut-être pas cette autorisation.

**Cause possible** | **Recommandation**
--- | ---
Lorsque vous ajoutez une ressource pour la première fois, vous n’êtes pas *Contributeur* ni *Administrateur de l’accès utilisateur* (ou *Propriétaire*). | Utilisez un compte avec des autorisations *Contributeur* et *Administrateur de l’accès utilisateur* (ou *Propriétaire*) pour l’abonnement.
L’identité managée de Resource Mover n’a pas le rôle requis. | Ajoutez les rôles « Contributeur » et « Administrateur de l’accès utilisateur ».
L’identité managée de Resource Mover a été réinitialisée sur *Aucun*. | Réactivez une identité attribuée par le système dans les paramètres de collection de déplacements > **Identité**. Vous pouvez également ajouter de nouveau la ressource dans **Ajouter des ressources**, ce qui produit le même résultat.  
L’abonnement a été déplacé vers un autre locataire. | Désactivez puis activez l’identité managée pour la collection de déplacements.

### <a name="how-can-i-do-multiple-moves-together"></a>Comment puis-je effectuer plusieurs déplacements en même temps ?

Modifiez les combinaisons source/cible en fonction des besoins à l’aide de l’option de modification dans le portail.

### <a name="what-happens-when-i-remove-a-resource-from-a-list-of-move-resources"></a>Que se passe-t-il quand je supprime une ressource d’une liste de ressources à déplacer ?

Vous pouvez supprimer des ressources que vous avez ajoutées à la liste de ressources à déplacer. Le comportement de suppression exact dépend de l’état de la ressource. [Plus d’informations](remove-move-resources.md#vm-resource-state-after-removing)



## <a name="next-steps"></a>Étapes suivantes

[En savoir plus](about-move-process.md) sur les composants de Resource Mover et le processus de déplacement.
