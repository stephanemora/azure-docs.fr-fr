---
title: Contrôler l’adresse IP sortante Azure Functions avec une passerelle NAT de réseau virtuel Azure
description: Tutoriel pas à pas qui montre comment configurer la traduction d’adresses réseau pour une fonction connectée à un réseau virtuel Azure.
ms.topic: tutorial
ms.author: kyburns
ms.date: 2/26/2021
ms.openlocfilehash: 5bb491e367ed813f09197a193745c231261c88c7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104658115"
---
# <a name="tutorial-control-azure-functions-outbound-ip-with-an-azure-virtual-network-nat-gateway"></a>Tutoriel : Contrôler l’adresse IP sortante Azure Functions avec une passerelle NAT de réseau virtuel Azure

La traduction d’adresses réseau (NAT) simplifie la connectivité Internet sortante-uniquement pour les réseaux virtuels. Quand il est configuré sur un sous-réseau, toute la connectivité sortante utilise vos adresses IP publiques statiques spécifiées. NAT peut être utile pour les fonctions Azure ou les applications web qui doivent consommer un service tiers qui utilise une liste verte d’adresses IP comme mesure de sécurité. Pour plus d’informations, consultez [Qu’est-ce que le service NAT de Réseau virtuel ?](../virtual-network/nat-overview.md).

Ce tutoriel montre comment utiliser la traduction d’adresses réseau de réseau virtuel pour router le trafic sortant à partir d’une fonction déclenchée par HTTP. Cette fonction vous permet de vérifier sa propre adresse IP sortante. Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Créer un réseau virtuel
> * Créer une application de fonction dans un plan Premium.
> * Créer une adresse IP publique
> * Créer une passerelle NAT.
> * Configurer une application de fonction de manière à router le trafic sortant via la passerelle NAT.

## <a name="topology"></a>Topologie

Le diagramme suivant représente l’architecture de la solution que vous créez :

![Interface utilisateur pour l’intégration de la passerelle NAT](./media/functions-how-to-use-nat-gateway/topology.png)

Les fonctions qui s’exécutent dans le plan Premium ont les mêmes fonctionnalités d’hébergement que les applications web dans Azure App Service, ce qui inclut la fonctionnalité d’intégration de réseau virtuel. Pour en savoir plus sur l’intégration d’un réseau virtuel, y compris la résolution des problèmes et la configuration avancée, consultez [Intégrer une application à un réseau virtuel Azure](../app-service/web-sites-integrate-with-vnet.md).

## <a name="prerequisites"></a>Prérequis

Pour ce tutoriel, il est important de comprendre l’adressage IP et la gestion des sous-réseaux. Vous pouvez commencer par [cet article qui couvre les principes fondamentaux de l’adressage et de la gestion de sous-réseaux](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). De nombreux autres articles et vidéos sont disponibles en ligne.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

