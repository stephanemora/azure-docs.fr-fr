---
title: Sécurité du réseau dans Azure Data Lake Storage Gen1 | Microsoft Docs
description: Comprendre l’intégration au pare-feu IP et au réseau virtuel dans Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/09/2018
ms.author: elsung
ms.openlocfilehash: 0da5962bc0b48a387ee82a1db36099682e14bca3
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/12/2018
ms.locfileid: "49168121"
---
# <a name="virtual-network-integration-for-azure-data-lake-storage-gen1---preview"></a>Intégration de réseau virtuel dans Azure Data Lake Storage Gen1 : Préversion

Présentation de l’intégration du réseau virtuel dans Azure Data Lake Storage Gen1 (en préversion). L’intégration au réseau virtuel permet d’éviter tout accès non autorisé à vos comptes Azure Data Lake Storage Gen1 en n’autorisant leur accès qu’à vos réseaux et sous-réseaux virtuels spécifiques. Vous pouvez maintenant configurer votre compte ADLS Gen1 pour n’accepter que le trafic provenant des réseaux et sous-réseaux virtuels désignés, et bloquer tous les autres accès. Votre compte ADLS est ainsi protégé contre les menaces externes.

L’intégration au réseau virtuel dans ADLS Gen1 fait appel à la sécurité des points de terminaison fournisseurs de réseau virtuel entre votre réseau virtuel et le service Azure Active Directory afin de générer des revendications de sécurité supplémentaires dans le jeton d’accès. Ces revendications permettent ensuite d’authentifier votre réseau virtuel à votre compte ADLS Gen1 et d’y accéder.

