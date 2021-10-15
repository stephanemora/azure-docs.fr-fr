---
title: 'Tutoriel : Configurer manuellement Azure Front Door pour Azure Static Web Apps'
description: Apprenez à configurer Azure Front Door pour Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: how-to
ms.date: 09/20/2021
ms.author: cshoe
ms.openlocfilehash: aa377c63f0282bb43e8f69088f7925d9ac5ffa0d
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/03/2021
ms.locfileid: "129400683"
---
# <a name="tutorial-manually-configure-azure-front-door-for-azure-static-web-apps"></a>Tutoriel : Configurer manuellement Azure Front Door pour Azure Static Web Apps

Apprenez à ajouter [Azure Front Door](../frontdoor/front-door-overview.md) comme CDN pour votre application web statique.  Azure Front Door est un point d’entrée évolutif et sécurisé qui permet de livrer rapidement vos applications web.

Dans ce tutoriel, vous apprenez à effectuer les opérations suivantes :

> [!div class="checklist"]
>
> - Créer une instance Azure Front Door
> - Associer Azure Front Door à votre site Azure Static Web Apps

> [!NOTE]
> Ce tutoriel requiert le plan Azure Static Web Apps Standard.

## <a name="copy-url"></a>Copier l’URL

1. Accédez au portail Azure.

1. Ouvrez l’application web statique à laquelle vous souhaitez appliquer Azure Front Door.

1. Accédez à la section *Vue d’ensemble*.

1. Copiez l’*URL* dans votre Presse-papiers pour une utilisation ultérieure.

## <a name="add-azure-front-door"></a>Ajouter Azure Front Door

1. Accédez au portail Azure.

1. Sélectionnez **Créer une ressource**.

1. Recherchez **Azure Front Door**.

1. Sélectionnez **Front Door**.

    Veillez à ne pas sélectionner le service étiqueté *Front Door Standard/Premium*, car les étapes du service Standard/Premium diffèrent de ce qui est présenté dans ce tutoriel.

1. Sélectionnez **Create** (Créer).

1. Dans l’onglet *Informations de base*, entrez les valeurs suivantes :

    | Paramètre | Valeur |
    |---|---|
    | Abonnement | Sélectionnez votre abonnement Azure. |
    | Resource group | Entrez un nom de groupe de ressources. Ce nom est souvent le même que celui du groupe utilisé par votre application web statique. |
    | Emplacement du groupe de ressources | Si vous créez un nouveau groupe de ressources, entrez l’emplacement le plus proche de vous. |

    Sélectionnez **Suivant : Configuration >** .

1. Dans l’onglet *Configuration*, sélectionnez le **signe plus** à côté de *Serveurs frontaux/domaines*, puis entrez la valeur suivante :

    | Paramètre | Valeur |
    |---|---|
    | Nom de l’hôte | Entrez un nom unique pour votre hôte Front Door. |

    Acceptez les valeurs par défaut pour le reste du formulaire, puis sélectionnez **Ajouter**.

1. Sélectionnez le **signe plus** à côté de *Pools principaux*, puis entrez la valeur suivante :

    | Paramètre | Valeur |
    |---|---|
    | Nom | Entrez **my-static-web-app-pool**. |

1. Sélectionnez **+ Ajouter un serveur principal**, puis entrez les valeurs suivantes :

    | Paramètre | Valeur |
    |---|---|
    | Type de l’hôte backend | Sélectionnez un **hôte personnalisé**. |
    | Nom de l’hôte backend | Entrez le nom d’hôte de votre application web statique. Assurez-vous que votre valeur ne comprend pas de barre oblique de fin de protocole. (Par exemple : `desert-rain-04056.azurestaticapps.net`)  |
    | En-tête d’hôte principal | Entrez le nom d’hôte de votre application web statique. Assurez-vous que votre valeur ne comprend pas de barre oblique de fin de protocole. (Par exemple : `desert-rain-04056.azurestaticapps.net`) |

    Acceptez les valeurs par défaut pour le reste du formulaire, puis sélectionnez **Ajouter**.

1. Sélectionnez **Ajouter**.

1. Sélectionnez le **signe plus** à côté de *Règle d’acheminement*, puis entrez la valeur suivante :

    | Paramètre | Valeur |
    |---|---|
    | Nom | Entrez **my-routing-rule**. |

    Acceptez les valeurs par défaut pour le reste du formulaire, puis sélectionnez **Ajouter**.

1. Sélectionnez **Revoir + créer**.

1. Sélectionnez **Create** (Créer).

    Le processus de création peut prendre quelques minutes.

