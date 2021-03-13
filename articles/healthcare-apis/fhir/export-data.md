---
title: Exécuter l’exportation en appelant la commande $export sur API Azure pour FHIR
description: Cet article explique comment exporter des données FHIR en utilisant $export.
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 2/19/2021
ms.author: cavoeg
ms.openlocfilehash: 9ed78baed35312b9a33c71a3e49b7e9dca22eb9f
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019155"
---
# <a name="how-to-export-fhir-data"></a>Comment exporter des données FHIR


La fonctionnalité d’exportation en bloc autorise l’exportation de données à partir du serveur FHIR en fonction de la [spécification FHIR](https://hl7.org/fhir/uv/bulkdata/export/index.html). 

Avant d’utiliser $export, vous devez vous assurer que l’API Azure pour FHIR est configurée. Pour configurer les paramètres d’exportation et créer un compte de stockage Azure, consultez la [page Configurer les données d’exportation](configure-export-data.md).

## <a name="using-export-command"></a>Utilisation de la commande $export

Après avoir configuré l’API Azure pour FHIR pour l’exportation, vous pouvez utiliser la commande $export afin d’exporter les données à partir du service. Les données seront stockées dans le compte de stockage que vous avez spécifié lors de la configuration de l’exportation. Pour savoir comment appeler la commande $export dans le serveur FHIR, consultez la documentation sur la [spécification $export HL7 FHIR](https://hl7.org/Fhir/uv/bulkdata/export/index.html). 

L’API Azure pour FHIR prend en charge $export aux niveaux suivants :
* [Système](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---system-level-export) : `GET https://<<FHIR service base URL>>/$export>>`
* [Patient](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---all-patients) : `GET https://<<FHIR service base URL>>/Patient/$export>>`
* [Groupe de patients*](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---group-of-patients). L’API Azure pour FHIR exporte toutes les ressources associées, mais n’exporte pas les caractéristiques du groupe : `GET https://<<FHIR service base URL>>/Group/[ID]/$export>>`

Lorsque les données sont exportées, un fichier distinct est créé pour chaque type de ressource. Pour vous assurer que les fichiers exportés ne deviennent pas trop gros, nous créons un nouveau fichier dès que la taille d’un fichier exporté unique est supérieure à 64 Mo. Le résultat est que vous pouvez obtenir plusieurs fichiers pour chaque type de ressource, qui sera énuméré (par ex., Patient-1.ndjson, Patient-2.ndjson). 


> [!Note] 
> `Patient/$export` et `Group/[ID]/$export` peuvent exporter les ressources dupliquées si la ressource se trouve dans un compartiment de plusieurs ressources ou dans plusieurs groupes.

En outre, la vérification de l’état d’exportation à l’aide de l’URL renvoyée par l’en-tête Location pendant la mise en file d’attente est prise en charge avec l’annulation du travail d’exportation réel.

### <a name="exporting-fhir-data-to-adls-gen2"></a>Exportation de données FHIR vers ADLS Gen2

Actuellement, nous prenons en charge $export pour les comptes de stockage ADLS Gen2 activés, avec la limitation suivante :

- L’utilisateur ne peut pas encore tirer parti des [espaces de noms hiérarchiques](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace) ; Il n’existe aucun moyen de cibler l’exportation vers un sous-répertoire spécifique au sein du conteneur. Nous fournissons uniquement la possibilité de cibler un conteneur spécifique (où nous créons un nouveau dossier pour chaque exportation).

- Une fois l’exportation terminée, nous n’exportons jamais quoi que ce soit dans ce dossier, car les exportations ultérieures vers le même conteneur se trouvent dans un dossier nouvellement créé.


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

## <a name="secure-export-to-azure-storage"></a>Exportation sécurisée vers le stockage Azure

L’API Azure pour FHIR prend en charge une opération d’exportation sécurisée. L’une des options permettant d’exécuter une exportation sécurisée consiste à autoriser l’accès au compte de stockage Azure à des adresses IP spécifiques associées à l’API Azure pour FHIR. Les configurations sont différentes selon que le compte de stockage se trouve dans le même emplacement ou dans un emplacement différent de celui de l’API Azure pour FHIR.

### <a name="when-the-azure-storage-account-is-in-a-different-region"></a>Lorsque le compte de stockage Azure se trouve dans une autre région

Sélectionnez le panneau mise en réseau du compte de stockage Azure à partir du portail. 

   :::image type="content" source="media/export-data/storage-networking.png" alt-text="Paramètres de mise en réseau de stockage Azure." lightbox="media/export-data/storage-networking.png":::
   
Sélectionnez « réseaux sélectionnés » et spécifiez l’adresse IP dans la zone **plage d’adresses** sous la section de pare-feu ajouter des \| plages d’adresses IP pour autoriser l’accès à partir d’Internet ou de vos réseaux locaux. Vous pouvez trouver l’adresse IP dans le tableau ci-dessous pour la région Azure dans laquelle le service API Azure pour FHIR est approvisionné.

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

### <a name="when-the-azure-storage-account-is-in-the-same-region"></a>Lorsque le compte de stockage Azure se trouve dans la même région

Le processus de configuration est le même que ci-dessus, sauf qu’une plage d’adresses IP spécifique au format CIDR est utilisée à la place, 100.64.0.0/10. La raison pour laquelle la plage d’adresses IP, qui comprend 100.64.0.0 – 100.127.255.255, doit être spécifiée, est que l’adresse IP réelle utilisée par le service varie, mais se situe dans la plage, pour chaque demande de $export.

> [!Note] 
> Il est possible qu’une adresse IP privée dans la plage de 10.0.2.0/24 puisse être utilisée à la place. Dans ce cas, l’opération de $export échoue. Vous pouvez retenter la demande de $export, mais il n’existe aucune garantie qu’une adresse IP dans la plage de 100.64.0.0/10 sera utilisée la prochaine fois. C’est le comportement de mise en réseau connu de la conception. L’alternative consiste à configurer le compte de stockage dans une autre région.
    
## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à exporter des ressources FHIR à l’aide de la commande $export. Ensuite, apprenez à exporter des données anonymisées :
 
>[!div class="nextstepaction"]
>[Exporter des données anonymisées](de-identified-export.md)
