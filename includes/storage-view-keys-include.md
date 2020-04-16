---
title: Fichier include
description: Fichier include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/06/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 57368331e83111836e13b6c3796cacad9816e318
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521281"
---
Pour afficher et copier les clés d’accès ou la chaîne de connexion de votre compte de stockage à partir du portail Azure :

1. Accédez au [portail Azure](https://portal.azure.com).
2. Recherchez votre compte de stockage.
3. Sous **Paramètres**, sélectionnez **Clés d’accès**. Vos clés d’accès au compte s’affichent, ainsi que la chaîne de connexion complète de chaque clé.
4. Recherchez la valeur de **Clé** sous **clé1**, puis cliquez sur le bouton **Copier** pour copier la clé de compte.
5. Vous pouvez aussi copier la chaîne de connexion complète. Recherchez la valeur de **Chaîne de connexion** sous **clé1**, puis cliquez sur le bouton **Copier** pour copier la chaîne de connexion.

    ![Capture d’écran montrant comment afficher les clés d’accès sur le portail Azure](media/storage-view-keys-include/portal-connection-string.png)

Vous pouvez utiliser la clé de votre choix pour accéder au Stockage Azure, mais il est en général conseillé d’utiliser la première et de réserver la deuxième pour le roulement des clés.

Pour visualiser ou lire les clés d’accès d’un compte, l’utilisateur doit être administrateur de service ou titulaire d’un rôle RBAC qui comprend **Microsoft.Storage/storageAccounts/listkeys/action**. Voici quelques rôles RBAC intégrés qui incluent cette action : **Propriétaire**, **Contributeur** et **Rôle de service d’opérateur de clé de compte de stockage**. Pour plus d’informations sur le rôle d’administrateur de service, consultez [Rôles d’administrateur d’abonnement classique, rôles RBAC Azure et rôles Azure AD](../articles/role-based-access-control/rbac-and-directory-admin-roles.md). Pour plus d’informations sur les rôles intégrés pour Stockage Azure, consultez la section **Stockage** dans [Rôles intégrés Azure pour Azure RBAC](../articles/role-based-access-control/built-in-roles.md#storage).