> [!NOTE]
> Il s’agit d’une technologie en préversion. Nous ne recommandons pas de l’utiliser dans des environnements de production.
>
> L’utilisation de ces fonctionnalités n’entraîne aucuns frais supplémentaires. Votre compte sera facturé au tarif standard d’ADLS Gen1 ([tarification](https://azure.microsoft.com/pricing/details/data-lake-store/?cdn=disable)) et de tous les services Azure que vous utilisez ([tarification](https://azure.microsoft.com/pricing/#product-picker)).

## <a name="scenarios-for-vnet-integration-for-adls-gen1"></a>Scénarios d’intégration au réseau virtuel dans ADLS Gen1

L’intégration au réseau virtuel dans ADLS Gen1 permet de restreindre l’accès à votre compte ADLS Gen1 depuis les réseaux et sous-réseaux virtuels désignés.  Les autres réseaux virtuels et machines virtuelles dans Azure ne peuvent pas accéder à votre compte une fois que celui-ci est bloqué sur le sous-réseau du réseau virtuel spécifié.  Sur le plan fonctionnel, l’intégration au réseau virtuel dans ADLS Gen1 permet le même scénario que les [points de terminaison fournisseurs de réseau virtuel](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview).  Quelques différences importantes sont détaillées dans les sections ci-dessous. 

![Diagramme de scénario de l’intégration au réseau virtuel dans ADLS Gen1](media/data-lake-store-network-security/scenario-diagram.png)

> [!NOTE]
> Les règles de pare-feu IP existantes peuvent être utilisées en plus des règles de réseau virtuel afin d’autoriser également l’accès depuis les réseaux locaux. 

## <a name="optimal-routing-with-adls-gen1-vnet-integration"></a>Routage optimal grâce à l’intégration au réseau virtuel dans ADLS Gen1

L’[optimisation des itinéraires ](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview#key-benefits) depuis votre réseau virtuel constitue l’un des principaux avantages des points de terminaison fournisseurs de réseau virtuel.  Pour effectuer la même optimisation d’itinéraire vers les comptes ADLS Gen1, utilisez les [Itinéraires définis par l’utilisateur](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined) suivants de votre réseau virtuel vers votre compte ADLS Gen1 :

- **Adresse IP publique ADLS** : utilisez l’adresse IP publique de vos comptes ADLS Gen1 cibles.  Vous pouvez identifier les adresses IP de votre compte ADLS Gen1 en [résolvant les noms DNS](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-connectivity-from-vnets#enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity) de vos comptes.  Créez une entrée différente pour chaque adresse.

```azurecli
# Create a Route table for your resource group
az network route-table create --resource-group $RgName --name $RouteTableName

# Create Route Table Rules for ADLS Public IP Addresses
# There will be one rule per ADLS Public IP Addresses 
az network route-table route create --name toADLSregion1 --resource-group $RgName --route-table-name $RouteTableName --address-prefix <ADLS Public IP Address> --next-hop-type Internet

# Update the VNet and apply the newly created Route Table to it
az network vnet subnet update --vnet-name $VnetName --name $SubnetName --resource-group $RgName --route-table $RouteTableName
```

## <a name="data-exfiltration-from-the-customer-vnet"></a>Exfiltration des données du réseau virtuel du client

En plus de sécuriser les comptes ADLS concernant l’accès à partir des réseaux virtuels, vous souhaitez peut-être aussi vérifier l’absence d’exfiltration vers un compte non autorisé.

Nous vous recommandons d’utiliser un pare-feu dans votre réseau virtuel afin de filtrer le trafic sortant en fonction de l’URL du compte de destination et de n’autoriser l’accès qu’aux comptes ADLS Gen1 autorisés.

Voici quelques options disponibles :
- [Pare-feu Azure](https://docs.microsoft.com/azure/firewall/overview) : Vous pouvez [déployer et configurer un Pare-feu Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) pour votre réseau virtuel et sécuriser le trafic ADLS sortant en n’autorisant que l’URL connue et autorisée du compte.
- Pare-feu d’[appliance virtuelle de réseau](https://azure.microsoft.com/solutions/network-appliances/) : Si votre administrateur autorise uniquement l’utilisation de certains fournisseurs de pare-feu commerciaux, vous pouvez utiliser une solution de pare-feu NVA disponible sur la Place de marché Microsoft Azure pour assurer la même fonction.

> [!NOTE]
> Le fait d’utiliser des pare-feu dans le chemin des données ajoute un saut supplémentaire dans le chemin de données et peut influer sur les performances du réseau dans les échanges de données de bout en bout, notamment le débit disponible et la latence des connexions. 

## <a name="limitations"></a>Limites
1.  Les clusters HDInsight doivent être créés peu de temps après avoir été ajoutés à la préversion.  Les clusters créés avant la prise en charge de l’intégration au réseau virtuel dans ADLS Gen1 doivent être créés à nouveau pour que cette nouvelle fonctionnalité soit prise en charge. 
2.  Lors de la création d’un nouveau cluster HDInsight, la sélection d’un compte ADLS Gen1 dont l’intégration au réseau virtuel est activée fait échouer le processus. Vous devez d’abord désactiver la règle de réseau virtuel ou vous pouvez **Autoriser l’accès à tous les réseaux et services** dans le panneau **Pare-feu et réseaux virtuels** du compte ADLS.  Pour en savoir plus, consultez la section [Exceptions](##Exceptions).
3.  La préversion de l’intégration au réseau virtuel ADLS Gen1 ne fonctionne pas avec les [identités managées des ressources Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).  
4.  Les données des fichiers/dossiers de votre compte ADLS Gen1 activé sur le réseau virtuel ne sont pas accessibles depuis le portail.  Il s’agit notamment de l’accès à partir d’une machine virtuelle située sur le réseau virtuel et d’activités comme l’utilisation de l’Explorateur de données.  Les activités de gestion du compte continuent de fonctionner.  Les données des fichiers/dossiers de votre compte ADLS compatible avec le réseau virtuel sont accessibles par le biais de toutes les ressources autres que le portail : accès au Kit de développement logiciel (SDK), scripts PowerShell, autres services Azure (lorsqu’ils ne proviennent pas du portail), etc. 

## <a name="configuration"></a>Configuration

### <a name="step1-configure-your-vnet-to-use-aad-service-endpoint"></a>Étape 1 : Configurer votre réseau virtuel pour utiliser le point de terminaison du service AAD
1.  Accédez au Portail Azure et connectez-vous à votre compte. 
2.  [Créer un réseau virtuel ](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)dans votre abonnement ou accédez à un réseau virtuel existant.  Le réseau virtuel doit se trouver dans la même région que le compte ADLS Gen 1. 
3.  Dans le panneau Réseau virtuel, sélectionnez **Points de terminaison fournisseurs**. 
4.  Cliquez sur **Ajouter** pour ajouter un nouveau point de terminaison fournisseur.
![Ajout d’un point de terminaison fournisseur de réseau virtuel](media/data-lake-store-network-security/config-vnet-1.png)
5.  Choisissez **Microsoft.AzureActiveDirectory** comme service du point de terminaison.
![Sélection du point de terminaison fournisseur Microsoft.AzureActiveDirectory](media/data-lake-store-network-security/config-vnet-2.png)
6.  Choisissez les sous-réseaux pour lesquels vous souhaitez autoriser la connectivité, puis cliquez sur **Ajouter**.
![Sélectionner le sous-réseau](media/data-lake-store-network-security/config-vnet-3.png)
7.  L’ajout du point de terminaison fournisseur peut prendre jusqu’à 15 minutes. Il s’affichera dans la liste après avoir été ajouté. Vérifiez qu’il s’affiche et que tous les détails correspondent à la configuration. 
![Ajout réussi du point de terminaison fournisseur](media/data-lake-store-network-security/config-vnet-4.png)

### <a name="step-2-set-up-the-allowed-vnetsubnet-for-your-adls-gen1-account"></a>Étape 2 : Configurer le réseau virtuel/sous-réseau autorisé pour votre compte ADLS Gen1
1.  Après avoir configuré votre réseau virtuel, [créez un nouveau compte Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account) dans votre abonnement ou accédez à un compte ADLS Gen1 existant. Le compte ADLS Gen1 doit se trouver dans la même région que le réseau virtuel. 
2.  Choisissez **Pare-feu et réseaux virtuels**.

  > [!NOTE]
  > Si **Pare-feu et réseaux virtuels** ne s’affiche pas dans les paramètres, déconnectez-vous du portail. Fermez le navigateur. Effacez le cache du navigateur. Redémarrez la machine et essayez à nouveau.

  ![Ajout d’une règle de réseau virtuel à votre compte ADLS](media/data-lake-store-network-security/config-adls-1.png)
3.  Sélectionnez **Réseaux sélectionnés**. 
4.  Cliquez sur **Ajouter un réseau virtuel existant**.
  ![Ajout d’un réseau virtuel existant](media/data-lake-store-network-security/config-adls-2.png)
5.  Choisissez les réseaux virtuels et les sous-réseaux à autoriser pour la connectivité, puis cliquez sur **Ajouter**.
  ![Choix du réseau virtuel et des sous-réseaux](media/data-lake-store-network-security/config-adls-3.png)
6.  Assurez-vous que les réseaux virtuels et les sous-réseaux apparaissent correctement dans la liste, puis sélectionnez **Enregistrer**.
  ![Enregistrement de la nouvelle règle](media/data-lake-store-network-security/config-adls-4.png)

  > [!NOTE]
  > Cela peut prendre jusqu’à 5 minutes pour que les réglages prennent effet après l’enregistrement.

7.  [Facultatif] En plus des réseaux virtuels et des sous-réseaux, vous pouvez autoriser la connectivité à partir d’adresses IP spécifiques dans la section **Pare-feu** sur la même page. 

## <a name="exceptions"></a>Exceptions
Il existe deux cases à cocher dans la zone Exceptions du panneau **Pare-feu et réseaux virtuels** pour activer la connectivité depuis un ensemble de services et de machines virtuelles sur Azure.
![Exceptions de pare-feu et de réseau virtuel](media/data-lake-store-network-security/firewall-exceptions.png)
- L’option **Autoriser tous les services Azure à accéder à ce compte Data Lake Storage Gen1** permet à tous les services Azure tels qu’Azure Data Factory, Event Hubs, toutes les machines virtuelles Azure, etc. de communiquer avec votre compte ADLS.

- L’option **Autoriser Azure Data Lake Analytics à accéder à ce compte Data Lake Storage Gen1** permet au service Azure Data Lake Analytics de se connecter à ce compte ADLS. 

Nous vous recommandons de désactiver ces exceptions et de ne les activer que si vous avez besoin de vous connecter à ces autres services en dehors de votre réseau virtuel.
