---
title: Systèmes d’exploitation invités pour Azure Stack | Microsoft Docs
description: Ces systèmes d’exploitation invités peuvent être utilisés avec Azure Stack.
services: azure-stack
documentationcenter: ''
author: Brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: Brenduns
ms.reviewer: JeffGoldner
ms.openlocfilehash: 8d9337053c8905886ed4429d64f8ef5b4e2c7d14
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060445"
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Systèmes d’exploitation invités pris en charge par Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

## <a name="windows"></a>Windows

Azure Stack prend en charge les systèmes d’exploitation invités Windows répertoriés dans le tableau suivant :

| Système d’exploitation | Description | Disponible sur la Place de marché |
| --- | --- | --- | --- | --- | --- |
| Windows Server, version 1709 | 64 bits | Core avec conteneurs |
| Windows Server 2016 | 64 bits |  Centre de données, centre de données principal, centre de données avec conteneurs |
| Windows Server 2012 R2 | 64 bits |  Centre de données |
| Windows Server 2012 | 64 bits |  Centre de données |
| Windows Server 2008 R2 SP1 | 64 bits |  Centre de données |
| Windows Server 2008 SP2 | 64 bits |  Apportez votre propre image |
| Windows 10 *(voir remarque 1)* | 64 bits, Professionnel et Entreprise | Apportez votre propre image |

***Remarque 1 :*** *pour déployer des systèmes d’exploitation clients Windows 10 sur Azure Stack, vous devez disposer d’une [licence Windows par utilisateur](https://www.microsoft.com/en-us/Licensing/product-licensing/windows10.aspx) ou acheter auprès d’un Qualified Multitenant Hoster ([QMTH](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)).*

Les images de la Place de marché sont disponibles avec un système de licence de paiement à l’utilisation ou BYOL (apportez votre propre licence) (EA/SPLA). L’utilisation des deux sur une instance unique d’Azure Stack n’est pas prise en charge. Au cours du déploiement, Azure Stack injecte une version appropriée de l’agent invité dans l’image.

 Les éditions de centre de données sont disponibles au téléchargement sur la Place de marché ; Les clients peuvent apporter leurs propres images de serveur, y compris d’autres éditions. Les images des clients Windows ne sont pas disponibles sur la Place de marché.

## <a name="linux"></a>Linux

Les distributions Linux répertoriées comme disponibles sur la Place de marché incluent l’agent Windows Azure Linux (WALA) nécessaire. Si vous ajoutez votre propre image à Azure Stack, suivez les instructions de l’article [Ajouter des images Linux à Azure Stack](azure-stack-linux.md).

> [!NOTE]
> Les images personnalisées doivent être générées avec la dernière version publique de WALA. Les versions antérieures à 2.2.18 peuvent ne pas fonctionner correctement sur Azure Stack.
>
> [init-cloud](https://cloud-init.io/) n’est pas pris en charge sur Azure Stack pour le moment.

| Distribution | Description | Publisher | Marketplace |
| --- | --- | --- | --- | --- | --- |
| CentOS-based 6.9 | 64 bits | Rogue Wave | OUI |
| CentOS-based 7.4 | 64 bits | Rogue Wave | OUI |
| ClearLinux | 64 bits | ClearLinux.org | OUI |
| Conteneur Linux |  64 bits | CoreOS | Stable |
| Debian 8 Jessie | 64 bits | credativ |  OUI |
| Debian 9 "Stretch" | 64 bits | credativ | OUI |
| Red Hat Enterprise Linux 7.x | 64 bits | Red Hat |Apportez votre propre image |
| SLES 11SP4 | 64 bits | SUSE | OUI |
| SLES 12SP3 | 64 bits | SUSE | OUI |
| Ubuntu 14.04-LTS | 64 bits | Canonical | OUI |
| Ubuntu 16.04-LTS | 64 bits | Canonical | OUI |
| Ubuntu 18.04-LTS | 64 bits | Canonical | OUI |

D’autres distributions de Linux pourraient être prises en charge à l’avenir.

Pour plus d’informations de support pour Red Hat Enterprise Linux, reportez-vous à [Red Hat et Azure Stack : Forum aux Questions](https://access.redhat.com/articles/3413531).
