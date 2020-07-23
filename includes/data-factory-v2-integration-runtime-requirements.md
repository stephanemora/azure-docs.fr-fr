---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: 994b8285999bfa52e2aea9a57ad832aefddcfb76
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86544406"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Si votre magasin de données se trouve dans un réseau local, un réseau virtuel Azure ou un cloud privé virtuel Amazon, vous devez configurer un [runtime d’intégration auto-hébergé](../articles/data-factory/create-self-hosted-integration-runtime.md) pour vous y connecter.

Si votre magasin de données est un service de données cloud managé dans lequel l’accès est limité à la liste verte d’adresses IP définie dans les règles de pare-feu, vous pouvez utiliser le runtime d’intégration Azure et ajouter [ses adresses IP](../articles/data-factory/azure-integration-runtime-ip-addresses.md) dans la liste d’autorisations. 

Consultez [Stratégies d’accès aux données](../articles/data-factory/data-access-strategies.md) pour plus d’informations sur les mécanismes de sécurité réseau pris en charge par Data Factory pour accéder aux magasins de données en général.
