---
title: Gérer les protocoles et les chiffrements dans Gestion des API Azure | Microsoft Docs
description: Découvrez comment gérer les protocoles (TLS) et les chiffrements (DES) dans Gestion des API Azure.
services: api-management
documentationcenter: ''
author: dlepow
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/07/2021
ms.author: danlep
ms.openlocfilehash: 02442bf73d16486ce51b765e6922bbf434fb69f1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128609799"
---
# <a name="manage-protocols-and-ciphers-in-azure-api-management"></a>Gérer les protocoles et les chiffrements dans Gestion des API Azure

La Gestion des API Azure prend en charge plusieurs versions du protocole TLS (Transport Layer Security) pour :
* Côté client
* Côté serveur principal
* Chiffrement 3DES

Ce guide vous montre comment gérer la configuration des protocoles et des chiffrements pour une instance Gestion des API Azure.

![Gérer les protocoles et les chiffrements dans Gestion des API Azure](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers.png)

## <a name="prerequisites"></a>Prérequis

* Une instance APIM. Si vous ne l’avez pas déjà fait, [créez-en un](get-started-create-service-instance.md).

## <a name="how-to-manage-tls-protocols-and-3des-cipher"></a>Comment gérer les protocoles TLS et le chiffrement 3DES

1. Accédez à votre **instance Gestion des API** dans le portail Azure.
1. Faites défiler jusqu’à la section **Sécurité** dans le menu latéral.
1. Sous la section Sécurité, sélectionnez **Protocoles + chiffrements**.  
1. Activez ou désactivez les protocoles ou les chiffrements souhaités.
1. Cliquez sur **Enregistrer**. Les modifications sont appliquées dans l’heure qui suit.  

> [!NOTE]
> Certains protocoles ou suites de chiffrement (par exemple, TLS côté serveur principal 1.2) ne peuvent pas être activés ou désactivés à partir du portail Azure. Au lieu de cela, vous devez appliquer l’appel REST. Utilisez la `properties.customProperties` structure de l’article [Créer/Mettre à jour l’API REST du service de Gestion des API](/rest/api/apimanagement/2020-06-01-preview/api-management-service/create-or-update#request-body).

## <a name="next-steps"></a>Étapes suivantes

* Apprenez-en davantage sur [TLS](/dotnet/framework/network-programming/tls).
* Découvrez plus de [vidéos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sur Gestion des API.
