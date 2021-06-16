---
title: DNS inversé pour les services Azure - Azure DNS
description: Grâce à ce parcours d’apprentissage, commencez à configurer des recherches DNS inversé pour les services hébergés dans Azure.
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/29/2021
ms.author: rohink
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e9f952f0bb534ddad767280991ab0829ff9fa1b8
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110696944"
---
# <a name="configure-reverse-dns-for-services-hosted-in-azure"></a>Configurer des DNS inversés dans les services hébergés par Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Cet article explique comment configurer des recherches DNS inversées dans les services hébergés par Azure.

Les services Azure utilisent des adresses IP assignées par Azure et détenues par Microsoft. Ces enregistrements DNS inversés (enregistrements PTR) doivent être créés dans les zones de recherches DNS inversées correspondantes, détenues par Microsoft.

Ce scénario diffère de la capacité à [héberger les zones de recherche DNS inversées](dns-reverse-dns-hosting.md) de vos plages d’adresses IP assignées dans le service Azure DNS. Ici, les plages d’adresses IP représentées par la zone de recherche inversée doivent être assignées à votre organisation. C’est généralement le rôle de votre FAI.

Avant de lire cet article, vous devez vous familiariser avec le [DNS inversé dans Azure DNS](dns-reverse-dns-overview.md).

Dans Azure DNS, les ressources de calcul telles que les machines virtuelles, les groupes de machines virtuelles identiques et les clusters Service Fabric ont des adresses IP publiques. Les recherche DNS inversées sont configurées à l’aide de la propriété 'ReverseFqdn' de l’adresse IP publique.

Actuellement, les DNS inversés ne sont pas pris en charge dans Azure App Service et Application Gateway.

## <a name="validation-of-reverse-dns-records"></a>Validation des enregistrements DNS inversés

Un tiers ne doit pas être en mesure de créer d’enregistrements DNS inversés pour le mappage du service Azure dans vos domaines DNS. C’est pourquoi Azure vous permet uniquement de créer un enregistrement DNS inversé si le nom de domaine est le même ou s’il est résolu en une adresse IP publique dans le même abonnement. Cette restriction s’applique également au service cloud.

Cette validation n’intervient que lorsqu’un enregistrement DNS inversé est défini ou modifié. Aucune revalidation périodique n’est effectuée.

Par exemple, supposons que la ressource d’adresse IP publique possède le nom DNS `contosoapp1.northus.cloudapp.azure.com` et l’adresse IP `23.96.52.53`. Le nom de domaine complet inversé de l’adresse IP publique peut être spécifié comme suit :

* Nom DNS de l’adresse IP publique : `contosoapp1.northus.cloudapp.azure.com`.
* Nom DNS d’une autre PublicIpAddress dans le même abonnement, par exemple : `contosoapp2.westus.cloudapp.azure.com`.
* Nom DNS personnel, par exemple : `app1.contoso.com` . Si le nom est configuré *en premier* en tant qu’enregistrement CNAME pointant vers `contosoapp1.northus.cloudapp.azure.com`. Le nom peut également pointer vers une autre adresse IP publique dans le même abonnement.
* Nom DNS personnel, par exemple : `app1.contoso.com` . Si le nom est configuré *en premier* en tant qu’enregistrement A pointant vers l’adresse IP 23.96.52.53. Le nom peut également pointer vers une autre adresse IP dans le même abonnement.

Les mêmes contraintes s’appliquent aux DNS inversés dans les services Cloud.

## <a name="reverse-dns-for-public-ip-address-resources"></a>DNS inversés pour les ressources d’adresse IP publique

Cette section détaille la marche à suivre pour configurer des DNS inversés pour des ressources d’adresse IP publique dans le modèle de déploiement du Gestionnaire de ressources. Pour ce faire, vous pouvez utiliser Azure PowerShell, Azure Classic CLI ou Azure CLI. Actuellement, la configuration de DNS inversés pour une ressource d’adresse IP publique n’est pas prise en charge dans le portail Azure.

Actuellement, Azure ne prend en charge les DNS inversés que pour les ressources d’adresse IPv4 publique.

