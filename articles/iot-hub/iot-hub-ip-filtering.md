---
title: Filtres de connexion IP d’Azure Hub IoT | Microsoft Docs
description: Utilisation de filtres IP pour bloquer les connexions à partir d’adresses IP spécifiques pour votre Azure IoT Hub. Vous pouvez bloquer les connexions à partir d’adresses IP individuelles ou de plages d’adresses IP.
author: rezasherafat
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: rezas
ms.openlocfilehash: 02059409000ee5b68fbcb8695f580980c95effe6
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2019
ms.locfileid: "56731705"
---
# <a name="use-ip-filters"></a>Utiliser des filtres IP

La sécurité est un aspect important de toute solution IoT basée sur Azure IoT Hub. Parfois, dans le cadre de votre configuration de sécurité, vous devez spécifier explicitement les adresses IP à partir desquelles les appareils peuvent se connecter. La fonctionnalité *Filtre IP* vous permet de configurer des règles de rejet ou d’acceptation du trafic provenant de certaines adresses IPv4.

## <a name="when-to-use"></a>Quand utiliser

Il existe deux cas d’utilisation spécifiques illustrant lorsqu’il est utile de bloquer les points de terminaison IoT Hub pour certaines adresses IP :

* Votre IoT Hub ne doit recevoir du trafic qu’à partir d’une plage spécifiée d’adresses IP et rejeter tout le reste. Par exemple, vous utilisez votre IoT Hub avec [Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) pour créer des connexions privées entre un IoT Hub et votre infrastructure locale.

* Vous devez refuser le trafic provenant d’adresses IP qui ont été identifiées comme suspectes par l’administrateur de IoT Hub.

## <a name="how-filter-rules-are-applied"></a>Application des règles de filtre

Les règles de filtre IP sont appliquées au niveau du service IoT Hub. Par conséquent, les règles de filtre IP s’appliquent à toutes les connexions issues des appareils et des applications principales utilisant n’importe quel protocole pris en charge.

Toute tentative de connexion à partir d’une adresse IP qui correspond à une règle IP de rejet dans votre IoT Hub reçoit un code d’état 401 non autorisé et une description. Le message de réponse ne mentionne pas la règle IP.

## <a name="default-setting"></a>Paramètre par défaut

Par défaut, la grille **Filtre IP** dans le portail pour un IoT Hub est vide. Ce paramètre par défaut signifie que votre hub accepte les connexions de n’importe quelle adresse IP. Ce paramètre par défaut est équivalent à une règle qui accepte la plage d’adresses IP 0.0.0.0/0.

