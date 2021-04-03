---
title: Fichier include
description: Fichier include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/04/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 4170671b1dbcb8d2932b54f101cf5691b2461e31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86035543"
---
## <a name="copy-your-credentials-from-the-azure-portal"></a>Copier vos informations d’identification depuis le portail Azure

L’exemple d’application a besoin d’authentifier l’accès à votre compte de stockage. Pour l’authentification, ajoutez les informations d’identification de votre compte de stockage à l’application sous la forme d’une chaîne de connexion. Affichez les informations d'identification de votre compte de stockage en suivant ces étapes :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Recherchez votre compte de stockage.
3. Dans la section **Paramètres** de la présentation du compte de stockage, sélectionnez **Clés d’accès**. Vos clés d’accès au compte s’affichent, ainsi que la chaîne de connexion complète de chaque clé.
4. Recherchez la valeur de **Chaîne de connexion** sous **clé1**, puis sélectionnez le bouton **Copier** pour copier la chaîne de connexion. Vous allez ajouter la valeur de chaîne de connexion dans une variable d’environnement à l’étape suivante.

    ![Capture d’écran montrant comment copier une chaîne de connexion à partir du portail Azure](media/storage-copy-connection-string-portal/portal-connection-string.png)