### <a name="add-reverse-dns-to-an-existing-publicipaddresses"></a>Ajouter un DNS inversé à des adresses IP publiques existantes

#### <a name="azure-powershell"></a>Azure PowerShell

Pour mettre à jour un DNS inversé à des adresses IP publiques existantes :

```azurepowershell-interactive
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

Pour ajouter un DNS inversé à une adresse IP publique existante qui ne comporte pas encore de nom DNS, vous devez également spécifier un nom DNS :

```azurepowershell-interactive
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings = New-Object -TypeName "Microsoft.Azure.Commands.Network.Models.PSPublicIpAddressDnsSettings"
$pip.DnsSettings.DomainNameLabel = "contosoapp1"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-classic-cli"></a>Azure Classic CLI

Pour ajouter un DNS inversé à des adresses IP publiques existantes :

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -f contosoapp1.westus.cloudapp.azure.com.
```

Pour ajouter un DNS inversé à une adresse IP publique existante qui ne comporte pas encore de nom DNS, vous devez également spécifier un nom DNS :

```azurecli-interactive
azure network public-ip set -n PublicIp -g MyResourceGroup -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.
```

#### <a name="azure-cli"></a>Azure CLI

Pour ajouter un DNS inversé à des adresses IP publiques existantes :

```azurecli-interacgive
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com.
```

Pour ajouter un DNS inversé à une adresse IP publique existante qui ne comporte pas encore de nom DNS, vous devez également spécifier un nom DNS :

```azurecli-interactive
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com --dns-name contosoapp1
```

### <a name="create-a-public-ip-address-with-reverse-dns"></a>Création d’une adresse IP publique avec un DNS inversé

Pour créer une nouvelle adresse IP publique avec la propriété DNS inversée spécifiée :

#### <a name="azure-powershell"></a>Azure PowerShell

```azurepowershell-interactive
New-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup" -Location "WestUS" -AllocationMethod Dynamic -DomainNameLabel "contosoapp2" -ReverseFqdn "contosoapp2.westus.cloudapp.azure.com."
```

#### <a name="azure-classic-cli"></a>Azure Classic CLI

```azurecli
azure network public-ip create -n PublicIp -g MyResourceGroup -l westus -d contosoapp3 -f contosoapp3.westus.cloudapp.azure.com.
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az network public-ip create --name PublicIp --resource-group MyResourceGroup --location westcentralus --dns-name contosoapp1 --reverse-fqdn contosoapp1.westcentralus.cloudapp.azure.com
```

### <a name="view-reverse-dns-for-an-existing-publicipaddress"></a>Afficher DNS inversé pour des adresses IP publiques existantes

Pour afficher la valeur définie d’une adresse IP publique existante :

#### <a name="azure-powershell"></a>Azure PowerShell

```azurepowershell-interactive
Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
```

#### <a name="azure-classic-cli"></a>Azure Classic CLI

```azurecli
azure network public-ip show -n PublicIp -g MyResourceGroup
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az network public-ip show --name PublicIp --resource-group MyResourceGroup
```

### <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>Suppression d’un enregistrement DNS inversé d’une adresse IP publique existante

Pour supprimer une propriété DNS inversée d’une adresse IP publique existante :

#### <a name="azure-powershell"></a>Azure PowerShell

```azurepowershell-interactive
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = ""
Set-AzPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-classic-cli"></a>Azure Classic CLI

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup –f ""
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn ""
```

## <a name="configure-reverse-dns-for-cloud-services"></a>Configurer un DNS inversé pour les services Cloud

Cette section détaille la marche à suivre pour configurer un DNS inversé pour les services Cloud dans le modèle de déploiement classique, à l’aide d’Azure PowerShell. La configuration de DNS inversé pour les services cloud n’est pas possible par le biais du portail Azure, d’Azure Classic CLI ou d’Azure CLI.

### <a name="add-reverse-dns-to-existing-cloud-services"></a>Ajout d’un DNS inversé aux services cloud existants

Pour ajouter un enregistrement DNS inversé à un service cloud existant :

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="create-a-cloud-service-with-reverse-dns"></a>Création d’un service cloud avec un DNS inversé

Pour créer un nouveau service cloud avec la propriété DNS inversée spécifiée :

```powershell
New-AzureService –ServiceName "contosoapp1" –Location "West US" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="view-reverse-dns-for-existing-cloud-services"></a>Affichage d’un DNS inversé pour les services cloud existants

