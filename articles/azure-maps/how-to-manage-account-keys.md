---
title: Guide pratique sur la gestion de votre compte et de vos clés Azure Maps | Microsoft Docs
description: Vous pouvez utiliser le portail Azure pour gérer votre compte et vos clés d’accès Azure Maps.
services: azure-maps
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: article
ms.service: azure-maps
manager: timlt
ms.openlocfilehash: 091b91b51f7c880a1edbf65a0e2c83560d4bc38b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-manage-your-azure-maps-account-and-keys"></a>Guide pratique sur la gestion de votre compte et de vos clés Azure Maps

Vous pouvez gérer votre compte et vos clés Azure Maps par le biais du portail Azure. Lorsque vous avez un compte et une clé, vous pouvez mettre en œuvre les API dans votre site web ou votre application mobile.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-a-new-account"></a>Créer un nouveau compte

1. Connectez-vous au [Portail Azure](http://portal.azure.com).

1. Cliquez sur **Créer une ressource** en haut à gauche du portail Azure.

2. Recherchez et sélectionnez **Cartes**, puis cliquez sur **Créer**.

3. Saisissez les informations associées au nouveau compte. 

![Entrer les informations du compte dans le portail](./media/how-to-manage-account-keys/new-account-portal.png)

## <a name="manage-keys-on-the-account-page"></a>Gérer les clés dans la page du compte

Une fois le compte créé, vous obtenez des clés générées de manière aléatoire. Vous utilisez les clés pour vous authentifier sur les API Maps lorsque vous souhaitez récupérer les données de cartes ou créer une nouvelle instance de carte JavaScript. 

Vous pouvez rechercher vos clés dans le portail Azure. Accédez à votre compte, puis sélectionnez **Clés** dans le menu.

![Gérer les clés du compte dans le portail](./media/how-to-manage-account-keys/account-keys-portal.png)

À partir de cette page, vous pouvez copier vos clés ou en générer de nouvelles. 

## <a name="delete-an-account"></a>Supprimer un compte

Vous pouvez supprimer un compte à partir du portail Azure. Accédez à la page de vue d’ensemble du compte, puis sélectionnez **Supprimer**.

![Supprimer votre compte dans le portail](./media/how-to-manage-account-keys/account-delete-portal.png)

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment utiliser l’[API de gestion de Maps](https://docs.microsoft.com/rest/api/maps-management/accounts) pour créer, mettre à jour et supprimer des comptes Maps. 