1. Sélectionnez **Accéder à la ressource**.

1. Sélectionnez **Vue d’ensemble**.

1. Sélectionnez le lien étiqueté *Hôte frontend*.

    Lorsque vous sélectionnez ce lien, une erreur 404 peut s’afficher si le site n’est pas complètement propagé. Au lieu d’actualiser la page, attendez quelques minutes, puis revenez à la fenêtre *Vue d’ensemble* et sélectionnez le lien étiqueté *Hôte frontend*.

1. À partir de la fenêtre *Vue d’ensemble*, copiez la valeur intitulée **ID Front Door** et collez-la dans un fichier pour une utilisation ultérieure.

> [!IMPORTANT]
> Par défaut, Azure Front Door configure des [sondes d’intégrité](../frontdoor/front-door-health-probes.md) qui peuvent avoir un impact sur vos statistiques de trafic. Vous pouvez modifier les valeurs par défaut des [sondes d’intégrité](../frontdoor/front-door-health-probes.md).

## <a name="update-static-web-app-configuration"></a>Mettre à jour la configuration des applications web statiques

Pour terminer l’intégration à Front Door, vous devez mettre à jour le fichier de configuration de l’application pour :

- Restreindre le trafic vers votre site uniquement via Front Door
- Restreindre le trafic vers votre site uniquement depuis votre instance Front Door
- Définir les domaines qui peuvent accéder à votre site

Ouvrez le fichier [staticwebapp.config.json](configuration.md) de votre site et apportez les modifications suivantes.

1. Restreignez le trafic de sorte qu’il utilise uniquement Front Door en ajoutant la section suivante au fichier de configuration :

    ```json
    "networking": {
      "allowedIpRanges": ["AzureFrontDoor.Backend"]
    }
    ```

1. Pour définir quelles instances et quels domaines d’Azure Front Door peuvent accéder à votre site, ajoutez la section `forwardingGateway`.

    ```json
    "forwardingGateway": {
      "requiredHeaders": {
        "X-Azure-FDID" : "<YOUR-FRONT-DOOR-ID>"
      },
      "allowedForwardedHosts": [
        "my-sitename.azurefd.net"
      ]
    }
    ```

    Tout d’abord, configurez votre application pour qu’elle autorise uniquement le trafic provenant de votre instance Front Door. Dans chaque requête du serveur principal, Front Door ajoute automatiquement un en-tête `X-Azure-FDID` qui contient l’ID de votre instance Front Door. En configurant votre application web statique pour qu’elle exige cet en-tête, vous limitez le trafic exclusivement à votre instance Front Door. Dans la section `forwardingGateway` de votre fichier de configuration, ajoutez la section `requiredHeaders` et définissez l’en-tête `X-Azure-FDID`. Remplacez `<YOUR-FRONT-DOOR-ID>` par l’*ID Front Door* que vous avez mis de côté précédemment.

    Ensuite, ajoutez le nom d’hôte Azure Front Door (et non le nom d’hôte Azure Static Web Apps) dans le tableau `allowedForwardedHosts`. Si vous avez des domaines personnalisés configurés dans votre instance Front Door, incluez-les également dans cette liste.

    Dans cet exemple, remplacez `my-sitename.azurefd.net` par le nom d’hôte Azure Front Door de votre site.

Grâce à cette configuration, votre site n’est plus disponible via le nom d’hôte `*.azurestaticapps.net` généré, mais exclusivement via les noms d’hôte configurés dans votre instance Front Door.

> [!NOTE]
> Lorsque vous déployez des mises à jour de fichiers existants dans votre application web statique, Azure Front Door peut continuer à alimenter des versions plus anciennes de vos fichiers jusqu’à l’expiration de leur [durée de vie](https://wikipedia.org/wiki/Time_to_live). [Videz le cache d’Azure Front Door](../frontdoor/front-door-caching.md#cache-purge) pour les chemins d’accès concernés afin de vous assurer que les fichiers les plus récents sont alimentés.

Maintenant que Front Door gère votre site, vous n’utilisez plus la fonctionnalité de domaine personnalisé Azure Static Web Apps. Azure Front Door dispose d’un processus distinct pour ajouter un domaine personnalisé. Reportez-vous à [Ajouter un domaine personnalisé à votre Front Door](../frontdoor/front-door-custom-domain.md). Lorsque vous ajoutez un domaine personnalisé à Front Door, vous devez mettre à jour le fichier de configuration de votre application web statique pour l’inclure dans la liste `allowedForwardedHosts`.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter une API](apis.md)
