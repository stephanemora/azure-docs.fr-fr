---
title: Développer une application web Azure AD sécurisée | Microsoft Docs
description: Cet exemple d’application simple met en œuvre les meilleures pratiques de sécurité qui améliorent votre application, de même que le niveau de sécurité de votre organisation lorsque vous développez sur Azure.
keywords: na
services: security
documentationcenter: na
author: TerryLanfear
manager: alclabo
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2019
ms.author: terrylan
ms.openlocfilehash: a936fb4a0a6eadc2840fc6d642428091a6b0fe9e
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771272"
---
# <a name="develop-secure-app-for-an-azure-ad-app"></a>Développer une application sécurisée pour une application Azure AD
## <a name="overview"></a>Vue d’ensemble

Cet exemple est une application web Azure Active Directory simple contenant des liens vers des ressources de sécurité pour développer des applications sur Azure. L’application met en œuvre les meilleures pratiques en matière de sécurité pour vous aider à améliorer votre application et le niveau de sécurité de votre organisation lorsque vous développez des applications sur Azure.

Les scripts de déploiement configurent l’infrastructure. Une fois les scripts de déploiement exécutés, vous devez procéder à une configuration manuelle dans le portail Azure pour lier les composants et services. Cet exemple est destiné aux développeurs expérimentés sur Azure qui travaillent dans le secteur de la vente au détail et qui souhaitent générer une application Azure Active Directory sécurisée avec une infrastructure Azure sécurisée. 


Lors du développement et du déploiement de cette application, vous allez apprendre à 
- Créer une instance Azure Key Vault, stocker des secrets dans cette instance et en récupérer à partir de celle-ci.
- Déployer l’application web Azure dédiée qui est isolée avec un accès au pare-feu front-end. 
- Créer et configurer une instance Azure Application Gateway avec un pare-feu qui utilise les 10 principaux ensembles de règles OWASP. 
- Activer le chiffrement des données en transit comme au repos à l’aide des services Azure. 
- Configurer le centre de sécurité et la stratégie Azure pour évaluer les conformités. 

Après avoir développé et déployé cette application, vous aurez configuré l’exemple d’application web suivant avec la configuration et les mesures de sécurité décrites.

## <a name="architecture"></a>Architecture
L’application est une application multiniveau type présentant trois niveaux. Le serveur frontal, le back end et la couche de base de données avec composants de surveillance et de gestion des secrets intégrés sont présentés ici :

![Architecture d’application](./media/secure-aad-app/architecture.png)

Cette solution utilise les services Azure suivants. Pour obtenir les détails de l’architecture de déploiement, consultez la section Architecture de déploiement. 

L’architecture est constituée des composants suivants

- [Azure Application Gateway](../../application-gateway/index.yml) : Fournit la passerelle et le pare-feu pour l'architecture de l'application.
- [Application Insights](../../azure-monitor/app/app-insights-overview.md). Fournit un service extensible de gestion des performances des applications (APM) sur de multiples plateformes.
- [Azure Key Vault](../../key-vault/index.yml). Stocke et chiffre les secrets de l’application et gère la création des stratégies d’accès qui s'y rapportent.
- [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md). Fournit un service cloud de gestion des identités et des accès, la connexion et l’accès aux ressources.
- [Azure DNS (Domain Name System)](../../dns/dns-overview.md). Fournit le service d’hébergement du domaine.
- [Équilibreur de charge Azure](../../load-balancer/load-balancer-overview.md). Permet de mettre à l’échelle vos applications et de créer une haute disponibilité pour vos services.
- [Azure Web App](../../app-service/overview.md).  Fournit un service HTTP pour l’hébergement des applications web.
- [Azure Security Center](../../security-center/index.yml). Fournit une protection avancée contre les menaces pour vos charges de travail hybrides dans le cloud.
- [Azure Policy](../../governance/policy/overview.md). Fournit une évaluation de vos ressources afin de détecter toute non-conformité avec les stratégies affectées.

## <a name="threat-model"></a>Modèle de menace
La modélisation des menaces est le processus d’identification des menaces de sécurité potentielles pour votre entreprise et votre application, puis de vérification qu'un plan d'atténuation approprié, est en place.

Cet exemple a utilisé [Microsoft Threat Modeling Tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) pour implémenter la modélisation des menaces pour l’exemple d’application sécurisée. En reconstituant les composants et les flux de données, vous pouvez identifier les problèmes et menaces dès le début du processus de développement. Cela fera gagner du temps et de l’argent ultérieurement.

Voici le modèle de menace de l’exemple d’application

![Modèle de menace](./media/secure-aad-app/threat-model.png)

Certains exemples de menaces et de vulnérabilités potentielles générés par l'outil de modélisation sont illustrés dans la capture d'écran suivante. Le modèle de menace offre une vue d’ensemble de la surface d’attaque exposée et invite les développeurs à réfléchir à la manière d’atténuer les problèmes.

![Sortie du modèle de menace](./media/secure-aad-app/threat-model-output.png)

### <a name="prerequisites"></a>Conditions préalables requises
Pour permettre le bon fonctionnement de l'application, vous devez installer les outils suivants :

