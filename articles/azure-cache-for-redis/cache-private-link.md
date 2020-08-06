---
title: Azure Cache pour Redis avec Azure Private Link (préversion)
description: Un point de terminaison privé Azure est une interface réseau qui vous connecte de façon privée et sécurisée à Azure Cache pour Redis optimisé par Azure Private Link. Cet article explique comment créer un cache Azure, un réseau virtuel Azure et un point de terminaison privé à l’aide du portail Azure.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: d85fe36bb948ae9a0c81fa25f87450c7f5fe93b7
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337260"
---
# <a name="azure-cache-for-redis-with-azure-private-link-preview"></a>Azure Cache pour Redis avec Azure Private Link (préversion)
Un point de terminaison privé Azure est une interface réseau qui vous connecte de façon privée et sécurisée à Azure Cache pour Redis optimisé par Azure Private Link. 

Cet article explique comment créer un cache Azure, un réseau virtuel Azure et un point de terminaison privé à l’aide du portail Azure.  

## <a name="prerequisites"></a>Prérequis
* Abonnement Azure -  [créez-en un gratuitement](https://azure.microsoft.com/free/)

> [!NOTE]
  > Cette fonctionnalité est actuellement disponible en préversion - [contactez-nous](mailto:azurecache@microsoft.com) si vous êtes intéressé.
  >


## <a name="create-a-cache"></a>Création d'un cache
1. Pour créer un cache, connectez-vous au [portail Azure](https://portal.azure.com), puis sélectionnez **Créer une ressource**. 

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Sélectionnez Créer une ressource.":::
   
1. Dans la page **Nouvelle**, sélectionnez **Bases de données**, puis **Azure Cache pour Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Sélectionnez Azure Cache pour Redis.":::
   
1. Dans la page **Nouveau cache Redis**, configurez les paramètres du nouveau cache.
   
   | Paramètre      | Valeur suggérée  | Description |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Nom DNS** | Entrez un nom globalement unique. | Le nom du cache doit être une chaîne de 1 à 63 caractères ne contenant que des chiffres, des lettres et des traits d’union. Le nom doit commencer et se terminer par un chiffre ou une lettre, et ne peut pas contenir de traits d’union consécutifs. Le *nom d’hôte* de votre instance de cache sera *\<DNS name>.redis.cache.windows.net*. | 
   | **Abonnement** | Dans la liste déroulante, sélectionnez votre abonnement. | Abonnement sous lequel créer cette nouvelle instance d’Azure Cache pour Redis. | 
   | **Groupe de ressources** | Dans la liste déroulante, sélectionnez un groupe de ressources ou choisissez **Créer nouveau**, puis entrez un nouveau nom de groupe de ressources. | Nom du groupe de ressources dans lequel créer votre cache et d’autres ressources. En plaçant toutes les ressources de votre application dans un seul groupe de ressources, vous pouvez facilement les gérer ou les supprimer ensemble. | 
   | **Lieu** | Dans la liste déroulante, sélectionnez un emplacement. | Choisissez une [Région](https://azure.microsoft.com/regions/) proche d’autres services qui utiliseront votre cache. |
   | **Niveau tarifaire** | Sélectionnez un [Niveau tarifaire](https://azure.microsoft.com/pricing/details/cache/). |  Le niveau tarifaire détermine la taille, les performances et les fonctionnalités disponibles pour le cache. Pour plus d’informations, consultez [Présentation du cache Azure pour Redis](cache-overview.md). |
   
1. Sélectionnez **Create** (Créer). 
   
    :::image type="content" source="media/cache-private-link/3-new-cache.png" alt-text="Créez Azure Cache pour Redis.":::
   
   La création du cache prend un certain temps. Vous pouvez surveiller la progression dans la page **Vue d’ensemble** du Azure Cache pour Redis. Lorsque **État** indique **En cours d’exécution**, le cache est prêt pour utilisation.
    
    :::image type="content" source="media/cache-private-link/4-status.png" alt-text="Azure Cache pour Redis créé.":::

## <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Dans cette section, vous allez créer un réseau virtuel et un sous-réseau.

1. Sélectionnez **Créer une ressource**.

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Sélectionnez Créer une ressource.":::

2. Dans la page **Nouvelle**, sélectionnez **Mise en réseau**, puis sélectionnez **Réseau virtuel**.

    :::image type="content" source="media/cache-private-link/5-select-vnet.png" alt-text="Créez un réseau virtuel.":::

3. Dans **Créer un réseau virtuel**, entrez ou sélectionnez les informations suivantes sous l’onglet **Général** :

    | **Paramètre**          | **Valeur**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Détails du projet**  |                                                                 |
    | Abonnement     | Dans la liste déroulante, sélectionnez votre abonnement.                                  |
    | Groupe de ressources   | Déroulez la liste et sélectionnez un groupe de ressources. |
    | **Détails de l’instance** |                                                                 |
    | Nom             | Entrez **\<virtual-network-name>**                                    |
    | Région           | Sélectionnez **\<region-name>** |

4. Sélectionnez l’onglet **Adresses IP**, ou sélectionnez le bouton **Suivant : Adresses IP** au bas de la page.

5. Sous l’onglet **Adresses IP**, entrez les informations suivantes :

    | Paramètre            | Valeur                      |
    |--------------------|----------------------------|
    | Espace d’adressage IPv4 | Entrez **\<IPv4-address-space>** |

6. Sous **Nom de sous-réseau**, sélectionnez le mot **par défaut**.

7. Dans **Modifier le sous-réseau**, entrez les informations suivantes :

    | Paramètre            | Valeur                      |
    |--------------------|----------------------------|
    | Nom du sous-réseau | Entrez **\<subnet-name>** |
    | Plage d’adresses de sous-réseau | Entrez **\<subnet-address-range>**

8. Sélectionnez **Enregistrer**.

9. Sélectionnez l’onglet **Vérifier + créer**, ou sélectionnez le bouton **Vérifier + créer**.

10. Sélectionnez **Create** (Créer).


## <a name="create-a-private-endpoint"></a>Créer un Private Endpoint 

Dans cette section, vous allez créer un point de terminaison privé et le connecter au cache que vous avez créé précédemment.

1. Recherchez **Liaison privée**, puis appuyez sur Entrée ou sélectionnez l’option dans les suggestions de recherche.

    :::image type="content" source="media/cache-private-link/7-create-private-link.png" alt-text="Recherchez une liaison privée.":::

2. Sur le côté gauche de l’écran, sélectionnez **Points de terminaison privés**.

    :::image type="content" source="media/cache-private-link/8-select-private-endpoint.png" alt-text="Sélectionnez une liaison privée.":::

3. Sélectionnez le bouton **+ Ajouter** pour créer votre point de terminaison privé. 

    :::image type="content" source="media/cache-private-link/9-add-private-endpoint.png" alt-text="Ajoutez une liaison privée.":::

4. Dans la page **Créer un point de terminaison privé**, configurez les paramètres de votre point de terminaison privé.

    | Paramètre | Valeur |
    | ------- | ----- |
    | **DÉTAILS DU PROJET** | |
    | Abonnement | Dans la liste déroulante, sélectionnez votre abonnement. |
    | Resource group | Déroulez la liste et sélectionnez un groupe de ressources. |
    | **DÉTAILS DE L’INSTANCE** |  |
    | Nom |Entrez un nom pour votre point de terminaison privé.  |
    | Région |Dans la liste déroulante, sélectionnez un emplacement. |
    |||

5. Sélectionnez le bouton **Suivant : Ressource** en bas de la page.

6. Sous l’onglet **Ressources**, sélectionnez votre abonnement, choisissez le type de ressource Microsoft.Cache/Redis, puis sélectionnez le cache que vous avez créé dans la section précédente.

    :::image type="content" source="media/cache-private-link/10-resource-private-endpoint.png" alt-text="Ressources pour liaison privée.":::

7. Sélectionnez le bouton **Suivant : Configuration** en bas de la page.

8. Sous l’onglet **Configuration**, sélectionnez le réseau virtuel et le sous-réseau que vous avez créés dans la section précédente.

    :::image type="content" source="media/cache-private-link/11-configuration-private-endpoint.png" alt-text="Configuration de liaison privée.":::

9. Sélectionnez le bouton **Suivant : Étiquettes** au bas de la page.

10. Sous l’onglet **Balises**, entrez le nom et la valeur si vous souhaitez catégoriser la ressource. Cette étape est facultative.

    :::image type="content" source="media/cache-private-link/12-tags-private-endpoint.png" alt-text="Balises pour liaison privée.":::

11. Sélectionnez **Vérifier + créer**. Vous êtes redirigé vers l’onglet  **Vérifier + créer** où Azure valide votre configuration.

12. Une fois le message vert **Validation réussie** s’affiche, sélectionnez **Créer**.


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur Liaison privée, consultez la [documentation d’Azure Private Link](https://docs.microsoft.com/azure/private-link/private-link-overview). 

