---
title: Migrer d’Azure Application Gateway et de pare-feu d’applications de la version v1 vers v2 Web
description: Cet article vous explique comment migrer d’Azure Application Gateway et des pare-feu d’applications Web à partir de v1 vers v2
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 6/5/2019
ms.author: victorh
ms.openlocfilehash: 44d5ce3e194c873a564039934f518cb3a0e142e3
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66497175"
---
# <a name="migrate-azure-application-gateway-and-web-application-firewall-from-v1-to-v2"></a>Migrer d’Azure Application Gateway et de pare-feu d’applications de la version v1 vers v2 Web

[Azure Application Gateway et des pare-feu d’applications Web (WAF) v2](application-gateway-autoscaling-zone-redundant.md) est maintenant disponible, offre des fonctionnalités supplémentaires telles que de redondance de zone de disponibilité et de mise à l’échelle. Toutefois, les passerelles v1 existantes ne sont pas automatiquement mis à niveau vers la version 2. Si vous souhaitez migrer à partir de v1 vers v2, suivez les étapes décrites dans cet article.

Il existe deux phases dans une migration :

1. Migration de la configuration
2. Migrer le trafic de client

Cet article couvre la migration de configuration. Migration de trafic client varie en fonction de votre environnement spécifique. Toutefois, certaines recommandations de haut niveau, générales [sont fournies](#migrate-client-traffic).

## <a name="migration-overview"></a>Présentation de la migration

Un script Azure PowerShell est disponible qui effectue les opérations suivantes :

* Crée une nouvelle passerelle Standard_v2 ou WAF_v2 dans un sous-réseau de réseau virtuel que vous spécifiez.
* Copie en toute transparence la configuration associée à la passerelle de Standard ou WAF v1 à la passerelle Standard_V2 ou WAF_V2 nouvellement créée.

### <a name="caveatslimitations"></a>Caveats\Limitations

* La nouvelle passerelle v2 a de nouveau des adresses IP publiques et privées. Il n’est pas possible de déplacer les adresses IP associées à la passerelle v1 existante en toute transparence vers la version 2. Toutefois, vous pouvez allouer une (non allouée) publique ou privée adresse IP existante vers la nouvelle passerelle v2.
* Vous devez fournir un espace d’adressage IP pour un autre sous-réseau au sein de votre réseau virtuel où se trouve votre passerelle v1. Le script ne peut pas créer la passerelle v2 dans tous les sous-réseaux existants qui ont déjà une passerelle v1. Toutefois, si le sous-réseau existant a déjà une passerelle v2, qui fonctionne toujours fourni qu'est suffisamment espace d’adressage IP.
* Pour migrer une configuration de SSL, vous devez spécifier tous les certificats SSL utilisés dans votre passerelle v1.
* Si vous avez le mode FIPS est activé pour votre passerelle V1, il ne sera pas migré vers votre nouvelle passerelle v2. Mode FIPS n’est pas pris en charge dans v2.
* v2 ne prend en charge IPv6, les passerelles de v1 IPv6 est activé ne sont pas migrées. Si vous exécutez le script, il ne peut pas terminer.
* Si la passerelle v1 a uniquement une adresse IP privée, le script crée une adresse IP publique et une adresse IP privée pour la nouvelle passerelle v2. les passerelles v2 ne prend en charge uniquement les adresses IP privées.

## <a name="download-the-script"></a>Téléchargez le script

Téléchargez le script de migration à partir de la [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureAppGWMigration).

## <a name="use-the-script"></a>Utilisez le script

Il existe deux options pour vous en fonction de vos préférences et la configuration de l’environnement PowerShell locale :

* Si vous n’avez pas les modules Azure Az installés, ou que vous êtes prêt à désinstaller les modules Azure Az, la meilleure option consiste à utiliser le `Install-Script` option permettant d’exécuter le script.
* Si vous devez conserver les modules Azure Az, la meilleure solution consiste à télécharger le script et l’exécuter directement.

Pour déterminer si vous avez les modules Azure Az installés, exécutez `Get-InstalledModule -Name az`. Si vous ne voyez pas un installé des modules de Az, vous pouvez ensuite utiliser le `Install-Script` (méthode).

### <a name="install-using-the-install-script-method"></a>Installer à l’aide de la méthode Install-Script

Pour utiliser cette option, vous devez pas les modules Azure Az installés sur votre ordinateur. S’ils sont installés, la commande suivante affiche une erreur. Vous pouvez désinstaller les modules Azure Az, ou utiliser l’autre option pour télécharger le script manuellement et l’exécuter.
  
Exécutez le script avec la commande suivante :

`Install-Script -Name AzureAppGWMigration`

Cette commande installe également les modules Az requis.  

### <a name="install-using-the-script-directly"></a>Installer en utilisant le script directement

Si vous avez certains modules Azure Az installés et que vous ne pouvez pas désinstaller les (ou que vous ne souhaitez pas les désinstaller), vous pouvez télécharger manuellement le script en utilisant la **téléchargement manuel** onglet dans le lien de téléchargement du script. Le script est téléchargé sous forme de fichier nupkg brutes. Pour installer le script à partir de ce fichier de package NuGet, consultez [le téléchargement de Package manuel](https://docs.microsoft.com/powershell/gallery/how-to/working-with-packages/manual-download).

Pour exécuter le script :

1. Utilisez `Connect-AzAccount` pour se connecter à Azure.

1. Utilisez `Import-Module Az` pour importer les modules Az.

1. Exécutez `Get-Help AzureAppGWMigration.ps1` pour examiner les paramètres requis :

   `AzureAppGwMigration.ps1
    -resourceId <v1 application gateway Resource ID>
    -subnetAddressRange <subnet space you want to use>
    -appgwName <string to use to append>
    -sslCertificates <comma-separated SSLCert objects as above>
    -trustedRootCertificates <comma-separated Trusted Root Cert objects as above>
    -privateIpAddress <private IP string>
    -publicIpResourceName <public IP name string>
    -validateMigration -enableAutoScale`

   Paramètres du script :
   * **ID de ressource : [chaîne] : Requis** -l’ID de ressource Azure pour votre v1 Standard existant ou d’une passerelle de v1 WAF. Pour rechercher cette valeur de chaîne, accédez au portail Azure, sélectionnez votre passerelle d’application ou de la ressource de pare-feu d’applications Web, puis cliquez sur le **propriétés** lien pour la passerelle. L’ID de ressource se trouve sur cette page.

     Vous pouvez également exécuter les commandes Azure PowerShell suivantes pour obtenir l’ID de ressource :

     ```azurepowershell
     $appgw = Get-AzApplicationGateway -Name <v1 gateway name> -ResourceGroupName <resource group Name> 
     $appgw.Id
     ```

   * **subnetAddressRange : [chaîne] :  Requis** -il s’agit d’un nouveau sous-réseau qui contient votre nouvelle passerelle v2 l’espace d’adressage IP que vous avez alloué (ou que vous souhaitez allouer). Cela doit être spécifié dans la notation CIDR. Exemple : 10.0.0.0/24. Vous n’avez pas besoin créer ce sous-réseau à l’avance. Le script crée pour vous si elle n’existe pas.
   * **appgwName : [chaîne] : Facultatif**. Il s’agit d’une chaîne que vous spécifiez pour l’utiliser comme nom pour la nouvelle passerelle Standard_v2 ou WAF_v2. Si ce paramètre n’est pas fourni, le nom de votre passerelle v1 existant sera être utilisé avec le suffixe *_v2* ajouté.
   * **sslCertificates : [PSApplicationGatewaySslCertificate] : Facultatif**.  Une liste séparée par des virgules d’objets PSApplicationGatewaySslCertificate que vous créez pour représenter les certificats SSL à partir de votre passerelle v1 doit être téléchargée vers la nouvelle passerelle v2. Pour chacun de vos certificats SSL configurés pour votre v1 Standard ou d’une passerelle de v1 WAF, vous pouvez créer un nouvel objet PSApplicationGatewaySslCertificate via la `New-AzApplicationGatewaySslCertificate` commande indiqué ici. Vous devez le chemin d’accès à votre fichier de certificat SSL et le mot de passe.

       Ce paramètre n’est facultatif si vous n’avez pas les écouteurs HTTPS configurées pour votre passerelle de v1 ou un pare-feu d’applications Web. Si vous avez au moins une configuration de l’écouteur HTTPS, vous devez spécifier ce paramètre.

      ```azurepowershell  
      $password = ConvertTo-SecureString <cert-password> -AsPlainText -Force
      $mySslCert1 = New-AzApplicationGatewaySslCertificate -Name "Cert01" `
        -CertificateFile <Cert-File-Path-1> `
        -Password $password 
      $mySslCert2 = New-AzApplicationGatewaySslCertificate -Name "Cert02" `
        -CertificateFile <Cert-File-Path-2> `
        -Password $password
      ```

      Vous pouvez transmettre `$mySslCert1, $mySslCert2` (séparées par des virgules) dans l’exemple précédent en tant que valeurs pour ce paramètre dans le script.
   * **trustedRootCertificates : [PSApplicationGatewayTrustedRootCertificate] : Facultatif**. Une liste séparée par des virgules d’objets PSApplicationGatewayTrustedRootCertificate que vous créez pour représenter le [certificats racine approuvés](ssl-overview.md) pour l’authentification de vos instances de serveur principal à partir de votre passerelle v2.  

      Pour créer une liste d’objets de PSApplicationGatewayTrustedRootCertificate, consultez [New-AzApplicationGatewayTrustedRootCertificate](https://docs.microsoft.com/powershell/module/Az.Network/New-AzApplicationGatewayTrustedRootCertificate?view=azps-2.1.0&viewFallbackFrom=azps-2.0.0).
   * **privateIpAddress : [chaîne] : Facultatif**. Une adresse IP privée spécifique que vous souhaitez associer à votre nouvelle passerelle v2.  Il doit s’agir à partir du même réseau virtuel que vous allouez pour votre nouvelle passerelle v2. Si ce n’est pas spécifié, le script alloue une adresse IP privée pour votre passerelle v2.
    * **publicIpResourceId : [chaîne] : Facultatif**. L’ID de ressource d’une ressource d’adresse IP publique dans votre abonnement que vous souhaitez allouer à la nouvelle passerelle v2. S’il n’est pas spécifié, le script alloue une nouvelle adresse IP publique dans le même groupe de ressources. Le nom est le nom de la passerelle v2 avec *- IP* ajouté.
   * **validateMigration : [basculer] : Facultatif**. Utilisez ce paramètre si vous souhaitez que le script pour effectuer des validations de comparaison une configuration de base après la création de la passerelle v2 et la copie de la configuration. Par défaut, aucune validation n’est effectuée.
   * **enableAutoScale : [basculer] : Facultatif**. Utilisez ce paramètre si vous souhaitez que le script pour activer la mise à l’échelle sur la nouvelle passerelle v2 après sa création. Par défaut, à l’échelle automatique est désactivée. Vous pouvez toujours l’activer manuellement par la suite la passerelle v2 nouvellement créé.

1. Exécutez le script en utilisant les paramètres appropriés.

    **Exemple**

   ```azurepowershell
   AzureAppGWMigration.ps1 `
      -resourceId /subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/applicationGateways/myv1appgateway `
      -subnetAddressRange 10.0.0.0/24 `
      -appgwname "MynewV2gw" `
      -sslCertificates $Certs `
      -trustedRootCertificates $trustedCert `
      -privateIpAddress "10.0.0.1" `
      -publicIpResourceName "MyPublicIP" `
      -validateMigration -enableAutoScale
   ```

## <a name="migrate-client-traffic"></a>Migrer le trafic client

Tout d’abord, vérifiez que le script a créé une nouvelle passerelle v2 avec la configuration exacte migré à partir de votre passerelle v1. Vous pouvez vérifier cela à partir du portail Azure.

En outre, envoyez une petite quantité de trafic via la passerelle v2 comme un test manuel.
  
Voici quelques scénarios où votre passerelle d’application actuel (Standard) peut recevoir le trafic des clients et nos recommandations pour chacune d’elles :

* **Une zone DNS personnalisée (par exemple, contoso.com) qui pointe vers l’adresse IP de serveur frontal (à l’aide d’un enregistrement A) associé à votre v1 Standard ou de la passerelle de v1 WAF**.

    Vous pouvez mettre à jour votre enregistrement DNS pour pointer vers l’étiquette frontend IP ou DNS associé à votre passerelle d’application Standard_v2. Selon la durée de vie configurée sur votre enregistrement DNS, il peut prendre un certain temps pour tout le trafic de client à migrer vers votre nouvelle passerelle v2.
* **Une zone DNS personnalisée (par exemple, contoso.com) qui pointe vers l’étiquette DNS (par exemple : *myappgw.eastus.cloudapp.azure.com* à l’aide d’un enregistrement CNAME) associé à votre passerelle v1**.

   Vous avez deux possibilités :

  * Si vous utilisez des adresses IP publiques sur votre passerelle d’application, vous pouvez le faire contrôlée, migration granulaire à l’aide d’un profil Traffic Manager incrémentielle acheminer le trafic (méthode de routage du trafic par pondération) vers la nouvelle passerelle v2.

    Procéder en ajoutant les étiquettes DNS de la v1 et v2 passerelles d’application pour le [profil Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md#weighted-traffic-routing-method)et nommons votre enregistrement DNS personnalisé (par exemple, www.contoso.com) vers le domaine Traffic Manager (par exemple, Contoso.trafficmanager.NET).
  * Ou bien, vous pouvez mettre à jour votre enregistrement DNS de domaine personnalisé pour pointer vers le nom DNS de la nouvelle passerelle d’application v2. Selon la durée de vie configurée sur votre enregistrement DNS, il peut prendre un certain temps pour tout le trafic de client à migrer vers votre nouvelle passerelle v2.
* **Vos clients connectent sur le serveur frontal d’adresse IP de votre passerelle d’application**.

   Mettre à jour vos clients pour utiliser les adresses IP associées à la passerelle d’application v2 nouvellement créé. Nous vous recommandons de ne pas utiliser directement des adresses IP. Envisagez d’utiliser l’étiquette du nom DNS (par exemple, yourgateway.eastus.cloudapp.azure.com) associé à votre passerelle d’application que vous pouvez CNAME à votre propre zone DNS personnalisé (par exemple, contoso.com).

## <a name="common-questions"></a>Questions courantes

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Existe-t-il des restrictions avec le script Azure PowerShell pour migrer la configuration de la version v1 vers v2 ?

Oui. Consultez [mises en garde/Limitations](#caveatslimitations).

### <a name="is-this-article-and-the-azure-powershell-script-applicable-for-application-gateway-waf-product-as-well"></a>Cet article et le script Azure PowerShell est applicable pour le produit de l’Application Gateway WAF ainsi ? 

Oui.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-v1-gateway-to-the-newly-created-v2-gateway"></a>Le script Azure PowerShell également basculer le trafic à partir de ma passerelle v1 à la passerelle nouvellement créé v2 ?

Non. Le script Azure PowerShell migre uniquement la configuration. Migration du trafic réelle est votre responsabilité et dans votre contrôle.

### <a name="is-the-new-v2-gateway-created-by-the-azure-powershell-script-sized-appropriately-to-handle-all-of-the-traffic-that-is-currently-served-by-my-v1-gateway"></a>La nouvelle passerelle v2 créée par le script Azure PowerShell est dimensionnée correctement pour traiter tout le trafic qui est actuellement pris en charge par ma passerelle v1 ?

Le script Azure PowerShell crée une nouvelle passerelle v2 avec une taille appropriée pour gérer le trafic sur votre passerelle V1 existante. À l’échelle automatique est désactivée par défaut, mais vous pouvez activer la mise à l’échelle lorsque vous exécutez le script.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>J’ai rencontré des problèmes à l’aide de ce script. Comment puis-je obtenir une aide ?
  
Vous pouvez envoyer un e-mail à appgwmigrationsup@microsoft.com, ouvrez une demande de support avec le Support Azure ou les deux.

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur Application Gateway v2](application-gateway-autoscaling-zone-redundant.md)
