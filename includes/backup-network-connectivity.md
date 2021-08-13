---
title: Fichier include
description: Fichier include
services: backup
author: v-amallick
manager: carmonm
ms.service: backup
ms.topic: include
ms.date: 06/04/2021
ms.author: v-amallick
ms.custom: include file
ms.openlocfilehash: 36a45be18e5614371e3e29dc2907f5c25ba772b9
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111952981"
---
L’agent MARS nécessite l’accès aux points de terminaison de service Azure Active Directory, Stockage Azure et Sauvegarde Azure. Pour obtenir les plages d’adresses IP publiques, consultez le [fichier JSON](https://www.microsoft.com/en-us/download/confirmation.aspx?id=56519&preserveview=true). Autorisez l’accès aux adresses IP correspondant à Sauvegarde Azure (AzureBackup), Stockage Azure (Storage) et Azure Active Directory (AzureActiveDirectory). En outre, selon votre version de Windows, les vérifications de la connectivité réseau du système d’exploitation auront besoin d’un accès à `www.msftconnecttest.com` ou `www.msftncsi.com`.

Si votre machine dispose d’un accès limité à Internet, assurez-vous que les paramètres du pare-feu, du proxy et du réseau autorisent l’accès aux noms de domaine complets et aux IP publiques suivants.

### <a name="url-and-ip-access"></a>URL et accès IP

**Noms de domaine complets**

- `www.msftncsi.com`
- `www.msftconnecttest.com`
- *.microsoft.com
- *.windowsazure.com
- *.microsoftonline.com
- \* .windows.net

**Adresses IP**

- 20.190.128.0/18
- 40.126.0.0/18

Si vous êtes un client du gouvernement des États-Unis, vérifiez que vous avez accès aux URL suivantes :

- `www.msftncsi.com`
- *.microsoft.com
- *.windowsazure.us
- *.microsoftonline.us
- \* .windows.net
- *.usgovcloudapi.net

L’accès à toutes les URL et adresses IP listées ci-dessus utilise le protocole HTTPS sur le port 443.

Lorsque vous sauvegardez des fichiers et des dossiers à partir de machines virtuelles Azure à l’aide de l’agent MARS, vous devez également configurer le réseau virtuel Azure pour autoriser l’accès. Si vous utilisez des groupes de sécurité réseau (NSG), utilisez la balise de service AzureBackup pour autoriser l’accès sortant vers Sauvegarde Azure. En plus de l’étiquette pour Sauvegarde Azure, vous devez également autoriser la connectivité pour l’authentification et le transfert de données en créant des [règles NSG](../articles/virtual-network/network-security-groups-overview.md#service-tags) similaires pour Azure AD (AzureActiveDirectory) et Stockage Azure (Storage).

Afin de créer une règle pour l’étiquette de Sauvegarde Azure, procédez comme suit :

1. Dans **Tous les services**, accédez à **Groupes de sécurité réseau** et sélectionnez le groupe de sécurité réseau.
1. Sous **PARAMÈTRES**, sélectionnez **Règles de sécurité de trafic sortant**.
1. Sélectionnez **Ajouter**.
1. Fournissez toutes les informations nécessaires à la création d’une nouvelle règle, comme décrit dans les [paramètres de règle de sécurité](../articles/virtual-network/manage-network-security-group.md#security-rule-settings).<br>Vérifiez que les options sont définies comme suit :
   - **Destination** est définie sur _Étiquette de service_.
   - **Étiquette de service de destination** est définie sur _AzureBackup_.
1. Sélectionnez **Ajouter** pour enregistrer la règle de sécurité de trafic sortant que vous venez de créer.

De même, vous pouvez créer des règles de sécurité de trafic sortant NSG pour Stockage Azure et Azure AD. Pour en savoir plus sur les étiquettes de service, consultez l’article [Étiquettes de service de réseau virtuel](../articles/virtual-network/service-tags-overview.md).

### <a name="azure-expressroute-support"></a>Support Azure ExpressRoute

Vous pouvez sauvegarder vos données par l’intermédiaire d’Azure ExpressRoute en utilisant le Peering public (disponible pour les anciens circuits). Nous ne prenons pas en charge la sauvegarde du Peering Microsoft sur un Peering privé.

Pour utiliser le Peering public, assurez-vous que les adresses et domaines suivants disposent d’un accès HTTPS sur le port 443 à :

- `www.msftncsi.com`
- `www.msftconnecttest.com`
- *.microsoft.com
- *.windowsazure.com
- *.microsoftonline.com
- \* .windows.net

**Adresses IP**
- 20.190.128.0/18
- 40.126.0.0/18

Pour utiliser le peering Microsoft, sélectionnez les services, les régions et les valeurs de communauté pertinentes suivants :
- Azure Active Directory (12076:5060)
- Région Azure, en fonction de la localisation de votre coffre Recovery Services
- Stockage Azure, en fonction de la localisation de votre coffre Recovery Services

En savoir plus sur la [configuration requise pour le routage ExpressRoute](../articles/expressroute/expressroute-routing.md#bgp).

>[!NOTE]
>Le peering public est déprécié pour les nouveaux circuits.


### <a name="private-endpoint-support"></a>Prise en charge d'un point de terminaison privé

Vous pouvez désormais utiliser des points de terminaison privés pour sauvegarder en toute sécurité les données des serveurs vers votre coffre Recovery Services. Étant donné qu’il est impossible d’accéder à Azure AD via des points de terminaison privés, vous devez autoriser séparément les adresses IP et les noms de domaine complets nécessaires à Azure AD pour l’accès sortant.

Lorsque vous utilisez l’agent MARS pour sauvegarder vos ressources locales, assurez-vous que votre réseau local (contenant vos ressources à sauvegarder) est appairé au réseau virtuel Azure qui contient un point de terminaison privé pour le coffre. Vous pouvez ensuite continuer à installer l’agent MARS et configurer la sauvegarde. Toutefois, vous devez vous assurer que toutes les communications pour la sauvegarde s’effectuent uniquement par le biais du réseau homologué.

Si vous supprimez des points de terminaison privés pour le coffre après l’enregistrement d’un agent MARS, vous devez réinscrire le conteneur auprès du coffre. Vous n’avez pas besoin d’arrêter leur protection.
En savoir plus sur les [points de terminaison privés pour Sauvegarde Azure](../articles/backup/private-endpoints.md).

### <a name="throttling-support"></a>Prise en charge de la limitation

**Fonctionnalité** | **Détails**
--- | ---
Contrôle de la bande passante | Pris en charge. Dans l’agent MARS, utilisez **Changer les propriétés** pour ajuster la bande passante.
Limitation du réseau | Non disponible pour les machines sauvegardées qui exécutent Windows Server 2008 R2, Windows Server 2008 SP2 ou Windows 7.
