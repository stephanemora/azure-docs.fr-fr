---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: 66b7159d19838cf08158d794f9b8e8819efc3f41
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112039833"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime setup from connector articles.
-->
Si votre magasin de données se trouve dans un réseau local, un réseau virtuel Azure ou un cloud privé virtuel Amazon, vous devez configurer un [runtime d’intégration auto-hébergé](../create-self-hosted-integration-runtime.md) pour vous y connecter.

Si votre magasin de données est un service de données cloud managé, vous pouvez utiliser Azure Integration Runtime. Si l’accès est limité aux adresses IP qui sont approuvées dans les règles de pare-feu, vous pouvez ajouter les [adresses IP Azure Integration Runtime](../azure-integration-runtime-ip-addresses.md) dans la liste d’autorisation. 

Vous pouvez également utiliser la fonctionnalité de [runtime d’intégration de réseau virtuel managé](../tutorial-managed-virtual-network-on-premise-sql-server.md) dans Azure Data Factory pour accéder au réseau local sans installer et configurer un runtime d’intégration auto-hébergé.

Pour plus d’informations sur les mécanismes de sécurité réseau et les options pris en charge par Data Factory, consultez [Stratégies d’accès aux données](../data-access-strategies.md).
