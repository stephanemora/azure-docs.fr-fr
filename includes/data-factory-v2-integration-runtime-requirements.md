---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: fbde8bc28f8fc34b7a6a6443950b8733c6dcff45
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91672119"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Si votre magasin de données se trouve dans un réseau local, un réseau virtuel Azure ou un cloud privé virtuel Amazon, vous devez configurer un [runtime d’intégration auto-hébergé](../articles/data-factory/create-self-hosted-integration-runtime.md) pour vous y connecter.

Si votre magasin de données est un service de données cloud managé, vous pouvez aussi utiliser Azure Integration Runtime. Si l’accès est limité aux adresses IP qui sont approuvées dans les règles de pare-feu, vous pouvez choisir d’ajouter les [adresses IP Azure Integration Runtime](../articles/data-factory/azure-integration-runtime-ip-addresses.md) dans la liste d’autorisation. 

Pour plus d’informations sur les mécanismes de sécurité réseau et les options pris en charge par Data Factory, consultez [Stratégies d’accès aux données](../articles/data-factory/data-access-strategies.md).
