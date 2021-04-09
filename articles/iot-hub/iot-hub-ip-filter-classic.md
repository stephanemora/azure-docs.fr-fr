---
title: Filtre IP classique Azure IoT Hub (déconséillé) | Microsoft Docs
description: Comment remplacer le filtre IP classique par une version plus récente et quels sont les avantages
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: jlian
ms.openlocfilehash: 6f326bafb311acedc48c5a349c78f1cd6bcebc87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101661152"
---
# <a name="iot-hub-classic-ip-filter-and-how-to-upgrade"></a>Filtre IP classique IoT Hub et comment procéder à une mise à niveau 

Le filtre IP pour IoT Hub mis à niveau, sécurisé par défaut, protège le point de terminaison intégré. Nous essayons de ne jamais apporter de changements cassants, mais le modèle de sécurité renforcée du filtre IP mis à niveau est incompatible avec le filtre IP classique dont nous annonçons la mise hors service. Pour en savoir plus sur le nouveau filtre IP mis à niveau, consultez [Nouveautés](#whats-new) et [Filtres IP IoT Hub](iot-hub-ip-filtering.md).

Pour éviter toute interruption de service, vous devez effectuer la mise à niveau guidée avant l’échéance de la migration. À ce moment-là, la mise à niveau sera effectuée automatiquement. Pour en savoir plus sur la chronologie de la migration, consultez [Mise à jour Azure](https://aka.ms/ipfilterv2azupdate).

## <a name="how-to-upgrade"></a>Mise à niveau

1.  Visitez le portail Azure
2.  Accédez à votre hub IoT.
3.  Sélectionnez **Mise en réseau** dans le menu Place de marché.
4.  Une bannière vous invitant à mettre à niveau votre filtre IP vers le nouveau modèle doit s’afficher. Sélectionnez **Oui** pour continuer.
    :::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-upgrade-banner.png" alt-text="Image représentant l’invite de bannière pour mettre à niveau le filtre IP classique":::
5.  Étant donné que le nouveau filtre IP bloque toutes les adresses IP par défaut, la mise à niveau supprime vos règles de refus individuelles, mais vous donne la possibilité de les passer en revue avant de les enregistrer. Examinez attentivement les règles pour vous assurer qu’elles vous conviennent.
6.  Suivez les invites pour terminer la mise à niveau.

## <a name="whats-new"></a>Nouveautés

### <a name="secure-by-default"></a>Sécurisé par défaut

Le filtre IP classique autorise implicitement toutes les adresses IP à se connecter au IoT Hub par défaut, ce qui ne correspond pas aux scénarios de sécurité réseau les plus courants. En règle générale, vous souhaitez que seules les adresses IP approuvées puissent se connecter à votre IoT Hub et refuser toutes les autres. Un processus en plusieurs étapes est requis pour atteindre cet objectif avec le filtre IP classique. Par exemple, si vous souhaitez uniquement accepter le trafic à partir de `192.168.100.0/22`, vous devez

1. Configurer une seule règle *autoriser* pour `192.168.100.0/22`.
1. Configurer une règle de *blocage* différente pour `0.0.0.0/0` (la règle « bloquer tout »).
1. Vérifier que les règles sont triées correctement, avec la règle d’autorisation placée avant la règle de blocage.

Dans la pratique, ce processus à plusieurs étapes entraîne une confusion. Les utilisateurs ne configuraient pas la règle « bloquer tout » ou ne triaient pas les règles correctement, entraînant une exposition inattendue. 

Le nouveau filtre IP bloque toutes les adresses IP par défaut. Seules les plages d’adresses IP que vous ajoutez explicitement sont autorisées à se connecter à IoT Hub. Dans l’exemple ci-dessus, les étapes 2 et 3 ne sont plus nécessaires. Ce nouveau comportement simplifie la configuration et se conforme au [principe de sécurité par défaut](https://wikipedia.org/wiki/Secure_by_default).

### <a name="protect-the-built-in-event-hub-compatible-endpoint"></a>Protéger le point de terminaison intégré compatible avec Event Hub

Le filtre IP classique ne peut pas être appliqué au point de terminaison intégré. Cette limitation signifie que, même avec une règle « bloquer tout » (bloquer `0.0.0.0/0`) configurée, le point de terminaison intégré est toujours accessible à partir de n’importe quelle adresse IP.

Le nouveau filtre IP fournit une option permettant d’appliquer des règles au point de terminaison intégré, ce qui réduit l’exposition aux menaces de sécurité réseau.

:::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-built-in-endpoint.png" alt-text="Image illustrant le bouton bascule pour l’application au point de terminaison intégré ou pas":::

> [!NOTE]
> Cette option n’est pas disponible pour les hubs IoT (F1) gratuits. Pour appliquer des règles de filtre IP au point de terminaison intégré, utilisez un hub IoT payant.

### <a name="api-impact"></a>Impact sur l’API

Le filtre IP mis à niveau est disponible dans l’API de ressource IoT Hub à partir de `2020-08-31` (ainsi que `2020-08-31-preview`) et les versions ultérieures. Le filtre IP classique est toujours disponible dans toutes les versions d’API, mais sera supprimé dans une version future de l’API à l’échéance de la migration. Pour en savoir plus sur la chronologie de la migration, consultez [Mise à jour Azure](https://aka.ms/ipfilterv2azupdate).

## <a name="tip-try-the-changes-before-they-apply"></a>Conseil : testez les changements avant qu’ils n’entrent en vigueur

Étant donné que le nouveau filtre IP bloque toutes les adresses IP par défaut, les règles de blocage individuelles ne sont plus compatibles. Par conséquent, le processus de mise à niveau guidée supprime ces règles de blocage individuelles. 

Pour tester le changement avec le filtre IP classique :

1. Accédez à l’onglet **Mise en réseau** dans votre hub IoT
1. Notez la configuration de votre filtre IP (classique) existant, au cas où vous souhaiteriez effectuer une restauration
1. En regard des règles avec la mention **Bloquer**, sélectionnez l’icône de la corbeille pour les supprimer.
1. Ajoutez une autre règle en bas avec `0.0.0.0/0`, puis choisissez **Bloquer**
1. Sélectionnez **Enregistrer**.

Cette configuration imite le comportement du nouveau filtre IP après une mise à niveau à partir du filtre classique. La protection du point de terminaison intégré, dont il n’est pas possible de tester l’utilisation avec le filtre IP classique, constitue une exception. Toutefois, cette fonctionnalité est facultative. Vous n’avez donc pas besoin de l’utiliser si vous pensez qu’elle risque de perturber quelque chose.

## <a name="tip-check-diagnostic-logs-for-all-ip-connections-to-your-iot-hub"></a>Conseil : consultez toutes les connexions IP à votre hub IoT dans les journaux de diagnostic

Pour garantir une transition fluide, vérifiez vos journaux de diagnostic sous la catégorie Connexions. Recherchez la propriété `maskedIpAddress` pour voir si les plages sont celles que vous attendez. Rappel : le nouveau filtre IP bloque toutes les adresses IP qui n’ont pas été ajoutées explicitement.

## <a name="iot-hub-classic-ip-filter-documentation-retired"></a>Documentation sur le filtre IP classique IoT Hub (supprimée)

> [!IMPORTANT]
> Vous trouverez ci-dessous la documentation d’origine du filtre IP classique, qui est en cours de suppression.

La sécurité est un aspect important de toute solution IoT basée sur Azure IoT Hub. Parfois, dans le cadre de votre configuration de sécurité, vous devez spécifier explicitement les adresses IP à partir desquelles les appareils peuvent se connecter. La fonctionnalité *Filtre IP* vous permet de configurer des règles de rejet ou d’acceptation du trafic provenant de certaines adresses IPv4.

### <a name="when-to-use"></a>Quand l’utiliser

Il existe deux cas d’utilisation spécifiques illustrant lorsqu’il est utile de bloquer les points de terminaison IoT Hub pour certaines adresses IP :

* Votre IoT Hub ne doit recevoir du trafic qu’à partir d’une plage spécifiée d’adresses IP et rejeter tout le reste. Par exemple, vous utilisez votre IoT Hub avec [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services) pour créer des connexions privées entre un IoT Hub et votre infrastructure locale.

* Vous devez refuser le trafic provenant d’adresses IP qui ont été identifiées comme suspectes par l’administrateur de IoT Hub.

### <a name="how-filter-rules-are-applied"></a>Application des règles de filtre

Les règles de filtre IP sont appliquées au niveau du service IoT Hub. Par conséquent, les règles de filtre IP s’appliquent à toutes les connexions issues des appareils et des applications principales utilisant n’importe quel protocole pris en charge. Toutefois, les clients lisant directement à partir du [point de terminaison compatible avec Event Hub intégré](iot-hub-devguide-messages-read-builtin.md) (pas via la chaîne de connexion IoT Hub) ne sont pas liés aux règles de filtre IP. 

Toute tentative de connexion à partir d’une adresse IP qui correspond à une règle IP de rejet dans votre IoT Hub reçoit un code d’état 401 non autorisé et une description. Le message de réponse ne mentionne pas la règle IP. Le rejet d’adresses IP est de nature à empêcher d’autres services Azure (comme Azure Stream Analytics, Azure Virtual Machines ou l’Explorateur d’appareils dans le portail Azure) d’interagir avec IoT Hub.

> [!NOTE]
> Si vous devez utiliser Azure Stream Analytics (ASA) pour lire les messages à partir d’un hub IoT avec le filtre IP activé, utilisez le nom compatible avec Event Hub et le point de terminaison de votre hub IoT pour ajouter manuellement une [entrée de flux Event Hubs](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs) dans l’ASA.

### <a name="default-setting"></a>Paramètre par défaut

Par défaut, la grille **Filtre IP** dans le portail pour un IoT Hub est vide. Ce paramètre par défaut signifie que votre hub accepte les connexions de n’importe quelle adresse IP. Ce paramètre par défaut est équivalent à une règle qui accepte la plage d’adresses IP 0.0.0.0/0.

Pour accéder à la page Paramètres de filtre IP, sélectionnez **Réseau**, **Accès public**, puis choisissez **Plages d’adresses IP sélectionnées** :

:::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-default.png" alt-text="Paramètres de filtre IP par défaut d’IoT Hub":::

### <a name="add-or-edit-an-ip-filter-rule"></a>Ajouter ou modifier une règle de filtre IP

Pour ajouter une règle de filtre IP, sélectionnez **+ Ajouter une règle de filtre IP**.

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-add-rule.png" alt-text="Ajouter une règle de filtre IP à un IoT Hub":::

Après avoir sélectionné **Ajouter une règle de filtre IP**, renseignez les champs.

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-after-selecting-add.png" alt-text="Après avoir sélectionné Ajouter une règle de filtre IP":::

* Donnez un **nom** à la règle de filtre IP. Ce nom doit être une chaîne alphanumérique unique qui ne prend pas en compte la casse et qui ne dépasse pas 128 caractères. Seuls les caractères alphanumériques ASCII 7 bits et `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` sont acceptés.

* Fournissez une adresse IPv4 unique ou un bloc d’adresses IP en notation CIDR. Par exemple, dans la notation CIDR, 192.168.100.0/22 représente les 1024 adresses IPv4 allant de 192.168.100.0 à 192.168.103.255.

* Sélectionnez **Autoriser** ou **Bloquer** comme **action** pour la règle de filtre IP.

Après avoir rempli les champs, sélectionnez **Enregistrer** pour enregistrer la règle. Une alerte s’affiche vous informant que la mise à jour est en cours.

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-save-new-rule.png" alt-text="Notification sur l’enregistrement d’une règle de filtre IP":::

L’option **Ajouter** est désactivée lorsque vous atteignez le nombre maximal de dix règles de filtre IP.

Pour modifier une règle existante, sélectionnez les données que vous souhaitez modifier, effectuez la modification, puis sélectionnez **Enregistrer** pour enregistrer votre modification.

### <a name="delete-an-ip-filter-rule"></a>Suppression d’une règle de filtre IP

Pour supprimer une règle de filtre IP, sélectionnez l’icône de la corbeille sur cette ligne, puis sélectionnez **Enregistrer**. La règle est supprimée et la modification est enregistrée.

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-delete-rule.png" alt-text="Supprimer une règle de filtre IP de l’IoT Hub":::

### <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>Récupérer et mettre à jour des filtres IP à l'aide d'Azure CLI

Les filtres IP de votre hub IoT peuvent être récupérés et mis à jour via [Azure CLI](/cli/azure/).

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

### <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>Récupérer et mettre à jour des filtres IP à l'aide d'Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Les filtres IP de votre hub IoT peuvent être récupérés et définis via [Azure PowerShell](/powershell/azure/).

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

### <a name="update-ip-filter-rules-using-rest"></a>Mettre à jour les règles de filtrage IP à l'aide de REST

Vous pouvez également récupérer et modifier les filtres IP de votre hub IoT via le point de terminaison REST du fournisseur de ressources Azure. Reportez-vous à `properties.ipFilterRules` dans la [méthode createorupdate](/rest/api/iothub/iothubresource/createorupdate).

### <a name="ip-filter-rule-evaluation"></a>Évaluation de règle de filtre IP

Les règles de filtre IP sont appliquées dans l’ordre et la première règle qui correspond à l’adresse IP détermine l’action d’acceptation ou de rejet.

Par exemple, si vous souhaitez accepter les adresses dans la plage 192.168.100.0/22 et rejeter tout le reste, la première règle de la grille doit accepter la plage d’adresses 192.168.100.0/22. La règle suivante doit rejeter toutes les adresses à l’aide de la plage 0.0.0.0/0.

Vous pouvez modifier l’ordre de vos règles de filtre IP dans la grille en cliquant sur les trois points verticaux au début d’une ligne et en effectuant un glisser-déplacer.

Pour enregistrer le nouvel ordre de vos règles de filtre IP, cliquez sur **Enregistrer**.

:::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-rule-order.png" alt-text="Modifier l’ordre de vos règles de filtre IP IoT Hub":::

## <a name="next-steps"></a>Étapes suivantes

Pour explorer davantage les capacités de IoT Hub, consultez :

* [Utiliser des filtres IP](iot-hub-ip-filtering.md)