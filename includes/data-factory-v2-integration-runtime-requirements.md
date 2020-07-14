---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 0b03957178af0578d2c6cd91d7377c93f413cec3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84629535"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Si votre magasin de données est configuré de l’une des manières suivantes, vous devez configurer un [runtime d’intégration auto-hébergé](../articles/data-factory/create-self-hosted-integration-runtime.md) pour pouvoir vous connecter à ce magasin de données :

- Le magasin de données se trouve dans un réseau local, au sein d’un réseau virtuel Azure ou au sein d’un cloud privé virtuel Amazon.
- Le magasin de données est un service de données cloud managé dans lequel l’accès est limité à la liste verte d’adresses IP définie dans les règles de pare-feu.
