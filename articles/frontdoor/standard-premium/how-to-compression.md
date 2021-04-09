---
title: Améliorer les performances en compressant les fichiers dans Azure Front Door Standard/Premium (préversion)
description: Découvrez comment améliorer la vitesse de transfert de fichiers et les performances de chargement de page en compressant vos fichiers dans Azure Front Door.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 4b526d82465862b1c0d27aed6443c6d7199bfb5b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101097654"
---
# <a name="improve-performance-by-compressing-files-in-azure-front-door-standardpremium-preview"></a>Améliorer les performances en compressant les fichiers dans Azure Front Door Standard/Premium (préversion)

> [!Note]
> Cette documentation est destinée à Azure Front Door Standard/Premium (préversion). Vous recherchez des informations sur Azure Front Door ? Affichez [ici](../front-door-overview.md).

La compression de fichier est une méthode efficace pour améliorer la vitesse de transfert de fichiers et les performances de chargement de page. La compression réduit la taille du fichier avant qu’il ne soit envoyé par le serveur. La compression de fichier peut réduire les coûts de bande passante et procurer une meilleure expérience à vos utilisateurs.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (préversion) est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
Il existe deux façons d’activer la compression de fichiers :

- Activation de la compression sur votre serveur d’origine. Azure Front Door fait transiter les fichiers compressés et les remet aux clients qui les demandent.
- Activation de la compression directement sur les serveurs POP Azure Front Door (*compression à la volée*). Dans ce cas, Azure Front Door compresse les fichiers et les envoie aux utilisateurs finaux.

> [!IMPORTANT]
> La propagation sur le réseau des modifications apportées à la configuration Azure Front Door prend jusqu’à 10 minutes. Si vous configurez la compression de votre point de terminaison CDN pour la première fois, patientez 1 à 2 heures avant de chercher à résoudre un problème, pour être certain que les paramètres de compression ont été propagés à tous les POP.

## <a name="enabling-compression"></a>Activation de la compression

> [!Note]
> Dans Azure Front Door, la compression fait partie de l’**activation de la mise en cache** dans la route. Vous ne pouvez tirer parti de la compression dans Azure Front Door que si vous **activez la mise en cache**.

Vous pouvez activer la compression en procédant comme suit :
* Lors d’une création rapide : quand vous activez la mise en cache, vous pouvez activer la compression.
* Lors d’une création personnalisée : activez la mise en cache et la compression quand vous ajoutez une route. 
* Dans la route Endpoint Manager.
* Dans la page Optimisation.

### <a name="enable-compression-in-endpoint-manager"></a>Activer la compression dans Endpoint Manager

1. À partir de la page de profil Azure Front Door Standard/Premium, accédez à **Endpoint Manager** et sélectionnez le point de terminaison pour lequel vous souhaitez activer la compression.

1. Sélectionnez **Modifier le point de terminaison**, puis sélectionnez la **route** pour laquelle vous souhaitez activer la compression. 

   :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-1.png" alt-text="Capture d’écran de la page d’arrivée d’Endpoint Manager" lightbox="../media/how-to-compression/front-door-compression-endpoint-manager-1-expanded.png":::   

1. Vérifiez que la case **Activer la mise en cache** est cochée, puis cochez la case **Activer la compression**.

   :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-2.png" alt-text="Activer la compression dans Endpoint Manager":::   

1. Sélectionnez **Mettre à jour** pour enregistrer la configuration.

### <a name="enable-compression-in-optimization"></a>Activer la compression dans Optimisations

1. Dans la page de profil Azure Front Door Standard/Premium, accédez à **Optimisations** sous Paramètres. Développez le point de terminaison pour voir la liste des routes. 

1. Sélectionnez les points de suspension en regard de la **route** pour laquelle la compression est *désactivée*. Ensuite, sélectionnez **Configurer la route**.

   :::image type="content" source="../media/how-to-compression/front-door-compression-optimization-1.png" alt-text="Écran d’activation de la compression dans la page Optimisations" lightbox="../media/how-to-compression/front-door-compression-optimization-1-expanded.png"::: 

1. Vérifiez que la case **Activer la mise en cache** est cochée, puis cochez la case **Activer la compression**.

     :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-2.png" alt-text="Capture d’écran de l’activation de la compression dans Endpoint Manager"::: 

