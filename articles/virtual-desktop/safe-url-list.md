---
title: Liste des URL sécurisées Windows Virtual Desktop - Azure
description: Liste des URL que vous devez débloquer pour que votre déploiement Windows Virtual Desktop fonctionne comme prévu.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 07/15/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9f7a3b51afa11562123a280da8634e100a22e6b6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075603"
---
# <a name="safe-url-list"></a>Liste des URL sécurisées

Vous devez débloquer certaines URL pour le bon fonctionnement de votre déploiement Windows Virtual Desktop. Cet article répertorie ces URL pour vous permettre de déterminer celles qui sont sécurisées.

## <a name="virtual-machines"></a>Machines virtuelles

Les machines virtuelles Azure que vous créez pour Windows Virtual Desktop doivent avoir accès aux URL suivantes :

|Adresse|Port TCP sortant|Objectif|Balise du service|
|---|---|---|---|
|*.wvd.microsoft.com|443|Trafic de service|WindowsVirtualDesktop|
|mrsglobalsteus2prod.blob.core.windows.net|443|Agent et mises à jour de pile SXS|AzureCloud|
|*.core.windows.net|443|Trafic de l’agent|AzureCloud|
|*.servicebus.windows.net|443|Trafic de l’agent|AzureCloud|
|prod.warmpath.msftcloudes.com|443|Trafic de l’agent|AzureCloud|
|catalogartifact.azureedge.net|443|Place de marché Azure|AzureCloud|
|kms.core.windows.net|1688|Activation de Windows|Internet|
|wvdportalstorageblob.blob.core.windows.net|443|Prise en charge du portail Azure|AzureCloud|
| 169.254.169.254 | 80 | [Point de terminaison Azure Instance Metadata Service](../virtual-machines/windows/instance-metadata-service.md) | N/A |

>[!IMPORTANT]
>Windows Virtual Desktop prend désormais en charge l’étiquette FQDN. Pour plus d’informations, consultez [Utiliser le pare-feu Azure pour protéger les déploiements de Windows Virtual Desktop](../firewall/protect-windows-virtual-desktop.md).
>
>Nous vous recommandons d’utiliser des étiquettes de nom de domaine complet ou de service à la place des URL pour éviter tout problème lié aux services. Les étiquettes et URL répertoriées correspondent uniquement aux sites et ressources Windows Virtual Desktop. Elles n’incluent pas les URL d’autres services comme Azure Active Directory.

Le tableau suivant liste les URL facultatives auxquelles vos machines virtuelles Azure peuvent accéder :

|Adresse|Port TCP sortant|Objectif|Étiquette du service|
|---|---|---|---|
|*.microsoftonline.com|443|Authentification auprès de Microsoft Online Services|None|
|*.events.data.microsoft.com|443|Service de télémétrie|None|
|www.msftconnecttest.com|443|Détecte si l’interface est connectée à internet|None|
|*.prod.do.dsp.mp.microsoft.com|443|Windows Update|None|
|login.windows.net|443|Se connecter à Microsoft Online Services, Microsoft 365|None|
|*.sfx.ms|443|Mises à jour pour le logiciel client OneDrive|None|
|*.digicert.com|443|Vérification de la révocation de certificat|None|

>[!NOTE]
>Actuellement, Windows Virtual Desktop ne dispose pas d’une liste de plages d’adresses IP que vous pouvez débloquer pour autoriser le trafic réseau. Pour le moment, seules certaines URL spécifiques peuvent être débloquées.
>
>Pour obtenir la liste des URL sécurisées liées à Office, notamment les URL liées à Azure Active Directory obligatoires, consultez [URL et plages d’adresses IP Office 365](/office365/enterprise/urls-and-ip-address-ranges).
>
>Vous devez utiliser le caractère générique (*) pour les URL impliquant du trafic de service. Si vous préférez ne pas utiliser * pour le trafic lié à l’agent, voici comment trouver les URL sans caractères génériques :
>
>1. Inscrivez vos machines virtuelles dans le pool d’hôtes Windows Virtual Desktop.
>2. Ouvrez **Observateur d’événements**, accédez à **Journaux Windows** > **Application** > **WVD-Agent**, puis recherchez l’ID d’événement 3701.
>3. Placez en liste verte les URL que vous trouvez sous l’ID d’événement 3701. Les URL sous l’ID d’événement 3701 sont spécifiques à la région. Vous devez répéter le processus de déblocage avec les URL appropriées pour chaque région où vous souhaitez déployer vos machines virtuelles.

## <a name="remote-desktop-clients"></a>Clients Bureau à distance

Les clients Bureau à distance que vous utilisez doivent avoir accès aux URL suivantes :

|Adresse|Port TCP sortant|Objectif|Client(s)|
|---|---|---|---|
|*.wvd.microsoft.com|443|Trafic de service|Tous|
|*.servicebus.windows.net|443|Résolution des problèmes de données|Tous|
|go.microsoft.com|443|Microsoft FWLinks|Tous|
|aka.ms|443|Réducteur d’URL Microsoft|Tous|
|docs.microsoft.com|443|Documentation|Tous|
|privacy.microsoft.com|443|Déclaration de confidentialité|Tous|
|query.prod.cms.rt.microsoft.com|443|Mises à jour de client|Windows Desktop|

>[!IMPORTANT]
>L’ouverture de ces URL est essentielle pour une expérience client fiable. Il n’est pas possible de bloquer l’accès à ces URL, car cela affecterait le fonctionnement du service. Ces URL correspondent seulement aux sites et ressources du client, et n’incluent pas les URL d’autres services comme Azure Active Directory.