- Un éditeur de code pour modifier et voir le code de l’application.[Visual Studio Code](https://code.visualstudio.com/) est une option open source.
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest,) sur votre ordinateur de développement.
- [Git](https://git-scm.com/) sur votre système. Git est utilisé pour cloner le code source localement.
- [jq](https://stedolan.github.io/jq/) est un outil UNIX pour interroger JSON de manière conviviale.

Vous devez disposer d’un abonnement Azure pour déployer les ressources de l’exemple d’application. Si vous n’avez pas d’abonnement Azure, vous pouvez [créer un compte gratuit](https://azure.microsoft.com/free/) pour tester l'exemple d'application.

Après avoir installé ces outils, vous êtes prêt à déployer l’application sur Azure.

### <a name="implementation-guidance"></a>Conseils d’implémentation
Le script de déploiement peut être divisé en quatre phases. Chaque phase déploie et configure une ressource Azure présente dans le [diagramme d'architecture](#architecture).

Ces quatre phases sont les suivantes

- Déployer Azure Key Vault.
- Déployer Azure Web Apps.
- Déployer Application Gateway avec le pare-feu d’applications web.
- Configurer une ressource Azure AD avec une application déployée.

Chaque phase s’appuie sur la précédente en utilisant la configuration des ressources précédemment déployées.

Pour mener à bien la procédure d’implémentation, vérifiez que vous avez installé les outils répertoriés sous [Conditions préalables](#prerequisites).

#### <a name="deploy-azure-key-vault"></a>Déployer Azure Key Vault
Dans cette section, vous allez créer et déployer une instance Azure Key Vault utilisée pour stocker les secrets et certificats.

Au terme du déploiement, vous disposerez d’une instance Azure Key Vault déployée sur Azure.

Pour déployer Azure Key Vault à l’aide de PowerShell
 
1. Déclarez les variables pour Azure Key Vault.
2. Inscrivez le fournisseur Azure Key Vault.
3. Créez le groupe de ressources de l'instance.
4. Créez l’instance Azure Key Vault dans le groupe de ressources créé à l’étape 3.

#### <a name="the-below-azure-ad-user-will-have-admin-permissions-to-the-key-vault"></a>L’utilisateur Azure AD ci-dessous disposera d’autorisations d’administrateur sur le coffre de clés
    $keyVaultAdminUsers = @($user1,user2)

#### <a name="register-the-az-providers"></a>Inscrire les fournisseurs de zones de disponibilité (AZ)
    Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault

#### <a name="create-the-azure-key-vault-instance"></a>Créer l’instance Azure Key Vault
    New-AzKeyVault -Name $kvName 
                -ResourceGroupName $ResourceGroup 
                -Location 'East US'
                -EnabledForDiskEncryption

#### <a name="add-the-administrator-policies-to-the-key-vault"></a>Ajouter les stratégies d’administrateur au coffre de clés
    foreach ($keyVaultAdminUser in $keyVaultAdminUsers) {
    $UserObjectId = (Get-AzADUser -SearchString $keyVaultAdminUser).Id
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName -ObjectId $UserObjectId 
    -PermissionsToKeys all -PermissionsToSecrets all -PermissionsToCertificates all
    }

#### <a name="to-create-an-access-policy-to-allow-a-user-to-get-and-list-cryptographic-keys-certificates-and-secrets-if-you-know-the-user-principal-name"></a>Pour créer une stratégie d’accès visant à permettre à un utilisateur d’obtenir et de lister les clés de chiffrement, les certificats et les secrets si vous connaissez le nom d’utilisateur principal :
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName 
                           -ResourceGroupName $resourceGroupName 
                           -UserPrincipalName 'user1@contoso.com 
                           -PermissionsToCertificates list, get 
                           -PermissionsToKeys list, get 
                           -PermissionsToSecrets list, get 

Il est recommandé d’utiliser des identités managées pour les ressources Azure des applications utilisant Key Vault afin d'accéder aux ressources. Votre niveau de sécurité est renforcé lorsque les clés d’accès Key Vault ne sont pas stockées dans le code ou la configuration.

Un certificat racine est inclus dans le conteneur. Les étapes nécessaires pour obtenir le certificat sont les suivantes

1. Téléchargez le fichier de certificat émis par l’[autorité de certification](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt).
2. Décodez le fichier de certificat :

   ```powershell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```
Ce script crée une identité attribuée pour l'instance App Service et celle-ci peut être utilisée avec MSI pour interagir avec Azure Key Vault, sans coder en dur les secrets dans le code ou la configuration.

Dans le portail, accédez à l’instance Azure Key Vault pour autoriser l’identité attribuée dans l'onglet Stratégie d’accès. Sélectionnez **Ajouter une nouvelle stratégie d’accès**. Sous **Sélectionner le principal**, recherchez le nom de l’application (similaire au nom de l'instance App Service créée).
Un principal de service joint à l’application doit être visible. Sélectionnez-le et enregistrez la page de stratégie d’accès, comme illustré dans la capture d’écran suivante.

L’application devant uniquement récupérer des clés, sélectionnez l'autorisation **Get** dans les options de secrets pour autoriser l’accès tout en limitant les privilèges accordés.

![Stratégie d’accès à Key Vault](./media/secure-aad-app/kv-access-policy.png)

*Créer une stratégie d’accès à Key Vault*

Enregistrez la stratégie d’accès, puis enregistrez la nouvelle modification dans l'onglet **Stratégies d'accès** pour mettre à jour les stratégies.

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>Déployer Application Gateway avec le pare-feu d’applications web
Dans les applications web, il est déconseillé d'exposer publiquement les services sur Internet.
L’équilibrage de charge et les règles de pare-feu offrent plus de sécurité et de contrôle sur le trafic entrant et en facilitent la gestion.

Pour déployer une instance Application Gateway

1. Créez un groupe de ressources où héberger Application Gateway.
2. Approvisionnez un réseau virtuel à joindre à la passerelle.
3. Créez un sous-réseau pour la passerelle dans le réseau virtuel.
4. Approvisionnez une adresse IP publique.
5. Approvisionnez la passerelle Application Gateway.
6. Activez le pare-feu d’applications web sur la passerelle.

```
Connect-AzAccount
Select-AzSubscription -SubscriptionId '$SubscriptionId'
New-AzResourceGroup -Name appgw-rg -Location "East US"

#Create a virtual network and a subnet for the application gateway

#Assign an address range for the subnet to be used for the application gateway.

$gwSubnet = New-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

#Assign an address range to be used for the back-end address pool.

$nicSubnet = New-AzVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.0.0/24

#Create a virtual network with the subnets defined in the preceding steps.

$vnet = New-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "East US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet

#Retrieve the virtual network resource and subnet resources to be used in the steps that follow.

$vnet = Get-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
$nicSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet


#Create a public IP address for the front-end configuration

$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "East US" -AllocationMethod Dynamic

#Create an application gateway configuration object

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

#Create a front-end IP configuration

$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

#Configure the back-end IP address pool with the IP addresses of the back-end web servers

$pool = New-AzApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 10.0.0.0

#Configure the front-end IP port for the public IP endpoint

$fp = New-AzApplicationGatewayFrontendPort -Name 'port01'  -Port 443

#Configure the certificate for the application gateway. This certificate is used to decrypt and reencrypt the traffic on the application gateway

$passwd = ConvertTo-SecureString  "P@ssword!1" -AsPlainText -Force 
$cert = New-AzApplicationGatewaySSLCertificate -Name cert01 -CertificateFile "C:\AAD\Securities\Certificates\sslcert.com.cer" -Password $passwd 

#Create the HTTP listener for the application gateway

$listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert

#Upload the certificate to be used on the SSL-enabled back-end pool resources

#$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'allowlistcert1' -CertificateFile C:\cert.cer

$trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile "C:\AAD\Securities\Certificates\sslcert.com.cer"

#Configure the HTTP settings for the application gateway back end

$poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name "setting01" -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"

#Create a load-balancer routing rule that configures the load balancer

$rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

#Configure the instance size of the application gateway

$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

#Configure the SSL policy to be used on the application gateway

$SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -PolicyType Custom

$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "East US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -TrustedRootCertificate $trustedRootCert01 -Verbose

```

#### <a name="deploy-azure-web-apps"></a>Déployer Azure Web Apps
Azure App Service vous permet de générer et d’héberger des applications web à l’aide de langages comme Python, Ruby, C# et Java. Azure prend également en charge les conteneurs personnalisés, qui permettent l'exécution de la plupart des langages de programmation sur la plateforme Azure App Service.

#### <a name="create-an-app-service-plan-in-free-tier"></a>Créer un plan App Service au niveau Gratuit
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName $webappname -Tier Free

#### <a name="create-a-web-app"></a>Créer une application web
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName $webappname

    Write-Host "Configure a CNAME record that maps $fqdn to $webappname.azurewebsites.net"
    Read-Host "Press [Enter] key when ready ..."

#### <a name="before-continuing-go-to-your-azure-domain-name-system-configuration-ui-for-your-custom-domain-and-follow-the-instructions-at-httpsakamsappservicecustomdns-to-configure-a-cname-record-for-the-hostname-www-and-point-it-your-web-apps-default-domain-name"></a>Avant de poursuivre, accédez à votre interface utilisateur de configuration d’Azure DNS (Domain Name System) pour votre domaine personnalisé, puis suivez les instructions mentionnées sur https://aka.ms/appservicecustomdns afin de configurer un enregistrement CNAME pour le nom d’hôte « www » et le pointer vers le nom de domaine par défaut de votre application web

#### <a name="upgrade-app-service-plan-to-shared-tier-minimum-required-by-custom-domains"></a>Mettre à niveau le plan App Service vers le niveau Partagé (minimum exigé par les domaines personnalisés)
    Set-AzAppServicePlan -Name $webappname -ResourceGroupName $webappname -Tier Shared

#### <a name="add-a-custom-domain-name-to-the-web-app"></a>Ajouter un nom de domaine personnalisé à l’application web
    Set-AzWebApp -Name $webappname -ResourceGroupName $webappname `-HostNames @($fqdn,"$webappname.azurewebsites.net")

## <a name="guidance-and-recommendations"></a>Instructions et recommandations

### <a name="network"></a>Réseau
Une fois le déploiement terminé, vous disposez d’une passerelle Application Gateway avec pare-feu d’applications web activé.

L’instance de passerelle expose le port 443 pour HTTPS. Cette configuration permet de s'assurer que l'application est uniquement accessible sur le port 443 via HTTPS.

Bloquer les ports non utilisés et limiter la surface d'attaque relève d'une bonne pratique en matière de sécurité.

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>Ajouter des groupes de sécurité réseau à l’instance App Service

Les instances App Service peuvent être intégrées aux réseaux virtuels. Cette intégration permet de les configurer avec les stratégies de groupe de sécurité réseau qui gèrent le trafic entrant et sortant de l’application.

1. Pour activer cette fonctionnalité, dans le panneau de l’instance Azure App Service, sous **Paramètres**, sélectionnez **Mise en réseau**. Dans le volet droit, effectuez la configuration sous **Intégration de réseau virtuel**.

   ![Intégration du nouveau réseau virtuel](./media/secure-web-app/app-vnet-menu.png)

    *Intégration d’un nouveau réseau virtuel pour App Service*
1. Sur la page suivante, sélectionnez **Ajouter un réseau virtuel (préversion)** .

1. Dans le menu suivant, sélectionnez le réseau virtuel créé lors du déploiement er commençant par `aad-vnet`. Vous pouvez créer un sous-réseau ou sélectionner un sous-réseau existant.
   Dans ce cas, créez un sous-réseau. Définissez la **Plage d'adresses** sur **10.0.3.0/24** et nommez le sous-réseau **app-subnet**.

   ![Configuration du réseau virtuel App Service](./media/secure-web-app/app-vnet-config.png)

    *Configuration du réseau virtuel pour App Service*

Une fois l'intégration au réseau virtuel activée, vous pouvez ajouter des groupes de sécurité réseau à l'application.

1. Dans la zone de recherche, recherchez les **groupe de sécurité réseau**. Sélectionnez **Groupes de sécurité réseau** dans les résultats.

    ![Rechercher les groupes de sécurité réseau](./media/secure-web-app/nsg-search-menu.png)

    *Rechercher les groupes de sécurité réseau*

2. Dans le menu suivant, sélectionnez **Ajouter**. Entrez le **Nom** du groupe de sécurité réseau et le **Groupe de ressources** dans lequel il doit se trouver. Ce groupe de sécurité réseau sera appliqué au sous-réseau de la passerelle d’application.

    ![Créer un groupe de sécurité réseau](./media/secure-web-app/nsg-create-new.png)

    *Créer un groupe de sécurité réseau*

3. Une fois le groupe de sécurité réseau créé, sélectionnez-le. Dans ce panneau, sous **Paramètres**, sélectionnez **Règles de sécurité de trafic entrant**. Configurez ces paramètres pour autoriser les connexions à la passerelle Application Gateway via le port 443.

   ![Configurer le groupe de sécurité réseau](./media/secure-web-app/nsg-gateway-config.png)

   *Configurer le groupe de sécurité réseau*

4. Dans les règles de trafic sortant du groupe de sécurité réseau de la passerelle, ajoutez une règle autorisant les connexions sortantes vers l’instance App Service en créant une règle qui cible la balise de service `AppService`

   ![Ajouter les règles de trafic sortant pour le groupe de sécurité réseau](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *Ajouter les règles de trafic sortant pour le groupe de sécurité réseau*

    Ajoutez une autre règle de trafic sortant pour autoriser la passerelle à envoyer des règles de trafic sortant à un réseau virtuel.

   ![Ajouter une autre règle de trafic sortant](./media/secure-web-app/nsg-outbound-vnet.png)

    *Ajouter une autre règle de trafic sortant*

5. Dans le panneau des sous-réseaux du groupe de sécurité réseau, sélectionnez **Associer**, sélectionnez le réseau virtuel créé lors du déploiement, puis le sous-réseau de passerelle nommé **gw-subnet**. Le groupe de sécurité réseau est appliqué au sous-réseau.

6. Créez un autre groupe de sécurité réseau comme à l’étape précédente, mais pour l’instance App Service cette fois. Donnez-lui un nom. Ajoutez la règle de trafic entrant pour le port 443 comme vous l’avez fait pour le groupe de sécurité réseau de la passerelle Application Gateway.

   Si une instance App Service est déployée sur une instance de App Service Environment (ce qui n’est pas le cas de cette application), vous pouvez ajouter des règles de trafic entrant pour autoriser les sondes Azure Service Health en ouvrant les ports 454-455 sur les groupes de sécurité entrants de votre groupe de sécurité réseau App Service. Voici la configuration :

   ![Ajouter des règles pour les sondes Azure Service Health](./media/secure-web-app/nsg-create-healthprobes.png)

    *Ajouter des règles pour les sondes Azure Service Health (App Service Environment uniquement)*

Pour limiter la surface d’attaque, modifiez les paramètres réseau App Service afin d'autoriser uniquement la passerelle Application Gateway à accéder à l’application.
Pour appliquer ce paramètres, accédez à l’onglet Réseau d’App Service, sélectionnez l’onglet **Restrictions d’adresse IP**, puis créez une règle d’autorisation qui autorise uniquement l’adresse IP de la passerelle d’application à accéder directement au service. Vous pouvez récupérer l’adresse IP de la passerelle à partir de sa page Vue d'ensemble. Dans l'onglet **CIDR d'adresse IP**, entrez l’adresse IP au format suivant : `<GATEWAY_IP_ADDRESS>/32`.

![Autoriser uniquement la passerelle](./media/secure-web-app/app-allow-gw-only.png)

*Autoriser uniquement l’adresse IP de la passerelle à accéder à App Service*

### <a name="azure-domain-name-system"></a>Azure DNS (Domain Name System) 
Azure Domain Name System, ou Azure DNS (Domain Name System), est chargé de traduire (ou résoudre) un nom de site web ou de service en une adresse IP. Azure Domain Name System (https://docs.microsoft.com/azure/dns/dns-overview) est un service d’hébergement pour les domaines Domain Name System qui offre une résolution de noms à l’aide de l’infrastructure Azure. En hébergeant des domaines dans Azure, les utilisateurs peuvent gérer des enregistrements Domain Name System en utilisant les mêmes informations d’identification, API, outils et modes de facturation que d’autres services Azure. Azure Domain Name System prend également en charge les domaines Domain Name System privés.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption
Azure Disk Encryption utilise la fonctionnalité BitLocker de Windows pour chiffrer les volumes des disques de données. La solution s’intègre à Azure Key Vault pour faciliter le contrôle et la gestion des clés de chiffrement de disque.

### <a name="identity-management"></a>Gestion des identités
Les technologies suivantes offrent des fonctionnalités permettant de gérer l’accès aux données de titulaire de carte dans l’environnement Azure
- Azure Active Directory est le service cloud multilocataire de gestion des identités et des annuaires fourni par Microsoft. Tous les utilisateurs de cette solution sont créés dans Azure Active Directory, notamment ceux qui accèdent à Azure WebApp.
- Le contrôle d’accès en fonction du rôle Azure permet aux administrateurs de définir des autorisations d’accès affinées pour n’accorder aux utilisateurs que l’accès dont ils ont besoin dans le cadre de leur travail. Au lieu d’accorder à tous les utilisateurs des autorisations illimitées sur les ressources Azure, les administrateurs peuvent autoriser seulement certaines actions pour accéder aux données de titulaire de carte. Par exemple, l’accès aux abonnements est limité à l’administrateur d’abonnements.
- Azure Active Directory Privileged Identity Management permet aux clients de restreindre le nombre d’utilisateurs qui ont accès à des informations spécifiques, comme les données de titulaires de carte. Les administrateurs peuvent utiliser Azure Active Directory Privileged Identity Management pour découvrir, restreindre et superviser les identités privilégiées et leur accès aux ressources. Cette fonctionnalité permet également d’appliquer un accès administratif juste-à-temps à la demande si nécessaire.
- La solution Azure Active Directory Identity Protection détecte les vulnérabilités risquant d’affecter les identités d’une organisation, configure des réponses automatiques aux actions suspectes détectées en lien avec les identités d’une organisation, et examine les incidents suspects afin de prendre les mesures appropriées pour résoudre ces derniers.
### <a name="secrets-management"></a>Gestion des secrets
La solution utilise Azure Key Vault pour gérer les clés et les secrets. Azure Key Vault permet de protéger les clés de chiffrement et les secrets utilisés par les services et les applications cloud. Les fonctionnalités Azure Key Vault suivantes aident les clients à protéger les données et à y accéder
   - Les stratégies d’accès avancées sont configurées en fonction des besoins.
   - Les stratégies d’accès Key Vault sont définies avec des autorisations minimales requises pour les clés et les secrets.
   - L’ensemble des clés et secrets figurant dans Key Vault présentent des dates d’expiration.
   - Toutes les clés dans Key Vault sont protégées par des modules de sécurité matériels spécialisés. Le type de clé est une clé RSA de 2 048 bits protégée par un module de sécurité matériel (HSM).
   - Avec Key Vault, vous pouvez chiffrer les clés et les secrets (comme les clés d’authentification, les clés de compte de stockage, les clés de chiffrement de données, les fichiers .PFX et les mots de passe) à l’aide de clés protégées par des modules de sécurité matériels (HSM). 
   - Utilisez le contrôle d’accès en fonction du rôle (RBAC) pour attribuer des autorisations à des utilisateurs, groupes et applications dans une étendue donnée.     
   - Utilisez Key Vault pour gérer vos certificats TLS avec renouvellement automatique. 
   - Les journaux de diagnostics pour Key Vault sont activés avec une période de rétention d’au moins 365 jours.
   - Les opérations de chiffrement autorisées pour les clés sont restreintes à celles qui sont nécessaires.
### <a name="azure-security-center"></a>Azure Security Center
Avec Azure Security Center, les clients peuvent appliquer et gérer de façon centralisée des stratégies de sécurité sur les charges de travail, limiter l’exposition aux menaces ainsi que détecter les attaques et y répondre. De plus, 
   - Azure Security Center accède aux configurations existantes des services Azure pour fournir des suggestions en matière de configuration et de service. Cela contribue à l’amélioration du dispositif de sécurité et à la protection des données.
   - Azure Security Center utilise diverses fonctionnalités de détection pour avertir les clients des attaques potentielles qui ciblent leur environnement. Ces alertes fournissent de précieuses informations sur le déclencheur de l’alerte, les ressources ciblées et la source de l’attaque. Azure Security Center fournit un ensemble d’alertes de sécurité prédéfinies, qui sont déclenchées en cas de menace ou d’activité suspecte. Les règles d’alerte personnalisées d’Azure Security Center permettent aux clients de définir de nouvelles alertes de sécurité basées sur les données déjà collectées dans leur environnement.
   - Azure Security Center fournit des incidents et des alertes de sécurité hiérarchisés, ce qui permet aux clients de découvrir et de résoudre plus facilement les problèmes de sécurité potentiels. Un rapport de Threat Intelligence est généré pour chaque menace détectée afin d’aider les équipes chargées de répondre aux incidents à examiner et à contrecarrer les menaces.
### <a name="azure-application-gateway"></a>Azure Application Gateway 
   l’architecture réduit le risque de failles de sécurité en utilisant une passerelle d’application avec un pare-feu d’applications web Azure configuré et l’ensemble de règles OWASP activé. Les autres fonctionnalités incluent notamment les suivantes
   - SSL de bout en bout.
   - Désactivation de TLS versions 1.0 et 1.1.
   - Activation de TLS version 1.2.
   - Pare-feu d’applications web (mode de prévention).
   - Mode de prévention avec l’ensemble de règles OWASP 3.0.
   - Activation de la journalisation des diagnostics.
   - Sondes d’intégrité personnalisées.
   - Azure Security Center et un conseiller Azure Advisor fournissent une protection et des notifications supplémentaires. Azure Security Center fournit également un système de réputation.
### <a name="logging-and-auditing"></a>Journalisation et audit
Les services Azure assurent une journalisation complète de l’activité du système et des utilisateurs, ainsi que de l’intégrité du système :
   - Journaux d’activité : les [journaux d’activité](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fournissent des insights sur les opérations ayant été effectuées sur les ressources d’un abonnement. Les journaux d’activité peuvent aider à déterminer l’initiateur, l’heure d’exécution et l’état d’une opération.
   - Journaux de diagnostic : les [journaux de diagnostic](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluent l’ensemble des journaux d’activité générés par chaque ressource. Ils incluent les journaux système des événements Windows, les journaux d’activité de Stockage Azure, les journaux d’audit de Key Vault, ainsi que les journaux d’activité de pare-feu et d’accès d’Application Gateway. Tous les journaux de diagnostic sont consignés dans un compte de stockage Azure centralisé et chiffré pour l’archivage. L’utilisateur peut configurer la rétention jusqu’à 730 jours pour répondre aux exigences de rétention spécifiques de l’entreprise.
### <a name="azure-monitor-logs"></a>Journaux d’activité Azure Monitor
   ces journaux sont regroupés dans [Journaux Azure Monitor](https://azure.microsoft.com/services/log-analytics/) à des fins de traitement, de stockage et de génération de rapports de tableau de bord. Une fois collectées, les données sont organisées dans différentes tables en fonction du type de données dans des espaces de travail Log Analytics. Toutes les données sont ainsi analysées ensemble, quelle que soit leur source d’origine. Par ailleurs, Azure Security Center s’intègre à Journaux Azure Monitor pour permettre aux clients d’utiliser des requêtes Kusto ; ils peuvent alors accéder à leurs données d’événement de sécurité et les combiner avec des données provenant d’autres services.

   Les [solutions de supervision](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) Azure suivantes sont incluses dans cette architecture

   - [Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment) : la solution Active Directory Health Check évalue les risques et l’intégrité des environnements de serveur à intervalles réguliers, et fournit une liste hiérarchisée de suggestions spécifiques pour l’infrastructure de serveur déployée.
   - [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth) : la solution Agent Health indique le nombre d’agents déployés et leur répartition géographique, ainsi que le nombre d’agents qui ne répondent pas ou qui envoient des données opérationnelles.
   - [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity) : la solution Activity Log Analytics facilite l’analyse des journaux d’activité Azure de tous les abonnements Azure d’un client.
### <a name="azure-monitor"></a>Azure Monitor
   [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) aide les utilisateurs à suivre les performances, à garantir la sécurité et à identifier les tendances en permettant aux organisations d’effectuer des audits, de créer des alertes et d’archiver des données, notamment en suivant les appels d’API dans leurs ressources Azure.
### <a name="application-insights"></a>Application Insights 
   [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) est un service extensible de gestion des performances des applications destiné aux développeurs web sur de multiples plateformes. La solution Application Insights détecte des anomalies de performances et permet aux clients de surveiller leurs applications web dynamiques. Elle inclut de puissants outils d’analyse qui aident les clients à diagnostiquer les problèmes et à comprendre ce que les utilisateurs font réellement avec leurs applications. Elle a été conçue pour aider les clients à améliorer continuellement les performances et l’ergonomie des applications.

### <a name="azure-key-vault"></a>Azure Key Vault
   Créez, pour l’organisation, un coffre dans lequel stocker les clés et assurez la responsabilité de tâches opérationnelles comme celles mentionnées ci-dessous

   - Les données stockées dans Key Vault sont les suivantes   
   - Clé Application Insights
   - Clé d’accès de stockage des données
   - Chaîne de connexion
   - Nom de la table de données
   - Informations d’identification de l’utilisateur
   - Les stratégies d’accès avancées sont configurées en fonction des besoins.
   - Les stratégies d’accès Key Vault sont définies avec les autorisations minimales exigées pour les clés et les secrets
   - Toutes les clés et tous les secrets dans Key Vault ont des dates d’expiration.
   - Toutes les clés dans Key Vault sont protégées par des modules de sécurité matériels (HSM) [Type de clé = clé RSA de 2 048 bits protégée par       
     module de sécurité matériel (HSM).]
   - Les autorisations minimales requises sont accordées à l’ensemble des utilisateurs et identités à l’aide d’un contrôle d’accès en fonction du rôle (RBAC).
   - Les applications ne partagent pas un coffre de clés, sauf si elles s’approuvent mutuellement et qu’elles ont besoin d’accéder aux mêmes secrets au moment de l’exécution.
   - Les journaux de diagnostics pour Key Vault sont activés avec une période de rétention d’au moins 365 jours.
   - Les opérations de chiffrement autorisées pour les clés sont limitées à celles qui sont nécessaires.

### <a name="vpn-and-expressroute"></a>VPN et ExpressRoute
   Un tunnel VPN sécurisé ou [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) doivent être configurés en établissant une connexion sécurisée aux ressources déployées dans le cadre de cette architecture de référence d’application web PaaS. En configurant correctement un VPN ou un routage ExpressRoute, les clients peuvent ajouter une couche de protection au niveau des données en transit.

   En implémentant un tunnel VPN sécurisé avec Azure, il est possible de créer une connexion privée virtuelle entre un réseau local et un réseau virtuel Azure. Cette connexion via Internet permet aux clients de « tunneler » des informations en toute sécurité par le biais d’une liaison chiffrée entre leur réseau et Azure. La technologie de réseau privé virtuel de site à site, aussi sécurisée qu’éprouvée, est déployée par des entreprises de toutes tailles depuis des décennies. Le mode tunnel IPsec est utilisé dans cette option comme mécanisme de chiffrement.

   Étant donné que le trafic à l’intérieur du tunnel VPN transite par Internet avec un VPN de site à site, Microsoft offre une autre option de connexion encore plus sécurisée. Azure ExpressRoute est une liaison réseau étendu dédiée entre Azure et un emplacement local ou un fournisseur d’hébergement Exchange. Les connexions ExpressRoute ne transitent pas par Internet, et offrent de meilleurs niveaux de fiabilité, de rapidité, de latence et de sécurité que les connexions classiques via Internet. En outre, comme il s’agit d’une connexion directe du fournisseur de télécommunications du client, les données ne circulent pas sur Internet et n’y sont donc pas exposées.

   Les meilleures pratiques concernant l’implémentation d’un réseau hybride sécurisé qui étend un réseau local à Azure sont [décrites ici](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

#### <a name="implement-azure-active-directory-oidc"></a>Implémenter OIDC Azure Active Directory

1. Pour cloner le dépôt de code source, utilisez la commande Git suivante

 ``` git
 git clone https://github.com/Azure-Samples/AAD-Security
   ```
## <a name="update-the-redirect-urls"></a>Mettre à jour les URL de redirection
1.  Revenez au portail Azure. Dans le volet de navigation de gauche, sélectionnez le service Azure Active Directory, puis Inscriptions d’applications.
2.  Dans l’écran qui s’affiche, sélectionnez l’application WebApp-OpenIDConnect-DotNet-code-v2.
3.  Sous l’onglet Authentification o   Dans la section URI de redirection, sélectionnez Web dans la zone de liste déroulante, puis ajoutez les URI de redirection suivants.
    https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signin-oidc o   Dans la section Paramètres avancés, définissez URL de déconnexion sur https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signout-oidc
4.  Sous l’onglet Personnalisation o   Mettez à jour l’URL de la page d’accueil avec l’adresse de votre service d’application ; par exemple, https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net.
        o   Enregistrez la configuration.
5.  Si votre application appelle une API web, veillez à appliquer les changements nécessaires sur le projet appsettings.json, afin qu’il appelle l’URL d’API publiée au lieu de localhost.
Publication de l’exemple
    1.  À partir de l’onglet Vue d’ensemble d’App Service, téléchargez le profil de publication en cliquant sur le lien Obtenir le profil de publication, puis enregistrez-le. D’autres mécanismes de déploiement, comme le contrôle de code source, peuvent également être utilisés.
    2.  Basculez vers Visual Studio, puis accédez au projet WebApp-OpenIDConnect-DotNet-code-v2. Cliquez avec le bouton droit sur le projet dans l’Explorateur de solutions, puis sélectionnez Publier. Cliquez sur Importer le profil dans la barre inférieure, puis importez le profil de publication que vous avez téléchargé précédemment.
    3.  Cliquez sur Configurer puis, sous l’onglet Connexion, mettez à jour l’URL de destination afin que l’URL de la page d’accueil soit une URL HTTPS ; par exemple, https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net. Cliquez sur Suivant.
    4.  Sous l’onglet Paramètres, vérifiez que l’option Activer l’authentification d’organisation n’est PAS sélectionnée. Cliquez sur Enregistrer. Cliquez sur Publier dans l’écran principal.
    5.  Visual Studio publie le projet et ouvre automatiquement l’URL du projet dans un navigateur. Si la page web par défaut du projet s’affiche, la publication a réussi.
#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>Implémenter Multi-Factor Authentication pour Azure Active Directory
   Les administrateurs doivent s’assurer que les comptes d’abonnement du portail sont protégés. L'abonnement gérant les ressources que vous créez, il est vulnérable aux attaques. Pour protéger l’abonnement, activez Multi-Factor Authentication dans l'onglet **Azure Active Directory** correspondant.

   Une instance Azure AD fonctionne selon les stratégies appliquées à un utilisateur ou à des groupes d’utilisateurs répondant à certains critères.
Azure crée une stratégie par défaut qui spécifie que les administrateurs doivent utiliser une authentification à deux facteurs pour se connecter au portail.
Après avoir activé cette stratégie, vous pouvez être invité à vous déconnecter puis à vous reconnecter au portail Azure.

Pour activer l’authentification multifacteur pour les connexions d’administrateur

   1. Dans le portail Azure, accédez à l'onglet **Azure Active Directory**.
   2. Sous la catégorie Sécurité, sélectionnez Accès conditionnel. L’écran suivant s’affiche

       ![Accès conditionnel - Stratégies](./media/secure-aad-app/ad-mfa-conditional-add.png)

Si vous ne pouvez pas créer de stratégie

   1. Accédez à l'onglet **MFA**.
   2. Sélectionnez un lien **Essai gratuit** Azure AD Premium pour vous abonner à l’essai gratuit.

   ![Un essai gratuit Azure AD Premium](./media/secure-aad-app/ad-trial-premium.png)

Revenez à l'écran Accès conditionnel.

   1. Sélectionnez l’onglet Nouvelle stratégie.
   2. Entrez le nom de la stratégie.
   3. Sélectionnez les utilisateurs ou les groupes pour lesquels vous souhaitez activer l’authentification multifacteur.
   4. Sous **Contrôle d'accès**, sélectionnez l'onglet **Octroi**, puis **Exiger une authentification multifacteur** (et d'autres paramètres, si vous le souhaitez).

   ![Exiger une authentification multifacteur](./media/secure-aad-app/ad-mfa-conditional-add.png)

   Pour activer la stratégie, cochez la case située en haut de l'écran ou utilisez l'onglet **Accès conditionnel**. Une fois la stratégie activée, les utilisateurs doivent utiliser l’authentification multifacteur pour se connecter au portail.

   Une stratégie de base requiert l’authentification multifacteur pour tous les administrateurs Azure. Vous pouvez l’activer immédiatement dans le portail. L’activation de cette stratégie peut invalider la session active et vous obliger à vous reconnecter.

   Si la stratégie de base de référence n’est pas activée
   1.   Sélectionnez **Demander l’authentification multifacteur pour les administrateurs**.
   2.   Sélectionnez **Utiliser la stratégie immédiatement**.

   ![Sélectionner Utiliser la stratégie immédiatement](./media/secure-aad-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>Utiliser Azure Sentinel pour surveiller les applications et les ressources

   À mesure qu'une application se développe, il devient difficile de regrouper tous les signaux et métriques de sécurité reçus à partir des ressources et de les utiliser dans le cadre d'une approche orientée action.

   Azure Sentinel est conçu pour collecter les données, détecter les types de menaces possibles et fournir une visibilité sur les incidents de sécurité.
En attendant une intervention manuelle, Azure Sentinel peut utiliser des playbooks pré-écrits pour déclencher des alertes et processus de gestion des incidents.

   L’exemple d’application se compose de plusieurs ressources qu'Azure Sentinel peut surveiller.
Pour configurer Azure Sentinel, vous devez d’abord créer un espace de travail Log Analytics afin d'y stocker toutes les données issues des différentes ressources.

Pour créer cet espace de travail

   1. Dans la zone de recherche du portail Azure, recherchez **Log Analytics**. Sélectionnez **Espaces de travail Log Analytics**.

   ![Rechercher des espaces de travail Log Analytics](./media/secure-aad-app/sentinel-log-analytics.png)

   *Rechercher des espaces de travail Log Analytics*

   2. Sur la page suivante, sélectionnez **Ajouter**, puis indiquez un nom, un groupe de ressources et l'emplacement de l'espace de travail.
   ![Créer un espace de travail Log Analytics](./media/secure-aad-app/sentinel-log-analytics-create.png)

   *Créer un espace de travail Log Analytics*

   3. Utilisez la zone de recherche pour rechercher **Azure Sentinel**.

   ![Rechercher Azure Sentinel](./media/secure-aad-app/sentinel-add.png)

   *Rechercher Azure Sentinel*

   4. Sélectionnez **Ajouter**, puis l'espace de travail Log Analytics que vous avez sélectionné précédemment.

   ![Ajouter un espace Log Analytics](./media/secure-aad-app/sentinel-workspace-add.png)

   *Ajouter un espace de travail Log Analytics*

   5. Dans la page **Azure Sentinel - Connecteurs de données**, sous **Configuration**, sélectionnez **Connecteurs de données**. Vous pouvez lier le tableau des services Azure qui s'affiche à l’instance de stockage Log Analytics à des fins d'analyse dans Azure Sentinel.

   ![Connecteurs de données Log Analytics](./media/secure-aad-app/sentinel-connectors.png)

      *Ajouter un connecteur de données à Azure Sentinel*

   Par exemple, pour connecter la passerelle Application Gateway, procédez comme suit :

   1. Ouvrez le panneau de l'instance Azure Application Gateway.
   2. Sous **Supervision**, sélectionnez **Paramètres de diagnostic**.
   3. Sélectionnez **Ajouter le paramètre de diagnostic**.

   ![Ajouter des diagnostics Application Gateway](./media/secure-aad-app/sentinel-gateway-connector.png)
         
   *Ajouter des diagnostics Application Gateway*

   4. Dans la page **Paramètres de diagnostic**, sélectionnez l’espace de travail log Analytics que vous avez créé, puis toutes les métriques que vous souhaitez collecter et envoyer à Azure Sentinel. Sélectionnez **Enregistrer**.

   ![Paramètres du connecteur Azure Sentinel](./media/secure-aad-app/sentinel-connector-settings.png)



## <a name="cost-considerations"></a>Considérations relatives au coût
   Si vous n'avez pas encore de compte Azure, vous pouvez en créer un gratuitement. Pour commencer, accédez à la [page de compte gratuit](https://azure.microsoft.com/free/), voyez ce que vous pouvez faire avec un compte Azure gratuit et découvrez les produits gratuits pendant 12 mois.

   Pour déployer les ressources dans l’exemple d’application avec les fonctionnalités de sécurité, vous devez payer certaines fonctionnalités Premium. À mesure que l'application évolue et que les niveaux et essais gratuits offerts par Azure doivent être mis à niveau pour répondre aux besoins de l’application, les coûts sont susceptibles d'augmenter. Utiliser la [calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/) pour estimer les coûts.

## <a name="next-steps"></a>Étapes suivantes
   Les articles suivants peuvent vous aider à concevoir, développer et déployer des applications sécurisées.

- [Conception](secure-design.md)
- [Développement](secure-develop.md)
- [Déployer](secure-deploy.md)
