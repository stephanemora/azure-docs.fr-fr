---
title: Importer une spécification OpenAPI à l’aide du portail Azure | Microsoft Docs
description: Découvrez comment importer une spécification OpenAPI avec le service Gestion des API, puis tester l’API dans les portails des développeurs et Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/20/2020
ms.author: apimpm
ms.openlocfilehash: 346156c624a4c8b1c1d6cc490fc0559f364b4b9d
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87905565"
---
# <a name="import-an-openapi-specification"></a>Importer une spécification OpenAPI

Cet article montre comment importer une API de serveur principal de « spécification OpenAPI » se trouvant à l’adresse https://conferenceapi.azurewebsites.net?format=json. Cette API de serveur principal est fournie par Microsoft et hébergée sur Azure. L’article explique également comment tester l’API APIM.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Importer une API de serveur principal de spécification OpenAPI
> * Tester l’API dans le portail Azure
> * Tester l’API dans le portail des développeurs

## <a name="prerequisites"></a>Prérequis

Suivez ce guide de démarrage rapide : [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>Importer et publier une API back-end

1. À partir du portail Azure, accédez à votre service Gestion des API, puis sélectionnez **API** dans le menu.
2. Sélectionnez **Spécification OpenAPI** dans la liste **Ajouter une nouvelle API**.

    ![Spécification OpenAPI](./media/import-api-from-oas/oas-api.png)
3. Entrez les paramètres de l’API. Vous pouvez définir les valeurs lors de la création, ou les configurer ultérieurement en accédant à l’onglet **Paramètres**. Les paramètres sont expliqués dans le tutoriel [Importer et publier votre première API](import-and-publish.md#-import-and-publish-a-backend-api).
4. Sélectionnez **Create** (Créer).

> [!NOTE]
> Les limitations relatives à l’importation d’API sont documentées dans [un autre article](api-management-api-import-restrictions.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Transform and protect your API](transform-api.md) (Transformer et protéger votre API)
