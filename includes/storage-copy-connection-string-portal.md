---
title: Fichier Include
description: Fichier Include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/04/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: bef314e60b962e39b3a55d0fb7acb40d4d1b4c32
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38732876"
---
## <a name="copy-your-credentials-from-the-azure-portal"></a>Copier vos informations d’identification depuis le portail Azure

L’exemple d’application a besoin d’authentifier l’accès à votre compte de stockage. Pour l’authentification, vous devez fournir vos informations d’identification du compte de stockage à l’application sous la forme d’une chaîne de connexion. Pour afficher les informations d’identification de votre compte de stockage :

1. Accédez au [portail Azure](https://portal.azure.com).
2. Recherchez votre compte de stockage.
3. Dans la section **Paramètres** de la présentation du compte de stockage, sélectionnez **Clés d’accès**. Vos clés d’accès au compte s’affichent, ainsi que la chaîne de connexion complète de chaque clé.   
4. Recherchez la valeur de **Chaîne de connexion** sous **clé1**, puis cliquez sur le bouton **Copier** pour copier la chaîne de connexion. Vous allez ajouter la valeur de chaîne de connexion dans une variable d’environnement à l’étape suivante.

    ![Capture d’écran montrant comment copier une chaîne de connexion à partir du portail Azure](media/storage-copy-connection-string-portal/portal-connection-string.png)