Si vous avez déjà suivi le tutoriel d’[intégration de fonctions avec un réseau virtuel Azure](./functions-create-vnet.md), vous pouvez passer à [Créer une fonction de déclencheur HTTP](#create-function).

## <a name="create-a-virtual-network"></a>Créez un réseau virtuel

1. Dans le menu du Portail Azure, sélectionnez **Créer une ressource**. À partir de la Place de marché Azure, sélectionnez **Mise en réseau** > **Réseau virtuel**.

1. Dans **Créer un réseau virtuel**, entrez ou sélectionnez les paramètres spécifiés comme indiqué dans le tableau suivant :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Subscription | Sélectionnez votre abonnement.|
    | Resource group | Sélectionnez **Créer nouveau**, entrez *myResourceGroup* et sélectionnez **OK**. |
    | Nom | Entrez *myResourceGroup-vnet*. |
    | Location | Sélectionnez **USA Est**.|

1. Sélectionnez **Suivant : Adresses IP** et, pour **Espace d’adressage IPv4**, entrez *10.10.0.0/16*.

1. Sélectionnez **Ajouter un sous-réseau**, puis entrez *Tutorial-Net* pour **Nom de sous-réseau** et *10.10.1.0/24* pour la **Plage d’adresses de sous-réseau**.

    ![Onglet Adresses IP pour la création d’un réseau virtuel](./media/functions-how-to-use-nat-gateway/create-vnet-2-ip-space.png)

1. Sélectionnez **Ajouter**, puis **Vérifier + créer**. Conservez les autres valeurs par défaut, puis sélectionnez **Créer**.

1. Dans **Créer un réseau virtuel**, sélectionnez **Créer**.

Maintenant, vous allez créer une application de fonction dans le [plan Premium](functions-premium-plan.md). Ce plan fournit une mise à l’échelle serverless lors de la prise en charge de l’intégration d’un réseau virtuel.

## <a name="create-a-function-app-in-a-premium-plan"></a>Créer une application de fonction sur le plan Premium

> [!NOTE]  
> Pour une expérience optimale dans ce tutoriel, choisissez .NET pour la pile d’exécution et Windows comme système d’exploitation. Créez votre application de fonction dans la même région que votre réseau virtuel.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

## <a name="connect-your-function-app-to-the-virtual-network"></a>Connecter l’application de fonction au réseau virtuel

Vous pouvez maintenant connecter votre application de fonction au réseau virtuel.

1. Dans votre application de fonction, sélectionnez **Réseau** dans le menu de gauche puis, sous **Intégration au réseau virtuel**, sélectionnez **Cliquez ici pour configurer**.

    :::image type="content" source="./media/functions-how-to-use-nat-gateway/networking-0.png" alt-text="Choisir la mise en réseau dans l’application de fonction":::

1. Dans la page **Intégration au réseau virtuel**, sélectionnez **Ajouter un réseau virtuel**.

1. Dans **État de la fonctionnalité réseau**, utilisez les paramètres figurant dans le tableau sous l’image :

    ![Définir le réseau virtuel de l’application de fonction](./media/functions-how-to-use-nat-gateway/networking-3.png)

    | Paramètre      | Valeur suggérée  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **Réseau virtuel** | MyResourceGroup-vnet | Ce réseau virtuel est celui que vous avez créé précédemment. |
    | **Sous-réseau** | Créer un sous-réseau | Créez un sous-réseau dans le réseau virtuel que votre application de fonction utilisera. L’intégration au réseau virtuel doit être configurée pour utiliser un sous-réseau vide. |
    | **Nom du sous-réseau** | Function-Net | Nom du nouveau sous-réseau. |
    | **Bloc d’adresses du réseau virtuel** | 10.10.0.0/16 | Vous devez avoir un seul bloc d’adresses défini. |
    | **Bloc d’adresses du sous-réseau** | 10.10.2.0/24   | La taille du sous-réseau limite le nombre total d’instances vers lequel votre application de fonction du plan Premium peut effectuer un scale-out. Cet exemple utilise un sous-réseau `/24` avec 254 adresses d’hôte disponibles. Ce sous-réseau est surprovisionné, mais facile à calculer. |

1. Sélectionnez **OK** pour ajouter le sous-réseau. Fermez les pages **Intégration de réseau virtuel** et **État de la fonctionnalité réseau** pour revenir à la page de votre application de fonction.

L’application de fonction peut maintenant accéder au réseau virtuel. Ensuite, vous allez ajouter une fonction déclenchée par HTTP à l’application de fonction.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>Créer une fonction de déclencheur HTTP

1. Dans le menu de gauche de la fenêtre **Fonctions**, sélectionnez **Fonctions**, puis **Ajouter** dans le menu supérieur. 
 
1. Dans la fenêtre **Nouvelle fonction**, sélectionnez **Déclencheur HTTP** et acceptez le nom par défaut pour la **Nouvelle fonction** ou entrez un nouveau nom. 

1. Dans **Coder + Tester**, remplacez le code du script C# généré par un modèle (.csx) par le code suivant : 

    ```csharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");
    
        var client = new HttpClient();
        var response = await client.GetAsync(@"https://ifconfig.me");
        var responseMessage = await response.Content.ReadAsStringAsync();
    
        return new OkObjectResult(responseMessage);
    }
    ```

    Ce code appelle un site web externe qui retourne l’adresse IP de l’appelant, en l’occurrence cette fonction. Cette méthode vous permet de déterminer facilement l’adresse IP sortante utilisée par votre application de fonction.

Vous êtes maintenant prêt à exécuter la fonction et à vérifier les adresses IP sortantes actuelles.

## <a name="verify-current-outbound-ips"></a>Vérifier les adresses IP sortantes actuelles

Vous pouvez maintenant exécuter la fonction. Mais avant, consultez le portail pour vérifier quelles adresses IP sortantes sont utilisées par l’application de fonction.  

1. Dans votre application de fonction, sélectionnez **Propriétés** et examinez le champ **Adresses IP sortantes**.

    ![Afficher les adresses IP sortantes de l’application de fonction](./media/functions-how-to-use-nat-gateway/function-properties-ip.png)

1. À présent, revenez à votre fonction de déclencheur HTTP, sélectionnez **Coder + Tester**, puis **Tester/Exécuter**.

    ![Tester la fonction](./media/functions-how-to-use-nat-gateway/function-code-test.png)

1. Sélectionnez **Exécuter** pour exécuter la fonction, puis basculez vers la **Sortie**. 

    ![Tester la sortie de fonction](./media/functions-how-to-use-nat-gateway/function-test-1-output.png)

1. Vérifiez que l’adresse IP mentionnée dans le corps de la réponse HTTP est l’une des valeurs des adresses IP sortantes que vous avez vues précédemment.

À présent, vous pouvez créer une adresse IP publique et utiliser une passerelle NAT pour modifier cette adresse IP sortante.

## <a name="create-public-ip"></a>Créer une adresse IP publique

1. À partir de votre groupe de ressources, sélectionnez **Ajouter**, recherchez **Adresse IP publique** sur la Place de marché Azure, puis sélectionnez **Créer**. Utilisez les paramètres indiqués dans le tableau sous l’image ci-après :

    ![Créer une adresse IP publique](./media/functions-how-to-use-nat-gateway/create-public-ip.png)

    | Paramètre      | Valeur suggérée  |
    | ------------ | ---------------- |
    | **Version de l’adresse IP** | IPv4 |
    | **Référence (SKU)** | Standard |
    | **Niveau** | Régionale |
    | **Nom** | Adresse IP sortante |
    | **Abonnement** | Vérifiez que votre abonnement est affiché | 
    | **Groupe de ressources** | myResourceGroup (ou le nom que vous avez donné à votre groupe de ressources) |
    | **Lieu** | USA Est (ou l’emplacement que vous avez affecté à vos autres ressources) |
    | **Zone de disponibilité** | Aucune zone |

1. Sélectionnez **Créer** pour soumettre le déploiement.

1. Une fois le déploiement terminé, accédez à la ressource d’adresse IP publique que vous venez de créer et affichez l’adresse IP dans la **Vue d’ensemble**.

    ![Afficher l’adresse IP publique](./media/functions-how-to-use-nat-gateway/public-ip-overview.png)

## <a name="create-nat-gateway"></a>Créer une passerelle NAT

Nous allons maintenant créer la passerelle NAT. Si vous avez commencé avec le [précédent tutoriel sur les réseaux virtuels](functions-create-vnet.md), `Function-Net` est le nom de sous-réseau suggéré et `MyResourceGroup-vnet` est le nom de réseau virtuel suggéré dans ce tutoriel.

1. À partir de votre groupe de ressources, sélectionnez **Ajouter**, recherchez **Passerelle NAT** sur la Place de marché Azure, puis sélectionnez **Créer**. Utilisez les paramètres du tableau sous l’image pour renseigner l’onglet **Informations de base** :

    ![Créer une passerelle NAT](./media/functions-how-to-use-nat-gateway/create-nat-1-basics.png)

    | Paramètre      | Valeur suggérée  |
    | ------------ | ---------------- |  
    | **Abonnement** | Votre abonnement | 
    | **Groupe de ressources** | myResourceGroup (ou le nom que vous avez donné à votre groupe de ressources) |
    | **Nom de la passerelle NAT** | myNatGateway |
    | **Région** | USA Est (ou la région que vous avez affectée à vos autres ressources) |
    | **Zone de disponibilité** | None |

1. Sélectionnez **Suivant : Adresse IP sortante**. Dans le champ **Adresses IP publiques**, sélectionnez l’adresse IP publique que vous avez créée. Laissez l’option **Préfixes d’adresses IP publiques** désélectionnée.

1. Sélectionnez **Suivant : Sous-réseau**. Sélectionnez la ressource *myResourceGroup-vnet* dans le champ **Réseau virtuel** et le sous-réseau *Function-Net*.

    ![Sélectionner un sous-réseau](./media/functions-how-to-use-nat-gateway/create-nat-3-subnet.png)

1. Sélectionnez **Vérifier + créer**, puis **Créer** pour soumettre le déploiement.

Une fois le déploiement terminé, la passerelle NAT est prête à router le trafic du sous-réseau de votre application de fonction vers Internet.

## <a name="update-function-configuration"></a>Mettre à jour la configuration de la fonction

À présent, vous devez ajouter un paramètre d’application `WEBSITE_VNET_ROUTE_ALL` avec la valeur `1`.  Ce paramètre force le trafic sortant à transiter par le réseau virtuel et la passerelle NAT associée. Sans ce paramètre, le trafic Internet ne sera pas routé par le biais du réseau virtuel intégré, et vous verrez les mêmes adresses IP sortantes. 

1. Accédez à votre application de fonction dans le portail Azure, puis sélectionnez **Configuration** dans le menu de gauche.

1. Sous **Paramètres de l’application**, sélectionnez **+ Nouveau paramètre d’application** et renseignez les champs avec les valeurs suivantes :

    |Nom du champ  |Valeur |
    |---|---|
    |**Nom**    |WEBSITE_VNET_ROUTE_ALL|
    |**Valeur**   |1|

1. Sélectionnez **OK** pour fermer la boîte de dialogue Nouveau paramètre d’application.

1. Sélectionnez **Enregistrer**, puis **Continuer** pour enregistrer les paramètres.

L’application de fonction est maintenant configurée pour router le trafic par le biais de son réseau virtuel associé.

## <a name="verify-new-outbound-ips"></a>Vérifier les nouvelles adresses IP sortantes

Répétez [les étapes précédentes](#verify-current-outbound-ips) pour réexécuter la fonction. L’adresse IP sortante que vous avez configurée dans la traduction d’adresses réseau doit maintenant être visible dans la sortie de la fonction.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Vous avez créé des ressources pour suivre ce tutoriel. Vous serez facturé pour ces ressources en fonction de [l’état de votre compte](https://azure.microsoft.com/account/) et de la [tarification du service](https://azure.microsoft.com/pricing/). Pour éviter les coûts supplémentaires, supprimez les ressources lorsque vous n’en avez plus besoin. 

[!INCLUDE [functions-quickstart-cleanup-inner](../../includes/functions-quickstart-cleanup-inner.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Options de mise en réseau d’Azure Functions](functions-networking-options.md)
