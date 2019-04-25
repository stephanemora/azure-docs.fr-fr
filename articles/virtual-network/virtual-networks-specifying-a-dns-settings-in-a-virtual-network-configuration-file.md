---
title: Définition des paramètres DNS dans un fichier de configuration de réseau virtuel | Microsoft Docs
description: Comment modifier les paramètres de serveur DNS dans un réseau virtuel à l’aide d’un fichier de configuration de réseau virtuel dans le modèle de déploiement classique
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
tags: azure-service-management
ms.assetid: a8905927-92ac-42b5-8c33-8e42c000692c
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2016
ms.author: genli
ms.openlocfilehash: 36f7ed9b02b66718327c1a05a6cf29eedf39e7a5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232854"
---
# <a name="specifying-dns-settings-in-a-virtual-network-configuration-file"></a>Définition des paramètres DNS dans un fichier de configuration de réseau virtuel
Un fichier de configuration réseau comporte deux éléments que vous pouvez utiliser pour spécifier les paramètres du système DNS (Domain Name) : **DnsServers** et **DnsServerRef**. Vous pouvez ajouter une liste de serveurs DNS en définissant leur adresse IP et leur nom de référence sur l’élément **DnsServers** . Ensuite, utilisez un élément **DnsServerRef** afin de spécifier les entrées du serveur DNS de l’élément DnsServers qui sont utilisées pour différents sites au sein de votre réseau virtuel.

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Cet article traite du modèle de déploiement classique.

Le fichier de configuration réseau peut contenir les éléments suivants. Le titre de chaque élément est associé à une page qui fournit des informations supplémentaires sur les paramètres de valeurs de l’élément.

> [!IMPORTANT]
> Pour plus d'informations sur la configuration du fichier de configuration réseau, consultez la section [Configuration d'un réseau virtuel à l'aide d'un fichier de configuration réseau](virtual-networks-using-network-configuration-file.md). Pour plus d’informations sur les éléments contenus dans le fichier de configuration réseau, consultez [Schéma de configuration du réseau virtuel Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).
> 
> 

[Élément Dns](https://go.microsoft.com/fwlink/?LinkId=248093)

    <Dns>
      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>
    </Dns>

> [!WARNING]
> L’attribut **name** de l’élément **DnsServer** est utilisé uniquement comme référence pour l’élément **DnsServerRef**. Il ne représente aucunement le nom d’hôte attribué au serveur DNS. Chaque attribut **DnsServer** doit être unique au sein de l’abonnement Microsoft Azure
> 
> 

[Élément de sites de réseau virtuel](https://go.microsoft.com/fwlink/?LinkId=248093)

    <DnsServersRef>
      <DnsServerRef name="ID1" />
      <DnsServerRef name="ID2" />
      <DnsServerRef name="ID3" />
    </DnsServersRef>

> [!NOTE]
> Pour définir ce paramètre pour l’élément de sites de réseau virtuel, vous devez préalablement le définir dans l’élément DNS. L’attribut *name* DnsServerRef de l’élément VirtualNetworkSite doit faire référence à une valeur de nom spécifiée dans l’élément DNS pour l’attribut *name* DnsServer.
> 
> 

## <a name="next-steps"></a>Étapes suivantes
* Découvrez le [schéma de configuration du réseau virtuel Azure](https://go.microsoft.com/fwlink/?LinkId=248093).
* Découvrez le [schéma de configuration du service Azure](https://msdn.microsoft.com/library/windowsazure/ee758710).
* [Configuration d’un réseau virtuel à l’aide d’un fichier de configuration réseau](virtual-networks-using-network-configuration-file.md)