1. Cliquez sur **Update**.

## <a name="modify-compression-content-type"></a>Modifier le type de contenu de la compression

Vous pouvez modifier la liste par défaut des types MIME dans la page Optimisations.

1. Dans la page de profil Azure Front Door Standard/Premium, accédez à **Optimisations** sous Paramètres. Sélectionnez ensuite la **route** pour laquelle la compression est *activée*.

1. Sélectionnez les points de suspension en regard de la **route** pour laquelle la compression est *activée*. Sélectionnez ensuite **Afficher les types de fichiers compressés**.

   :::image type="content" source="../media/how-to-compression/front-door-compression-edit-content-type.png" alt-text="Capture d’écran de la page Optimisations" lightbox="../media/how-to-compression/front-door-compression-edit-content-type-expanded.png"::: 

1. Supprimez des formats par défaut ou sélectionnez **Ajouter** pour ajouter de nouveaux types de contenu.

   :::image type="content" source="../media/how-to-compression/front-door-compression-edit-content-type-2.png" alt-text="Capture d’écran de la page de personnalisation de la compression de fichier"::: 

1. Sélectionnez **Enregistrer** pour mettre à jour la configuration de la compression.

## <a name="disabling-compression"></a>Désactivation de la compression

Vous pouvez désactiver la compression en procédant comme suit :
* Désactiver la compression dans la route Endpoint Manager
* Désactiver la compression dans la page Optimisations

### <a name="disable-compression-in-endpoint-manager"></a>Désactiver la compression dans Endpoint Manager

1. Dans la page de profil Azure Front Door Standard/Premium, accédez à **Endpoint Manager** sous Paramètres. Sélectionnez le point de terminaison pour lequel vous souhaitez désactiver la compression.

1. Sélectionnez **Modifier le point de terminaison**, puis sélectionnez la **route** pour laquelle vous souhaitez désactiver la compression. Décochez la case **Activer la compression**.

1. Sélectionnez **Mettre à jour** pour enregistrer la configuration.

### <a name="disable-compression-in-optimizations"></a>Désactiver la compression dans Optimisations

1. Dans la page de profil Azure Front Door Standard/Premium, accédez à **Optimisations** sous Paramètres. Sélectionnez ensuite la **route** pour laquelle la compression est *activée*.

1. Sélectionnez les points de suspension en regard de la **route** pour laquelle la compression est *activée*, puis sélectionnez *Configurer la route*.

    :::image type="content" source="../media/how-to-compression/front-door-disable-compression-optimization.png" alt-text="Capture d’écran de la désactivation de la compression dans la page Optimisations"::: 

1. Décochez la case **Activer la compression**.

    :::image type="content" source="../media/how-to-compression/front-door-disable-compression-optimization-2.png" alt-text="Capture d’écran de la page Mettre à jour la route pour la désactivation de la compression"::: 

1. Sélectionnez **Mettre à jour** pour enregistrer la configuration.

## <a name="compression-rules"></a>Règles de compression

Dans Azure Front Door, seuls les fichiers éligibles sont compressés. Pour être éligible pour la compression, un fichier doit :
* Être de type MIME 
* Être supérieur à 1 Ko
* Être inférieur à 8 Mo

Ces profils prennent en charge les encodages de compression suivants :
* gzip (GNU zip)
* brotli 

Si la requête prend en charge plusieurs types de compression, la compression brotli est prioritaire.

Quand une demande liée à un actif multimédia spécifie la compression gzip et que cette demande cause un défaut du cache, Azure Front Door effectue une compression gzip du composant directement sur le serveur POP. Ensuite, le fichier compressé est servi à partir du cache.

Si l’origine utilise l’encodage de transfert mémorisé en bloc (CTE) pour envoyer des données compressées au POP Azure Front Door, les réponses supérieures à 8 Mo ne sont pas prises en charge. 

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment configurer votre premier [ensemble de règles](how-to-configure-rule-set.md).
- Découvrez-en plus sur les [conditions de correspondance d’un ensemble de règles](concept-rule-set-match-conditions.md).
- Découvrez-en plus sur l’[ensemble de règles Azure Front Door](concept-rule-set.md).
