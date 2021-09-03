---
title: Créer un environnement App Service Environment
description: Découvrez comment créer un environnement App Service Environment.
author: ccompy
ms.assetid: 7690d846-8da3-4692-8647-0bf5adfd862a
ms.topic: article
ms.date: 07/06/2021
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 45d2c817f579cd183337a42e252dd3e606eafefa
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113433196"
---
# <a name="create-an-app-service-environment"></a>Créer un environnement App Service Environment
> [!NOTE]
> Cet article concerne la fonctionnalité App Service Environment v3 qui est utilisée avec les plans App Service Isolé v2.
> 


L’environnement [ASE (App Service Environment)][Intro] est un déploiement monolocataire d’App Service, qui s’injecte dans votre réseau virtuel Azure. Le déploiement d'un environnement ASE requiert l'utilisation d'un sous-réseau. Ce sous-réseau ne peut pas être utilisé à d’autres fins que pour l’environnement ASE. 

## <a name="before-you-create-your-ase"></a>Avant de créer votre ASE

Une fois votre environnement ASE créé, vous ne pouvez plus modifier les éléments suivants :

- Emplacement
- Abonnement
- Resource group
- Réseau virtuel Azure utilisé
- Sous-réseaux utilisés
- Taille du sous-réseau
- Nom de votre environnement ASE

Le sous-réseau doit être suffisamment grand pour contenir la taille maximale à laquelle votre environnement ASE sera mis à l'échelle. Choisissez un sous-réseau suffisamment grand pour prendre en charge vos besoins maximum de mise à l’échelle, car il ne peut pas être modifié après la création. La taille recommandée est /24 avec 256 adresses.

## <a name="creating-an-ase-in-the-portal"></a>Création d’un environnement ASE dans le portail

1. Pour créer un environnement ASE, recherchez **App Service Environment v3** sur la Place de marché.  
2. Paramètres de base :  Sélectionnez l’abonnement, sélectionnez ou créez le groupe de ressources, puis entrez le nom de votre environnement ASE.  Sélectionnez le type d'adresse IP virtuelle. Si vous sélectionnez Interne, votre adresse ASE entrante sera une adresse de votre sous-réseau ASE. Si vous sélectionnez Externe, votre adresse ASE entrante sera une adresse publique accessible sur Internet. Le nom de l’environnement ASE sera également utilisé pour le suffixe de domaine de votre environnement ASE. Si le nom de votre environnement ASE est *contoso* et que vous disposez d'une adresse IP virtuelle interne, le suffixe du domaine sera *contoso.appserviceenvironment.net*. Si le nom de votre environnement ASE est *contoso* et que vous disposez d'une adresse IP virtuelle externe, le suffixe du domaine sera *contoso.p.azurewebsites.net*. 
![App Service Environment - Créer un onglet de base](./media/creation/creation-basics.png)
3. Hébergement : sélectionnez *Activé* ou *Désactivé* pour le déploiement du groupe hôte. Le déploiement du groupe hôte est utilisé pour sélectionner un matériel dédié. Si vous sélectionnez Activé, votre environnement ASE sera déployé sur du matériel dédié. Lorsque vous optez pour un déploiement sur du matériel dédié, vous êtes facturé pour l'intégralité de l'hôte dédié lors de la création de l'environnement ASE, puis un prix réduit est appliqué pour vos instances de plan App Service. 
![App Service Environment - Sélections d’hébergement](./media/creation/creation-hosting.png)
4. Mise en réseau : sélectionnez ou créez votre Réseau virtuel, sélectionnez ou créez votre sous-réseau. Si vous créez un environnement ASE avec adresse IP virtuelle interne, vous aurez la possibilité de configurer des zones privées Azure DNS pour que le suffixe de votre domaine pointe vers cet environnement. Pour en savoir plus sur la configuration manuelle de DNS, consultez la section DNS sous [Utiliser une instance d'App Service Environment][UsingASE].
![App Service Environment - Sélections réseau](./media/creation/creation-networking.png)
5. Vérifier et créer : Vérifiez que votre configuration est correcte, puis sélectionnez Créer. Comptez près de deux heures pour la création de votre environnement ASE. 

Une fois la création de l’environnement ASE terminée, vous pouvez le sélectionner comme emplacement lors de la création de vos applications. Pour en savoir plus sur la création d'applications dans votre nouvel environnement ASE ou sur la gestion de celui-ci, consultez [Utiliser une instance d'App Service Environment][UsingASE]

## <a name="dedicated-hosts"></a>Hôtes dédiés

L’environnement ASE est normalement déployé sur des machines virtuelles provisionnées sur un hyperviseur multilocataire. Si vous devez déployer sur des systèmes dédiés, notamment le matériel, vous pouvez provisionner votre environnement ASE sur des hôtes dédiés. Les hôtes dédiés sont fournis par paire pour assurer la redondance. Les déploiements ASE basés sur un hôte dédié ne sont pas facturés au tarif normal. Des frais sont facturés pour l’hôte dédié, puis pour chaque instance de plan App Service. Les déploiements sur des groupes hôtes ne sont pas redondants d'une zone à l'autre. Pour effectuer un déploiement sur des hôtes dédiés, sélectionnez l'option **Activer** correspondant au déploiement du groupe hôte sous l'onglet Hébergement

<!--Links-->
[Intro]: ./overview.md
[MakeASE]: ./creation.md
[ASENetwork]: ./networking.md
[UsingASE]: ./using.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/alerts/alerts-log.md
