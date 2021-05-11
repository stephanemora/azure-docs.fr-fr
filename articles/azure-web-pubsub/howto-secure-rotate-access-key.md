---
title: Procéder à la rotation des clés d'accès pour le service Azure Web PubSub
description: Clarification des raisons pour lesquelles le client doit procéder à une rotation régulière des clés d'accès et description de la procédure à suivre.
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: how-to
ms.date: 03/23/2021
ms.openlocfilehash: 73c31051a177dc16711048bec984a90c786a28f2
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108166914"
---
# <a name="how-to-rotate-access-key-for-azure-web-pubsub-service"></a>Procéder à la rotation des clés d'accès pour le service Azure Web PubSub

Chaque instance du service Azure Web PubSub dispose d'une paire de clés d'accès appelées clé primaire et clé secondaire. Ces clés servent à authentifier les clients lors des demandes d'accès au service. Les clés sont associées à l'URL du point de terminaison de l'instance. Protégez vos clés et changez-les régulièrement. Vous disposez de deux clés d’accès, ce qui signifie que vous pouvez maintenir des connexions à l’aide d’une clé pendant que vous regénérez l’autre clé.

## <a name="why-rotate-access-keys"></a>Pourquoi régénérer les clés d’accès ?

Pour des raisons de sécurité et de conformité, vous devez changer régulièrement vos clés d’accès.

## <a name="regenerate-access-keys"></a>Régénération de clés d'accès

1. Accédez au [portail Azure ](https://portal.azure.com/) et connectez-vous à l’aide de vos informations d’identification.

1. Recherchez la section **Clés** dans l'instance du service Azure Web PubSub dont vous souhaitez régénérer les clés.

1. Sélectionnez **Clés** dans le menu de navigation.

1. Sélectionnez **Régénérer la clé primaire** ou **Régénérer la clé secondaire**.

   Une nouvelle clé est créée et s’affiche avec la chaîne de connexion correspondante.

Vous pourrez également régénérer les clés à l'aide d'Azure CLI une fois le service PubSub Web Azure en disponibilité générale.

## <a name="update-configurations-with-new-connection-strings"></a>Mettre à jour les configurations avec les nouvelles chaînes de connexion

1. Copiez la chaîne de connexion que vous venez de générer.

1. Mettez à jour toutes les configurations pour utiliser la nouvelle chaîne de connexion.

1. Redémarrez l’application si besoin.

## <a name="forced-access-key-regeneration"></a>Régénération forcée des clés d’accès

Dans certaines situations, le service Azure Web PubSub peut imposer une régénération des clés d'accès. Le service en informe alors les clients par e-mail et via une notification sur le portail. Si vous recevez ce message ou si vous rencontrez un problème de clé d’accès au niveau du service, regénérez les clés en suivant ce guide.