---
title: Comment configurer Palo Alto pour Azure Spring Cloud
description: Comment configurer Palo Alto pour Azure Spring Cloud
author: karlerickson
ms.author: vaangadi
ms.topic: how-to
ms.service: spring-cloud
ms.date: 09/17/2021
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: a65a6ea1fb2070b6a1879521b257c2738930bf3a
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368474"
---
# <a name="how-to-configure-palo-alto-for-azure-spring-cloud"></a>Comment configurer Palo Alto pour Azure Spring Cloud

**Cet article s’applique à :** ✔️ Java ✔️ C#

Cet article explique comment utiliser Azure Spring Cloud avec un pare-feu Palo Alto.

Par exemple, l’[architecture de référence d’Azure Spring Cloud](/azure/spring-cloud/reference-architecture) comprend un pare-feu Azure pour sécuriser vos applications. Toutefois, si vos déploiements actuels incluent un pare-feu Palo Alto, vous pouvez omettre le pare-feu Azure du déploiement d’Azure Spring Cloud et utiliser Palo Alto à la place, comme décrit dans cet article.

Vous devez conserver les informations de configuration, telles que les règles et les caractères génériques d’adresse, dans des fichiers CSV dans un référentiel Git. Cet article explique comment utiliser l’automatisation pour appliquer ces fichiers à Palo Alto. Pour comprendre la configuration à appliquer à Palo Alto, consultez [Responsabilités du client pour l’exécution d’Azure Spring Cloud dans un réseau virtuel](/azure/spring-cloud/vnet-customer-responsibilities). 

