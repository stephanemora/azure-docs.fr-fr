---
title: Liaison privée pour I’API Azure pour FHIR
description: Cet article explique comment configurer un point de terminaison privé pour les services de l’API Azure pour FHIR
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 04/29/2021
ms.author: zxue
ms.openlocfilehash: 248d499d166c6e397ef422b5ff653709b8b075e9
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108733447"
---
# <a name="configure-private-link"></a>Configurer une liaison privée

Le lien privé vous permet d’accéder à l’API Azure pour FHIR via un point de terminaison privé, qui est une interface réseau qui vous connecte en toute sécurité et en toute sécurité à l’aide d’une adresse IP privée de votre réseau virtuel. Avec un lien privé, vous pouvez accéder à nos services en toute sécurité à partir de votre réseau virtuel en tant que service de premier ordre sans avoir à passer par un système DNS (Domain Name System) public. Cet article explique comment créer, tester et gérer votre point de terminaison privé pour l’API Azure pour FHIR.

>[!Note]
>Ni la liaison privée ni l’API Azure pour FHIR ne peuvent être déplacés d’un groupe de ressources ou d’un abonnement à un autre une fois la liaison privée activée. Pour effectuer un déplacement, supprimez d’abord le lien privé, puis l’API Azure pour FHIR. Créez un nouveau lien privé une fois le déplacement terminé. Avant de supprimer une liaison privée, évaluez les ramifications de sécurité potentielles.
>
>Si l’exportation des journaux et des métriques d’audit est activée pour l’API Azure pour FHIR, mettez à jour le paramètre d’exportation via les **paramètres de diagnostic** à partir du portail.

## <a name="prerequisites"></a>Prérequis

Avant de créer un point de terminaison privé, vous devez d’abord créer des ressources Azure :

- Groupe de ressources : groupe de ressources Azure qui contiendra le réseau virtuel et le point de terminaison privé.
- API Azure pour FHIR : ressource FHIR que vous souhaitez placer derrière un point de terminaison privé.
- Réseau virtuel : réseau virtuel auquel vos services client et le point de terminaison privé seront connectés.

Pour plus d’informations, consultez [la documentation sur les liens privés](../../private-link/index.yml).

## <a name="disable-public-network-access"></a>Désactiver l’accès au réseau public

La création d’un point de terminaison privé pour votre ressource FHIR ne désactive pas automatiquement le trafic public vers celui-ci. Pour ce faire, vous devez mettre à jour votre ressource FHIR pour définir une nouvelle propriété « accès public » de « activé » sur « désactivé ». Soyez prudent lorsque vous désactivez l’accès public au réseau, car toutes les demandes adressées à votre service FHIR, qui ne proviennent pas d’un point de terminaison privé correctement configuré sont refusées. Seul le trafic provenant de vos points de terminaison privés est autorisé.

:::image type="content" source="media/private-link/private-link-disable.png" alt-text="Désactivez l’accès au réseau public.":::

## <a name="create-private-endpoint"></a>Créer un point de terminaison privé

Pour créer un point de terminaison privé, un développeur disposant d’autorisations de contrôle d’accès en fonction du rôle (RBAC) sur la ressource FHIR peut utiliser le Portail Azure, [Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)ou [Azure CLI](../../private-link/create-private-endpoint-cli.md). Cet article vous guide tout au long des étapes d’utilisation de Portail Azure. Il est recommandé d’utiliser le Portail Azure, car il automatise la création et la configuration de la zone de DNS privé. Pour plus d’informations, consultez [guides de démarrage rapide de liens privés](../../private-link/create-private-endpoint-portal.md).

Vous pouvez créer un point de terminaison privé de deux façons. Le flux d’approbation automatique permet à un utilisateur disposant d’autorisations de RBAC sur la ressource FHIR de créer un point de terminaison privé sans avoir besoin d’approbation. Le flux d’approbation manuelle permet à un utilisateur dépourvu d’autorisation sur la ressource FHIR de demander l’approbation d’un point de terminaison privé par les propriétaires de la ressource FHIR.

