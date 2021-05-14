---
title: Adresses IP Azure Integration Runtime
description: Découvrez les adresses IP à partir desquelles vous devez autoriser le trafic entrant, afin de configurer correctement les pare-feu pour sécuriser l’accès réseau aux magasins de données.
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/06/2020
ms.openlocfilehash: 872a8b0124a8ad1c86b201ca90b391d03bb76921
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2021
ms.locfileid: "109485960"
---
# <a name="azure-integration-runtime-ip-addresses"></a>Adresses IP Azure Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Les adresses IP qu’Azure Integration Runtime utilise dépendent de la région où se trouve votre runtime d’intégration Azure. *Tous* les runtimes d’intégration Azure qui se trouvent dans une même région utilisent les mêmes plages d’adresses IP.

> [!IMPORTANT]  
> Les flux de données et Azure Integration Runtime qui activent le réseau virtuel géré ne prennent pas en charge l’utilisation de plages d’adresses IP fixes.
>
> Vous pouvez utiliser ces plages d’adresses IP pour le déplacement des données, le pipeline et les exécutions d’activités externes. Ces plages d’adresses IP peuvent être utilisées pour le filtrage dans les magasins de données/groupes de sécurité réseau (NSG)/pare-feu pour l’accès entrant à partir du runtime d’intégration Azure. 

## <a name="azure-integration-runtime-ip-addresses-specific-regions"></a>Adresses IP d’Azure Integration Runtime : Régions spécifiques

Autorisez le trafic provenant des adresses IP listées pour le runtime d’intégration Azure dans la région Azure spécifique où vos ressources sont situées. Vous pouvez récupérer une liste de plages d’adresses IP à partir du [lien de téléchargement de plage d’adresses IP des balises de service](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files). Par exemple, si la région Azure est **AustraliaEast**, vous pouvez obtenir une liste de plages d’adresses IP à partir de **DataFactory.AustraliaEast**.


## <a name="known-issue-with-azure-storage"></a>Problème connu avec le stockage Azure

* Lors de la connexion au compte de stockage Azure, les règles de réseau IP n’ont aucun effet sur les demandes provenant du runtime d’intégration Azure dans la même région que le compte de stockage. Pour plus d’informations, [consultez cet article](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range). 

  Au lieu de cela, nous vous suggérons d’utiliser des [services approuvés pour vous connecter au stockage Azure](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993). 

## <a name="next-steps"></a>Étapes suivantes

* [Considérations relatives à la sécurité des déplacements de données dans Azure Data Factory](data-movement-security-considerations.md)