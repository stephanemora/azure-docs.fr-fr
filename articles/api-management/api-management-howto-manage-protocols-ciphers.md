---
title: Gérer les protocoles et les chiffrements dans Gestion des API Azure | Microsoft Docs
description: Découvrez comment gérer les protocoles (TLS, SSL) et les chiffrements (DES) dans Gestion des API Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: a55d16a35b5eec1af2b24d02e158905493615999
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52441110"
---
# <a name="manage-protocols-and-ciphers-in-azure-api-management"></a>Gérer les protocoles et les chiffrements dans Gestion des API Azure

Gestion des API Azure prend en charge plusieurs versions du protocole TLS du côté client et du côté backend, ainsi que le chiffrement 3DES.

Ce guide vous montre comment gérer la configuration des protocoles et des chiffrements pour une instance Gestion des API Azure.

![Gérer les protocoles et les chiffrements dans Gestion des API Azure](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Prérequis

Pour suivre les étapes décrites dans cet article, vous devez avoir :

* Une instance Gestion des API

## <a name="how-to-manage-tls-protocols-and-3des-cipher"></a>Comment gérer les protocoles TLS et le chiffrement 3DES

1. Accédez à votre **instance Gestion des API** dans le portail Azure.
2. Sélectionnez **SSL** dans le menu.  
    ![Gérer les protocoles et les chiffrements dans Gestion des API Azure - Menu](./media/api-management-howto-manage-protocols-ciphers/api-management-menu.png)
3. Activez ou désactivez les protocoles ou les chiffrements souhaités.
4. Cliquez sur **Enregistrer**. Les modifications sont appliquées dans l’heure qui suit.  
    ![Gérer les protocoles et les chiffrements dans Gestion des API Azure - Enregistrer](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers-save.png)

## <a name="next-steps"></a>Étapes suivantes

* Découvrez plus d’informations sur [TLS (Transport Layer Security)](https://docs.microsoft.com/dotnet/framework/network-programming/tls).
* Découvrez plus de [vidéos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sur Gestion des API.