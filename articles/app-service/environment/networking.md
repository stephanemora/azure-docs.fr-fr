---
title: Mise en réseau d’App Service Environment
description: Détails de la mise en réseau d’App Service Environment
author: ccompy
ms.assetid: 6f262f63-aef5-4598-88d2-2f2c2f2bfc24
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 0a1221a8de10fd18768a1a0f0ac08277dc2901d6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98735031"
---
# <a name="app-service-environment-networking"></a>Mise en réseau d’App Service Environment

> [!NOTE]
> Cet article traite d’App Service Environment v3 (préversion)
> 

App Service Environment (ASE) est un déploiement monolocataire d’Azure App Service qui héberge des applications web, des applications API et des applications de fonction. Lorsque vous installez un environnement ASE, vous choisissez le réseau virtuel Azure (VNet) dans lequel vous souhaitez qu’il soit déployé. L’ensemble de l’application de trafic entrant et sortant se trouve à l’intérieur du réseau virtuel que vous spécifiez.  

ASEv3 utilise deux sous-réseaux.  Un sous-réseau est utilisé pour le point de terminaison privé qui gère le trafic entrant. Il peut s’agir d’un sous-réseau existant ou d’un nouveau sous-réseau.  Le sous-réseau de trafic entrant peut être utilisé à d’autres fins que le point de terminaison privé. Le sous-réseau de trafic sortant ne peut être utilisé que pour le trafic sortant à partir de l’environnement ASE. Rien d’autre ne peut être placé dans le sous-réseau de trafic sortant de l’environnement ASE.

## <a name="addresses"></a>Adresses 
Lors de sa création, l’environnement ASE a les adresses suivantes :

| Type d’adresse | description |
|--------------|-------------|
| Adresse entrante | L’adresse entrante est l’adresse du point de terminaison privé utilisée par votre environnement ASE. |
| Sous-réseau sortant | Le sous-réseau sortant est également le sous-réseau ASE. Dans la préversion, ce sous-réseau est uniquement utilisé pour le trafic sortant. |
| Adresse sortante Windows | Les applications Windows de cet environnement ASE utilisent cette adresse, par défaut, lors de l’établissement d’appels sortants vers Internet. |
| Adresse sortante Linux | Les applications Linux de cet environnement ASE utilisent cette adresse, par défaut, lors de l’établissement d’appels sortants vers Internet. |

ASEv3 contient des détails sur les adresses utilisées par l’environnement ASE dans la partie **Adresses IP** du portail ASE.

![Interface utilisateur des adresses ASE](./media/networking/networking-ip-addresses.png)

Si vous supprimez le point de terminaison privé utilisé par l’environnement ASE, vous ne pouvez pas atteindre les applications de votre environnement ASE.  

L’environnement ASE utilise des adresses dans le sous-réseau sortant pour prendre en charge l’infrastructure utilisée par l’environnement ASE. À mesure que vous mettez à l’échelle vos plans App Service dans votre environnement ASE, vous utiliserez davantage d’adresses. Les applications de l’environnement ASE n’ont pas d’adresses dédiées dans le sous-réseau sortant. Les adresses utilisées par une application dans le sous-réseau sortant par une application changent au fil du temps.

## <a name="ports"></a>Ports

L’environnement ASE reçoit le trafic d’application sur les ports 80 et 443.  Il n’existe aucune exigence de port entrant ou sortant pour l’environnement ASE. 

## <a name="extra-configurations"></a>Configurations supplémentaires

Contrairement à ASEv2, ASEv3 vous permet de définir des groupes de sécurité réseau (NSG) et des tables de routage (UDR) comme vous le souhaitez, sans restriction. Si vous souhaitez forcer le tunneling de tout le trafic sortant de votre environnement ASE vers un appareil NVA (appliance virtuelle réseau). Vous pouvez placer des appareils WAF devant tout le trafic entrant vers votre environnement ASE. 

## <a name="dns"></a>DNS

Les applications de votre environnement ASE utilisent le DNS avec lequel votre réseau virtuel est configuré. Suivez les instructions de l’article [Utilisation d’un environnement App Service Environment](./using.md#dns-configuration) pour configurer votre serveur DNS de façon à ce qu’il pointe vers votre environnement ASE. Si vous souhaitez que certaines applications utilisent un serveur DNS différent de celui avec lequel votre réseau virtuel est configuré, vous pouvez définir celui-ci manuellement pour chaque application avec les paramètres d’application WEBSITE_DNS_SERVER et WEBSITE_DNS_ALT_SERVER. Le paramètre d’application WEBSITE_DNS_ALT_SERVER configure le serveur DNS secondaire. Le serveur DNS secondaire est utilisé uniquement en l’absence de réponse du serveur DNS principal. 

## <a name="preview-limitation"></a>Limite de la préversion

Certaines fonctionnalités de mise en réseau ne sont pas disponibles avec ASEv3.  Les éléments qui ne sont pas disponibles dans ASEv3 sont les suivants :

• FTP • Débogage à distance • Déploiement de l’équilibrage de charge externe • Possibilité d’accéder à un registre de conteneurs privé pour les déploiements de conteneurs • Possibilité d’effectuer des appels à des réseaux virtuels avec un peering global • Possibilité de sauvegarder/restaurer à l’aide d’un compte de stockage sécurisé avec un point de terminaison de service ou un point de terminaison privé • Possibilité d’avoir des références Key Vault dans les paramètres d’application sur des comptes Key Vault sécurisés avec un point de terminaison de service ou un point de terminaison privé • Possibilité d’utiliser BYOS sur un compte de stockage sécurisé avec un point de terminaison de service ou un point de terminaison privé • Utilisation de Network Watcher ou de NSG Flow sur le trafic sortant
    
    