> [!Note]
> Dans la description de l’utilisation des API REST, cet article utilise la syntaxe des variables PowerShell pour indiquer les noms et les valeurs qui sont laissés à votre discrétion. Veillez à utiliser les mêmes valeurs dans toutes les étapes.
>
> Après avoir configuré le certificat TLS/SSL dans Palo Alto, supprimez l’argument `-SkipCertificateCheck` de tous les appels d’API REST de Palo Alto dans les exemples ci-dessous.
>
> Vous ne devez pas utiliser cet article comme référence pour les API REST de Palo Alto. Tous les exemples sont fournis à titre de démonstration uniquement. Pour des détails sur l’API faisant autorité, consultez [PAN-OS REST API](https://docs.paloaltonetworks.com/pan-os/9-1/pan-os-panorama-api/get-started-with-the-pan-os-rest-api/pan-os-rest-api.html) (API REST PAN-OS) dans la documentation de Palo Alto.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
* Un déploiement Palo Alto. Si vous n’avez pas de déploiement, vous pouvez approvisionner [Palo Alto à partir de Place de marché Azure](https://ms.portal.azure.com/#create/paloaltonetworks.vmseries-ngfwbundle2).
* [PowerShell](/powershell/scripting/install/installing-powershell)
* [Azure CLI](/cli/azure/install-azure-cli)

## <a name="configure-palo-alto"></a>Configurer Palo Alto

Tout d’abord, configurez le pare-feu VM-Series de Palo Alto. Pour obtenir des instructions détaillées, consultez [Deploy the VM-Series Firewall from the Azure Marketplace (Solution Template)](https://docs.paloaltonetworks.com/vm-series/9-1/vm-series-deployment/set-up-the-vm-series-firewall-on-azure/deploy-the-vm-series-firewall-on-azure-solution-template.html) [Déployer le pare-feu VM-Series à partir de Place de marché Azure (modèle de solution)]. Ces instructions vous aideront à approvisionner un pare-feu VM-Series et à configurer les sous-réseaux `Trust` et `UnTrust` ainsi que les cartes d’interface réseau associées. Pour rester cohérent, vous devez créer ce pare-feu dans l’espace d’adressage du réseau virtuel `Hub` dans l’architecture de référence.

Le [guide d’architecture de référence pour Azure](https://www.paloaltonetworks.com/resources/guides/azure-architecture-guide) explore plusieurs modèles de conception technique pour le déploiement du pare-feu sur Azure.

Le reste de cet article suppose que vous disposez des deux zones réseau préconfigurées suivantes :

* `Trust`, contenant l’interface connectée à un réseau virtuel appairé avec le réseau virtuel Azure Spring Cloud.
* `UnTrust`, contenant l’interface vers l’Internet public créé précédemment dans le guide de déploiement de VM-Series.

## <a name="prepare-csv-files"></a>Préparer les fichiers CSV

Créez ensuite trois fichiers CSV.

Nommez le premier fichier *AzureSpringCloudServices.csv*. Ce fichier doit contenir des ports d’entrée pour Azure Spring Cloud. Les valeurs indiquées dans l’exemple suivant sont uniquement fournies à titre de démonstration. Pour toutes les valeurs requises, consultez la section [Conditions requises concernant le réseau d’Azure Spring Cloud](/azure/spring-cloud/vnet-customer-responsibilities#azure-spring-cloud-network-requirements) dans [Responsabilités du client pour l’exécution d’Azure Spring Cloud dans un réseau virtuel](/azure/spring-cloud/vnet-customer-responsibilities).

```CSV
name,protocol,port,tag
ASC_1194,udp,1194,AzureSpringCloud
ASC_443,tcp,443,AzureSpringCloud
ASC_9000,tcp,9000,AzureSpringCloud
ASC_445,tcp,445,AzureSpringCloud
ASC_123,udp,123,AzureSpringCloud
```

Nommez le deuxième fichier *AzureSpringCloudUrlCategories.csv*. Ce fichier doit contenir les adresses (avec des caractères génériques) qui doivent être disponibles pour la sortie d’Azure Spring Cloud. Les valeurs indiquées dans l’exemple suivant sont uniquement fournies à titre de démonstration. Pour obtenir des valeurs à jour, consultez [Conditions requises/règles d’application concernant le nom de domaine complet d’Azure Spring Cloud](/azure/spring-cloud/vnet-customer-responsibilities#azure-spring-cloud-fqdn-requirementsapplication-rules).

```CSV
name,description
*.azmk8s.io,
mcr.microsoft.com,
*.cdn.mscr.io,
*.data.mcr.microsoft.com,
management.azure.com,
*.microsoftonline.com,
*.microsoft.com,
packages.microsoft.com,
acs-mirror.azureedge.net,
mscrl.microsoft.com,
crl.microsoft.com,
crl3.digicert.com
```

Nommez le troisième fichier *AzureMonitorAddresses.csv*. Ce fichier doit contenir toutes les adresses et plages d’adresses IP à rendre disponibles pour les métriques et la surveillance via Azure Monitor, si vous utilisez Azure Monitor. Les valeurs indiquées dans l’exemple suivant sont uniquement fournies à titre de démonstration. Pour obtenir des valeurs à jour, consultez [Adresses IP utilisées par Azure Monitor](/azure/azure-monitor/app/ip-addresses).

```CSV
name,type,address,tag
40.114.241.141,ip-netmask,40.114.241.141/32,AzureMonitor
104.45.136.42,ip-netmask,104.45.136.42/32,AzureMonitor
40.84.189.107,ip-netmask,40.84.189.107/32,AzureMonitor
168.63.242.221,ip-netmask,168.63.242.221/32,AzureMonitor
52.167.221.184,ip-netmask,52.167.221.184/32,AzureMonitor
live.applicationinsights.azure.com,fqdn,live.applicationinsights.azure.com,AzureMonitor
rt.applicationinsights.microsoft.com,fqdn,rt.applicationinsights.microsoft.com,AzureMonitor
rt.services.visualstudio.com,fqdn,rt.services.visualstudio.com,AzureMonitor

```

## <a name="authenticate-into-palo-alto"></a>S’authentifier auprès de Palo Alto

Ensuite, vous devez vous authentifier auprès de Palo Alto et obtenir une clé API. Pour plus d’informations, consultez [Get Your API Key](https://docs.paloaltonetworks.com/pan-os/9-1/pan-os-panorama-api/get-started-with-the-pan-os-xml-api/get-your-api-key.html) (Obtenir votre clé API) dans la documentation de Palo Alto.

L’exemple suivant utilise PowerShell pour s’authentifier et générer des en-têtes de demande qui seront utilisés plus loin dans cet article :

```powershell
$username=<username for PaloAlto>
$password=<password for PaloAlto>
$authResponse = irm "https://${PaloAltoIpAddress}/api/?type=keygen&user=${username}&password=${password}" -SkipCertificateCheck
$paloAltoHeaders = @{'X-PAN-KEY' = $authResponse.response.result.key; 'Content-Type' = 'application/json' }
```

## <a name="delete-existing-service-group"></a>Supprimer un groupe de services existant

Si vous avez effectué des tentatives de configuration antérieures, vous devez réinitialiser ces configurations et supprimer toutes les règles de sécurité et tous les groupes de services.

Supprimez la règle de sécurité à l’aide de l’[API REST de règle de sécurité](https://docs.paloaltonetworks.com/pan-os/9-1/pan-os-panorama-api/get-started-with-the-pan-os-rest-api/create-security-policy-rule-rest-api.html), comme illustré dans l’exemple suivant :

```powershell
$url = "https://${PaloAltoIpAddress}/restapi/v9.1/Policies/SecurityRules?location=vsys&vsys=vsys1&name=${paloAltoSecurityPolicyName}"
Invoke-RestMethod -Method Delete -Uri $url -Headers $paloAltoHeaders -SkipCertificateCheck
```

Supprimez le groupe de services comme indiqué dans l’exemple suivant :

```powershell
$url = "https://${PaloAltoIpAddress}/restapi/v9.1/Objects/ServiceGroups?location=vsys&vsys=vsys1&name=${paloAltoServiceGroupName}"
Invoke-RestMethod -Method Delete -Uri $url -Headers $paloAltoHeaders -SkipCertificateCheck
```

Supprimez chaque service Palo Alto (tel que défini dans *AzureSpringCloudServices.csv*) comme indiqué dans l’exemple suivant :

```powershell
Get-Content .\AzureSpringCloudServices.csv | ConvertFrom-Csv | select name | ForEach-Object {
    $url = "https://${PaloAltoIpAddress}/restapi/v9.1/Objects/Services?location=vsys&vsys=vsys1&name=${_}"
    Invoke-RestMethod -Method Delete -Uri $url -Headers $paloAltoHeaders -SkipCertificateCheck
}
```

## <a name="create-a-service-and-service-group"></a>Créer un service et un groupe de services

Pour automatiser la création de services à partir du fichier *AzureSpringCloudServices.csv* que vous avez créé précédemment, utilisez l’exemple suivant.

```powershell
# Define a function to create and submit a Palo Alto service creation request
function New-PaloAltoService {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory = $true, ValueFromPipeline = $true)]
        [PSCustomObject]
        $ServiceObject
    )
    PROCESS {
        $requestBody = @{
            'entry' = @{
                '@name'    = $ServiceObject.name
                'protocol' = @{
                    $ServiceObject.protocol = @{
                        'port'     = $ServiceObject.port
                        'override' = @{
                            'no' = @{}
                        }

                    }
                }
                'tag'      = @{
                    'member' = @($ServiceObject.tag)
                }
            }
        }

        # Some rules in the CSV may need to conain source ports or descriptions. If these are present, populate them in the request
        if ($ServiceObject.description) {
            $requestBody.entry.description = $ServiceObject.description
        }
        if ($ServiceObject.'source-port') {
            $requestBody.entry.protocol."$($ServiceObject.protocol)".'source-port' = $ServiceObject.'source-port'
        }

        # Send the request
        $name = $requestBody.entry.'@name'
        $url = "https://${PaloAltoIpAddress}/restapi/v9.1/Objects/Services?location=vsys&vsys=vsys1&name=${name}"
         Invoke-RestMethod -Method Post -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders -Body (ConvertTo-Json -WarningAction Ignore $requestBody -Depth 9) -Verbose
    }
}

# Now invoke that function for every row in AzureSpringCloudServices.csv
Get-Content ./AzureSpringCloudServices.csv | ConvertFrom-Csv | New-PaloAltoService
```

Ensuite, créez un groupe de services pour ces services, comme illustré dans l’exemple suivant :

```powershell
# Create a function to consume service definitions and submit a service group creation request
function New-PaloAltoServiceGroup {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory = $true, ValueFromPipeline = $true)]
        [PSCustomObject[]]
        $RuleData,

        [Parameter(Mandatory = $true)]
        [string]
        $ServiceGroupName
    )
    begin {
        [array] $names = @()
    }

    process {
        $names += $RuleData.name
    }

    end {
        $requestBody = @{ 'entry' = [ordered] @{
                '@name'   = $ServiceGroupName
                'members' = @{ 'member' = $names }
                'tag'     = @{ 'member' = 'AzureSpringCloud' }
            }
        }

        $url = "https://${PaloAltoIpAddress}/restapi/v9.1/Objects/ServiceGroups?location=vsys&vsys=vsys1&name=${ServiceGroupName}"

        Invoke-RestMethod -Method Post -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders -Body (ConvertTo-Json $requestBody) -Verbose
    }
}

# Run that function for all services in AzureSpringCloudServices.csv.
Get-Content ./AzureSpringCloudServices.csv | ConvertFrom-Csv | New-PaloAltoServiceGroup -ServiceGroupName 'AzureSpringCloud_SG'
```

## <a name="create-custom-url-categories"></a>Créer des catégories d’URL personnalisées

Ensuite, définissez des catégories d’URL personnalisées pour le groupe de services afin de permettre la sortie d’Azure Spring Cloud, comme illustré dans l’exemple suivant.

```powershell
# Read Service entries from CSV to enter into Palo Alto
$csvImport = Get-Content ${PSScriptRoot}/AzureSpringCloudUrls.csv | ConvertFrom-Csv

# Convert name column of CSV to add to the Custom URL Group in Palo Alto
$requestBody = @{ 'entry' = [ordered] @{
        '@name' = 'AzureSpringCloud_SG'
        'list'  = @{ 'member' = $csvImport.name }
        'type'  = 'URL List'
    }
} | ConvertTo-Json -Depth 9

$url = "https://${PaloAltoIpAddress}/restapi/v9.1/Objects/CustomURLCategories?location=vsys&vsys=vsys1&name=AzureSpringCloud_SG"

try {
    $existingObject = Invoke-RestMethod -Method Get -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders
    Invoke-RestMethod -Method Delete -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders
}
catch {
}

Invoke-RestMethod -Method Post -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders -Body $requestBody -Verbose
```

## <a name="create-a-security-rule"></a>Créer une règle de sécurité

Ensuite, créez un fichier JSON pour contenir une règle de sécurité. Nommez le fichier *SecurityRule.json* et ajoutez-y le contenu suivant. Les noms des deux zones `Trust` et `UnTrust` correspondent aux noms de zone décrits précédemment dans la section [Configurer Palo Alto](#configure-palo-alto). L’entrée `service/member` contient le nom du groupe de services créé au cours des étapes précédentes.

```json
{
    "entry": [
        {
            "@name": "azureSpringCloudRule",
            "@location": "vsys",
            "@vsys": "vsys1",
            "to": {
                "member": [
                    "UnTrust"
                ]
            },
            "from": {
                "member": [
                    "Trust"
                ]
            },
            "source-user": {
                "member": [
                    "any"
                ]
            },
            "application": {
                "member": [
                    "any"
                ]
            },
            "service": {
                "member": [
                    "AzureSpringCloud_SG"
                ]
            },
            "hip-profiles": {
                "member": [
                    "any"
                ]
            },
            "action": "allow",
            "category": {
                "member": [
                    "any"
                ]
            },
            "source": {
                "member": [
                    "any"
                ]
            },
            "destination": {
                "member": [
                    "any"
                ]
            }
        }
    ]
}
```

À présent, appliquez cette règle à Palo Alto, comme illustré dans l’exemple suivant.

```powershell
$url = "https://${PaloAltoIpAddress}/restapi/v9.1/Policies/SecurityRules?location=vsys&vsys=vsys1&name=azureSpringCloudRule"

# Delete the rule if it already exists
try {
    $getResult = Invoke-RestMethod -Headers $paloAltoHeaders -Method Get -SkipCertificateCheck -Uri $url -Verbose
    if ($getResult.'@status' -eq 'success') {
        Invoke-RestMethod -Method Delete  -Headers $paloAltoHeaders -SkipCertificateCheck -Uri $url
    }
}
catch {}

# Create the rule from the JSON file
Invoke-WebRequest -Uri $url -Method Post -Headers $paloAltoHeaders -Body (Get-Content SecurityRule.json) -SkipCertificateCheck
```

## <a name="create-azure-monitor-addresses"></a>Créer des adresses Azure Monitor

Ensuite, utilisez le fichier *AzureMonitorAddresses.csv* pour définir des objets d’adresse dans Palo Alto. L’exemple de code suivant vous montre comment automatiser cette tâche.

```powershell
Get-Content ./AzureMonitorAddresses.csv | ConvertFrom-Csv | ForEach-Object {
    $requestBody = @{ 'entry' = [ordered]@{
            '@name' = $_.name
            $_.type = $_.address
            'tag'   = @{ 'member' = @($_.tag) }
        }
    }

    $name = $requestBody.entry.'@name'
    $url = "https://${PaloAltoIpAddress}/restapi/v9.1/Objects/Addresses?location=vsys&vsys=vsys1&name=${name}"

    # Delete the address if it already exists
    try {
        Invoke-RestMethod -Method Delete -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders
    }
    catch {
    }

    # Create the address
    Invoke-RestMethod -Method Post -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders -Body (ConvertTo-Json -WarningAction Ignore $requestBody -Depth 3) -Verbose
}
```

## <a name="commit-changes-to-palo-alto"></a>Valider les modifications apportées à Palo Alto

Vous devez valider certaines des modifications ci-dessus pour qu’elles deviennent actives. Pour ce faire, vous pouvez utiliser l’appel d’API REST suivant.

```powershell
$url = "https://${PaloAltoIpAddress}/api/?type=commit&cmd=<commit></commit>"
Invoke-RestMethod -Method Get -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders
```

## <a name="configure-the-security-rules-for-azure-spring-cloud-subnets"></a>Configurer les règles de sécurité pour les sous-réseaux d’Azure Spring Cloud

Ensuite, ajoutez des règles de sécurité réseau pour permettre au trafic de Palo Alto d’accéder à Azure Spring Cloud. Les exemples suivants font référence aux groupes de sécurité réseau (NSG) spokes créés par l’architecture de référence : `nsg-spokeapp` et `nsg-spokeruntime`.

Exécutez les commandes Azure CLI suivantes dans une fenêtre PowerShell pour créer la règle de sécurité réseau nécessaire pour chacun de ces NSG, où `$PaloAltoAddressPrefix` est l’adresse CIDR (Classless Inter-Domain Routing) des adresses IP privées de Palo Alto.

```azurecli
az network nsg rule create `
    --resource-group $ResourceGroupName `
    --name 'allow-palo-alto' `
    --nsg-name 'nsg-spokeapp' `
    --access Allow `
    --source-address-prefixes $PaloAltoAddressPrefix `
    --priority 1000
az network nsg rule create `
    --resource-group $ResourceGroupName `
    --name 'allow-palo-alto' `
    --nsg-name 'nsg-spokeruntime' `
    --access Allow `
    --source-address-prefixes $PaloAltoAddressPrefix `
    --priority 1000
```

## <a name="configure-the-next-hop"></a>Configurer le tronçon suivant

Une fois que vous avez configuré Palo Alto, configurez Azure Spring Cloud pour que Palo Alto soit le tronçon suivant pour l’accès Internet sortant. Vous pouvez utiliser les commandes Azure CLI suivantes dans une fenêtre PowerShell pour cette configuration. Veillez à fournir des valeurs pour les variables suivantes :

* `$AppResourceGroupName` : Nom du groupe de ressources qui contient votre instance Azure Spring Cloud.
* `$AzureSpringCloudServiceSubnetRouteTableName` : Nom de la table de routage des sous-réseaux du service/runtime Azure Spring Cloud. Dans l’architecture de référence, ce nom est défini sur `rt-spokeruntime`.
* `$AzureSpringCloudAppSubnetRouteTableName` : Nom de la table de routage des sous-réseaux des applications Azure Spring Cloud. Dans l’architecture de référence, ce nom est défini sur `rt-spokeapp`.

```azurecli
az network route-table route create `
    --resource-group ${AppResourceGroupName} `
    --name default `
    --route-table-name ${AzureSpringCloudServiceSubnetRouteTableName} `
    --address-prefix 0.0.0.0/0 `
    --next-hop-type VirtualAppliance `
    --next-hop-ip-address ${PaloAltoIpAddress} `
    --verbose

az network route-table route create `
    --resource-group ${AppResourceGroupName} `
    --name default `
    --route-table-name ${AzureSpringCloudAppSubnetRouteTableName} `
    --address-prefix 0.0.0.0/0 `
    --next-hop-type VirtualAppliance `
    --next-hop-ip-address ${PaloAltoIpAddress} `
    --verbose
```

Votre configuration est maintenant terminée.

## <a name="next-steps"></a>Étapes suivantes

* [Envoyer en streaming les journaux d’application Azure Spring Cloud en temps réel](/azure/spring-cloud/how-to-log-streaming)
* [Agent In-process Java d’Application Insights dans Azure Spring Cloud](/azure/spring-cloud/how-to-application-insights)
* [Automatiser les déploiements d’applications dans Azure Spring Cloud](/azure/spring-cloud/how-to-cicd)
