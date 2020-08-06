---
author: baanders
description: Fichier include pour vérifier l’attribution de rôle dans la configuration d’Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: e651b02bf72ced58b6cba637a68ace3258514176
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87405574"
---
Une façon de vérifier que vous avez correctement configuré l’attribution de rôle consiste à afficher les attributions de rôle pour l’instance Azure Digital Twins dans le [portail Azure](https://portal.azure.com). Accédez à la page du portail des [instances Azure Digital Twins](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) (vous pouvez utiliser ce lien ou le rechercher dans la barre de recherche du portail), puis sélectionnez le nom de l’instance à vérifier. 

Ensuite, affichez tous les rôles qui lui sont attribués sous *Contrôle d’accès (IAM) > Attributions de rôle*. L’utilisateur doit s’afficher dans la liste avec un rôle de *Propriétaire Azure Digital Twins (préversion)* . 

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Vue des attributions de rôles pour une instance Azure Digital Twins dans le portail Azure":::