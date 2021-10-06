---
title: Partager une galerie à l’aide du contrôle d’accès en fonction du rôle
description: Découvrez comment partager une galerie à l’aide du contrôle d’accès en fonction du rôle.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 08/31/2021
ms.author: cynthn
ms.custom: template-how-to
ms.openlocfilehash: 01c98ba8d619573c241e5a8fdfa2f774b4aed22d
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123452625"
---
# <a name="use-rbac-to-share-image-gallery-resources"></a>Utiliser le RBAC pour partager des ressources de la galerie d’images

Tout comme la galerie d’images partagées, la définition d’image et la version d’image sont des ressources, qui peuvent être partagées à l’aide des contrôles d’accès en fonction du rôle (RBAC) Azure natifs intégrés. Azure RBAC permet de partager ces ressources avec d’autres utilisateurs, principaux de service et groupes. Vous pouvez même partager l’accès avec des personnes en dehors du locataire au sein duquel ils ont été créés. Un utilisateur disposant d’un accès à la version d’image partagée peut déployer une machine virtuelle ou un groupe de machines virtuelles identiques.  

Nous vous recommandons de partager les images au niveau de la galerie, afin de proposer une expérience optimale. Nous vous déconseillons de partager des versions d’images individuelles. Pour plus d’informations sur Azure RBAC, consultez [Attribution de rôles Azure](../role-based-access-control/role-assignments-portal.md).

Si l’utilisateur se trouve en dehors de votre organisation, il recevra un message d’invitation à rejoindre l’organisation. L’utilisateur doit accepter l’invitation, puis il pourra voir la galerie et toutes les définitions et versions d’image versions dans sa liste de ressources.

## <a name="share-a-gallery"></a>Partager une galerie

### <a name="portal"></a>[Portail](#tab/portal)

Si l’utilisateur se trouve en dehors de votre organisation, il recevra un message d’invitation à rejoindre l’organisation. L’utilisateur doit accepter l’invitation, puis il pourra voir la galerie et toutes les définitions et versions d’image versions dans sa liste de ressources.

1. Sur la page de votre galerie d’images, sélectionnez **contrôle d’accès (IAM)** dans le menu de gauche. 
1. Sous **Ajouter une attribution de rôle**, sélectionnez **Ajouter**. Le volet **Ajouter une attribution de rôle** s’ouvre. 
1. Sous **Rôle**, sélectionnez **Lecteur**.
1. Sous **Attribuer l’accès à**, laissez la valeur par défaut **Utilisateur, groupe ou principal du service Azure AD**.
1. Sous **Sélectionner**, saisissez l’adresse de messagerie de la personne que vous souhaitez inviter.
1. Si l’utilisateur se trouve en dehors de votre organisation, vous verrez le message **Cet utilisateur recevra un e-mail qui lui permettra de collaborer avec Microsoft.** Sélectionnez l’utilisateur avec son adresse e-mail, puis cliquez sur **Enregistrer**.


### <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli)

Pour obtenir l’ID d’objet de votre galerie, utilisez [az sig show](/cli/azure/sig#az_sig_show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Utilisez l’ID d’objet en tant qu’étendue, ainsi qu’une adresse e-mail et [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) pour donner aux utilisateurs un accès à la galerie d’images partagées. Remplacez `<email-address>` et `<gallery iD>` par vos propres informations.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

Pour plus d’informations sur le partage de ressources à l’aide de RBAC, consultez [Gérer l’accès à l’aide de RBAC et Azure CLI](../role-based-access-control/role-assignments-cli.md).

### <a name="powershell"></a>[PowerShell](#tab/powershell)

Utilisez une adresse e-mail et la cmdlet [Get-AzADUser](/powershell/module/az.resources/get-azaduser) pour obtenir l’ID d’objet de l’utilisateur, puis utilisez [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) pour leur donner accès à la galerie. Remplacez l’exemple d’e-mail, alinne_montes@contoso.com dans cet exemple, par vos propres informations.

```azurepowershell-interactive
# Get the object ID for the user
$user = Get-AzADUser -StartsWith alinne_montes@contoso.com
# Grant access to the user for our gallery
New-AzRoleAssignment `
   -ObjectId $user.Id `
   -RoleDefinitionName Reader `
   -ResourceName $gallery.Name `
   -ResourceType Microsoft.Compute/galleries `
   -ResourceGroupName $resourceGroup.ResourceGroupName

```

---


## <a name="next-steps"></a>Étapes suivantes

Créez une [définition d’image et une version d’image](image-version.md).

[Générateur d’image Azure (préversion)](./image-builder-overview.md) permet d’automatiser la création d’une version de l’image. Vous pouvez même l’utiliser pour mettre à jour et [créer une nouvelle version de l’image à partir d’une image existante](./windows/image-builder-gallery-update-image-version.md). 

Vous pouvez également créer la ressource de galerie d’images partagées à l’aide de modèles. Plusieurs modèles de démarrage rapide Azure sont disponibles : 

- [Créer une galerie d’images partagées](https://azure.microsoft.com/resources/templates/sig-create/)
- [Créer une définition d’image dans une galerie d’images partagées](https://azure.microsoft.com/resources/templates/sig-image-definition-create/)
- [Créer une version d’image dans une galerie d’images partagées](https://azure.microsoft.com/resources/templates/sig-image-version-create/)
