---
title: Cas d’usage pour les domaines d’événements dans Azure Event Grid
description: Cet article présente quelques cas d’usage des domaines d’événements dans Azure Event Grid.
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 24a338717b44567bad0ec1575d98ddaeada71113
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/21/2021
ms.locfileid: "112413618"
---
# <a name="use-cases-for-event-domains-in-azure-event-grid"></a>Cas d’usage pour les domaines d’événements dans Azure Event Grid
Cet article présente quelques cas d’usage des domaines d’événements dans Azure Event Grid. 

## <a name="use-case-1"></a>Cas d’utilisation 1 
[!INCLUDE [event-grid-domain-example-use-case.md](./includes/event-grid-domain-example-use-case.md)]

## <a name="use-case-2"></a>Cas d’utilisation 2
Il existe une limite de 500 abonnements aux événements lors de l’utilisation des rubriques système. Si vous avez besoin de plus de 500 abonnements aux événements pour une rubrique système, vous pouvez utiliser des domaines. 

Supposons que vous avez créé une rubrique système pour un Stockage Blob Azure et que vous deviez créer plus de 500 abonnements pour la rubrique, mais cela n’est pas possible en raison de la limitation (500 abonnements par rubrique). Dans ce cas, vous pouvez utiliser la solution suivante qui utilise un domaine d’événements. 

1. Créez un domaine, qui peut prendre en charge jusqu’à 100 000 rubriques et chaque rubrique de domaine peut avoir 500 abonnements aux événements. Ce modèle vous permet d’avoir 500 * 100 000 abonnements aux événements. 
1. Créer un abonnement à une fonction Azure pour la rubrique système de stockage Azure. Lorsque la fonction reçoit des événements de stockage Azure, elle peut enrichir et publier des événements dans une rubrique de domaine appropriée. Par exemple, la fonction peut analyser le nom du fichier Blob pour déterminer la rubrique relative au domaine cible et publier la rubrique relative à l’événement dans le domaine. 

:::image type="content" source="./media/event-domains-use-cases/use-case-2.jpg" alt-text="Domaines Azure Event Grid : cas d’usage 2":::


## <a name="next-steps"></a>Étapes suivantes
Pour découvrir comment configurer des domaines d’événements, créer des rubriques, créer des abonnements à des événements et publier des événements, voir [Gérer les domaines d’événements](./how-to-event-domains.md).
