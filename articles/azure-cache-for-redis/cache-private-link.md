---
title: Azure Cache pour Redis avec Azure Private Link (préversion)
description: Un point de terminaison privé Azure est une interface réseau qui vous connecte de façon privée et sécurisée à Azure Cache pour Redis optimisé par Azure Private Link. Cet article explique comment créer un cache Azure, un réseau virtuel Azure et un point de terminaison privé à l’aide du portail Azure.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: e2c071ff9cf020f99e990e670cfb29cca3c1ebbc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91838651"
---
# <a name="azure-cache-for-redis-with-azure-private-link-public-preview"></a>Azure Cache pour Redis avec Azure Private Link (Préversion publique)
Dans cet article, vous allez apprendre à créer un réseau virtuel et une instance Azure Cache pour Redis avec un point de terminaison privé à l’aide du portail Azure. Vous apprendrez également à ajouter un point de terminaison privé à une instance Azure Cache pour Redis existante.

Un point de terminaison privé Azure est une interface réseau qui vous connecte de façon privée et sécurisée à Azure Cache pour Redis optimisé par Azure Private Link. 

## <a name="prerequisites"></a>Prérequis
* Abonnement Azure -  [créez-en un gratuitement](https://azure.microsoft.com/free/)

> [!NOTE]
> Pour utiliser des points de terminaison privés, votre instance Azure Cache pour Redis doit avoir été créée après le 28 juillet 2020.
>
>

## <a name="create-a-private-endpoint-with-a-new-azure-cache-for-redis-instance"></a>Créer un point de terminaison privé avec une nouvelle instance Azure Cache pour Redis 

Dans cette section, vous allez créer une instance Azure Cache pour Redis avec un point de terminaison privé.

### <a name="create-a-virtual-network"></a>Créez un réseau virtuel 

1. Connectez-vous au [portail Azure](https://portal.azure.com) et sélectionnez **Créer une ressource**.

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Sélectionnez Créer une ressource.":::

2. Dans la page **Nouvelle**, sélectionnez **Mise en réseau**, puis sélectionnez **Réseau virtuel**.

3. Sélectionnez **Ajouter** pour créer un réseau virtuel.

4. Dans **Créer un réseau virtuel**, entrez ou sélectionnez les informations suivantes sous l’onglet **Général** :

   | Paramètre      | Valeur suggérée  | Description |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Abonnement** | Dans la liste déroulante, sélectionnez votre abonnement. | Abonnement dans lequel créer ce réseau virtuel. | 
   | **Groupe de ressources** | Dans la liste déroulante, sélectionnez un groupe de ressources ou choisissez **Créer nouveau**, puis entrez un nouveau nom de groupe de ressources. | Nom du groupe de ressources dans lequel créer votre réseau virtuel et d’autres ressources. En plaçant toutes les ressources de votre application dans un seul groupe de ressources, vous pouvez facilement les gérer ou les supprimer ensemble. | 
   | **Nom** | Entrez un nom de réseau virtuel. | Le nom doit commencer par une lettre ou un chiffre et se terminer par une lettre, un chiffre ou un trait de soulignement, et il ne peut contenir que des lettres, des chiffres, des traits de soulignement, des points ou des traits d’union. | 
   | **Région** | Dans la liste déroulante, sélectionnez une région. | Choisissez une [région](https://azure.microsoft.com/regions/) proche d’autres services qui utiliseront votre réseau virtuel. |

5. Sélectionnez l’onglet **Adresses IP** ou cliquez sur le bouton **Suivant : Adresses IP** au bas de la page.

6. Dans l’onglet **Adresses IP**, spécifiez l’**espace d’adressage IPv4** comme un ou plusieurs préfixes d’adresse en notation CIDR (par exemple, 192.168.1.0/24).

7. Sous **Nom du sous-réseau**, cliquez sur **Par défaut** pour modifier les propriétés du sous-réseau.

8. Dans le volet **Modifier le sous-réseau**, spécifiez un **nom de sous-réseau** ainsi que la **plage d’adresses du sous-réseau**. La plage d’adresses du sous-réseau doit être en notation CIDR (par exemple, 192.168.1.0/24). Elle doit être contenue dans l’espace d’adressage du réseau virtuel.

9. Sélectionnez **Enregistrer**.

10. Sélectionnez l’onglet **Vérifier + créer** cliquez sur le bouton **Vérifier + créer**.

11. Vérifiez que toutes les informations sont correctes, puis cliquez sur **Créer** pour approvisionner le réseau virtuel.

### <a name="create-an-azure-cache-for-redis-instance-with-a-private-endpoint"></a>Créer une instance Azure Cache pour Redis avec un point de terminaison privé
Pour créer une instance de cache, procédez comme suit.

1. Revenez à la page d’accueil du portail Azure ou ouvrez le menu latéral, puis sélectionnez **Créer une ressource**. 
   
1. Dans la page **Nouvelle**, sélectionnez **Bases de données**, puis **Azure Cache pour Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Sélectionnez Créer une ressource.":::
   
1. Dans la page **Nouveau cache Redis**, configurez les paramètres du nouveau cache.
   
   | Paramètre      | Valeur suggérée  | Description |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Nom DNS** | Entrez un nom globalement unique. | Le nom du cache doit être une chaîne de 1 à 63 caractères ne contenant que des chiffres, des lettres et des traits d’union. Le nom doit commencer et se terminer par un chiffre ou une lettre, et ne peut pas contenir de traits d’union consécutifs. Le *nom d’hôte* de votre instance de cache sera *\<DNS name>.redis.cache.windows.net*. | 
   | **Abonnement** | Dans la liste déroulante, sélectionnez votre abonnement. | Abonnement sous lequel créer cette nouvelle instance d’Azure Cache pour Redis. | 
   | **Groupe de ressources** | Dans la liste déroulante, sélectionnez un groupe de ressources ou choisissez **Créer nouveau**, puis entrez un nouveau nom de groupe de ressources. | Nom du groupe de ressources dans lequel créer votre cache et d’autres ressources. En plaçant toutes les ressources de votre application dans un seul groupe de ressources, vous pouvez facilement les gérer ou les supprimer ensemble. | 
   | **Lieu** | Dans la liste déroulante, sélectionnez un emplacement. | Choisissez une [Région](https://azure.microsoft.com/regions/) proche d’autres services qui utiliseront votre cache. |
   | **Niveau tarifaire** | Sélectionnez un [Niveau tarifaire](https://azure.microsoft.com/pricing/details/cache/). |  Le niveau tarifaire détermine la taille, les performances et les fonctionnalités disponibles pour le cache. Pour plus d’informations, consultez [Présentation du cache Azure pour Redis](cache-overview.md). |

1. Sélectionnez l’onglet **Réseau** ou cliquez sur le bouton **Réseau** au bas de la page.

1. Sous l’onglet **Réseau**, sélectionnez **Point de terminaison privé** comme méthode de connectivité.

1. Cliquez sur le bouton **Ajouter** pour créer votre point de terminaison privé.

    :::image type="content" source="media/cache-private-link/3-add-private-endpoint.png" alt-text="Sélectionnez Créer une ressource.":::

1. Dans la page **Créer un point de terminaison privé**, configurez les paramètres de votre point de terminaison privé avec le réseau virtuel et le sous-réseau que vous avez créés dans la dernière section, puis sélectionnez **OK**. 

1. Sélectionnez le bouton **Suivant : Avancé** ou cliquez sur le bouton **Suivant : Avancé** en bas de la page.

1. Sous l’onglet **Avancé** d’une instance de cache de base ou standard, sélectionnez Activer/désactiver si vous souhaitez activer un port non-TLS.

1. Sous l’onglet **Avancé** d’une instance de cache premium, configurez les paramètres pour le port non-TLS, le clustering et la persistance des données.


1. Sélectionnez le bouton **Suivant : Étiquettes** ou cliquez sur le bouton **Suivant : Étiquettes** au bas de la page.

1. Si vous le voulez, sous l’onglet **Étiquettes**, entrez le nom et la valeur si vous souhaitez catégoriser la ressource. 

1. Sélectionnez **Vérifier + créer**. Vous êtes redirigé vers l’onglet Vérifier + créer où Azure valide votre configuration.

1. Une fois que le message vert Validation réussie s’affiche, sélectionnez **Créer**.

La création du cache prend un certain temps. Vous pouvez surveiller la progression dans la page **Vue d’ensemble** d’Azure Cache pour Redis. Lorsque **État** indique **En cours d’exécution**, le cache est prêt pour utilisation. 
    

## <a name="create-a-private-endpoint-with-an-existing-azure-cache-for-redis-instance"></a>Créer un point de terminaison privé avec une instance Azure Cache pour Redis existante 

Dans cette section, vous allez ajouter un point de terminaison privé à une instance Azure Cache pour Redis existante. 

### <a name="create-a-virtual-network"></a>Créez un réseau virtuel 
Pour créer un réseau virtuel, procédez comme suit.

1. Connectez-vous au [portail Azure](https://portal.azure.com) et sélectionnez **Créer une ressource**.

2. Dans la page **Nouvelle**, sélectionnez **Mise en réseau**, puis sélectionnez **Réseau virtuel**.

3. Sélectionnez **Ajouter** pour créer un réseau virtuel.

4. Dans **Créer un réseau virtuel**, entrez ou sélectionnez les informations suivantes sous l’onglet **Général** :

   | Paramètre      | Valeur suggérée  | Description |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Abonnement** | Dans la liste déroulante, sélectionnez votre abonnement. | Abonnement dans lequel créer ce réseau virtuel. | 
   | **Groupe de ressources** | Dans la liste déroulante, sélectionnez un groupe de ressources ou choisissez **Créer nouveau**, puis entrez un nouveau nom de groupe de ressources. | Nom du groupe de ressources dans lequel créer votre réseau virtuel et d’autres ressources. En plaçant toutes les ressources de votre application dans un seul groupe de ressources, vous pouvez facilement les gérer ou les supprimer ensemble. | 
   | **Nom** | Entrez un nom de réseau virtuel. | Le nom doit commencer par une lettre ou un chiffre et se terminer par une lettre, un chiffre ou un trait de soulignement, et il ne peut contenir que des lettres, des chiffres, des traits de soulignement, des points ou des traits d’union. | 
   | **Région** | Dans la liste déroulante, sélectionnez une région. | Choisissez une [région](https://azure.microsoft.com/regions/) proche d’autres services qui utiliseront votre réseau virtuel. |

5. Sélectionnez l’onglet **Adresses IP** ou cliquez sur le bouton **Suivant : Adresses IP** au bas de la page.

6. Dans l’onglet **Adresses IP**, spécifiez l’**espace d’adressage IPv4** comme un ou plusieurs préfixes d’adresse en notation CIDR (par exemple, 192.168.1.0/24).

7. Sous **Nom du sous-réseau**, cliquez sur **Par défaut** pour modifier les propriétés du sous-réseau.

8. Dans le volet **Modifier le sous-réseau**, spécifiez un **nom de sous-réseau** ainsi que la **plage d’adresses du sous-réseau**. La plage d’adresses du sous-réseau doit être en notation CIDR (par exemple, 192.168.1.0/24). Elle doit être contenue dans l’espace d’adressage du réseau virtuel.

9. Sélectionnez **Enregistrer**.

10. Sélectionnez l’onglet **Vérifier + créer** cliquez sur le bouton **Vérifier + créer**.

11. Vérifiez que toutes les informations sont correctes, puis cliquez sur **Créer** pour approvisionner le réseau virtuel.

### <a name="create-a-private-endpoint"></a>Créer un Private Endpoint 

Pour créer un point de terminaison privé, procédez comme suit.

1. Dans le portail Azure, recherchez **Azure Cache pour Redis** et appuyez sur Entrée ou sélectionnez l’élément correspondant dans les suggestions de recherche.

    :::image type="content" source="media/cache-private-link/4-search-for-cache.png" alt-text="Sélectionnez Créer une ressource.":::

2. Sélectionnez l’instance de cache à laquelle vous souhaitez ajouter un point de terminaison privé.

3. Sur le côté gauche de l’écran, sélectionnez **(PRÉVERSION) Point de terminaison privé**.

4. Cliquez sur le bouton **Point de terminaison privé** pour créer votre point de terminaison privé.

    :::image type="content" source="media/cache-private-link/5-add-private-endpoint.png" alt-text="Sélectionnez Créer une ressource.":::

5. Dans la page **Créer un point de terminaison privé**, configurez les paramètres de votre point de terminaison privé.

   | Paramètre      | Valeur suggérée  | Description |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Abonnement** | Dans la liste déroulante, sélectionnez votre abonnement. | Abonnement dans lequel créer ce point de terminaison privé. | 
   | **Groupe de ressources** | Dans la liste déroulante, sélectionnez un groupe de ressources ou choisissez **Créer nouveau**, puis entrez un nouveau nom de groupe de ressources. | Nom du groupe de ressources dans lequel créer votre point de terminaison privé et d’autres ressources. En plaçant toutes les ressources de votre application dans un seul groupe de ressources, vous pouvez facilement les gérer ou les supprimer ensemble. | 
   | **Nom** | Entrez un nom de point de terminaison privé. | Le nom doit commencer par une lettre ou un chiffre et se terminer par une lettre, un chiffre ou un trait de soulignement, et il ne peut contenir que des lettres, des chiffres, des traits de soulignement, des points ou des traits d’union. | 
   | **Région** | Dans la liste déroulante, sélectionnez une région. | Choisissez une [région](https://azure.microsoft.com/regions/) proche d’autres services qui utiliseront votre point de terminaison privé. |

6. Cliquez sur le bouton **Next: Ressource** en bas de la page.

7. Sous l’onglet **Ressources**, sélectionnez votre abonnement, choisissez le type de ressource `Microsoft.Cache/Redis`, puis sélectionnez le cache auquel vous souhaitez connecter le point de terminaison privé.

8. Cliquez sur le bouton **Next: Configuration** en bas de la page.

9. Sous l’onglet **Configuration**, sélectionnez le réseau virtuel et le sous-réseau que vous avez créés dans la section précédente.

10. Cliquez sur le bouton **Next: Étiquettes** au bas de la page.

11. Si vous le voulez, sous l’onglet **Étiquettes**, entrez le nom et la valeur si vous souhaitez catégoriser la ressource.

12. Sélectionnez **Vérifier + créer**. Vous êtes redirigé vers l’onglet  **Vérifier + créer** où Azure valide votre configuration.

13. Une fois que le message vert **Validation réussie** s’affiche, sélectionnez **Créer**.


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur Azure Private Link, consultez la [documentation d’Azure Private Link](https://docs.microsoft.com/azure/private-link/private-link-overview). 

