---
title: Tutoriel – Déployer Azure Spring Cloud dans un réseau virtuel
description: Déployez Azure Spring Cloud dans un réseau virtuel (injection de réseau virtuel).
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/21/2020
ms.custom: devx-track-java
ms.openlocfilehash: 6e2df9168b880e565ea9b70c82c2c0c1b55b4db8
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94737241"
---
# <a name="tutorial-deploy-azure-spring-cloud-in-azure-virtual-network-vnet-injection"></a>Tutoriel : Déployer Azure Spring Cloud dans un réseau virtuel (injection de réseau virtuel)

**Cet article s’applique à :** ✔️ Java ✔️ C#

Ce tutoriel explique comment déployer une instance du service Azure Spring Cloud dans votre réseau virtuel. Ce procédé est parfois appelé « injection de réseau virtuel ».  

Le déploiement permet :

* L’isolement des applications et du runtime du service Azure Spring Cloud par rapport à Internet sur votre réseau d’entreprise
* L’interaction d’Azure Spring Cloud avec les systèmes de centres de données locaux et/ou les services Azure d’autres réseaux virtuels
* La possibilité pour les clients de contrôler les communications réseau entrantes et sortantes pour Azure Spring Cloud

## <a name="prerequisites"></a>Prérequis
Vous devez inscrire le fournisseur de ressources Azure Spring Cloud *Microsoft.AppPlatform* et *Microsoft.ContainerService* en suivant les instructions [Inscrire un fournisseur de ressources sur le portail Azure](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) ou en exécutant la commande CLI az suivante :

```azurecli
az provider register --namespace Microsoft.AppPlatform
az provider register --namespace Microsoft.ContainerService
```
## <a name="virtual-network-requirements"></a>Conditions requises pour le réseau virtuel
Le réseau virtuel dans lequel vous déployez votre instance du service Azure Spring Cloud doit respecter les prérequis suivantes :

* **Emplacement** : le réseau virtuel doit résider au même emplacement que l’instance du service Azure Spring Cloud.
* **Abonnement**: le réseau virtuel doit résider dans le même abonnement que l’instance du service Azure Spring Cloud.
* **Sous-réseaux** : le réseau virtuel doit comporter deux sous-réseaux dédiés à une instance du service Azure Spring Cloud : 
    * Un pour le runtime du service
    * Un pour vos applications de microservices Spring Boot. 
    * Il existe une relation un-à-un entre ces sous-réseaux et une instance du service Azure Spring Cloud. Vous devez utiliser un nouveau sous-réseau pour chaque instance de service que vous déployez. En outre, chaque sous-réseau ne peut inclure qu’une seule instance de service.
* **Espace d’adressage** : un bloc CIDR allant jusqu’à /28 pour le sous-réseau du runtime du service et un autre bloc CIDR allant jusqu’à /24 pour le sous-réseau des applications de microservices Spring Boot.
* **Table de routage** : aucune table de routage existante ne doit être associée aux sous-réseaux.

Les procédures suivantes décrivent la configuration du réseau virtuel pour contenir l’instance d’Azure Spring Cloud.

## <a name="create-a-virtual-network"></a>Créer un réseau virtuel
Si vous disposez déjà d’un réseau virtuel pour héberger une instance du service Azure Spring Cloud, ignorez les étapes 1, 2 et 3. Vous pouvez commencer à l’étape 4 pour préparer les sous-réseaux du réseau virtuel.

1. Dans le menu du Portail Azure, sélectionnez **Créer une ressource**. À partir de la Place de marché Azure, sélectionnez **Mise en réseau** > **Réseau virtuel**.

1. Dans la boîte de dialogue **Créer un réseau virtuel**, entrez ou sélectionnez les informations suivantes :

    |Paramètre          |Value                                             |
    |-----------------|--------------------------------------------------|
    |Abonnement     |Sélectionnez votre abonnement.                         |
    |Groupe de ressources   |Sélectionnez votre groupe de ressources ou créez-en un.  |
    |Nom             |Entrez *azure-spring-cloud-vnet*                   |
    |Emplacement         |Sélectionnez **USA Est**.                                |

1. Cliquez sur **Suivant : Adresses IP >** . 
 
1. Pour l’espace d’adressage IPv4, entrez 10.1.0.0/16.

1. Sélectionnez **Ajouter un sous-réseau**, puis entrez *service-runtime-subnet* pour **Nom du sous-réseau** et 10.1.0.0/24 pour **Plage d’adresses de sous-réseau**. Cliquez ensuite sur **Ajouter**.

1. Sélectionnez à nouveau **Ajouter un sous-réseau**, puis entrez **Nom du sous-réseau** et **Plage d’adresses de sous-réseau**, par exemple *apps-subnet* et 10.1.1.0/24.  Cliquez sur **Add**.

1. Cliquez sur **Vérifier + créer**. Laissez les autres valeurs par défaut et cliquez sur **Créer**.

## <a name="grant-service-permission-to-the-virtual-network"></a>Accorder l’autorisation du service au réseau virtuel

Sélectionnez le réseau virtuel *azure-spring-cloud-vnet* que vous avez créé précédemment.

