---
title: Créer une passerelle d’application Azure redondante dans une zone
description: Découvrez comment créer une passerelle d’application redondante dans une zone ne nécessitant pas de passerelle séparée dans chaque zone.
services: application-gateway
author: amsriva
manager: jpconnock
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 5/8/2018
ms.author: victorh
ms.openlocfilehash: 03bc58db813534fdd17c9567f6425ab7357ed901
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937702"
---
# <a name="create-a-zone-redundant-azure-application-gateway---private-preview"></a>Créer une passerelle d’application Azure redondante dans une zone - préversion privée

La plateforme de passerelle d’application redondante dans une zone est une nouvelle référence SKU qui offre de nombreuses améliorations par rapport à la référence SKU de passerelle d’application existante, notamment :
- **Résilience de la zone** : le déploiement d’une passerelle d’application peut désormais couvrir plusieurs zones de disponibilité, ce qui évite d’avoir à configurer et épingler plusieurs instances de passerelle d’application dans chaque zone à l’aide d’un gestionnaire de trafic. Vous pouvez choisir une ou plusieurs zones où les instances de passerelle d’application sont déployées, assurant ainsi une résilience en cas d’échec de la zone. Le pool principal pour les applications peut être distribué de la même façon entre les différentes zones de disponibilité.
- **Amélioration des performances**
- **Adresse IP virtuelle statique** : l’adresse IP virtuelle de la passerelle d’application prend désormais exclusivement en charge le type d’adresse IP virtuelle statique. Cela garantit que l’adresse IP virtuelle associée à la passerelle d’application ne change pas après un redémarrage.
- **Intégration du coffre de clés pour les certificats SSL du client**
- **Déploiement et mise à jour plus rapides**

> [!NOTE]
> La passerelle d’application redondante dans une zone est actuellement en préversion privée. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="supported-regions"></a>Régions prises en charge

Les passerelles d’application redondantes dans une zone sont actuellement prises en charge dans la région Est des États-Unis 2. D’autres régions seront bientôt ajoutées.

## <a name="topologies"></a>Topologies

Avec la version actuelle, vous n’avez plus besoin de créer des passerelles d’application épinglées dans une zone pour obtenir une redondance de zone. Le même déploiement de passerelle d’application peut désormais couvrir plusieurs zones.

Au moins trois instances sont requises pour s’assurer qu’elles sont réparties sur toutes les zones. La passerelle d’application distribue les instances entre les zones au fur et à mesure que d’autres instances sont ajoutées.

Les anciennes topologies de redondance dans une zone ressemblaient à ce qui suit :

![Ancienne topologie de redondance](media/create-zone-redundant/old-redundant.png)

La nouvelle topologie de redondance dans une zone ressemble au schéma suivant :

![Nouvelle topologie de redondance dans une zone](media/create-zone-redundant/new-redundant.png)

## <a name="deployment"></a>Déploiement

### <a name="prerequisites"></a>Prérequis


Actuellement, la redondance dans une zone est uniquement disponible en préversion privée. Pour être mis en liste verte, vous devez envoyer un e-mail à appgwxzone@microsoft.com. Après avoir reçu une confirmation, vous pouvez passer aux étapes suivantes. Fournissez les informations suivantes dans votre e-mail pour être mis en liste verte :

- Identifiant d’abonnement
- Nom de la région
- Nombre approximatif de passerelles d’application requises

### <a name="resource-manager-template-deployment"></a>Déploiement de modèle Resource Manager

1. Assurez-vous que l’abonnement que vous utilisez figure dans la liste verte, comme indiqué précédemment.
2. Après avoir reçu une confirmation, connectez-vous au compte Azure et, s’il existe plusieurs abonnements, sélectionnez l’abonnement approprié. Assurez-vous de sélectionner l’abonnement qui figure dans la liste verte.

   ```PowerShell
   Login-AzureRmAccount

   Select-AzureRmSubscription -Subscription "<whitelisted subscription name>”
   ```
3. Création d’un groupe de ressources

   ``` PowerShell
   New-AzureRmResourceGroup -Name <resource group name> -Location "East US 2"
   ```
