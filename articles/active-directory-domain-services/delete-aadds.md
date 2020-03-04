---
title: Supprimer Azure Active Directory Domain Services | Microsoft Docs
description: Découvrez comment désactiver ou supprimer un domaine managé Azure Active Directory Domain Services à partir du portail Azure
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: iainfou
ms.openlocfilehash: e1836f91b8afc1bb4f5b7e141949f3724c57c857
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77614039"
---
# <a name="delete-an-azure-active-directory-domain-services-managed-domain-using-the-azure-portal"></a>Désactiver ou supprimer un domaine managé Azure Active Directory Domain Services à partir du portail Azure

Si vous n’avez plus besoin d’un domaine managé, vous pouvez supprimer une instance Azure Active Directory Domain Services (Azure AD DS). Il n’existe aucune option permettant de désactiver de façon provisoire ou définitive un domaine managé Azure AD DS. La suppression du domaine managé Azure AD DS ne supprime ni n’impacte défavorablement le locataire Azure AD. Cet article explique comment supprimer un domaine managé Azure AD DS à partir du portail Azure.

> [!WARNING]
> **La suppression est définitive et ne peut pas être annulée.**
> Quand vous supprimez un domaine managé Azure AD DS, voici ce qu’il se passe :
>   * Les contrôleurs de domaine pour le domaine managé sont déprovisionnés et supprimés du réseau virtuel.
>   * Les données sur le domaine managé sont supprimées définitivement. Ces données incluent les unités d’organisation personnalisées, les stratégies de groupe, les enregistrements DNS personnalisés, les principaux de service, les GMSA, etc. que vous avez créés.
>   * Les machines jointes au domaine managé perdent leur relation d’approbation avec ce domaine et doivent être disjointes de celui-ci.
>       * Vous ne pouvez pas vous connecter à ces machines en utilisant des informations d’identification AD. Pour cela, vous devez utiliser les informations d’identification de l’administrateur local de la machine.

## <a name="delete-the-managed-domain"></a>Supprimer le domaine managé

Pour supprimer un domaine managé Azure AD DS, effectuez les étapes suivantes :

1. Sur le portail Azure, recherchez et sélectionnez **Azure AD Domain Services**.
1. Sélectionnez le nom de votre domaine managé Azure AD DS, par exemple *aaddscontoso.com*.
1. Dans la page **Vue d’ensemble**, sélectionnez **Supprimer**. Pour confirmer la suppression, tapez à nouveau le nom du domaine managé, puis sélectionnez **Supprimer**.

La suppression du domaine managé Azure AD DS peut prendre entre 15 et 20 minutes, voire plus.

## <a name="next-steps"></a>Étapes suivantes

N’hésitez pas à [faire part de vos commentaires][feedback] concernant les fonctionnalités que vous aimeriez voir dans Azure AD DS.

Si vous voulez redémarrer avec Azure AD DS, consultez [Créer et configurer une instance Azure Active Directory Domain Services][create-instance].

<!-- INTERNAL LINKS -->
[feedback]: contact-us.md
[create-instance]: tutorial-create-instance.md
