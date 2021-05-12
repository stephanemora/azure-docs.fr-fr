---
title: Utiliser Azure DNS avec d’autres services Azure DNS
description: Dans ce parcours d'apprentissage, commencez à découvrir comment utiliser Azure DNS pour résoudre les noms d’autres services Azure
services: dns
documentationcenter: na
author: rohinkoul
manager: kumudD
tags: azure dns
ms.assetid: e9b5eb94-7984-4640-9930-564bb9e82b78
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 05/03/2021
ms.author: rohink
ms.openlocfilehash: c9360555ff108937b51f9b9b491b6eec006b4f2e
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108745052"
---
# <a name="how-azure-dns-works-with-other-azure-services"></a>Fonctionnement d’Azure DNS avec d’autres services Azure

Azure DNS est un service hébergé de gestion de DNS et de résolution de noms. Vous pouvez l’utiliser pour créer des noms DNS publics pour d’autres applications et services que vous déployez dans Azure. La création d’un nom pour un service Azure dans votre domaine personnalisé est simple. Il vous suffit d’ajouter un enregistrement du type approprié pour votre service.

* Pour les adresses IP allouées de manière dynamique, vous pouvez créer un enregistrement DNS CNAME qui mappe vers le nom DNS qu’Azure a créé pour votre service. Les normes DNS vous empêchent d’utiliser un enregistrement CNAME à l’extrémité de la zone. Vous pouvez utiliser un enregistrement d’alias à la place. Pour plus d’informations, consultez [Tutoriel : Configurer un enregistrement d’alias pour référencer une adresse IP publique Azure](tutorial-alias-pip.md).
* Pour les adresses IP allouées de manière statique, vous pouvez créer un enregistrement DNS A en utilisant n’importe quel nom, ce qui inclut un nom de *domaine nu* au niveau de l’apex de zone.

Le tableau suivant présente les types d’enregistrements pris en charge que vous pouvez utiliser pour différents services Azure. Comme le montre le tableau, Azure DNS ne prend en charge que les enregistrements DNS pour les ressources réseau accessibles sur Internet. Azure DNS ne peut pas être utilisé pour la résolution de noms d’adresses privées internes.

| Service Azure | interface réseau | Description |
| --- | --- | --- |
| Azure Application Gateway |[Adresse IP publique front-end](dns-custom-domain.md#public-ip-address) |Vous pouvez créer un enregistrement DNS A ou CNAME. |
| Azure Load Balancer |[Adresse IP publique front-end](dns-custom-domain.md#public-ip-address) |Vous pouvez créer un enregistrement DNS A ou CNAME. Load Balancer peut disposer d’une adresse IP publique IPv6 affectée de manière dynamique. Créez un enregistrement CNAME pour une adresse IPv6. |
| Azure Traffic Manager |Nom public |Vous pouvez créer un enregistrement d’alias qui mappe au nom trafficmanager.net affecté à votre profil Traffic Manager. Pour plus d’informations, consultez [Tutoriel : Configurer un enregistrement d’alias pour prendre en charge des noms de domaine d’apex avec Traffic Manager](tutorial-alias-tm.md). |
| Services cloud Azure |[Adresse IP publique](dns-custom-domain.md#public-ip-address) |Pour les adresses IP allouées de manière statique, vous pouvez créer un enregistrement DNS A. Pour les adresses IP allouées de manière dynamique, vous devez créer un enregistrement CNAME qui mappe au nom *cloudapp.net* .|
| Azure App Service | [Adresse IP externe](dns-custom-domain.md#app-service-web-apps) |Pour les adresses IP externes, vous pouvez créer un enregistrement DNS A. Dans le cas contraire, vous devez créer un enregistrement CNAME qui mappe au nom azurewebsites.net. Pour plus d’informations, consultez [Mapper un nom de domaine personnalisé à une application Azure](../app-service/app-service-web-tutorial-custom-domain.md). |
| Machines virtuelles Azure Resource Manager |[Adresse IP publique](dns-custom-domain.md#public-ip-address) |Les machines virtuelles Resource Manager peuvent avoir des adresses IP publiques. Une machine virtuelle avec une adresse IP publique peut également être derrière un équilibreur de charge. Vous pouvez créer un enregistrement DNS A, CNAME ou d’alias pour l’adresse publique. Vous pouvez utiliser ce nom personnalisé pour contourner l’adresse IP virtuelle sur l’équilibreur de charge. |
| les machines virtuelles Classic, |[Adresse IP publique](dns-custom-domain.md#public-ip-address) |Les machines virtuelles classiques créées à l’aide de PowerShell ou de l’interface CLI peuvent être configurées avec une adresse virtuelle (réservée) dynamique ou statique. Vous pouvez créer un enregistrement DNS CNAME ou un enregistrement A, respectivement. |


## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [gérer des jeux d’enregistrements et des enregistrements](./dns-getstarted-portal.md) dans votre zone DNS.
