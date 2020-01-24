---
title: Géorécupération d’urgence Azure Spring Cloud | Microsoft Docs
description: Découvrez comment protéger votre application Spring Cloud contre les pannes régionales
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: brendm
ms.openlocfilehash: 4961e5a63e5bc1933cf19b1f291b521d89cbda0e
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279151"
---
# <a name="azure-spring-cloud-disaster-recovery"></a>Récupération d’urgence Azure Spring Cloud

Cet article explique certaines stratégies que vous pouvez utiliser pour protéger vos applications Azure Spring Cloud contre les temps d’arrêt.  Chaque région ou centre de données peut subir un temps d’arrêt causé par des catastrophes régionales, mais une planification soigneuse peut atténuer l’impact sur vos clients.

## <a name="plan-your-application-deployment"></a>Planifier le déploiement de votre application

Les applications Azure Spring Cloud s’exécutent dans une région spécifique.  Azure fonctionne dans plusieurs zones géographiques à travers le monde. Une zone géographique Azure est une zone définie du monde contenant au moins une région Azure. Une région Azure est une zone géographique contenant un ou plusieurs centres de données.  Chaque région Azure est associée à une autre région au sein de la même région géographique, constituant ainsi des paires régionales. Azure sérialise les mises à jour de plateforme (maintenance planifiée) à travers les paires régionales, garantissant ainsi qu’une seule région de chaque paire est mise à jour à la fois. En cas de panne affectant plusieurs régions, au moins l’une des régions de chaque paire est prioritaire pour la récupération.

Pour garantir la haute disponibilité et la protection contre les sinistres, vous devez déployer vos applications Spring Cloud dans plusieurs régions.  Azure fournit une liste de [régions jumelées](../best-practices-availability-paired-regions.md) pour vous permettre de planifier vos déploiements Spring Cloud sur des paires régionales.  Nous vous recommandons de prendre en compte trois facteurs clés lors de la conception de votre architecture de micro-service : la disponibilité des régions, les régions jumelées Azure et la disponibilité du service.

*  Disponibilité dans les régions :  Choisissez une zone géographique proche de vos utilisateurs pour réduire la latence réseau et la durée de transmission.
*  Régions jumelées Azure :  Choisissez des régions jumelées au sein de votre zone géographique choisie pour garantir des mises à jour de plateforme coordonnées et des efforts de récupération hiérarchisés si nécessaire.
*  Disponibilité du service :   Décidez si vos régions appairées doivent avoir un niveau de disponibilité chaud/chaud, chaud/tiède ou chaud/froid.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Utiliser Azure Traffic Manager pour router le trafic

[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) fournit un équilibreur de charge de trafic DNS qui peut répartir le trafic réseau entre plusieurs régions.  Utilisez Azure Traffic Manager pour diriger les clients vers l’instance de service Azure Spring Cloud la plus proche.  Pour des performances et une redondance optimales, dirigez tout le trafic d’application via Azure Traffic Manager avant de l’envoyer à votre service Azure Spring Cloud.

Si vous avez des applications Azure Spring Cloud dans plusieurs régions, utilisez Azure Traffic Manager pour contrôler le flux du trafic vers vos applications dans chaque région.  Définissez un point de terminaison Azure Traffic Manager pour chaque service à l’aide de l’adresse IP du service. Les clients doivent se connecter à un nom DNS Azure Traffic Manager qui pointe vers le service Azure Spring Cloud.  Azure Traffic Manager équilibre la charge du trafic sur les points de terminaison définis.  Si un incident survient dans un centre de données, Azure Traffic Manager dirige le trafic de cette région vers sa paire, garantissant ainsi la continuité du service.