---
title: Exécuter l’exportation en appelant la commande $export sur API Azure pour FHIR
description: Cet article explique comment exporter des données FHIR en utilisant $export.
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 5/17/2021
ms.author: cavoeg
ms.openlocfilehash: 50f79d8b73b6c716e14504d6d763d900a7bed488
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110078654"
---
# <a name="how-to-export-fhir-data"></a>Comment exporter des données FHIR


La fonctionnalité d’exportation en bloc autorise l’exportation de données à partir du serveur FHIR en fonction de la [spécification FHIR](https://hl7.org/fhir/uv/bulkdata/export/index.html). 

Avant d’utiliser $export, assurez-vous que l’API Azure pour FHIR est configurée pour l’utiliser. Pour configurer les paramètres d’exportation et créer un compte de stockage Azure, consultez la [page Configurer les données d’exportation](configure-export-data.md).

## <a name="using-export-command"></a>Utilisation de la commande $export

Après avoir configuré l’API Azure pour FHIR pour l’exportation, vous pouvez utiliser la commande $export afin d’exporter les données à partir du service. Les données seront stockées dans le compte de stockage que vous avez spécifié lors de la configuration de l’exportation. Pour savoir comment appeler la commande $export dans le serveur FHIR, consultez la documentation sur la [spécification $export HL7 FHIR](https://hl7.org/Fhir/uv/bulkdata/export/index.html).


**Travaux bloqués dans un état incorrect**

Dans certains cas, il est possible qu’un travail soit bloqué dans un état incorrect. Cela peut se produire en particulier si les autorisations du compte de stockage n’ont pas été configurées correctement. Une façon de valider si votre exportation est réussie consiste à vérifier votre compte de stockage pour voir si les fichiers conteneur correspondants (c’est-à-dire, ndjson) sont présents. S’ils ne sont pas présents et qu’aucun autre travail d’exportation n’est en cours d’exécution, il est possible que le travail en cours soit bloqué dans un état incorrect. Vous devez annuler le travail d’exportation en envoyant une demande d’annulation et essayer à nouveau de remettre la tâche en file d’attente. Notre durée d’exécution par défaut pour une exportation en mauvais état est de 10 minutes avant l’arrêt et le passage à une nouvelle tâche, ou l’exportation. 

L’API Azure pour FHIR prend en charge $export aux niveaux suivants :
* [Système](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---system-level-export) : `GET https://<<FHIR service base URL>>/$export>>`
* [Patient](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---all-patients) : `GET https://<<FHIR service base URL>>/Patient/$export>>`
* [Groupe de patients *](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---group-of-patients) -l’API Azure pour FHIR exporte toutes les ressources associées mais n’exporte pas les caractéristiques du groupe : `GET https://<<FHIR service base URL>>/Group/[ID]/$export>>`

Lorsque les données sont exportées, un fichier distinct est créé pour chaque type de ressource. Pour vous assurer que les fichiers exportés ne sont pas trop volumineux. Nous créons un nouveau fichier une fois que la taille d’un fichier exporté unique devient supérieure à 64 Mo. Le résultat est que vous pouvez obtenir plusieurs fichiers pour chaque type de ressource, qui sera énuméré (c’est-à-dire, patient-1. ndjson, patient-2. ndjson). 


> [!Note] 
> `Patient/$export` et `Group/[ID]/$export` peuvent exporter les ressources dupliquées si la ressource se trouve dans un compartiment de plusieurs ressources ou dans plusieurs groupes.

En outre, la vérification de l’état d’exportation à l’aide de l’URL renvoyée par l’en-tête Location pendant la mise en file d’attente est prise en charge avec l’annulation du travail d’exportation réel.

### <a name="exporting-fhir-data-to-adls-gen2"></a>Exporter des données FHIR vers ADLS Gen2

Actuellement, nous prenons en charge $export pour les comptes de stockage avec ADLS Gen2, avec la limitation suivante :

- L’utilisateur ne peut pas tirer parti des [espaces de noms hiérarchiques](../../storage/blobs/data-lake-storage-namespace.md), mais il n’existe pas de moyen de cibler l’exportation vers un sous-répertoire spécifique au sein du conteneur. Seul le ciblage d’un conteneur spécifique (un dossier y est alors créé à chaque exportation) est possible.
- Une fois l’exportation terminée, plus rien n’est exporté dans ce dossier, car les exportations suivantes à destination de ce même conteneur se trouvent dans un nouveau dossier.


## <a name="settings-and-parameters"></a>Paramètres et configurations

### <a name="headers"></a>En-têtes
Deux paramètres d’en-tête doivent être définis pour des travaux $export. Les valeurs sont définies par la [spécification $export](https://hl7.org/Fhir/uv/bulkdata/export/index.html#headers) actuelle.
* **Accept** : application/fhir+json
* **Prefer** : respond-async

### <a name="query-parameters"></a>Paramètres de la requête
L’API Azure pour FHIR prend en charge les paramètres de requête suivants. Tous ces paramètres sont facultatifs :

|Paramètre de requête.        | Défini par la spécification FHIR ?    |  Description|
|------------------------|---|------------|
| \_outputFormat | Oui | Prend actuellement en charge trois valeurs à aligner sur la spécification FHIR : application/fhir+ndjson, application/ndjson ou simplement ndjson. Tous les travaux d’exportation retournent `ndjson` et la valeur transmise n’a aucun effet sur le comportement du code. |
| \_since | Oui | Vous permet d’exporter uniquement les ressources qui ont été modifiées depuis l’heure indiquée. |
| \_type | Oui | Vous permet de spécifier les types de ressources qui seront inclus. Par exemple, \_type=Patient retournera uniquement des ressources relatives aux patients.|
| \_typefilter | Oui | Pour demander un filtrage plus affiné, vous pouvez utiliser \_typefilter avec le paramètre \_type. La valeur du paramètre _typeFilter est une liste séparée par des virgules de requêtes FHIR qui limitent davantage les résultats. |
| \_container | Non |  Spécifie le conteneur dans le compte de stockage configuré où les données doivent être exportées. Si un conteneur est spécifié, les données sont exportées vers celui-ci dans un nouveau dossier portant son nom. Si le conteneur n’est pas spécifié, elles sont exportées vers un nouveau conteneur utilisant le timestamp et l’ID de travail. |

> [!Note]
> Seuls les comptes de stockage dans le même abonnement que pour l’API Azure pour FHIR peuvent être enregistrés comme destination pour les opérations de $export.

## <a name="secure-export-to-azure-storage"></a>Sécuriser l’exportation vers Stockage Azure

L’API Azure pour FHIR prend en charge l’exportation sécurisée. Choisissez l’une des deux options ci-dessous :

* Autorisation de l’API Azure pour FHIR en tant que service approuvé Microsoft pour accéder au compte de stockage Azure.
 
* Autoriser des adresses IP spécifiques associées à l’API Azure pour FHIR à accéder au compte de stockage Azure. Cette option fournit deux configurations différentes selon que le compte de stockage se trouve au même emplacement que, ou se trouve dans un emplacement différent de celui de l’API Azure pour FHIR.

### <a name="allowing-azure-api-for-fhir-as-a-microsoft-trusted-service"></a>Autorisation de l’API Azure pour FHIR en tant que service approuvé Microsoft

Sélectionnez un compte de stockage dans la Portail Azure, puis sélectionnez le panneau **mise en réseau** . Sélectionnez **réseaux sélectionnés** sous l’onglet **pare-feu et réseaux virtuels** .

> [!IMPORTANT]
> Vérifiez que vous disposez de l’autorisation d’accès au compte de stockage pour l’API Azure pour FHIR à l’aide de son identité gérée. Pour plus d’informations, consultez [configurer les paramètres d’exportation et configurer le compte de stockage](https://docs.microsoft.com/azure/healthcare-apis/fhir/configure-export-data).

  :::image type="content" source="media/export-data/storage-networking.png" alt-text="Paramètres réseau de Stockage Azure." lightbox="media/export-data/storage-networking.png":::

Dans la section **exceptions** , activez la case **à cocher Autoriser les services Microsoft approuvés à accéder à ce compte de stockage** et enregistrer le paramètre. 

:::image type="content" source="media/export-data/exceptions.png" alt-text="Autorisez les services Microsoft approuvés à accéder à ce compte de stockage.":::

Vous êtes maintenant prêt à exporter des données FHIR vers le compte de stockage en toute sécurité. Notez que le compte de stockage se trouve sur les réseaux sélectionnés et n’est pas accessible publiquement. Pour accéder aux fichiers, vous pouvez activer et utiliser des points de terminaison privés pour le compte de stockage, ou activer tous les réseaux pour le compte de stockage pendant une brève période de temps.

> [!IMPORTANT]
> L’interface utilisateur sera mise à jour ultérieurement pour vous permettre de sélectionner le type de ressource pour l’API Azure pour FHIR et une instance de service spécifique.

### <a name="allowing-specific-ip-addresses-for-the-azure-storage-account-in-a-different-region"></a>Autoriser des adresses IP spécifiques pour le compte de stockage Azure dans une autre région

Sélectionnez **mise en réseau** du compte de stockage Azure à partir du portail. 
   
Sélectionnez **Réseaux sélectionnés**. Dans la section pare-feu, spécifiez l’adresse IP dans la zone **plage d’adresses** . Ajoutez des plages d’adresses IP pour autoriser l’accès à partir d’Internet ou de vos réseaux locaux. Vous pouvez trouver l’adresse IP dans le tableau ci-dessous pour la région Azure dans laquelle le service API Azure pour FHIR est approvisionné.

|**Région Azure**         |**Adresse IP publique** |
|:----------------------|:-------------------|
| Australie Est       | 20.53.44.80       |
| Centre du Canada       | 20.48.192.84      |
| USA Centre           | 52.182.208.31     |
| USA Est              | 20.62.128.148     |
| USA Est 2            | 20.49.102.228     |
| USA Est 2 (EUAP)       | 20.39.26.254      |
| Allemagne Nord        | 51.116.51.33      |
| Allemagne Centre-Ouest | 51.116.146.216    |
| Japon Est           | 20.191.160.26     |
| Centre de la Corée        | 20.41.69.51       |
| Centre-Nord des États-Unis     | 20.49.114.188     |
| Europe Nord         | 52.146.131.52     |
| Afrique du Sud Nord   | 102.133.220.197   |
| États-Unis - partie centrale méridionale     | 13.73.254.220     |
| Asie Sud-Est       | 23.98.108.42      |
| Suisse Nord    | 51.107.60.95      |
| Sud du Royaume-Uni             | 51.104.30.170     |
| Ouest du Royaume-Uni              | 51.137.164.94     |
| Centre-USA Ouest      | 52.150.156.44     |
| Europe Ouest          | 20.61.98.66       |
| USA Ouest 2            | 40.64.135.77      |

> [!NOTE]
> Les étapes ci-dessus sont similaires aux étapes de configuration décrites dans le document Comment convertir des données en FHIR (version préliminaire). Pour plus d’informations, consultez [héberger et utiliser des modèles](https://docs.microsoft.com/azure/healthcare-apis/fhir/convert-data#host-and-use-templates)

### <a name="allowing-specific-ip-addresses-for-the-azure-storage-account-in-the-same-region"></a>Autoriser des adresses IP spécifiques pour le compte de stockage Azure dans la même région

Le processus de configuration est le même que précédemment à ceci près qu’une plage d’adresses IP spécifique au format CIDR est ici utilisée (100.64.0.0/10). Si la plage d’adresses IP, qui comprend 100.64.0.0 – 100.127.255.255, doit être spécifiée, c’est parce que l’adresse IP effectivement utilisée par le service varie à chaque demande $export, tout en restant dans la plage.

> [!Note] 
> Il est possible qu’une adresse IP privée contenue dans la plage 10.0.2.0/24 soit utilisée à la place. Dans ce cas, l’opération de $export échoue. Vous pouvez retenter la demande de $export, mais il n’existe aucune garantie qu’une adresse IP dans la plage de 100.64.0.0/10 sera utilisée la prochaine fois. Ce comportement réseau bien connu est lié à la conception. Une autre solution consiste à configurer le compte de stockage dans une autre région.
    
## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à exporter des ressources FHIR à l’aide de la commande $export. Ensuite, pour savoir comment exporter des données déidentifiées, consultez :
 
>[!div class="nextstepaction"]
>[Exporter des données anonymisées](de-identified-export.md)