Pour afficher la propriété DNS inversée pour un service cloud existant :

```powershell
Get-AzureService "contosoapp1"
```

### <a name="remove-reverse-dns-from-existing-cloud-services"></a>Suppression d’un DNS inversé des services cloud existants

Pour supprimer la propriété DNS inversée d’un service cloud existant :

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn ""
```

## <a name="faq"></a>Questions fréquentes (FAQ)

### <a name="how-much-do-reverse-dns-records-cost"></a>Combien coûtent les enregistrements DNS inversés ?

Ils sont gratuits. Les enregistrements DNS inversés ou les requêtes n’entraînent aucun frais supplémentaire.

### <a name="will-my-reverse-dns-records-resolve-from-the-internet"></a>Mes enregistrements DNS inversés seront-ils résolus à partir d'Internet ?

Oui. Dès que vous avez défini la propriété DNS inversée pour votre service Azure, Azure gère toutes les délégations DNS et les zones DNS requises pour s’assurer de sa résolution pour tous les utilisateurs Internet.

### <a name="are-default-reverse-dns-records-created-for-my-azure-services"></a>Des enregistrements DNS inversés par défaut sont-ils créés pour mes services Azure ?

Non. Le DNS inversé est une fonctionnalité optionnelle. Aucun enregistrement DNS inversé par défaut n’est créé si vous choisissez de ne pas les configurer.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>Quel est le format du nom de domaine complet (FQDN) ?

Les noms de domaine complets sont spécifiés dans l’ordre chronologique et doivent se terminer par un point (par exemple, « app1.contoso.com. »).

### <a name="what-happens-if-the-validation-check-for-the-reverse-dns-ive-specified-fails"></a>Que se passe-t-il si le test de validation pour le DNS inversé que j’ai spécifié échoue ?

Lorsque le test de validation pour le DNS inversé échoue, l’opération de configuration de l’enregistrement DNS inversé échoue également. Corrigez la valeur DNS inversée, puis réessayez.

### <a name="can-i-configure-reverse-dns-for-azure-app-service"></a>Puis-je configurer un DNS inversé pour Azure App Service ?

Non. Le DNS inversé n’est pas pris en charge dans Azure App Service.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-azure-service"></a>Puis-je configurer plusieurs enregistrements DNS inversés pour mon service Azure ?

Non. Azure ne prend en charge qu’un seul enregistrement DNS inversé pour chaque service cloud Azure ou adresse IP publique.

### <a name="can-i-configure-reverse-dns-for-ipv6-publicipaddress-resources"></a>Puis-je configurer un DNS inversé pour des ressources PublicIpAddress IPv6 ?

Non. Actuellement, Azure ne prend en charge le DNS inversé que pour les ressources PublicIpAddress IPv4.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>Puis-je envoyer des courriers électroniques à des domaines externes à partir d’Azure Compute Services ?

La possibilité technique d’envoyer un e-mail directement à partir d’un déploiement Azure technique varie selon le type d’abonnement. Quel que soit le type d’abonnement, Microsoft recommande d’utiliser des services de relais de courrier de confiance pour l’envoi de courrier sortant. Pour plus d’informations, consultez [Sécurité d’Azure renforcée pour l’envoi de courrier - Mise à jour de novembre 2017](../virtual-network/troubleshoot-outbound-smtp-connectivity.md).

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur le DNS inversé, consultez [Recherche DNS inversée sur Wikipedia](https://en.wikipedia.org/wiki/Reverse_DNS_lookup).
* Découvrez comment [héberger la zone de recherche inversée pour la plage d’adresses IP fournie par votre fournisseur de services Internet dans Azure DNS](dns-reverse-dns-for-azure-services.md).