4. Téléchargez les modèles à partir de [GitHub](https://github.com/amitsriva/CrossZonePreview) et notez le dossier dans lequel vous les enregistrez.
5. Créez un nouveau déploiement dans le groupe de ressources que vous avez créé. Modifiez le modèle et le fichier de paramètres de manière appropriée avant le déploiement. 

   Le schéma suivant indique où vous pouvez récupérer l’ID de locataire sur le portail Azure :

   ![ID de locataire à partir du portail](media/create-zone-redundant/tenant-id.png)

Le modèle crée les ressources suivantes :

- **Identité d’utilisateur affectée** : elle est utilisée pour activer des instances de passerelle d’application afin d’accéder au coffre de clés et de récupérer les certificats stockés par l’utilisateur.
- **Coffre de clés** : le coffre de clés où est stocké le certificat de l’utilisateur. Cela peut également être un coffre de clés existant.
- **Secret** : la clé privée qui est stockée dans le coffre de clés.
- **Stratégie d’accès** : une stratégie d’accès appliquée au coffre de clés qui accorde des autorisations en s’appuyant sur l’identité d’utilisateur affectée afin que les déploiements de passerelle d’application puissent récupérer les certificats utilisateur.
- **Adresse IP publique** : adresse IP réservée qui est utilisée pour accéder à la passerelle d’application. Cette adresse IP ne change jamais pendant tout le cycle de vie de la passerelle d’application.
- **Groupes de sécurité réseau** : un groupe de sécurité réseau créé automatiquement sur le sous-réseau de la passerelle d’application, qui ouvre le trafic du port sur le port d’écoute configuré. Il est explicitement créé et géré dans la nouvelle référence SKU et non dans l’ancienne référence SKU où ce groupe de sécurité réseau était implicite.
- **Réseau virtuel** : le réseau virtuel sur lequel la passerelle d’application et les applications sont déployées.
- **Passerelle d’application** : crée une passerelle d’application avec des instances dans les zones requises. Par défaut, toutes les zones (1, 2 et 3) sont sélectionnées. Le nom de la référence SKU est remplacé par *Standard_v2*. Le nom de cette référence SKU est susceptible de changer. Actuellement, la configuration de la mise à l’échelle automatique a des valeurs min et max définies en fonction du nombre d’instances requises. Une fois que la mise à l’échelle automatique est activée, ces valeurs peuvent être ajustées.

```PowerShell
New-AzureRmResourceGroupDeployment -Name Deployment1 -ResourceGroupName AmitVMSSLinuxTest9 -TemplateFile <complete path to template.json> -TemplateParameterFile <complete path to parameters.json>
```
### <a name="powershell-deployment"></a>Déploiement PowerShell

1. Assurez-vous que l’abonnement utilisé figure dans la liste verte, comme mentionné précédemment dans les conditions requises.
2. Téléchargez et installez le MSI PowerShell privé à partir de [GitHub](https://github.com/amitsriva/CrossZonePreview/blob/master/Azure-Cmdlets-5.7.0.19009-x64.msi).
3. Téléchargez le fichier zip PowerShell privé à partir de l’emplacement mentionné dans l’e-mail de confirmation d’inscription à la préversion. Décompressez le fichier sur votre disque et notez son emplacement.
4. Une fois la préversion activée, chargez les modules de préversion avant de vous connecter à votre compte :

   ```PowerShell
   $azurePSPath = "<complete path to Azure-PowerShell folder>"
   import-module "$azurePSPath\AzureRM.Profile\AzureRM.Profile.psd1"
   import-module "$azurePSPath\Azure.Storage\Azure.Storage.psd1"
   import-module "$azurePSPath\AzureRM.Resources\AzureRM.Resources.psd1"
   import-module "$azurePSPath\AzureRM.Network\AzureRM.Network.psd1"
   import-module "$azurePSPath\AzureRM.KeyVault\AzureRM.KeyVault.psd1"
   ```

4. Connectez-vous au compte Azure et, s’il existe plusieurs abonnements, sélectionnez l’abonnement souhaité. Assurez-vous de sélectionner l’abonnement qui figure dans la liste verte.
5. Exécutez les commandes suivantes pour établir des constantes communes qui incluent des noms pour la plupart des entités en cours de création. 

   Modifiez les entrées en fonction de vos préférences d’attribution de noms.

   ```PowerShell
   $location = "eastus2"
   $version = "300"

   $rgname = "RG_A_$version"
   $appgwName = "AGW_A_$version"
   $vaultName = "KVA$version"
   $userAssignedIdentityName = "UI_A_$version"
   $certificateName = "KVCA$version"
   $nsgName = "NSG_A_$version"
   $vnetName = "VN_A_$version"
   $gwSubnetName = "SN_A_$version"
   $gipconfigname = "GC_A_$version"
   $publicIpName = "PIP_A_$version"
   $fipconfig01Name = "FC_A_$version"
   $poolName = "BP_A_$version"
   $frontendPort01Name = "FP1_A_$version"
   $frontendPort02Name = "FP2_A_$version"
   $poolSetting01Name = "BS_A_$version"
   $listener01Name = "HL1_A_$version"
   $listener02Name = "HL2_A_$version"
   $rule01Name = "RR1_A_$version"
   $rule02Name = "RR2_A_$version"
   $AddressPrefix = "111.111.222.0" 
   ```
6. Créez le groupe de ressources :

   ```PowerShell
   $resourceGroup = New-AzureRmResourceGroup -Name $rgname -Location $location -Force
   ```
7. Créez l’identité d’utilisateur affectée utilisée pour donner accès à la passerelle d’application afin de récupérer des certificats dans le coffre de clés.

   ```PowerShell
   $userAssignedIdentity = New-AzureRmResource -ResourceGroupName $rgname -Location $location -ResourceName $userAssignedIdentityName -ResourceType Microsoft.ManagedIdentity/userAssignedIdentities -Force
   ```
8. Créez le coffre de clés utilisé pour stocker vos certificats :

   ```PowerShell
   $keyVault = New-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $rgname -Location $location -EnableSoftDelete
   ```
9. Chargez un certificat comme secret dans le coffre de clés :

   ```PowerShell
   $securepfxpwd = ConvertTo-SecureString -String "<password>" -AsPlainText -Force

   $cert = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name         $certificateName -FilePath ‘<path to pfx file>'  -Password $securepfxpwd
   ```
10. Attribuez la stratégie d’accès au coffre de clés en fonction de l’identité d’utilisateur affectée. Ainsi, les instances de la passerelle d’application peuvent accéder au secret du coffre de clés :

   ```PowerShell
   Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $rgname -PermissionsToSecrets get -ObjectId $userAssignedIdentity.Properties.principalId
   ```
11. Créez le groupe de sécurité réseau (NSG) pour autoriser l’accès au sous-réseau de passerelle d’application sur les ports où sont créés les nouveaux ports d’écoute.

    Par exemple, pour HTTP/HTTPS sur les ports par défaut, le groupe de sécurité réseau permettrait l’accès entrant aux ports 80, 443 et 65200-65 535 pour les opérations de gestion.

   ```PowerShell
   $srule01 = New-AzureRmNetworkSecurityRuleConfig -Name "listeners" -Direction Inbound -SourceAddressPrefix * -SourcePortRange * -Protocol * -DestinationAddressPrefix * -DestinationPortRange 22,80,443 -Access Allow -Priority 100

   $srule02 = New-AzureRmNetworkSecurityRuleConfig -Name "managementPorts" -Direction Inbound -SourceAddressPrefix * -SourcePortRange * -Protocol * -DestinationAddressPrefix * -DestinationPortRange "65200-65535" -Access Allow -Priority 101

   $nsg = New-AzureRmNetworkSecurityGroup -Name $nsgName -ResourceGroupName $rgname -Location $location -SecurityRules $srule01,$srule02 -Force
   ```

12. Créez un réseau virtuel et des sous-réseaux :

   ```PowerShell
   $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 
   $gwSubnetName -AddressPrefix "$AddressPrefix/24" -NetworkSecurityGroup $nsg

   $vnet = New-AzureRmvirtualNetwork -Name $vnetName -ResourceGroupName $rgname -Location $location -AddressPrefix "$AddressPrefix/24" -Subnet $gwSubnet -Force
   ```
13. Créez une adresse IP publique de type réservée/statique :

   ```PowerShell
   $publicip = New-AzureRmPublicIpAddress -ResourceGroupName $rgname -name $publicIpName -location $location -AllocationMethod Static -Sku Standard -Force
   ```

14. Créez la passerelle d’application :

   ```PowerShell
   $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name $gipconfigname -Subnet $gwSubnet

   $fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name $fipconfig01Name -PublicIPAddress $publicip

   $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name $poolName -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com

   $fp01 = New-AzureRmApplicationGatewayFrontendPort -Name $frontendPort01Name -Port 443

   $fp02 = New-AzureRmApplicationGatewayFrontendPort -Name $frontendPort02Name -Port 80

   $sslCert01 = New-AzureRmApplicationGatewaySslCertificate -Name "SSLCert" -KeyVaultSecretId $secret.Id

   $listener01 = New-AzureRmApplicationGatewayHttpListener -Name $listener01Name -Protocol Https -FrontendIPConfiguration
 $fipconfig01 -FrontendPort $fp01 -SslCertificate $sslCert01

   $listener02 = New-AzureRmApplicationGatewayHttpListener -Name $listener02Name -Protocol Http -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp02

   $poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name $poolSetting01Name -Port 80 -Protocol Http -CookieBasedAffinity Disabled

   $rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name $rule01Name -RuleType basic -BackendHttpSettings $poolSetting01 -HttpListener $listener01 -BackendAddressPool $pool

   $rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name $rule02Name -RuleType basic -BackendHttpSettings $poolSetting01 -HttpListener $listener02 -BackendAddressPool $pool

   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2

   $listeners = @($listener02)

   $fps = @($fp01, $fp02)

   $fipconfigs = @($fipconfig01)

   $sslCerts = @($sslCert01)

   $rules = @($rule01, $rule02)

   $listeners = @($listener01, $listener02)

   $appgw = New-AzureRmApplicationGateway -Name $appgwName -ResourceGroupName $rgname -Location $location -UserAssignedIdentityId $userAssignedIdentity.ResourceId -Probes $probeHttps -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -GatewayIpConfigurations $gipconfig -FrontendIpConfigurations $fipconfigs -FrontendPorts $fps -HttpListeners $listeners -RequestRoutingRules $rules -Sku $sku -SslPolicy $sslPolicy -sslCertificates $sslCerts -Force
   ```

15. Récupérez l’adresse IP de la passerelle d’application créée :

   ```PowerShell
   $pip = Get-AzureRmPublicIpAddress -Name $publicIpName -ResourceGroupName $rgname $pip.IpAddress
   ```

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

-  Serai-je facturé pour la passerelle d’application en préversion ?

   Pendant la préversion, aucuns frais ne s’appliquent. Vous serez facturé pour les ressources autres que la passerelle d’application, telles que le coffre de clés, les machines virtuelles, etc.
- Dans quelles régions la préversion est-elle disponible ?

   La préversion est actuellement disponible dans la région Est des États-Unis 2. D’autres régions seront bientôt ajoutées.
- Le portail est-il pris en charge dans la préversion ?

   Non, dans la préversion privée, la prise en charge se limite à un module PowerShell privé et au modèle Resource Manager.

- La charge de travail de production est-elle prise en charge dans la préversion privée ?

   Non, il n’existe aucun contrat de niveau de service ni aucun support dans la préversion privée. Les préversions ne sont pas recommandées pour les charges de travail de production. La prise en charge se limite à une interaction directe avec le groupe de produits à l’aide de l’alias de messagerie pour la préversion.

- Comment signaler un problème ?

   La préversion privée peut contenir des bogues et faire l’objet de fréquents déploiements de code. Utilisez l’alias du support appgwxzone@microsoft.com pour signaler des problèmes et demander de l’aide.

## <a name="known-issues-and-limitations"></a>Problèmes connus et limitations


|Problème  |Détails  |
|---------|---------|---------|
|Facturation     |Aucune facturation actuellement|
|Journaux de diagnostic (pas les métriques)     |Les journaux de performances et de demande/réponse n’apparaissent pas actuellement|
|Portail/CLI/Kit de développement logiciel     |Aucune prise en charge du portail, de la CLI ou du kit de développement logiciel. Le portail ne doit pas être utilisé pour publier des mises à jour pour les passerelles en préversion.|
|La mise à jour via le modèle échoue parfois     |Cela est dû à une condition de concurrence avec la stratégie d’accès KeyVault|Une fois le coffre de clés et l’identité d’utilisateur affectée créés, ils peuvent être supprimés du modèle et seules les références au secret et à l’identité sont requises dans le modèle.|
|Mise à l’échelle automatique     |Aucune prise en charge de la mise à l’échelle automatique actuellement|
|WAF     |WAF n’est actuellement pas pris en charge|
|Certificats fournis par l’utilisateur et adresses IP virtuelles dynamiques     |Ils ne sont pas pris en charge dans le nouveau modèle. Utilisez le coffre de clés pour stocker des certificats et des adresses IP virtuelles statiques.|
|Même sous-réseau pour l’ancienne version et la préversion de la passerelle d’application     |Un sous-réseau avec une passerelle d’application existante (ancien modèle) ne peut pas être utilisé pour la préversion privée.|
|HTTP/2, mode FIPS, WebSocket, Azure Web Apps en tant que principal     |Actuellement non pris en charge |


## <a name="support-and-feedback"></a>Support et commentaires

Pour la prise en charge et les commentaires, contactez appgwxzone@microsoft.com. Le groupe de produits de la passerelle d’application apprécie vos suggestions d’améliorations et donne des conseils si nécessaire.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur d’autres fonctionnalités de la passerelle d’application :

- [Qu’est-ce qu’Azure Application Gateway ?](overview.md)