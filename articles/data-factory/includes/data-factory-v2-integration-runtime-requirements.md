---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: f2d93449744ea7993ec22f200c51fea8280d1576
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108747793"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Si votre magasin de données se trouve dans un réseau local, un réseau virtuel Azure ou un cloud privé virtuel Amazon, vous devez configurer un [runtime d’intégration auto-hébergé](../create-self-hosted-integration-runtime.md) pour vous y connecter.

Si votre magasin de données est un service de données cloud managé, vous pouvez aussi utiliser Azure Integration Runtime. Si l’accès est limité aux adresses IP qui sont approuvées dans les règles de pare-feu, vous pouvez choisir d’ajouter les [adresses IP Azure Integration Runtime](../azure-integration-runtime-ip-addresses.md) dans la liste d’autorisation. 

Pour plus d’informations sur les mécanismes de sécurité réseau et les options pris en charge par Data Factory, consultez [Stratégies d’accès aux données](../data-access-strategies.md).
