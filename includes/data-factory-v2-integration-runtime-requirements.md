---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: 6c348b3dd0005eeab154aa2d74abc617cbd1d0cb
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87529384"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Si votre magasin de données se trouve dans un réseau local, un réseau virtuel Azure ou un cloud privé virtuel Amazon, vous devez configurer un [runtime d’intégration auto-hébergé](../articles/data-factory/create-self-hosted-integration-runtime.md) pour vous y connecter.

Si votre magasin de données est un service de données cloud managé, vous pouvez utiliser Azure Integration Runtime. Si l’accès est limité à la liste verte d’adresses IP définie dans les règles de pare-feu, vous pouvez choisir d’ajouter les [adresses IP Azure Integration Runtime](../articles/data-factory/azure-integration-runtime-ip-addresses.md) dans la liste verte. 

Pour plus d’informations sur les mécanismes de sécurité réseau et les options pris en charge par Data Factory, consultez [Stratégies d’accès aux données](../articles/data-factory/data-access-strategies.md).
