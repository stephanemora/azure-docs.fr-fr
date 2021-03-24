---
title: Utiliser des points de terminaison privés pour intégrer Azure Functions à un réseau virtuel
description: Tutoriel pas à pas qui vous montre comment connecter une fonction à un réseau virtuel Azure et le verrouiller avec des points de terminaison privés
ms.topic: article
ms.date: 2/22/2021
ms.openlocfilehash: a7bad58167009b4089724165813eb061996f1e6b
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102200204"
---
# <a name="tutorial-integrate-azure-functions-with-an-azure-virtual-network-using-private-endpoints"></a>Tutoriel : intégrer des Azure Functions à un réseau virtuel Azure à l’aide de points de terminaison privés

Ce tutoriel vous montre comment utiliser Azure Functions pour vous connecter aux ressources d’un réseau virtuel Azure avec des points de terminaison privé. Vous allez créer une fonction avec un compte de stockage verrouillé derrière un réseau virtuel qui utilise un déclencheur de file d’attente Service Bus.

> [!div class="checklist"]
> * Créer une application de fonction sur le plan Premium
> * Créer des ressources Azure (Service Bus, compte de stockage, réseau virtuel)
> * Verrouiller votre compte de stockage derrière un point de terminaison privé
> * Verrouiller votre Service Bus derrière un point de terminaison privé
> * Déployez une application de fonction avec les déclencheurs Service Bus et HTTP.
> * Verrouiller votre application de fonction derrière un point de terminaison privé
> * Vérifier que votre application de fonction est sécurisée derrière le réseau virtuel
> * Nettoyer les ressources

## <a name="create-a-function-app-in-a-premium-plan"></a>Créer une application de fonction sur le plan Premium

Tout d’abord, vous créez une application de fonction .NET dans le [plan Premium], car ce tutoriel utilise C#. D’autres langages sont également pris en charge dans Windows. Ce plan fournit une mise à l’échelle serverless lors de la prise en charge de l’intégration d’un réseau virtuel.

1. Dans le menu du portail Azure ou dans la page **Accueil**, sélectionnez **Créer une ressource**.

1. Dans la page **Nouveau**, sélectionnez **Calcul** > **Application de fonction**.

