---
title: Choisir une région pour Microsoft Azure Stack Edge Pro avec GPU | Microsoft Docs
description: Explique le choix des régions pour le service Azure Stack Edge, le stockage de données et les périphériques pour Azure Stack Edge Pro avec GPU, Azure Stack Edge Pro R et Azure Stack Edge Mini R.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/18/2021
ms.author: alkohli
ms.openlocfilehash: 7b585c61dd46339be71e66be66b402a0e107c194
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122564052"
---
# <a name="choosing-a-region-for-azure-stack-edge"></a>Choix d’une région pour Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-and-fpga-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-fpga-sku.md)]

Cet article décrit la disponibilité des régions pour le service Azure Stack Edge, le stockage de données et les livraisons d’appareils pour un appareil Azure Stack Edge, et explique comment votre choix de régions affecte le service, les performances et les facturations.


## <a name="overview"></a>Vue d'ensemble

Les centres de données Azure fonctionnent dans différentes zones géographiques du monde pour répondre aux besoins de performances, aux obligations et aux préférences des clients pour ce qui est de l’emplacement des données. Une zone géographique Azure est une zone définie du monde contenant au moins une région Azure. Une région Azure est une zone géographique contenant un ou plusieurs centres de données.

Le choix d’une région Azure est très important. Il est influencé par des facteurs comme la résidence et la souveraineté des données, la disponibilité du service, les performances, le coût et la redondance. Pour plus d’informations sur le choix d’une région, accédez à [Quelle région Azure est appropriée pour moi ?](https://azure.microsoft.com/overview/datacenters/how-to-choose/)

Pour un périphérique Azure Stack Edge Pro GPU, le choix de la région est déterminé par les facteurs suivants :

- Régions où le service Azure Stack Edge est disponible.
- Régions où les comptes de stockage qui stockent les données Azure Stack Edge doivent être localisés pour des performances optimales.
- Pays/régions vers lesquel(lle)s l’appareil Azure Stack Edge peut être expédié.


## <a name="region-availability-for-the-service"></a>Disponibilité des régions pour le service

Le service Azure Stack Edge est actuellement pris en charge dans les régions publiques des Etats-Unis, de l’Europe Ouest et de l’Asie du sud-est. **Ces trois régions prennent en charge les emplacements géographiques dans le monde entier.**

La région du service est le pays ou la région affecté(e) à la ressource de gestion Azure Stack Edge. La ressource de gestion utilise le service Azure Stack Edge pour activer, déployer et retourner un appareil Azure Stack Edge.

Le service Azure Stack Edge analyse également l’intégrité de l’appareil : problèmes, erreurs, alertes et si l’appareil est « actif ». Et le service surveille les compteurs d’utilisation et de consommation pour la facturation - les informations de plan de contrôle sur l’appareil. La région affectée à la ressource de gestion est la région dans laquelle les facturations se produisent.

L’appareil doit se connecter au service Azure Stack Edge pour l’activer. Si vous ne souhaitez pas une interaction supplémentaire avec le service, vous pouvez basculer l’appareil en mode déconnecté. 

Les données ne transitent pas par le service Azure Stack Edge. Flux de données entre l’appareil et le compte de stockage déployé dans la région d’origine des données du client. 

De manière générale, choisissez l’emplacement le plus proche de la région géographique dans laquelle vous déployez l’appareil. Toutefois, l’appareil et le service peuvent également être déployés à différents emplacements.

## <a name="region-availability-for-data-storage"></a>Disponibilité des régions pour le stockage des données

Les données Azure Stack Edge sont stockées dans des comptes de stockage Azure et ces comptes sont disponibles dans toutes les régions Azure. Lorsque vous créez un compte de stockage Azure, vous choisissez l’emplacement principal du compte de stockage. Ce choix détermine la région où les données résident.

Vous choisissez un compte de stockage lorsque vous [créez un partage sur votre appareil](azure-stack-edge-gpu-deploy-add-shares.md#add-a-share). Votre service Azure Stack Edge et le stockage Azure peuvent se trouver à deux emplacements différents.

En général, choisissez la région la plus proche de votre service pour votre compte de stockage. Toutefois, la région Microsoft Azure la plus proche peut ne pas être la région dont la latence est la plus faible. La latence dicte les performances du service réseau et par conséquent, les performances de l’appareil. Donc, si vous choisissez un compte de stockage dans une autre région, il est important de connaître les latences entre votre service et la région associée à votre compte de stockage.

## <a name="region-of-device"></a>Région de l’appareil

Pour connaître les pays et les régions géographiques où les modèles Azure Stack Edge sont disponibles, consultez la vue d’ensemble du produit :

- [Disponibilité des régions pour Azure Stack Edge Pro avec GPU](azure-stack-edge-gpu-overview.md#region-availability)
- [Disponibilité des régions pour Azure Stack Edge Pro R](azure-stack-edge-pro-r-overview.md#region-availability)
- [Disponibilité des régions pour Azure Stack Edge Mini R](azure-stack-edge-mini-r-overview.md#region-availability)

Microsoft peut expédier du matériel physique et fournir un remplacement de pièces détachées pour Azure Stack Edge vers ces régions géographiques.

> [!IMPORTANT]
> Ne placez pas un appareil physique Azure Stack Edge dans une région où Azure Stack Edge n’est pas pris en charge. Microsoft ne pourra pas être en mesure d’expédier des pièces de rechange dans les pays/régions où Azure Stack Edge n’est pas pris en charge.


## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les [tarifs de différents modèles Azure Stack Edge](https://azure.microsoft.com/pricing/details/azure-stack/edge/).
* [Préparez le déploiement de votre appareil Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md).
