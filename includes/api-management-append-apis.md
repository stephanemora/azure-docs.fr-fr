---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 04/16/2021
ms.author: vlvinogr
ms.openlocfilehash: 890cb1dc8d8a59f6b567497b968b53bb89ec1ecc
ms.sourcegitcommit: 49bd8e68bd1aff789766c24b91f957f6b4bf5a9b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2021
ms.locfileid: "108230623"
---
## <a name="append-other-apis"></a>Ajouter d’autres API

Vous pouvez composer une API à partir de plusieurs API exposées par différents services, notamment :
* La spécification OpenAPI
* Une API SOAP
* Une application web hébergée dans Azure App Service
* Application Azure Function
* Azure Logic Apps
* Azure Service Fabric

Pour ajouter une autre API à votre API existante, effectuez les étapes suivantes. 

>[!NOTE] 
> Une fois que vous avez importé une autre API, les opérations sont ajoutées à votre API actuelle.

1. Accédez à votre instance Gestion des API Azure dans le portail Azure.

    :::image type="content" source="./media/api-management-append-apis/service-page.png" alt-text="Accéder à l’instance Gestion des API Azure":::

1. Sélectionnez **API** dans la page **Vue d’ensemble** ou dans le menu situé à gauche.

    :::image type="content" source="./media/api-management-append-apis/api-select.png" alt-text="Sélectionner des API":::

1. Cliquez sur **...** à côté de l’API à laquelle vous souhaitez ajouter une autre API.
1. Sélectionnez **Importer** dans le menu déroulant.

    :::image type="content" source="./media/api-management-append-apis/append-01.png" alt-text="Sélectionner Importer":::

1. Sélectionnez un service à partir duquel importer une API.

    :::image type="content" source="./media/api-management-append-apis/select-to-import.png" alt-text="Sélectionner un service":::
