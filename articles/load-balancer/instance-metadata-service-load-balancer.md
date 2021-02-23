---
title: Récupérer des informations sur l’équilibreur de charge à l’aide d’Azure Instance Metadata Service
titleSuffix: Azure Load Balancer
description: Apprenez à utiliser Azure Instance Metadata Service pour récupérer des informations de l’équilibreur de charge.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: allensu
ms.openlocfilehash: 0d7d08eb5e77e744fb6ce0abefc550bc79de4c8c
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519080"
---
# <a name="retrieve-load-balancer-information-by-using-the-azure-instance-metadata-service"></a>Récupérer des informations sur l’équilibreur de charge à l’aide d’Azure Instance Metadata Service

IMDS (Azure Instance Metadata Service) fournit des informations sur les instances de machine virtuelle en cours d’exécution. Le service est une API REST disponible à une adresse IP connue et non routable (169.254.169.254). 

Lorsque vous placez des instances de machine virtuelle ou de groupe de machines virtuelles derrière Azure Standard Load Balancer, utilisez IMDS pour récupérer les métadonnées relatives à l’équilibreur de charge et aux instances.

Les métadonnées incluent les informations suivantes pour les machines virtuelles ou les groupes de machines virtuelles identiques :

* IP publiques de SKU Standard.
* Configurations des règles de trafic entrant de l’équilibreur de charge pour chaque adresse IP privée de l’interface réseau.
* Configurations des règles de trafic sortant de l’équilibreur de charge pour chaque adresse IP privée de l’interface réseau.

## <a name="access-the-load-balancer-metadata-using-the-imds"></a>Accéder aux métadonnées de l’équilibreur de charge à l’aide d’IMDS

Pour plus d’informations sur l’accès aux métadonnées de l’équilibreur de charge, consultez [Utiliser Azure Instance Metadata Service pour accéder aux informations de l’équilibreur de charge](howto-load-balancer-imds.md).

## <a name="troubleshoot-common-error-codes"></a>Résoudre les codes d’erreur courants

Pour plus d’informations sur les codes d’erreur courants et leurs méthodes d’atténuation, consultez [Résoudre les codes d’erreur courants lors de l’utilisation d’IMDS](troubleshoot-load-balancer-imds.md). 

## <a name="support"></a>Support

Si vous ne parvenez pas à récupérer une réponse de métadonnées après plusieurs tentatives, créez un ticket de support dans le portail Azure.

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur [Azure Instance Metadata Service](../virtual-machines/windows/instance-metadata-service.md)

[Déployer un équilibreur de charge standard](quickstart-load-balancer-standard-public-portal.md)

