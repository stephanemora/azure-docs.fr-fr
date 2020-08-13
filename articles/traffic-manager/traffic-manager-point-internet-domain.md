---
title: Pointer un domaine Internet vers Traffic Manager - Azure Traffic Manager
description: Cet article vous aide à rediriger votre nom de domaine d’entreprise vers un nom de domaine Traffic Manager.
services: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: rohink
ms.openlocfilehash: 69bdf9a0e04b4d9c2a55f1c0f346d601830ded09
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88053063"
---
# <a name="point-a-company-internet-domain-to-an-azure-traffic-manager-domain"></a>Redirection d’un domaine Internet d’entreprise vers un domaine Azure Traffic Manager

Lorsque vous créez un profil Traffic Manager, Azure attribue automatiquement un nom DNS pour ce profil. Pour utiliser un nom de votre zone DNS, créez un enregistrement DNS CNAME qui mappe sur le nom de domaine de votre profil Traffic Manager. Vous pouvez trouver le nom de domaine Traffic Manager dans la section **Général** de la page Configuration du profil Traffic Manager.

Par exemple, pour pointer le nom `www.contoso.com` vers le nom DNS de Traffic Manager `contoso.trafficmanager.net`, vous créez l’enregistrement de ressource DNS suivant :

`www.contoso.com IN CNAME contoso.trafficmanager.net.`

L’intégralité des requêtes de trafic pour *www\.contoso.com* est redirigée vers *contoso.trafficmanager.net*.

> [!IMPORTANT]
> Vous ne pouvez pas rediriger un domaine de second niveau tel que *contoso.com*vers le domaine Traffic Manager. Les normes de protocole DNS n’autorisent pas les enregistrements CNAME pour les noms de domaine de second niveau.

## <a name="next-steps"></a>Étapes suivantes

* [Méthodes de routage de Traffic Manager](traffic-manager-routing-methods.md)
* [Traffic Manager - Désactiver, activer ou supprimer un profil](disable-enable-or-delete-a-profile.md)
* [Traffic Manager - Désactiver ou activer un point de terminaison](disable-or-enable-an-endpoint.md)
