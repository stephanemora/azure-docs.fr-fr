---
title: Rotation des clés d’accès pour Azure SignalR Service
description: Clarification des raisons pour lesquelles le client doit régulièrement changer les clés d’accès et description de la procédure à suivre via l’interface utilisateur du portail et la CLI.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 09/13/2018
ms.author: zhshang
ms.openlocfilehash: 2c0f60b0ef3a90372fc4a095c830f39bc148f354
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53636062"
---
# <a name="access-key-rotation-for-azure-signalr-service"></a>Rotation des clés d’accès pour Azure SignalR Service

Chaque instance Azure SignalR Service dispose d’une paire de clés d’accès : une clé primaire et une clé secondaire. Ces clés servent à authentifier les clients SignalR lorsqu’ils soumettent des demandes au service. Les clés sont associées à l’URL du point de terminaison de l’instance. Protégez vos clés et changez-les régulièrement. Vous disposez de deux clés d’accès, ce qui signifie que vous pouvez maintenir des connexions à l’aide d’une clé pendant que vous régénérez l’autre clé.

## <a name="why-rotate-access-keys"></a>Pourquoi régénérer les clés d’accès ?

Pour des raisons de sécurité et de conformité, il est recommandé aux développeurs de changer régulièrement les clés d’accès.

## <a name="how-to-regenerate-access-keys"></a>Comment régénérer les clés d’accès ?

1. Accédez au [portail Azure ](https://portal.azure.com/) et connectez-vous à l’aide de vos informations d’identification.

1. Recherchez la section **Clés** dans l’instance Azure SignalR Service dont vous souhaitez régénérer les clés.

1. Cliquez sur **Clés** dans le menu de navigation.

1. Cliquez sur **Régénérer la clé primaire** ou **Régénérer la clé secondaire**.

Une nouvelle clé est créée et s’affiche avec la chaîne de connexion correspondante.

 ![Régénérer les clés](media/signalr-key-rotation/regenerate-keys.png)

Vous pouvez également regénérer les clés à l’aide [d’Azure CLI](/cli/azure/ext/signalr/signalr/key?view=azure-cli-latest#ext-signalr-az-signalr-key-renew).

## <a name="update-configurations-with-new-connection-strings"></a>Mettre à jour les configurations avec les nouvelles chaînes de connexion

1. Copiez la chaîne de connexion que vous venez de générer.

1. Mettez à jour toutes les configurations pour utiliser la nouvelle chaîne de connexion.

1. Redémarrez l’application si besoin.

## <a name="forced-access-key-regeneration"></a>Régénération forcée des clés d’accès

Azure SignalR Service peut imposer une régénération des clés d’accès dans certaines situations. Le service informe les clients par e-mail et via une notification sur le portail. Si vous recevez ce message ou si vous rencontrez un problème au niveau du service en raison d’une clé d’accès, régénérez les clés en suivant ce guide.

## <a name="next-steps"></a>Étapes suivantes

Nous vous recommandons de régénérer régulièrement les clés d’accès pour des raisons de sécurité.

Ce guide vous a expliqué comment régénérer des clés d’accès. Passez aux didacticiels suivants pour en savoir plus sur l’authentification avec OAuth ou Azure Functions.

> [!div class="nextstepaction"]
> [Intégrer avec une identité ASP.NET Core](./signalr-authenticate-oauth.md)

> [!div class="nextstepaction"]
> [Génération d’une application en temps réel serverless avec authentification](./signalr-authenticate-azure-functions.md)