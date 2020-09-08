---
title: Exécuter l’exportation en appelant la commande $export sur API Azure pour FHIR
description: Cet article explique comment configurer et utiliser une exportation anonymisée
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 8/26/2020
ms.author: matjazl
ms.openlocfilehash: 10d901f73006051e8b1ddd02aeb36b229c6a7761
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89269848"
---
# <a name="how-to-export-fhir-data"></a>Comment exporter des données FHIR

Pour configurer les paramètres d’exportation et créer un compte de stockage Azure, accédez [ici](configure-export-data.md).

## <a name="exporting-fhir-resources-using-export-command"></a>Exportation de ressources FHIR à l’aide de la commande $export

Après avoir configuré API Azure pour FHIR pour l’exportation, nous pouvons maintenant utiliser la commande $export pour exporter les données hors du service et vers le compte de stockage que nous avons spécifié lors de la configuration. Pour savoir comment appeler la commande $export dans le serveur FHIR, consultez la documentation sur la spécification $export à l’adresse [https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html). 

La commande $export dans l’API Azure pour FHIR accepte un paramètre facultatif _\_container_ qui peut être utilisé pour spécifier le conteneur à l’intérieur du compte de stockage configuré vers lequel les données doivent être exportées.

`https://<<FHIR service base URL>>/$export?_container=<<container_name>>`

> [!IMPORTANT]
> Notez qu’actuellement, l’API Azure pour FHIR prend uniquement en charge l’exportation au niveau du système, comme défini dans la spécification $export à l’adresse [https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html). En outre, seul le paramètre de requête _\_query_ est actuellement pris en charge.

## <a name="exporting-de-identified-data-preview"></a>Exportation de données anonymisées (préversion)

La commande $export peut également être utilisée pour exporter des données anonymisées à partir du serveur FHIR. Elle utilise le moteur d’anonymisation des [outils FHIR pour l’anonymisation](https://github.com/microsoft/FHIR-Tools-for-Anonymization), et accepte les détails de configuration de l’anonymisation dans les paramètres de requête. Vous pouvez créer votre propre fichier de configuration de l’anonymisation ou utiliser l’[exemple de fichier de configuration](https://github.com/microsoft/FHIR-Tools-for-Anonymization#sample-configuration-file-for-hipaa-safe-harbor-method) pour la méthode HIPAA Safe Harbor comme point de départ. 

 `https://<<FHIR service base URL>>/$export?_container=<<container_name>>&_anonymizationConfig=<<config file name>>&_anonymizationConfigEtag=<<ETag on storage>>`

|Paramètre de requête.            | Exemple |Caractère facultatif| Description|
|---------------------------|---------|-----------|------------|
| _\_anonymizationConfig_   |DemoConfig.json|Requis pour l’exportation anonymisée |Nom du fichier de configuration. Consultez le format du fichier de configuration [ici](https://github.com/microsoft/FHIR-Tools-for-Anonymization#configuration-file-format). Ce fichier doit être conservé à l’intérieur d’un conteneur nommé **anonymisation** au sein du compte de stockage Azure configuré comme emplacement d’exportation. |
| _\_anonymizationConfigEtag_|"0x8D8494A069489EC"|Facultatif pour l’exportation anonymisée|Il s’agit de l’ETag du fichier de configuration. Vous pouvez récupérer l’ETag à l’aide de l’explorateur de stockage Azure à partir de la propriété blob|

> [!IMPORTANT]
> Notez que l’exportation brute et l’exportation anonymisée écrivent dans le compte de stockage Azure spécifié dans le cadre de la configuration de l’exportation. Il est recommandé d’utiliser différents conteneurs correspondant à différentes configurations anonymisées, et de gérer l’accès utilisateur au niveau du conteneur.