1. Dans la page **De base**, utilisez les paramètres d’application de fonction comme indiqué dans le tableau ci-dessous :

    | Paramètre      | Valeur suggérée  | Description |
    | ------------ | ---------------- | ----------- |
    | **Abonnement** | Votre abonnement | Abonnement sous lequel est créée cette nouvelle application de fonction. |
    | **[Groupe de ressources](../azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Nom du nouveau groupe de ressources dans lequel créer votre Function App. |
    | **Nom de l’application de fonction** | Nom globalement unique | Nom qui identifie votre nouvelle Function App. Les caractères valides sont `a-z` (insensible à la casse), `0-9`et `-`.  |
    |**Publier**| Code | Option permettant de publier des fichiers de code ou un conteneur Docker. |
    | **Pile d’exécution** | .NET | Ce tutoriel utilise .NET |
    |**Région**| Région recommandée | Choisissez une [région](https://azure.microsoft.com/regions/) près de chez vous ou près d’autres services auxquels ont accès vos fonctions. |

1. Sélectionnez **Suivant : Hébergement**. Dans la page **Hébergement**, entrez les paramètres suivants :

    | Paramètre      | Valeur suggérée  | Description |
    | ------------ | ---------------- | ----------- |
    | **[Compte de stockage](../storage/common/storage-account-create.md)** |  Nom globalement unique |  Créez un compte de stockage utilisé par votre application de fonction. Les noms des comptes de stockage doivent comporter entre 3 et 24 caractères, uniquement des lettres minuscules et des chiffres. Vous pouvez également utiliser un compte existant qui doit répondre aux [exigences relatives aux comptes de stockage](./storage-considerations.md#storage-account-requirements). |
    |**Système d’exploitation**| Windows | Ce tutoriel utilise Windows |
    | **[Plan](./functions-scale.md)** | Premium | Plan d’hébergement qui définit la façon dont les ressources sont allouées à votre Function App. Sélectionnez **Premium**. Par défaut, un plan App Service est créé. **Référence et taille** est défini avec la valeur par défaut **EP1**, où EP signifie _élastique premium_. Pour plus d’informations, consultez la [liste des références SKU Premium](./functions-premium-plan.md#available-instance-skus).<br/>Lorsque vous exécutez des fonctions JavaScript dans un plan Premium, vous devez choisir une instance qui comporte moins de processeurs virtuels. Pour plus d’informations, consultez [Choisir des plans Premium à un cœur](./functions-reference-node.md#considerations-for-javascript-functions).  |

1. Sélectionnez **Suivant : Supervision**. Dans la page **Supervision**, entrez les paramètres suivants :

    | Paramètre      | Valeur suggérée  | Description |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](./functions-monitoring.md)** | Default | Crée une ressource Application Insights avec le même *nom de l’application* dans la région prise en charge la plus proche. En développant ce paramètre, vous pouvez changer le **Nouveau nom de ressource** ou choisir un autre **Emplacement** dans une [Zone géographique Azure](https://azure.microsoft.com/global-infrastructure/geographies/) où stocker vos données. |

1. Sélectionnez **Vérifier + créer** pour passer en revue les sélections de configuration d’application.

1. Dans la page **Vérifier + créer**, vérifiez vos paramètres, puis sélectionnez **Créer** pour provisionner et déployer l’application de fonction.

1. Cliquez sur l’icône **Notifications** en haut à droite du portail pour voir le message **Le déploiement a été effectué**.

1. Sélectionnez **Accéder à la ressource** pour afficher votre nouvelle application de fonction. Vous pouvez également sélectionner **Épingler au tableau de bord**. L’épinglage permet de revenir plus facilement à cette ressource d’application de fonction à partir de votre tableau de bord.

1. Félicitations ! Vous avez créé votre application de fonction Premium avec succès !

## <a name="create-azure-resources"></a>Créer des ressources Azure

### <a name="create-a-storage-account"></a>Créez un compte de stockage.

Un compte de stockage différent de celui créé lors de la création initiale de votre application de fonction est requis pour les réseaux virtuels.

1. Dans le menu du portail Azure ou dans la page **Accueil**, sélectionnez **Créer une ressource**.

1. Dans la page Nouveau, recherchez **Compte de stockage** et sélectionnez **Créer**

1. Dans l’onglet **De base**, définissez les paramètres comme indiqué dans le tableau ci-dessous. Vous pouvez laisser les paramètres par défaut pour le reste :

    | Paramètre      | Valeur suggérée  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Votre abonnement | Abonnement sous lequel vos ressources sont créées. | 
    | **[Groupe de ressources](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Choisissez le groupe de ressources que vous avez créé avec votre application de fonction. |
    | **Nom** | mysecurestorage| Nom de votre compte de stockage auquel le point de terminaison privé sera appliqué. |
    | **[Région](https://azure.microsoft.com/regions/)** | myFunctionRegion | Choisissez la région dans laquelle vous avez créé votre application de fonction. |

1. Sélectionnez **Revoir + créer**. Une fois la validation terminée, sélectionnez **Créer**.

### <a name="create-a-service-bus"></a>Créer un Service Bus

1. Dans le menu du portail Azure ou dans la page **Accueil**, sélectionnez **Créer une ressource**.

1. Dans la page Nouveau, recherchez **Service Bus**, puis sélectionnez **Créer**.

1. Dans l’onglet **De base**, définissez les paramètres comme indiqué dans le tableau ci-dessous. Vous pouvez laisser les paramètres par défaut pour le reste :

    | Paramètre      | Valeur suggérée  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Votre abonnement | Abonnement sous lequel vos ressources sont créées. |
    | **[Groupe de ressources](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Choisissez le groupe de ressources que vous avez créé avec votre application de fonction. |
    | **Nom** | myServiceBus| Nom de votre Service Bus auquel le point de terminaison privé sera appliqué. |
    | **[Région](https://azure.microsoft.com/regions/)** | myFunctionRegion | Choisissez la région dans laquelle vous avez créé votre application de fonction. |
    | **Niveau tarifaire** | Premium | Choisissez ce niveau pour utiliser des points de terminaison privés avec Service Bus. |

1. Sélectionnez **Revoir + créer**. Une fois la validation terminée, sélectionnez **Créer**.

### <a name="create-a-virtual-network"></a>Créer un réseau virtuel

Les ressources Azure de ce tutoriel s’intègrent à ou sont placées dans un réseau virtuel. Vous utiliserez des points de terminaison privés pour conserver le trafic réseau contenu dans le réseau virtuel.

Le tutoriel crée deux sous-réseaux :
- **par défaut** : sous-réseau pour les points de terminaison privés. Les adresses IP privées sont fournies à partir de ce sous-réseau.
- **fonctions**: sous-réseau pour l’intégration du réseau virtuel Azure Functions. Ce sous-réseau est délégué à l’application de fonction.

À présent, créez le réseau virtuel que l’application de fonction intègre.

1. Dans le menu du portail Azure ou dans la page Accueil, sélectionnez **Créer une ressource**.

1. Dans la page Nouveau, recherchez **Réseau virtuel**, puis sélectionnez **Créer**.

1. Dans l’onglet **De base**, utilisez les paramètres de réseau virtuel comme indiqué ci-dessous :

    | Paramètre      | Valeur suggérée  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Votre abonnement | Abonnement sous lequel vos ressources sont créées. | 
    | **[Groupe de ressources](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Choisissez le groupe de ressources que vous avez créé avec votre application de fonction. |
    | **Nom** | myVirtualNet| Nom de votre réseau virtuel auquel votre application de fonction se connectera. |
    | **[Région](https://azure.microsoft.com/regions/)** | myFunctionRegion | Choisissez la région dans laquelle vous avez créé votre application de fonction. |

1. Sous l’onglet **Adresses IP**, sélectionnez **Ajouter un sous-réseau**. Utilisez les paramètres comme indiqué ci-dessous lors de l’ajout d’un sous-réseau :

    :::image type="content" source="./media/functions-create-vnet/1-create-vnet-ip-address.png" alt-text="Capture d’écran de la vue de création d’une configuration de réseau virtuel.":::

    | Paramètre      | Valeur suggérée  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **Nom du sous-réseau** | functions | Nom du sous-réseau auquel votre application de fonction se connectera. | 
    | **Plage d’adresses de sous-réseau** | 10.0.1.0/24 | Notre espace d’adressage IPv4 dans l’image ci-dessus est 10.0.0.0/16. Si la version ci-dessus était 10.1.0.0/16, la *plage d’adresses de sous-réseau* recommandée serait 10.1.1.0/24. |

1. Sélectionnez **Revoir + créer**. Une fois la validation terminée, sélectionnez **Créer**.

## <a name="lock-down-your-storage-account-with-private-endpoints"></a>Verrouiller votre compte de stockage avec des points de terminaison privés

Les points de terminaison privés Azure sont utilisés pour se connecter à des ressources Azure spécifiques à l’aide d’une adresse IP privée. Cette connexion garantit que le trafic réseau reste dans le réseau virtuel choisi et que l’accès n’est disponible que pour des ressources spécifiques. À présent, créez les points de terminaison privés pour le stockage de fichiers Azure et le stockage d’objets BLOB Azure avec votre compte de stockage.

1. Dans votre nouveau compte de stockage, sélectionnez **Mise en réseau** dans le menu de gauche.

1. Sélectionnez l’onglet **Connexions de points de terminaison privés**, puis sélectionnez **Point de terminaison privé**.

    :::image type="content" source="./media/functions-create-vnet/2-navigate-private-endpoint-store.png" alt-text="Capture d’écran montrant comment naviguer pour créer des points de terminaison privés pour le compte de stockage.":::

1. Dans l’onglet **De base**, utilisez les paramètres de point de terminaison privé comme indiqué ci-dessous :

    | Paramètre      | Valeur suggérée  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Votre abonnement | Abonnement sous lequel vos ressources sont créées. | 
    | **[Groupe de ressources](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Choisissez le groupe de ressources que vous avez créé avec votre application de fonction. | |
    | **Nom** | file-endpoint | Nom du point de terminaison privé pour les fichiers de votre compte de stockage. |
    | **[Région](https://azure.microsoft.com/regions/)** | myFunctionRegion | Choisissez la région dans laquelle vous avez créé votre compte de stockage. |

1. Dans l’onglet **Ressource**, utilisez les paramètres de point de terminaison privé comme indiqué ci-dessous :

    | Paramètre      | Valeur suggérée  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Votre abonnement | Abonnement sous lequel vos ressources sont créées. | 
    | **Type de ressource**  | Microsoft.Storage/storageAccounts | Il s’agit du type de ressource pour les comptes de stockage. |
    | **Ressource** | mysecurestorage | Développez le compte de stockage que vous venez de créer |
    | **Sous-ressource cible** | fichier | Ce point de terminaison privé sera utilisé pour les fichiers du compte de stockage. |

1. Sous l’onglet **Configuration**, choisissez **Par défaut** pour le paramètre de sous-réseau.

1. Sélectionnez **Revoir + créer**. Une fois la validation terminée, sélectionnez **Créer**. Les ressources du réseau virtuel peuvent désormais communiquer avec les fichiers de stockage.

1. Créez un autre point de terminaison privé pour les objets BLOB. Pour l’onglet **Ressources**, utilisez les paramètres ci-dessous. Pour tous les autres paramètres, utilisez les mêmes paramètres que ceux des étapes de création de point de terminaison privé de fichier que vous venez de suivre.

    | Paramètre      | Valeur suggérée  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Votre abonnement | Abonnement sous lequel vos ressources sont créées. | 
    | **Type de ressource**  | Microsoft.Storage/storageAccounts | Il s’agit du type de ressource pour les comptes de stockage. |
    | **Ressource** | mysecurestorage | Développez le compte de stockage que vous venez de créer |
    | **Sous-ressource cible** | objet BLOB | Ce point de terminaison privé sera utilisé pour les blobs du compte de stockage. |

## <a name="lock-down-your-service-bus-with-a-private-endpoint"></a>Verrouiller votre Service Bus avec un point de terminaison privé

À présent, créez le point de terminaison privé de votre Azure Service Bus.

1. Dans votre nouveau Service Bus, sélectionnez **Mise en réseau** dans le menu de gauche.

1. Sélectionnez l’onglet **Connexions de points de terminaison privés**, puis sélectionnez **Point de terminaison privé**.

    :::image type="content" source="./media/functions-create-vnet/3-navigate-private-endpoint-service-bus.png" alt-text="Capture d’écran montrant comment accéder aux points de terminaison privés pour Service Bus.":::

1. Dans l’onglet **De base**, utilisez les paramètres de point de terminaison privé comme indiqué ci-dessous :

    | Paramètre      | Valeur suggérée  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Votre abonnement | Abonnement sous lequel vos ressources sont créées. | 
    | **[Groupe de ressources](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Choisissez le groupe de ressources que vous avez créé avec votre application de fonction. |
    | **Nom** | sb-endpoint | Nom du point de terminaison privé pour les fichiers de votre compte de stockage. |
    | **[Région](https://azure.microsoft.com/regions/)** | myFunctionRegion | Choisissez la région dans laquelle vous avez créé votre compte de stockage. |

1. Dans l’onglet **Ressource**, utilisez les paramètres de point de terminaison privé comme indiqué ci-dessous :

    | Paramètre      | Valeur suggérée  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Votre abonnement | Abonnement sous lequel vos ressources sont créées. | 
    | **Type de ressource**  | Microsoft.ServiceBus/namespaces | Il s’agit du type de ressource pour Service Bus. |
    | **Ressource** | myServiceBus | Le Service Bus que vous avez créé précédemment dans le tutoriel. |
    | **Sous-ressource cible** | espace de noms | Ce point de terminaison privé sera utilisé pour l’espace de noms de Service Bus. |

1. Sous l’onglet **Configuration**, choisissez **Par défaut** pour le paramètre de sous-réseau.

1. Sélectionnez **Revoir + créer**. Une fois la validation terminée, sélectionnez **Créer**. Les ressources du réseau virtuel peuvent désormais communiquer avec Service Bus.

## <a name="create-a-file-share"></a>Créer un partage de fichiers

1. Dans le compte de stockage que vous avez créé, sélectionnez **Partages de fichiers** dans le menu de gauche.

1. Sélectionnez **+ Partages de fichiers**. Fournissez **fichiers** comme nom pour le partage de fichiers dans le cadre de ce tutoriel.

    :::image type="content" source="./media/functions-create-vnet/4-create-file-share.png" alt-text="Capture d’écran montrant comment créer un partage de fichiers dans le compte de stockage.":::

## <a name="get-storage-account-connection-string"></a>Obtenir la chaîne de connexion du compte de stockage

1. Dans le compte de stockage que vous avez créé, sélectionnez **Clé d’accès** dans le menu de gauche.

1. Sélectionnez **Afficher les clés**. Copiez la chaîne de connexion de key1 et enregistrez-la. Nous aurons besoin de cette chaîne de connexion plus tard lors de la configuration des paramètres de l’application.

    :::image type="content" source="./media/functions-create-vnet/5-get-store-connection-string.png" alt-text="Capture d’écran montrant comment obtenir une chaîne de connexion de compte de stockage.":::

## <a name="create-a-queue"></a>Créer une file d’attente

Il s’agit de la file d’attente pour laquelle votre déclencheur Azure Functions Service Bus obtiendra des événements.

1. Dans Service Bus, sélectionnez **Files d’attente** dans le menu de gauche.

1. Sélectionnez **Stratégies d’accès partagé**. Indiquez **file d’attente** comme nom de la file d’attente dans le cadre de ce tutoriel.

    :::image type="content" source="./media/functions-create-vnet/6-create-queue.png" alt-text="Capture d’écran de la création d’une file d’attente Service Bus.":::

## <a name="get-service-bus-connection-string"></a>Obtenir une chaîne de connexion Service Bus

1. Dans Service Bus, sélectionnez **Stratégies d’accès partagé** dans le menu de gauche.

1. Sélectionnez **RootManageSharedAccessKey**. Copiez la **Chaîne de connexion principale**, puis enregistrez-la. Nous aurons besoin de cette chaîne de connexion plus tard lors de la configuration des paramètres de l’application.

    :::image type="content" source="./media/functions-create-vnet/7-get-service-bus-connection-string.png" alt-text="Capture d’écran montrant comment obtenir une chaîne de connexion Service Bus.":::

## <a name="integrate-function-app-with-your-virtual-network"></a>Intégrer l’application de fonction à votre réseau virtuel

Pour utiliser votre application de fonction avec des réseaux virtuels, vous devez la joindre à un sous-réseau. Nous utilisons un sous-réseau spécifique pour l’intégration de réseau virtuel Azure Functions et le sous-réseau par défaut pour tous les autres points de terminaison privés créés dans ce tutoriel.

1. Dans votre application de fonction, sélectionnez **Mise en réseau** dans le menu de gauche.

1. Sous Intégration au réseau virtuel, sélectionnez **Cliquer ici pour configurer**.

    :::image type="content" source="./media/functions-create-vnet/8-connect-app-vnet.png" alt-text="Capture d’écran montrant comment accéder à l’intégration du réseau virtuel.":::

1. Sélectionner **Ajouter un réseau virtuel**

1. Dans le panneau qui s’ouvre sous **Réseau virtuel**, sélectionnez le réseau virtuel que vous avez créé précédemment.

1. Sélectionnez le **Sous-réseau** que nous avons créé précédemment appelé **Functions**. Votre application de fonction est désormais intégrée à votre réseau virtuel !

    :::image type="content" source="./media/functions-create-vnet/9-connect-app-subnet.png" alt-text="Capture d’écran montrant comment connecter une application de fonction à un sous-réseau.":::

## <a name="configure-your-function-app-settings-for-private-endpoints"></a>Configurer les paramètres de votre application de fonction pour les points de terminaison privés

1. Dans votre application de fonction, sélectionnez **Configuration** dans le menu de gauche.

1. Pour utiliser votre application de fonction avec des réseaux virtuels, vous devez mettre à jour les paramètres d’application suivants. Sélectionnez **+ Nouveau paramètre d’application** ou le crayon **Modification** dans la colonne la plus à droite du tableau paramètres de l’application, le cas échéant. Lorsque vous avez terminé, sélectionnez **Enregistrer**.

    :::image type="content" source="./media/functions-create-vnet/10-configure-app-settings.png" alt-text="Capture d’écran montrant comment configurer les paramètres de l’application de fonction pour les points de terminaison privés.":::

    | Paramètre      | Valeur suggérée  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **AzureWebJobsStorage** | mysecurestorageConnectionString | La chaîne de connexion du compte de stockage que vous avez créé. Il s’agit de la chaîne de connexion de stockage à partir de la section [Obtenir la chaîne de connexion du compte de stockage](#get-storage-account-connection-string). Si vous modifiez ce paramètre, votre application de fonction utilisera désormais le compte de stockage sécurisé pour les opérations normales au moment du runtime. | 
    | **WEBSITE_CONTENTAZUREFILECONNECTIONSTRING**  | mysecurestorageConnectionString | La chaîne de connexion du compte de stockage que vous avez créé. En modifiant ce paramètre, votre application de fonction utilisera désormais le compte de stockage sécurisé pour Azure Files, utilisé lors du déploiement. |
    | **WEBSITE_CONTENTSHARE** | files | Le nom du partage de fichiers que vous avez créé dans le compte de stockage. Ce paramètre d’application est utilisé avec WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. |
    | **SERVICEBUS_CONNECTION** | myServiceBusConnectionString | Créez un paramètre d’application pour la chaîne de connexion de votre Service Bus. Il s’agit de la chaîne de connexion de stockage à partir de la section [Obtenir la chaîne de connexion de Service Bus](#get-service-bus-connection-string).|
    | **WEBSITE_CONTENTOVERVNET** | 1 | Créez ce paramètre d’application. La valeur 1 permet à votre application de fonction de se mettre à l’échelle lorsque votre compte de stockage est limité à un réseau virtuel. Vous devez activer ce paramètre lorsque vous limitez votre compte de stockage à un réseau virtuel. |
    | **WEBSITE_DNS_SERVER** | 168.63.129.16 | Créez ce paramètre d’application. Une fois que votre application s’intègre à un réseau virtuel, elle utilise le même serveur DNS que le réseau virtuel. Il s’agit de l’un des deux paramètres nécessaires pour que votre application de fonction fonctionne avec les zones privées Azure DNS et qu’elle soit requise lors de l’utilisation de points de terminaison privés. Ces paramètres vont envoyer tous les appels sortants de votre application à votre réseau virtuel. |
    | **WEBSITE_VNET_ROUTE_ALL** | 1 | Créez ce paramètre d’application. Une fois que votre application s’intègre à un réseau virtuel, elle utilise le même serveur DNS que le réseau virtuel. Il s’agit de l’un des deux paramètres nécessaires pour que votre application de fonction fonctionne avec les zones privées Azure DNS et qu’elle soit requise lors de l’utilisation de points de terminaison privés. Ces paramètres vont envoyer tous les appels sortants de votre application à votre réseau virtuel. |

1. En restant dans l’affichage **Configuration**, sélectionnez l’onglet **Paramètres du runtime de fonction**.

1. Configurez **Surveillance de la mise à l’échelle du runtime** sur **Activer**, puis sélectionnez **Enregistrer**. La mise à l’échelle pilotée par le runtime vous permet de connecter des fonctions de déclencheur non-HTTP à des services s’exécutant dans votre réseau virtuel.

    :::image type="content" source="./media/functions-create-vnet/11-enable-runtime-scaling.png" alt-text="Capture d’écran montrant comment activer la mise à l’échelle pilotée par le runtime pour Azure Functions.":::

## <a name="deploy-a-service-bus-trigger-and-http-trigger-to-your-function-app"></a>Déployer un déclencheur Service Bus et un déclencheur http dans votre application de fonction

1. Dans GitHub, accédez à l’exemple de référentiel suivant. Il contient une application de fonction avec deux fonctions : un déclencheur HTTP et un déclencheur de file d’attente Service Bus.

    <https://github.com/Azure-Samples/functions-vnet-tutorial>

1. En haut de la page, sélectionnez le bouton **Fourche** pour créer une fourche de ce référentiel dans votre propre compte ou organisation GitHub.

1. Dans votre application de fonction, sélectionnez **Centre de déploiement** dans le menu de gauche. Puis, sélectionnez **Paramètres**.

1. Dans l’onglet **Paramètres**, utilisez les paramètres de déploiement comme indiqué ci-dessous :

    | Paramètre      | Valeur suggérée  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **Source** | GitHub | Vous devez avoir créé un référentiel GitHub avec l’exemple de code à l’étape 2. | 
    | **Organisation**  | myOrganization | Il s’agit de l’organisation dans laquelle votre référentiel est archivé, généralement votre compte. |
    | **Référentiel** | myRepo | Référentiel que vous avez créé avec l’exemple de code. |
    | **Branche** | main | Il s’agit du référentiel que vous venez de créer. Utilisez donc la branche principale. |
    | **Pile d’exécution** | .NET | L’exemple de code est dans C#. |

1. Sélectionnez **Enregistrer**. 

    :::image type="content" source="./media/functions-create-vnet/12-deploy-portal.png" alt-text="Capture d’écran montrant comment déployer le code Azure Functions via le portail.":::

1. Le déploiement initial peu prendre quelques minutes. Un message d’état **Succès (actif)** s’affiche dans l’onglet **Journaux** lorsque votre application est déployée avec succès. Actualisez la page si nécessaire. 

1. Félicitations ! Vous avez déployé avec succès votre échantillon d’application de fonction.

## <a name="lock-down-your-function-app-with-a-private-endpoint"></a>Verrouiller votre application de fonction avec un point de terminaison privé

À présent, créez le point de terminaison privé de votre application de fonction. Ce point de terminaison privé connecte votre application de fonction en toute sécurité et de façon privée à votre réseau virtuel à l’aide d’une adresse IP privée. Pour plus d’informations sur les points de terminaison privés, accédez à la [documentation sur les points de terminaison privés](https://docs.microsoft.com/azure/private-link/private-endpoint-overview).

1. Dans votre application de fonction, sélectionnez **Mise en réseau** dans le menu de gauche.

1. Sélectionnez **Cliquer ici pour configurer** sous Connexions de points de terminaison privés.

    :::image type="content" source="./media/functions-create-vnet/14-navigate-app-private-endpoint.png" alt-text="Capture d’écran montrant comment accéder à un point de terminaison privé d’une application de fonction.":::

1. Sélectionnez **Ajouter**.

1. Dans le menu qui s’ouvre, utilisez les paramètres de point de terminaison privé comme indiqué ci-dessous :

    :::image type="content" source="./media/functions-create-vnet/15-create-app-private-endpoint.png" alt-text="Capture d’écran montrant comment créer un point de terminaison privé d’une application de fonction.":::

1. Sélectionnez **OK** pour ajouter le point de terminaison privé. Félicitations ! Vous avez correctement sécurisé votre application de fonction, Service Bus et votre compte de stockage avec des points de terminaison privés !

### <a name="test-your-locked-down-function-app"></a>Tester votre application de fonction verrouillée

1. Dans votre application de fonction, sélectionnez **Functions** dans le menu de gauche.

1. Sélectionnez **ServiceBusQueueTrigger**.

1. Dans le menu de gauche, sélectionnez **Surveiller**. Vous verrez que vous ne pouvez pas surveiller votre application. En effet, votre navigateur n’a pas accès au réseau virtuel, il ne peut donc pas accéder directement aux ressources au sein du réseau virtuel. Nous allons maintenant démontrer une autre méthode par laquelle vous pouvez toujours surveiller votre fonction, Application Insights.

1. Dans votre application de fonction, sélectionnez **Application Insights** dans le menu de gauche, puis sélectionnez **Afficher les données d’Application Insights**.

    :::image type="content" source="./media/functions-create-vnet/16-app-insights.png" alt-text="Capture d’écran montrant comment afficher Application Insights pour une application de fonction.":::

1. Dans le menu de gauche, sélectionnez **Métriques en temps réel**.

1. Ouvrez un nouvel onglet. Dans Service Bus, sélectionnez **Files d’attente** dans le menu de gauche.

1. Sélectionnez votre file d’attente.

1. Sélectionnez **Service Bus Explorer** dans le menu de gauche. Sous **Envoyer**, choisissez **Texte/brut** comme **Type de contenu** et entrez un message. 

1. Sélectionnez **Envoyer** pour envoyer un message.

    :::image type="content" source="./media/functions-create-vnet/17-send-service-bus-message.png" alt-text="Capture d’écran montrant comment envoyer des messages Service Bus à l’aide du portail.":::

1. Sous l’onglet avec les **Métriques en temps réel** ouvertes, vous devez voir que votre déclencheur de file d’attente Service Bus a été déclenché. Si ce n’est pas le cas, renvoyez le message à partir de **Service Bus Explorer**

    :::image type="content" source="./media/functions-create-vnet/18-hello-world.png" alt-text="Capture d’écran montrant comment afficher les messages à l’aide de métriques en temps réel pour les applications de fonction.":::

1. Félicitations ! Vous avez testé avec succès votre application de fonction configurée avec des points de terminaison privés.

### <a name="private-dns-zones"></a>Zones DNS privées
L’utilisation d’un point de terminaison privé pour se connecter aux ressources Azure consiste à se connecter à une adresse IP privée au lieu du point de terminaison public. Les services Azure existants sont configurés afin d’uutiliser le DNS existant pour se connecter au point de terminaison public. La configuration DNS devra être remplacée pour se connecter au point de terminaison privé.

Une zone DNS privée a été créée pour chaque ressource Azure configurée avec un point de terminaison privé. Un enregistrement DNS est créé pour chaque adresse IP privée associée au point de terminaison privé.

Les zones DNS suivantes ont été créées dans ce tutoriel :

- privatelink.file.core.windows.net
- privatelink.blob.core.windows.net
- privatelink.servicebus.windows.net
- privatelink.azurewebsites.net

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez créé une application de fonction Premium, un compte de stockage et un Service Bus. Vous les avez également sécurisés derrière des points de terminaison privés. En savoir plus sur les différentes fonctionnalités de mise en réseau disponibles ci-dessous :

> [!div class="nextstepaction"]
> [En savoir plus sur les options de mise en réseau dans Functions](./functions-networking-options.md)

[Plan Premium]: functions-premium-plan.md
