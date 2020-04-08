---
title: Gérer les protocoles et les chiffrements dans Gestion des API Azure | Microsoft Docs
description: Découvrez comment gérer les protocoles (TLS) et les chiffrements (DES) dans Gestion des API Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/29/2019
ms.author: apimpm
ms.openlocfilehash: 7f87389016286c9f1b91abc77155be9e94005371
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335868"
---
# <a name="manage-protocols-and-ciphers-in-azure-api-management"></a>Gérer les protocoles et les chiffrements dans Gestion des API Azure

Gestion des API Azure prend en charge plusieurs versions du protocole TLS du côté client et du côté backend, ainsi que le chiffrement 3DES.

Ce guide vous montre comment gérer la configuration des protocoles et des chiffrements pour une instance Gestion des API Azure.

![Gérer les protocoles et les chiffrements dans Gestion des API Azure](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers.png)

## <a name="prerequisites"></a>Prérequis

Pour suivre les étapes décrites dans cet article, vous devez avoir :

* Une instance Gestion des API

## <a name="how-to-manage-tls-protocols-and-3des-cipher"></a>Comment gérer les protocoles TLS et le chiffrement 3DES

1. Accédez à votre **instance Gestion des API** dans le portail Azure.
2. Sélectionnez **Paramètres de protocole** dans le menu.  
3. Activez ou désactivez les protocoles ou les chiffrements souhaités.
4. Cliquez sur **Enregistrer**. Les modifications sont appliquées dans l’heure qui suit.  

## <a name="next-steps"></a>Étapes suivantes

* Découvrez plus d’informations sur [TLS (Transport Layer Security)](https://docs.microsoft.com/dotnet/framework/network-programming/tls).
* Découvrez plus de [vidéos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sur Gestion des API.