1. Sélectionnez **Contrôle d’accès (IAM)** , puis **Ajouter > Ajouter une attribution de rôle**.

    ![Contrôle d’accès pour le réseau virtuel](./media/spring-cloud-v-net-injection/access-control.png)

2. Dans la boîte de dialogue **Ajouter une attribution de rôle**, entrez ou sélectionnez les informations suivantes :

    |Paramètre  |Valeur                                             |
    |---------|--------------------------------------------------|
    |Role     |Sélectionnez **Propriétaire**                                  |
    |Sélectionner   |Entrez *Fournisseur de ressources Azure Spring Cloud*      |

    Sélectionnez ensuite *Fournisseur de ressources Azure Spring Cloud*, puis cliquez sur **Enregistrer**.

    ![Accorder le fournisseur de ressources Azure Spring Cloud au réseau virtuel](./media/spring-cloud-v-net-injection/grant-azure-spring-cloud-resource-provider-to-vnet.png)

Vous pouvez obtenir le même résultat en exécutant la commande CLI az suivante :

```azurecli
VIRTUAL_NETWORK_RESOURCE_ID=`az network vnet show \
    --name ${NAME_OF_VIRTUAL_NETWORK} \
    --resource-group ${RESOURCE_GROUP_OF_VIRTUAL_NETWORK} \
    --query "id" \
    --output tsv`

az role assignment create \
    --role "Owner" \
    --scope ${VIRTUAL_NETWORK_RESOURCE_ID} \
    --assignee e8de9221-a19c-4c81-b814-fd37c6caf9d2
```

## <a name="deploy-azure-spring-cloud-service-instance-in-the-virtual-network"></a>Déployer une instance du service Azure Spring Cloud dans le réseau virtuel

1. Ouvrez le portail Azure à l’adresse https://ms.portal.azure.com.

1. Dans la zone de recherche du haut, faites une recherche sur **Azure Spring Cloud**, puis sélectionnez **Azure Spring Cloud** dans les résultats.

1. Dans la page **Azure Spring Cloud**, sélectionnez **+ Ajouter**.

1. Remplissez le formulaire sur la page **Créer** Azure Spring Cloud. 

1. Sélectionnez le même groupe de ressources et la même région que le réseau virtuel.

1. Pour **Nom**, sous **Détails sur le service**, sélectionnez *azure-spring-cloud-vnet*.

1. Sélectionnez l’onglet **Réseau** et sélectionnez ce qui suit :

    |Paramètre                                |Valeur                                             |
    |---------------------------------------|--------------------------------------------------|
    |Déployer dans votre propre réseau virtuel     |Sélectionnez **Oui**                                    |
    |Réseau virtuel                        |Sélectionnez *azure-spring-cloud-vnet*                  |
    |Sous-réseau du runtime du service                 |Sélectionnez *service-runtime-subnet*                   |
    |Sous-réseau des applications de microservices Spring Boot   |Sélectionnez *apps-subnet*                              |

    ![Onglet de création de réseau](./media/spring-cloud-v-net-injection/creation-blade-networking-tab.png)

1. Cliquez sur **Vérifier et créer**.

1. Vérifiez vos spécifications, puis cliquez sur **Créer**.

Après le déploiement, deux groupes de ressources supplémentaires sont créés dans votre abonnement pour héberger les ressources réseau de l’instance du service Azure Spring Cloud.  Accédez à **Accueil**, puis sélectionnez **Groupes de ressources** dans les éléments de menu du haut pour trouver les nouveaux groupes de ressources suivants.

Le groupe de ressources nommé *ap-svc-rt_{nom de l’instance du service}_{région de l’instance du service}* contient des ressources réseau pour le runtime du service de l’instance du service.

  ![Runtime du service](./media/spring-cloud-v-net-injection/service-runtime-resource-group.png)

Le groupe de ressources nommé *ap-app_{nom de l’instance du service}_{région de l’instance du service}* contient des ressources réseau pour vos applications de microservices Spring Boot de l’instance du service.

  ![Groupe de ressources des applications](./media/spring-cloud-v-net-injection/apps-resource-group.png)

Ces ressources réseau sont connectées à votre réseau virtuel créé ci-dessus.

  ![Réseau virtuel avec un appareil connecté](./media/spring-cloud-v-net-injection/vnet-with-connected-device.png)

   > [!Important]
   > Les groupes de ressources sont entièrement gérés par le service Azure Spring Cloud. Veillez À NE PAS y supprimer ou modifier manuellement des ressources.

## <a name="next-steps"></a>Étapes suivantes

[Déployer une application dans Azure Spring Cloud dans votre réseau virtuel](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/02-deploy-application-to-azure-spring-cloud-in-your-vnet.md)

## <a name="see-also"></a>Voir aussi

- [Résolution des problèmes Azure Spring Cloud dans un réseau virtuel](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/05-troubleshooting-azure-spring-cloud-in-vnet.md)
- [Responsabilités du client pour l’exécution d’Azure Spring Cloud dans un réseau virtuel](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/06-customer-responsibilities-for-running-azure-spring-cloud-in-vnet.md)