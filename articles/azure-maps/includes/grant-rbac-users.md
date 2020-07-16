---
title: Accorder l’accès en fonction du rôle pour des utilisateurs
titleSuffix: Azure Maps
description: Utilisez le contrôle d’accès en fonction du rôle pour octroyer aux utilisateurs l’autorisation vers Azure Maps
author: philmea
ms.author: philmea
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 86f89397f3685443071788580253ee11ce4b70be
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84988372"
---
## <a name="grant-role-based-access-for-users-to-azure-maps"></a>Accorder l’accès en fonction du rôle pour des utilisateurs à Azure Maps

Pour octroyer le *contrôle d’accès en fonction du rôle* (RBAC), vous devez attribuer soit un groupe Azure AD soit des principaux de sécurité à une ou plusieurs définitions de rôle de contrôle d’accès Azure Maps. Pour afficher les définitions de rôle RBAC disponibles pour Azure Maps, accédez à **Contrôle d’accès (IAM)** . Sélectionnez **Rôles**, puis recherchez les rôles qui commencent par *Azure Maps*.

* Pour gérer efficacement un grand nombre d’accès des utilisateurs à Azure Maps, consultez [Groupes Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups).
* Pour que les utilisateurs soient autorisés à s’authentifier auprès de l’application, les utilisateurs doivent être créés dans Azure AD. Consultez [Ajouter ou supprimer des utilisateurs à l’aide d’Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory).

En savoir plus sur [Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/) pour gérer efficacement un répertoire pour les utilisateurs.

1. Accédez à votre **compte Azure Maps**. Sélectionnez **Contrôle d’accès (IAM)**  > **Attributions de rôles**.

    ![Octroyer le RBAC](../media/how-to-manage-authentication/how-to-grant-rbac.png)

2. Sous l’onglet **Attributions de rôle**, sous **Rôle**, sélectionnez une définition de rôle Azure Maps intégrée, comme **Lecteur de données Azure Maps** ou **Contributeur aux données Azure Maps**. Sous **Attribuer l’accès à**, sélectionnez **Utilisateur, groupe ou principal du service Azure AD**. Sélectionnez le principal par nom. Ensuite, sélectionnez **Enregistrer**.

   * Pour plus d’informations, consultez [Ajouter ou supprimer des attributions de rôles](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

> [!WARNING]
> Les définitions de rôles intégrées Azure Maps fournissent un accès d’autorisation très large à de nombreuses API REST Azure Maps. Pour limiter les API à un minimum pour les utilisateurs, consultez [créer une définition de rôle personnalisée et attribuer des utilisateurs](https://docs.microsoft.com/azure/role-based-access-control/custom-roles) pour connaître la définition de rôle personnalisée. Cela permet aux utilisateurs d’obtenir les privilèges minimum nécessaires à l’application.