![Paramètres de filtre IP par défaut d’IoT Hub](./media/iot-hub-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>Ajouter ou modifier une règle de filtre IP

Lorsque vous ajoutez une règle de filtre IP, vous êtes invité à renseigner les valeurs suivantes :

* Un **nom de règle de filtre IP** qui doit être une chaîne unique, ne respectant pas la casse, alphanumérique, comptant jusqu'à 128 caractères. Seuls les caractères alphanumériques ASCII 7 bits et `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` sont acceptés.

* Sélectionnez **rejeter** ou **accepter** comme **action** pour la règle de filtre IP.

* Fournissez une adresse IPv4 unique ou un bloc d’adresses IP en notation CIDR. Par exemple, dans la notation CIDR, 192.168.100.0/22 représente les 1024 adresses IPv4 allant de 192.168.100.0 à 192.168.103.255.

![Ajouter une règle de filtre IP à un IoT Hub](./media/iot-hub-ip-filtering/ip-filter-add-rule.png)

Une fois la règle enregistrée, une alerte s’affiche vous informant que la mise à jour est en cours.

![Notification sur l’enregistrement d’une règle de filtre IP](./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png)

L’option **Ajouter** est désactivée lorsque vous atteignez le nombre maximal de dix règles de filtre IP.

Vous pouvez modifier une règle existante en double-cliquant sur la ligne qui contient la règle.

> [!NOTE]
> Le rejet d’adresses IP est de nature à empêcher d’autres services Azure (comme Azure Stream Analytics, Azure Virtual Machines ou l’Explorateur d’appareils dans le portail) d’interagir avec IoT Hub.

> [!WARNING]
> Si vous utilisez Azure Stream Analytics (ASA) pour lire les messages à partir d’un hub IoT avec le filtrage IP activé, utilisez le nom compatible avec Event Hub et le point de terminaison de votre hub IoT dans la chaîne de connexion ASA.

## <a name="delete-an-ip-filter-rule"></a>Suppression d’une règle de filtre IP

Pour supprimer une règle de filtre IP, sélectionnez une ou plusieurs règles dans la grille et cliquez sur **Supprimer**.

![Supprimer une règle de filtre IP de l’IoT Hub](./media/iot-hub-ip-filtering/ip-filter-delete-rule.png)

## <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>Récupérer et mettre à jour des filtres IP à l'aide d'Azure CLI

Les filtres IP de votre hub IoT peuvent être récupérés et mis à jour via [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest). 

Pour récupérer les filtres IP actuels de votre hub IoT, exécutez :

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

Cette opération renvoie un objet JSON dans lequel vos filtres IP existants sont répertoriés sous la clé `properties.ipFilterRules` :

```json
{
...
    "properties": {
        "ipFilterRules": [
        {
            "action": "Reject",
            "filterName": "MaliciousIP",
            "ipMask": "6.6.6.6/6"
        },
        {
            "action": "Allow",
            "filterName": "GoodIP",
            "ipMask": "131.107.160.200"
        },
        ...
        ],
    },
...
}
```

Pour ajouter un nouveau filtre IP à votre hub IoT, exécutez :

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules "{\"action\":\"Reject\",\"filterName\":\"MaliciousIP\",\"ipMask\":\"6.6.6.6/6\"}"
```

Pour supprimer un filtre IP existant de votre hub IoT, exécutez :

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules <ipFilterIndexToRemove>
```

Notez que `<ipFilterIndexToRemove>` doit correspondre à l'ordre des filtres IP dans les règles `properties.ipFilterRules` de votre hub IoT.


## <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>Récupérer et mettre à jour des filtres IP à l'aide d'Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Les filtres IP de votre concentrateur IoT peuvent être récupérées et définies via [Azure PowerShell](/powershell/azure/overview). 

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.ipFilterRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='MaliciousIP'; 'action'='Reject'; 'ipMask'='6.6.6.6/6'}

# Add your new IP filter rule
$iothubResource.Properties.ipFilterRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.ipFilterRules = @($iothubResource.Properties.ipFilterRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzResource -Force
```

## <a name="update-ip-filter-rules-using-rest"></a>Mettre à jour les règles de filtrage IP à l'aide de REST

Vous pouvez également récupérer et modifier les filtres IP de votre hub IoT via le point de terminaison REST du fournisseur de ressources Azure. Reportez-vous à `properties.ipFilterRules` dans la [méthode createorupdate](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate).


## <a name="ip-filter-rule-evaluation"></a>Évaluation de règle de filtre IP

Les règles de filtre IP sont appliquées dans l’ordre et la première règle qui correspond à l’adresse IP détermine l’action d’acceptation ou de rejet.

Par exemple, si vous souhaitez accepter les adresses dans la plage 192.168.100.0/22 et rejeter tout le reste, la première règle de la grille doit accepter la plage d’adresses 192.168.100.0/22. La règle suivante doit rejeter toutes les adresses à l’aide de la plage 0.0.0.0/0.

Vous pouvez modifier l’ordre de vos règles de filtre IP dans la grille en cliquant sur les trois points verticaux au début d’une ligne et en effectuant un glisser-déplacer.

Pour enregistrer le nouvel ordre de vos règles de filtre IP, cliquez sur **Enregistrer**.

![Modifier l’ordre de vos règles de filtre IP de l’IoT Hub](./media/iot-hub-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>Étapes suivantes

Pour explorer davantage les capacités de IoT Hub, consultez :

* [Surveillance des opérations](iot-hub-operations-monitoring.md)
* [Métriques d’IoT Hub](iot-hub-metrics.md)
