---
title: Verrouillage du trafic sortant de l’environnement Azure App Service
description: Décrit l’intégration du pare-feu Azure pour sécuriser le trafic sortant
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/24/2018
ms.author: ccompy
ms.openlocfilehash: ce0123528b3fb2454d8b83d59b5916363ae0e944
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51251574"
---
# <a name="locking-down-an-app-service-environment"></a>Verrouiller un environnement App Service

L’environnement ASE (App Service Environment) présente des dépendances externes auxquelles il doit accéder pour fonctionner correctement. L’environnement ASE se trouve dans le réseau virtuel Azure (VNet) des clients. Les clients doivent autoriser le trafic des dépendances de l’environnement ASE, ce qui représente un problème pour ceux qui souhaitent verrouiller tous les sorties provenant de leur réseau virtuel.

Un environnement ASE présente des dépendances entrantes. Le trafic de gestion entrant ne peut pas être envoyé via un dispositif de pare-feu. Les adresses sources de ce trafic sont connues et publiées dans le document [Adresses de gestion App Service Environment](https://docs.microsoft.com/azure/app-service/environment/management-addresses). Vous pouvez créer des règles de groupe de sécurité réseau avec ces informations pour sécuriser le trafic entrant.

Les dépendances sortantes de l’environnement ASE sont presque entièrement définies avec des noms FQDN, qui n’ont pas d’adresses statiques derrière eux. L’absence d’adresses statiques signifie que les groupes de sécurité réseau (NSG) ne peuvent pas être utilisés pour verrouiller le trafic sortant d’un environnement ASE. Les adresses changent assez souvent et il n’est donc pas possible de définir des règles basées sur la résolution actuelle et de les utiliser pour créer des groupes NSG. 

La solution pour sécuriser les adresses sortantes réside dans l’utilisation d’un dispositif de pare-feu pouvant contrôler le trafic sortant en fonction des noms de domaine. Le Pare-feu Azure peut restreindre le trafic HTTP et HTTPS sortant en fonction du nom de domaine complet de la destination.  

## <a name="configuring-azure-firewall-with-your-ase"></a>Configuration du pare-feu Azure avec votre environnement ASE 

Les étapes pour verrouiller les sorties de votre environnement ASE avec le pare-feu Azure sont les suivantes :

1. Créez un pare-feu Azure dans le réseau virtuel où est ou sera votre environnement ASE. [Documentation du pare-feu Azure](https://docs.microsoft.com/azure/firewall/)
2. Dans l’interface utilisateur du pare-feu Azure, sélectionnez l’étiquette App Service Environment FQDN.
3. Créez une table de routage avec les adresses de gestion provenant des [adresses de gestion App Service Environment]( https://docs.microsoft.com/azure/app-service/environment/management-addresses) avec un tronçon suivant Internet. Les entrées de la table de routage sont nécessaires pour éviter des problèmes de routage asymétrique.
4. Ajoutez des routes pour les dépendances d’adresse IP indiquées ci-dessous dans les dépendances d’adresse IP avec un tronçon suivant Internet.
5. Ajoutez une route à votre table de routage pour 0.0.0.0/0 avec comme tronçon suivant de votre pare-feu Azure.
6. Créez des points de terminaison de service pour votre sous-réseau ASE vers Azure SQL et Stockage Azure.
7. Affectez la table de routage que vous avez créée à votre sous-réseau ASE.

## <a name="application-traffic"></a>Trafic des applications 

Les étapes ci-dessus permettent à votre environnement ASE de fonctionner sans problème. Vous devrez tout de même configurer des éléments pour répondre aux besoins de vos applications. Dans un environnement ASE configuré avec le pare-feu Azure, les applications font face à deux problèmes.  

- Les noms FQDN des dépendances des applications doivent être ajoutés au pare-feu Azure ou à la table de routage.
- Les routes doivent être créées pour les adresses d’où provient le trafic pour éviter des problèmes de routage asymétrique.

Si vos applications ont des dépendances, celles-ci doivent être ajoutées à votre pare-feu Azure. Créez des règles d’application pour autoriser le trafic HTTP/HTTPS et des règles de réseau pour tout le reste. 

Si vous connaissez la plage d’adresses d’où provient le trafic de demande de vos applications, vous pouvez l’ajouter dans la table de routage qui est affectée à votre sous-réseau ASE. Si la plage d’adresses est grande ou non spécifiée, vous pouvez utiliser une appliance réseau comme la passerelle Application Gateway qui vous donnera une adresse à ajouter à votre table de routage. Pour plus d’informations sur la configuration d’une passerelle d’application avec votre environnement ASE ILB, lisez [Intégration de votre environnement App Service ILB à une passerelle d’application](https://docs.microsoft.com/azure/app-service/environment/integrate-with-application-gateway)


## <a name="dependencies"></a>Les dépendances

Azure App Service présente des dépendances externes. Elles peuvent être divisées en plusieurs grandes catégories :

- Des services compatibles avec les points de terminaison de service doivent être configurés avec des points de terminaison de service si vous souhaitez verrouiller le trafic réseau sortant.
- Les points de terminaison d’adresse IP n’ont pas de nom de domaine. Cela peut poser problème pour les dispositifs de pare-feu qui s’attendent à ce que tout le trafic HTTPS utilise des noms de domaine. Les points de terminaison d’adresse IP doivent être ajoutés à la table de routage définie sur le sous-réseau ASE.
- Les points de terminaison HTTP/HTTPS avec des noms FQDN peuvent être placés dans votre dispositif de pare-feu.
- Les points de terminaison HTTP/HTTPS avec des caractères génériques sont des dépendances qui peuvent varier avec votre environnement ASE selon le nombre de qualificateurs. 
- Les dépendances Linux sont uniquement un problème si vous déployez des applications Linux dans votre environnement ASE. Si vous ne déployez pas d’applications Linux dans votre environnement ASE, vous n’avez pas besoin d’ajouter ces adresses à votre pare-feu. 


#### <a name="service-endpoint-capable-dependencies"></a>Dépendances compatibles avec les points de terminaison de service 

| Point de terminaison |
|----------|
| Azure SQL |
| Stockage Azure |
| Azure KeyVault |


#### <a name="ip-address-dependencies"></a>Dépendances des adresses IP 

| Point de terminaison |
|----------|
| 40.77.24.27:443 |
| 13.82.184.151:443 |
| 13.68.109.212:443 |
| 13.90.249.229:443 |
| 13.91.102.27:443 |
| 104.45.230.69:443 |
| 168.62.226.198:12000 |


#### <a name="fqdn-httphttps-dependencies"></a>Dépendances HTTP/HTTPS FQDN 

| Point de terminaison |
|----------|
|graph.windows.net:443 |
|login.live.com:443 |
|login.windows.com:443 |
|login.windows.net:443 |
|login.microsoftonline.com:443 |
|client.wns.windows.com:443 |
|definitionupdates.microsoft.com:443 |
|go.microsoft.com:80 |
|go.microsoft.com:443 |
|www.microsoft.com:80 |
|www.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|ocsp.msocsp.com:443 |
|mscrl.microsoft.com:443 |
|mscrl.microsoft.com:80 |
|crl.microsoft.com:443 |
|crl.microsoft.com:80 |
|www.thawte.com:443 |
|crl3.digicert.com:80 |
|ocsp.digicert.com:80 |
|csc3-2009-2.crl.verisign.com:80 |
|crl.verisign.com:80 |
|ocsp.verisign.com:80 |
|azperfcounters1.blob.core.windows.net:443 |
|azurewatsonanalysis-prod.core.windows.net:443 |
|global.metrics.nsatc.net:80   |
|az-prod.metrics.nsatc.net:443 |
|antares.metrics.nsatc.net:443 |
|azglobal-black.azglobal.metrics.nsatc.net:443 |
|azglobal-red.azglobal.metrics.nsatc.net:443 |
|antares-black.antares.metrics.nsatc.net:443 |
|antares-red.antares.metrics.nsatc.net:443 |
|maupdateaccount.blob.core.windows.net:443 |
|clientconfig.passport.net:443 |
|packages.microsoft.com:443 |
|schemas.microsoft.com:80 |
|schemas.microsoft.com:443 |
|management.core.windows.net:443 |
|management.core.windows.net:80 |
|www.msftconnecttest.com:80 |
|shavamanifestcdnprod1.azureedge.net:443 |
|validation-v2.sls.microsoft.com:443 |
|flighting.cp.wd.microsoft.com:443 |
|dmd.metaservices.microsoft.com:80 |
|admin.core.windows.net:443 |
|azureprofileruploads.blob.core.windows.net:443 |
|azureprofileruploads2.blob.core.windows.net:443 |
|azureprofileruploads3.blob.core.windows.net:443 |
|azureprofileruploads4.blob.core.windows.net:443 |
|azureprofileruploads5.blob.core.windows.net:443 |

#### <a name="wildcard-httphttps-dependencies"></a>Dépendances HTTP/HTTPS avec caractères génériques 

| Point de terminaison |
|----------|
|gr-Prod-\*.cloudapp.net:443 |
| \*.management.azure.com:443 |
| \*.update.microsoft.com:443 |
| \*.windowsupdate.microsoft.com:443 |
|grmdsprod\*mini\*.servicebus.windows.net:443 |
|grmdsprod\*lini\*.servicebus.windows.net:443 |
|grsecprod\*mini\*.servicebus.windows.net:443 |
|grsecprod\*lini\*.servicebus.windows.net:443 |
|graudprod\*mini\*.servicebus.windows.net:443 |
|graudprod\*lini\*.servicebus.windows.net:443 |

#### <a name="linux-dependencies"></a>Dépendances Linux 

| Point de terminaison |
|----------|
|wawsinfraprodbay063.blob.core.windows.net:443 |
|registry-1.docker.io:443 |
|auth.docker.io:443 |
|production.cloudflare.docker.com:443 |
|download.docker.com:443 |
|us.archive.ubuntu.com:80 |
|download.mono-project.com:80 |
|packages.treasuredata.com:80|
|security.ubuntu.com:80 |

