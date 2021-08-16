---
title: Déployer Azure Spring Cloud dans un réseau virtuel
description: Déployez Azure Spring Cloud dans un réseau virtuel (injection de réseau virtuel).
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 07/21/2020
ms.custom: devx-track-java, devx-track-azurecli, subject-rbac-steps
ms.openlocfilehash: 0921c3d9bf254e3d486ec381c3243a8035bb6f50
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111750350"
---
# <a name="deploy-azure-spring-cloud-in-a-virtual-network"></a>Déployer Azure Spring Cloud dans un réseau virtuel

**Cet article s’applique à :** ✔️ Java ✔️ C#

Ce tutoriel explique comment déployer une instance Azure Spring Cloud dans votre réseau virtuel. Ce déploiement est parfois appelé « injection de réseau virtuel ».

Le déploiement permet :

* L’isolement des applications et du runtime du service Azure Spring Cloud par rapport à Internet sur votre réseau d’entreprise.
* L’interaction d’Azure Spring Cloud avec des systèmes de centres de données locaux ou des services Azure dans d’autres réseaux virtuels.
* La possibilité pour les clients de contrôler les communications réseau entrantes et sortantes pour Azure Spring Cloud

> [!Note]
> Vous pouvez sélectionner votre réseau virtuel Azure uniquement quand vous créez une instance de service Azure Spring Cloud. Vous ne pouvez pas choisir d’utiliser un autre réseau virtuel après la création de l’instance Azure Spring Cloud.

## <a name="prerequisites"></a>Prérequis

Inscrivez le fournisseur de ressources Azure Spring Cloud **Microsoft.AppPlatform** et **Microsoft.ContainerService** conformément aux instructions figurant dans [Inscrire un fournisseur de ressources sur le portail Azure](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) ou en exécutant la commande Azure CLI suivante :

```azurecli
az provider register --namespace Microsoft.AppPlatform
az provider register --namespace Microsoft.ContainerService
```

## <a name="virtual-network-requirements"></a>Conditions requises pour le réseau virtuel

Le réseau virtuel dans lequel vous déployez votre instance Azure Spring Cloud doit répondre aux conditions suivantes :

* **Emplacement** : le réseau virtuel doit résider au même emplacement que l’instance Azure Spring Cloud.
* **Abonnement**: le réseau virtuel doit figurer dans le même abonnement que l’instance Azure Spring Cloud.
* **Sous-réseaux** : le réseau virtuel doit inclure deux sous-réseaux dédiés à une instance Azure Spring Cloud :

    * Un pour le runtime du service.
    * Un pour vos applications de microservices Spring Boot.
    * Il existe une relation un-à-un entre ces sous-réseaux et une instance Azure Spring Cloud. Utilisez un nouveau sous-réseau pour chaque instance de service que vous déployez. Chaque sous-réseau ne peut inclure qu’une seule instance de service.