### <a name="auto-approval"></a>Approbation automatique

Vérifiez que la région du nouveau point de terminaison privé est identique à la région de votre réseau virtuel. La région de votre ressource FHIR peut être différente.

![Onglet Options de base du portail Azure](media/private-link/private-link-portal2.png)

Pour le type de ressource, recherchez et sélectionnez **Microsoft. HealthcareApis/services**. Pour la ressource, sélectionnez la ressource FHIR. Pour la sous-ressource cible, sélectionnez **FHIR**.

![Onglet Ressource du portail Azure](media/private-link/private-link-portal1.png)

Si vous ne disposez pas d’une zone DNS privé existante, sélectionnez **(nouveau) privatelink. azurehealthcareapis. com**. Si vous disposez d’une zone DNS privé configurée, vous pouvez la sélectionner dans la liste. Elle doit être au format **privatelink.azurehealthcareapis.com**.

![Onglet Configuration du portail Azure](media/private-link/private-link-portal3.png)

Une fois le déploiement terminé, vous pouvez revenir à l’onglet **connexions du point de terminaison privé** , auquel vous remarquerez que l’état de la connexion est **approuvé** .

### <a name="manual-approval"></a>Approbation manuelle

Pour une approbation manuelle, sous Ressource, sélectionnez la deuxième option, « Se connecter à une ressource Azure par ID de ressource ou alias ». Pour Sous-ressource cible, dans Approbation automatique, entrez « fhir ».

![Approbation manuelle](media/private-link/private-link-manual.png)

Une fois le déploiement terminé, vous pouvez revenir à l’onglet « Connexions de points de terminaison privés », sous lequel vous pouvez approuver, rejeter ou supprimer votre connexion.

![Options](media/private-link/private-link-options.png)

## <a name="test-private-endpoint"></a>Tester un point de terminaison privé

Pour vous assurer que votre serveur FHIR ne reçoit pas de trafic public après la désactivation de l’accès au réseau public, sélectionnez le point de terminaison/Metadata pour votre serveur sur votre ordinateur. Le message 403 Interdit devrait d’afficher. 


> [!NOTE]
> Il peut falloir jusqu’à 5 minutes après la mise à jour de l’indicateur d’accès au réseau public avant que le trafic public soit bloqué.

Pour vous assurer que votre point de terminaison privé peut envoyer le trafic vers votre serveur :

1. Créez une machine virtuelle (VM) qui est connectée au réseau virtuel et au sous-réseau sur lequel votre point de terminaison privé est configuré. Pour vous assurer que votre trafic à partir de la machine virtuelle utilise uniquement le réseau privé, désactivez le trafic Internet sortant à l’aide de la règle groupe de sécurité réseau (NSG).
2. RDP dans la machine virtuelle.
3. Accédez au point de terminaison/Metadata de votre serveur FHIR à partir de la machine virtuelle. Vous devez recevoir l’instruction de fonctionnalité en tant que réponse.

## <a name="manage-private-endpoint"></a>Point de terminaison privé managé

### <a name="view"></a>Affichage

Les points de terminaison privés et le contrôleur d’interface réseau (NIC) associé sont visibles dans Portail Azure du groupe de ressources dans lequel ils ont été créés.

![Affichage dans les ressources](media/private-link/private-link-view.png)

### <a name="delete"></a>Supprimer

Les points de terminaison privés peuvent uniquement être supprimés du Portail Azure à partir du panneau **vue d’ensemble** ou en sélectionnant l’option **supprimer** sous l’onglet **connexions de point de terminaison privé réseau** . Si vous sélectionnez **supprimer** , le point de terminaison privé et la carte réseau associée sont supprimés. Si vous supprimez tous les points de terminaison privés de la ressource FHIR et du réseau public, l’accès est désactivé et aucune demande ne le fera sur votre serveur FHIR.

![Supprimer un point de terminaison privé](media/private-link/private-link-delete.png)
