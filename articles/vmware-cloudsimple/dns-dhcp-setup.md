---
title: Azure VMware Solutions (AVS) - Configurer le DNS et le DHCP des charges de travail pour le cloud privé AVS
description: Décrit comment configurer le DNS et le DHCP des applications et des charges de travail s’exécutant dans votre environnement de cloud privé AVS
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f2a5cae868f2d8f3689f05dd9d466715ab2008a3
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024685"
---
# <a name="set-up-dns-and-dhcp-applications-and-workloads-in-your-avs-private-cloud"></a>Configurer des applications et des charges de travail DNS et DHCP dans votre cloud privé AVS

Les applications et charges de travail exécutées dans un environnement de cloud privé AVS nécessitent une résolution de noms et des services DHCP pour la recherche et l’attribution d’adresses IP. Une infrastructure DHCP et DNS appropriée est nécessaire pour fournir ces services. Vous pouvez configurer une machine virtuelle pour fournir ces services dans votre environnement de cloud privé AVS. 

## <a name="prerequisites"></a>Conditions préalables requises

* Un groupe de ports distribués avec un réseau VLAN configuré
* Configuration du routage vers des serveurs DNS locaux ou situés sur Internet
* Modèle de machine virtuelle ou ISO pour créer une machine virtuelle

## <a name="linux-based-dns-server-setup"></a>Configuration d’un serveur DNS basé sur Linux

Linux offre différents packages pour la configuration de serveurs DNS. Voici un [exemple de configuration de DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-ubuntu-18-04) avec des instructions pour la configuration d’un serveur DNS BIND open source.

## <a name="windows-based-setup"></a>Configuration Windows

Ces rubriques Microsoft expliquent comment configurer un serveur Windows en tant que serveur DNS et en tant que serveur DHCP.

* [Serveur Windows en tant que serveur DNS](https://docs.microsoft.com/windows-server/networking/dns/dns-top)
* [Serveur Windows en tant que serveur DHCP](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)
