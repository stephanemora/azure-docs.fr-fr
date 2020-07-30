---
title: Adresses IP Azure Integration Runtime
description: Découvrez les adresses IP à partir desquelles vous devez autoriser le trafic entrant, afin de configurer correctement les pare-feu pour sécuriser l’accès réseau aux magasins de données.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/06/2020
ms.openlocfilehash: 0c64a38295754e4754326dec126bfbc36e1bef61
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86523330"
---
# <a name="azure-integration-runtime-ip-addresses"></a>Adresses IP Azure Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Les adresses IP qu’Azure Integration Runtime utilise dépendent de la région où se trouve votre runtime d’intégration Azure. *Tous* les runtimes d’intégration Azure qui se trouvent dans une même région utilisent les mêmes plages d’adresses IP.

> [!IMPORTANT]  
> Les flux de données et Azure Integration Runtime qui activent le réseau virtuel géré ne prennent pas en charge l’utilisation de plages d’adresses IP fixes.
>
> Vous pouvez utiliser ces plages d’adresses IP pour le déplacement des données, le pipeline et les exécutions d’activités externes. Ces plages d’adresses IP peuvent être utilisées pour la mise en liste verte dans les magasins de données/groupes de sécurité réseau (NSG)/pare-feu pour l’accès entrant à partir du runtime d’intégration Azure. 

## <a name="azure-integration-runtime-ip-addresses-specific-regions"></a>Adresses IP d’Azure Integration Runtime : Régions spécifiques

Autorisez le trafic provenant des adresses IP listées pour le runtime d’intégration Azure dans la région Azure spécifique où vos ressources sont situées. Vous pouvez récupérer une liste de plages d’adresses IP à partir du [lien de téléchargement de plage d’adresses IP des balises de service](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files). Par exemple, si la région Azure est **AustraliaEast**, vous pouvez obtenir une liste de plages d’adresses IP à partir de **DataFactory.AustraliaEast**.


## <a name="known-issue-with-azure-storage"></a>Problème connu avec le stockage Azure

* Lors de la connexion au compte de stockage Azure, les règles de réseau IP n’ont aucun effet sur les demandes provenant du runtime d’intégration Azure dans la même région que le compte de stockage. Pour plus d’informations, [consultez cet article](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range). 

  Au lieu de cela, nous vous suggérons d’utiliser des [services approuvés pour vous connecter au stockage Azure](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993). 

## <a name="next-steps"></a>Étapes suivantes

* [Considérations relatives à la sécurité des déplacements de données dans Azure Data Factory](data-movement-security-considerations.md)
