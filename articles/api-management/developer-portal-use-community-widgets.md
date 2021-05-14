---
title: Utiliser les widgets de la ommunauté sur le portail des développeurs
titleSuffix: Azure API Management
description: En savoir plus sur les widgets de la communauté du portail des développeurs Gestion des API et sur la manière de les injecter et de les utiliser dans votre code.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: de6160aa2e556297287ae9e9ecd58a93e54d863f
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741091"
---
# <a name="use-community-widgets-in-the-developer-portal"></a>Utiliser les widgets de la ommunauté sur le portail des développeurs

Tous les développeurs placent leurs widgets fournis par la communauté dans le dossier `/community/widgets/` du [référentiel GitHub](https://github.com/Azure/api-management-developer-portal) du portail des développeurs Gestion des API. Chacun d’entre eux a été accepté par l’équipe du portail des développeurs. Vous pouvez utiliser les widgets en les injectant dans votre [version auto-hébergée](developer-portal-self-host.md) du portail. La version managée du portail des développeurs ne prend pas actuellement en charge les widgets de la communauté.

> [!NOTE]
> L’équipe du portail des développeurs inspecte minutieusement les widgets fournis et leurs dépendances. Toutefois, l’équipe ne peut pas garantir qu’il est possible de charger les widgets en toute sécurité. Suivez votre intuition lorsque vous décidez d’utiliser un widget fourni par la communauté. Pour en savoir plus sur nos mesures préventives, reportez-vous aux [directives pour la contribution de widgets](developer-portal-widget-contribution-guidelines.md#contribution-guidelines) .

## <a name="inject-and-use-external-widgets"></a>Injecter et utiliser des widgets externes

1. Configurez un [environnement local](developer-portal-self-host.md#step-1-set-up-local-environment) pour la dernière version du portail des développeurs.

1. Accédez au dossier du widget dans le répertoire `/community/widgets`. Lisez la description du widget dans le fichier `readme.md`.

1. Inscrivez le widget dans les modules du portail :

    1. `src/apim.design.module.ts` : un module qui enregistre les dépendances au moment de la conception.
    
        ```typescript
        import { WidgetNameDesignModule } from "../community/widgets/<widget-name>/widget.design.module";
    
        ...
    
            injector.bindModule(new WidgetNameDesignModule());
        ```
    
    1. `src/apim.publish.module.ts` : un module qui enregistre les dépendances au moment de la publication.
    
        ```typescript
        import { WidgetNamePublishModule } from "../community/widgets/<widget-name>/widget.publish.module";
    
        ...
    
            injector.bindModule(new WidgetNamePublishModule());
        ```
    
    1. `src/apim.runtime.module.ts` : un module qui enregistre les dépendances au moment de l’exécution.
    
        ```typescript
        import { WidgetNameRuntimeModule } from "../community/widgets/<widget-name>/widget.runtime.module";
    
        ...
    
            injector.bindModule(new WidgetNameRuntimeModule());
        ```

1. Vérifiez si le widget dispose d’un fichier `npm_dependencies`.

1. Si c’est le cas, copiez les commandes à partir du fichier et exécutez-les dans le répertoire supérieur du référentiel.

    Cela a pour effet d’installer les dépendances du widget.

1. Exécutez `npm start`.

Vous pouvez voir le widget dans la catégorie **Communauté** du le sélecteur de widget.

:::image type="content" source="media/developer-portal-use-community-widgets/widget-selector.png" alt-text="Capture d’écran du sélecteur de widgets":::


## <a name="next-steps"></a>Étapes suivantes


Pour plus d’informations sur le portail des développeurs :

- [Vue d’ensemble du portail des développeurs Gestion des API Azure](api-management-howto-developer-portal.md)

- [Widgets de collaboration](developer-portal-widget-contribution-guidelines.md)
