---
title: Exporter des enregistrements DNS pour un point de terminaison privé en utilisant le portail Azure
titleSuffix: Azure Private Link
description: Dans ce tutoriel, découvrez comment exporter les enregistrements DNS pour un point de terminaison privé dans le portail Azure.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: how-to
ms.date: 07/25/2021
ms.custom: how-to
ms.openlocfilehash: d4ad1fd995bfd4f1565215491037e6450bd65bf2
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114713091"
---
# <a name="export-dns-records-for-a-private-endpoint-using-the-azure-portal"></a>Exporter des enregistrements DNS pour un point de terminaison privé en utilisant le portail Azure

Un point de terminaison privé dans Azure nécessite des enregistrements DNS pour la résolution de noms du point de terminaison. L’enregistrement DNS résout l’adresse IP privée du point de terminaison pour la ressource configurée. Pour exporter les enregistrements DNS du point de terminaison, utilisez le Centre Private Link dans le portail.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un point de terminaison privé configuré dans votre abonnement. Pour l’exemple de cet article, un point de terminaison privé pour une application web Azure est utilisé. Pour plus d’informations sur la création d’un point de terminaison privé pour une application web, consultez [Tutoriel : Se connecter à une application web en utilisant un point de terminaison privé Azure](tutorial-private-endpoint-webapp-portal.md).

## <a name="export-endpoint-dns-records"></a>Exporter les enregistrements DNS du point de terminaison

Dans cette section, vous allez vous connecter au portail Azure et rechercher le Centre Private Link.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans la zone de recherche située en haut du portail, entrez **Private Link**.

3. Sélectionnez **Private Link**.

4. Dans le Centre Private Link, sélectionnez **Points de terminaison privés**.

    :::image type="content" source="./media/private-endpoint-export-dns/private-link-center.png" alt-text="Sélectionner des points de terminaison privés dans le Centre Private Link":::

5. Dans **Points de terminaison privés**, sélectionnez le point de terminaison pour lequel vous voulez exporter les enregistrements DNS. Sélectionnez **Télécharger le fichier hôte** pour télécharger les enregistrements DNS du point de terminaison dans un format de fichier hôte.
    
    :::image type="content" source="./media/private-endpoint-export-dns/download-host-file.png" alt-text="Télécharger les enregistrements DNS du point de terminaison":::

6. Les enregistrements du fichier hôte téléchargé se présentent comme suit :

    ```text
    # Exported from the Azure portal "2021-07-26 11:26:03Z"
    # Private IP    FQDN    Private Endpoint Id
    10.1.0.4    mywebapp8675.scm.azurewebsites.net    #/subscriptions/7cc654c6-760b-442f-bd02-1a8a64b17413/resourceGroups/myResourceGroup/providers/Microsoft.Network/privateEndpoints/mywebappendpoint
    10.1.0.4    mywebapp8675.azurewebsites.net    #/subscriptions/7cc654c6-760b-442f-bd02-1a8a64b17413/resourceGroups/myResourceGroup/providers/Microsoft.Network/privateEndpoints/mywebappendpoint
    ```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure Private Link et DNS, consultez [Configuration DNS des points de terminaison privés Azure](private-endpoint-dns.md).

Pour plus d’informations sur Azure Private Link, consultez :

* [Qu’est-ce que Liaison privée Azure ?](private-link-overview.md)
* [Qu’est-ce que le service Azure Private Link ?](private-link-service-overview.md)
* [Questions fréquentes (FAQ) sur Azure Private Link](private-link-faq.yml)