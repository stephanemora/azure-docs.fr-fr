---
author: jianleishen
ms.service: data-factory
ms.topic: include
ms.date: 09/29/2021
ms.author: jianleishen
ms.openlocfilehash: a5fbf3071c134b52fb053afc88f6efb51a5ea2f8
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129725433"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime setup from connector articles.
-->
Si votre magasin de données se trouve dans un réseau local, un réseau virtuel Azure ou un cloud privé virtuel Amazon, vous devez configurer un [runtime d’intégration auto-hébergé](../create-self-hosted-integration-runtime.md) pour vous y connecter.

Si votre magasin de données est un service de données cloud managé, vous pouvez utiliser Azure Integration Runtime. Si l’accès est limité aux adresses IP qui sont approuvées dans les règles de pare-feu, vous pouvez ajouter les [adresses IP Azure Integration Runtime](../azure-integration-runtime-ip-addresses.md) dans la liste d’autorisation. 

Vous pouvez également utiliser la fonctionnalité de [runtime d’intégration de réseau virtuel managé](../tutorial-managed-virtual-network-on-premise-sql-server.md) dans Azure Data Factory pour accéder au réseau local sans installer et configurer un runtime d’intégration auto-hébergé.

Pour plus d’informations sur les mécanismes de sécurité réseau et les options pris en charge par Data Factory, consultez [Stratégies d’accès aux données](../data-access-strategies.md).
