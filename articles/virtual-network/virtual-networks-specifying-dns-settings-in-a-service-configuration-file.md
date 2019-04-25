---
title: Définition des paramètres DNS dans un fichier de configuration de service | Microsoft Docs
description: spécification de paramètres DNS personnalisés à l'aide du fichier de configuration de service d’un réseau virtuel
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: 467a4b99-8691-40b3-b640-e25e49675c71
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2016
ms.author: genli
ms.openlocfilehash: 0ac488a67d8b9debf6539d199395997cf44cf1e4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232741"
---
# <a name="specifying-dns-settings-in-a-service-configuration-file"></a>Définition des paramètres DNS dans un fichier de configuration de service
## <a name="dns-elements"></a>Éléments DNS
Un fichier de configuration de service peut contenir un élément DnsServers avec une liste d’adresses IPv4 dédiée aux serveurs DNS que le service utilisera. Les paramètres du fichier de configuration de service priment sur les paramètres du fichier de configuration de réseau. Pour plus d’informations, consultez la rubrique [Schéma de configuration de service Azure (.cscfg)](https://msdn.microsoft.com/library/azure/ee758710.aspx).

**Élément NetworkConfiguration**

      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>

> [!WARNING]
> L’attribut **name** de l’élément **DnsServer** est utilisé uniquement comme nom de référence. Il ne représente aucunement le nom d’hôte attribué au serveur DNS. Chaque valeur d’attribut **DnsServer** doit être unique au sein de l’abonnement Microsoft Azure.
> 
> 

## <a name="see-also"></a>Voir aussi
[Schéma de configuration de service Azure (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710)

[Schéma de configuration du réseau virtuel Azure](https://go.microsoft.com/fwlink/?LinkId=248093)

[Configuration d’un réseau virtuel à l’aide d’un fichier de configuration réseau](https://go.microsoft.com/fwlink/?LinkId=248094)

[À propos des paramètres de réseau virtuel dans le Portail de gestion](https://go.microsoft.com/fwlink/?LinkId=248092)

