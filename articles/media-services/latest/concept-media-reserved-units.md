---
title: Unités réservées multimédias (MRU) - Azure
description: Les unités réservées Multimédia vous permettent de mettre à l'échelle le traitement multimédia et de déterminer la vitesse de vos tâches de traitement multimédia.
services: media-services
documentationcenter: ''
author: jiayali-ms
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: inhenkel
ms.openlocfilehash: 3fc68505712bc5cae0defd216b30a3c5913c3c77
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866435"
---
# <a name="media-reserved-units"></a>Unités réservées Multimédia

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Les unités réservées Multimédia (MRU) ont été précédemment utilisées dans les Services Media Azure v2 pour contrôler la concurrence et les performances d’encodage. Vous n’avez plus besoin de gérer des unités réservées Multimédia ou de demander des augmentations de quota pour un compte de services média, car le système est automatiquement mis à l’échelle en fonction de la charge. Vous verrez également que les performances sont égales ou améliorées par rapport à l’utilisation d’unités réservées Multimédia. 

Si vous avez un compte qui a été créé à l’aide d’une version antérieure à l’API 2020-05-01, vous avez toujours accès à l’API pour la gestion des MRU. Toutefois, aucune des configurations MRU que vous définissez ne sera utilisée pour contrôler la concurrence ou les performances d’encodage. Si vous ne voyez pas l’option permettant de gérer les unités réservées Multimédia dans le portail Azure, cela signifie que vous utilisez un compte créé avec l’API 2020-05-01 ou une version ultérieure. 

## <a name="billing"></a>Facturation

Bien qu’il y ait eu précédemment des frais pour les unités réservées Multimédia, à compter du 17 avril, 2021 il n’y aura plus de frais pour les comptes qui ont une configuration pour les unités réservées Multimédia. Pour plus d’informations sur la facturation des travaux d’encodage, veuillez consulter l’[Encodage de la vidéo et de l’audio avec les Services Media Services](encoding-concept.md)

Pour les comptes créés avec la version **2020-05-01** de l’API (à savoir la version v3) ou via le portail Azure, la mise à l’échelle et les unités réservées Multimédia ne sont plus nécessaires. La mise à l’échelle est désormais gérée automatiquement en interne par le service. Les unités réservées de Multimédia ne sont plus nécessaires ou prises en charge pour un compte de Services Media Azure. Pour plus d’informations, consultez les [unités réservées Multimédia (héritées)](concept-media-reserved-units.md) .

## <a name="see-also"></a>Voir aussi

* [Migrer de Media Services v2 vers v3](migrate-v-2-v-3-migration-introduction.md)
* [Mettre à l’échelle les unités réservées au multimédia avec l’interface CLI](media-reserved-units-cli-how-to.md)