* **Espace d’adressage** : blocs CIDR jusqu’à */28* pour le sous-réseau du runtime du service et le sous-réseau des applications de microservices Spring Boot.
* **Table de routage** : par défaut, les sous-réseaux n’ont pas besoin d’être associés à des tables de routage existantes. Vous pouvez [apporter votre propre table de routage](#bring-your-own-route-table).

Les procédures suivantes décrivent la configuration du réseau virtuel pour contenir l’instance d’Azure Spring Cloud.

## <a name="create-a-virtual-network"></a>Créer un réseau virtuel

Si vous disposez déjà d’un réseau virtuel pour héberger une instance Azure Spring Cloud, ignorez les étapes 1, 2 et 3. Vous pouvez commencer à l’étape 4 pour préparer les sous-réseaux du réseau virtuel.

1. Dans le menu du portail Azure, sélectionnez **Créer une ressource**. Dans la Place de marché Azure, sélectionnez **Mise en réseau** > **Réseau virtuel**.

1. Dans la boîte de dialogue **Créer un réseau virtuel**, entrez ou sélectionnez les informations suivantes :

    |Paramètre          |Value                                             |
    |-----------------|--------------------------------------------------|
    |Abonnement     |Sélectionnez votre abonnement.                         |
    |Groupe de ressources   |Sélectionnez votre groupe de ressources ou créez-en un.  |
    |Nom             |Entrez **azure-spring-cloud-vnet**.                 |
    |Emplacement         |Sélectionnez **USA Est**.                               |

1. Sélectionnez **Suivant : Adresses IP**.

1. Pour l’espace d’adressage IPv4, entrez **10.1.0.0/16**.

1. Sélectionnez **Ajouter un sous-réseau**. Ensuite, entrez **service-runtime-subnet** pour **Nom du sous-réseau** et entrez **10.1.0.0/28** pour **Plage d’adresses de sous-réseau**. Sélectionnez ensuite **Ajouter**.

1. Sélectionnez à nouveau **Ajouter un sous-réseau**, puis entrez **Nom du sous-réseau** et **Plage d’adresses de sous-réseau**. Par exemple, entrez **apps-subnet** et **10.1.1.0/28**. Sélectionnez ensuite **Ajouter**.

1. Sélectionnez **Revoir + créer**. Conservez les autres valeurs par défaut, puis sélectionnez **Créer**.

## <a name="grant-service-permission-to-the-virtual-network"></a>Accorder l’autorisation du service au réseau virtuel
Azure Spring Cloud nécessite l’autorisation **Propriétaire** sur votre réseau virtuel, afin d’accorder un principal de service dédié et dynamique sur le réseau virtuel en vue d’un déploiement et d’une maintenance supplémentaires.

Sélectionnez le réseau virtuel **azure-spring-cloud-vnet** que vous avez créé précédemment.

1. Sélectionnez **Contrôle d’accès (IAM)** , puis **Ajouter** > **Ajouter une attribution de rôle**.

    ![Capture d’écran de la fenêtre Contrôle d’accès](./media/spring-cloud-v-net-injection/access-control.png)

1. Attribuez le rôle *Propriétaire* au **Fournisseur de ressources Azure Spring Cloud**. Pour connaître les étapes détaillées, consultez [Attribuer des rôles Azure à l’aide du portail Azure](../role-based-access-control/role-assignments-portal.md).

Vous pouvez également effectuer cette étape en exécutant la commande Azure CLI suivante :

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

## <a name="deploy-an-azure-spring-cloud-instance"></a>Déployer une instance Azure Spring Cloud

Pour déployer une instance Azure Spring Cloud dans le réseau virtuel :

1. Ouvrez le [portail Azure](https://portal.azure.com).

1. Dans la zone de recherche supérieure, recherchez **Azure Spring Cloud**. Sélectionnez **Azure Spring Cloud** dans le résultat.

1. Dans la page **Azure Spring Cloud**, sélectionnez **+ Ajouter**.

1. Remplissez le formulaire sur la page **Créer** Azure Spring Cloud.

1. Sélectionnez le même groupe de ressources et la même région que le réseau virtuel.

1. Pour **Nom**, sous **Détails sur le service**, sélectionnez **azure-spring-cloud-vnet**.

1. Sélectionnez l’onglet **Mise en réseau** et les valeurs suivantes :

    |Paramètre                                |Valeur                                             |
    |---------------------------------------|--------------------------------------------------|
    |Déployer dans votre propre réseau virtuel     |Sélectionnez **Oui**.                                   |
    |Réseau virtuel                        |Sélectionnez **azure-spring-cloud-vnet**.               |
    |Sous-réseau du runtime du service                 |Sélectionnez **service-runtime-subnet**.                |
    |Sous-réseau des applications de microservices Spring Boot   |Sélectionnez **apps-subnet**.                           |

    ![Capture d’écran montrant l’onglet Mise en réseau de la page Créer Azure Spring Cloud](./media/spring-cloud-v-net-injection/creation-blade-networking-tab.png)

1. Sélectionnez **Examiner et créer**.

1. Vérifiez vos spécifications, puis cliquez sur **Créer**.

    ![Capture d’écran montrant la vérification des spécifications](./media/spring-cloud-v-net-injection/verify-specifications.png)

Après le déploiement, deux groupes de ressources supplémentaires sont créés dans votre abonnement pour héberger les ressources réseau pour l’instance Azure Spring Cloud. Accédez à **Accueil**, puis sélectionnez **Groupes de ressources** dans les éléments de menu du haut pour trouver les nouveaux groupes de ressources suivants.

Le groupe de ressources nommé **ap-svc-rt_{nom de l’instance du service}_{région de l’instance du service}** contient des ressources réseau pour le runtime du service de l’instance du service.

  ![Capture d’écran montrant le runtime du service](./media/spring-cloud-v-net-injection/service-runtime-resource-group.png)

Le groupe de ressources nommé **ap-app_{nom de l’instance du service}_{région de l’instance du service}** contient des ressources réseau pour vos applications de microservices Spring Boot de l’instance du service.

  ![Capture d’écran montrant le groupe de ressources des applications](./media/spring-cloud-v-net-injection/apps-resource-group.png)

Ces ressources réseau sont connectées à votre réseau virtuel créé dans l’image précédente.

  ![Capture d’écran montrant le réseau virtuel avec des appareils connectés](./media/spring-cloud-v-net-injection/vnet-with-connected-device.png)

   > [!Important]
   > Les groupes de ressources sont entièrement gérés par le service Azure Spring Cloud. Veillez *à ne pas* y supprimer ou modifier manuellement des ressources.

## <a name="using-smaller-subnet-ranges"></a>Utilisation de plages de sous-réseaux plus petites

Ce tableau indique le nombre maximal d’instances d’application prises en charge par Azure Spring Cloud à l’aide de plages de sous-réseaux plus petites.

| CIDR du sous-réseau d’application | Nombre total d’adresses IP | Adresses IP disponibles | Nombre maximal d’instances d’application                                        |
| --------------- | --------- | ------------- | ------------------------------------------------------------ |
| /28             | 16        | 8             | <p> Application avec 1 noyau :  96 <br/> Application avec 2 noyaux : 48<br/>  Application avec 3 noyaux : 32 <br/> Application avec 4 noyaux : 24 </p> |
| /27             | 32        | 24            | <p> Application avec 1 noyau :  228<br/> Application avec 2 noyaux : 144<br/>  Application avec 3 noyaux : 96 <br/>  Application avec 4 noyaux : 72</p> |
| /26             | 64        | 56            | <p> Application avec 1 noyau :  500<br/> Application avec 2 noyaux : 336<br/>  Application avec 3 noyaux : 224<br/>  Application avec 4 noyaux : 168</p> |
| /25             | 128       | 120           | <p> Application avec 1 noyau :  500<br> Application avec 2 noyaux :  500<br>  Application avec 3 noyaux :  480<br>  Application avec 4 noyaux : 360</p> |
| /24             | 256       | 248           | <p> Application avec 1 noyau :  500<br/> Application avec 2 noyaux :  500<br/>  Application avec 3 noyaux : 500<br/>  Application avec 4 noyaux : 500</p> |

Pour les sous-réseaux, cinq adresses IP sont réservées par Azure et au moins quatre adresses sont requises par Azure Spring Cloud. Au moins neuf adresses IP sont requises, si bien que /29 et /30 ne sont pas opérationnelles.

Pour un sous-réseau du runtime du service, la taille minimale est /28. Cette taille n’a aucun impact sur le nombre d’instances d’application.

## <a name="bring-your-own-route-table"></a>Apporter votre propre table de routage

Azure Spring Cloud prend en charge l’utilisation des sous-réseaux et des tables de routage existants.

Si vos sous-réseaux personnalisés ne contiennent pas de tables de routage, Azure Spring Cloud en crée pour chacun des sous-réseaux et y ajoute des règles tout au long du cycle de vie de l’instance. Si vos sous-réseaux personnalisés contiennent des tables de routage, Azure Spring Cloud reconnaît les tables de routage existantes pendant les opérations de l’instance et ajoute ou met à jour des règles en conséquence pour les opérations.

> [!Warning] 
> Des règles personnalisées peuvent être ajoutées aux tables de routage personnalisées et mises à jour. Toutefois, les règles sont ajoutées par Azure Spring Cloud et elles ne doivent pas être mises à jour ni supprimées. Les règles telles que 0.0.0.0/0 doivent toujours exister sur une table de route donnée et être mappées à la cible de votre passerelle Internet, telle qu’une appliance virtuelle réseau ou une autre passerelle de sortie. Lors de la mise à jour des règles, soyez prudent si seules vos règles personnalisées sont modifiées.


### <a name="route-table-requirements"></a>Spécifications des tables de routage

Les tables de routage auxquelles votre réseau virtuel personnalisé est associé doivent remplir les conditions suivantes :

* Vous pouvez associer vos tables de routage Azure à votre réseau virtuel uniquement lorsque vous créez une nouvelle instance de service Azure Spring Cloud. Vous ne pouvez pas choisir d’utiliser une autre table de routage après la création de l’instance Azure Spring Cloud.
* Le sous-réseau d’application de microservices et le sous-réseau du runtime du service doivent être associés à des tables de routage différentes ou à aucune d’entre elles.
* Les autorisations doivent être attribuées avant la création de l’instance. Veillez à accorder au **fournisseur de ressources Azure Spring Cloud** l’autorisation *Propriétaire* à vos tables de routage.
* La ressource de table de route associée ne peut pas être mise à jour après la création du cluster. Même si la ressource de table de route ne peut pas être mise à jour, les règles personnalisées peuvent être modifiées dans la table de route.
* Vous ne pouvez pas réutiliser une table de routage avec plusieurs instances en raison de règles d’acheminement pouvant entrer en conflit.

## <a name="next-steps"></a>Étapes suivantes

[Déployer une application dans Azure Spring Cloud dans votre réseau virtuel](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/02-deploy-application-to-azure-spring-cloud-in-your-vnet.md)

## <a name="see-also"></a>Voir aussi

- [Résolution des problèmes Azure Spring Cloud dans un réseau virtuel](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/05-troubleshooting-azure-spring-cloud-in-vnet.md)
- [Responsabilités du client pour l’exécution d’Azure Spring Cloud dans un réseau virtuel](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/06-customer-responsibilities-for-running-azure-spring-cloud-in-vnet.md)
