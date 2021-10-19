---
title: Déclaration de conformité DICOM pour les API de santé Azure
description: Ce document fournit des informations sur la déclaration de conformité DICOM pour les API de santé Azure.
services: healthcare-apis
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 10/05/2021
ms.author: aersoy
ms.openlocfilehash: 32a368243ab2114799dd9ce9d2ffcdf3306115c9
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130044664"
---
# <a name="dicom-conformance-statement"></a>Instruction de conformité DICOM

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

L' **API Azure pour le service DICOM** prend en charge un sous-ensemble de la &trade; norme DICOMweb. Cette prise en charge comprend les éléments suivants :

* [Magasin (ARRIM-RS)](#store-stow-rs)
* [Récupérer (WADO-RS)](#retrieve-wado-rs)
* [Recherche (QIDO-RS)](#search-qido-rs)

En outre, les API non standard suivantes sont prises en charge :

- [Supprimer](#delete)

Notre service utilise également le contrôle de version de l’API REST. Pour plus d’informations sur la façon de spécifier la version lors des demandes, consultez la documentation sur le contrôle [de version de l’API pour le service DICOM](api-versioning-dicom-service.md).

## <a name="store-stow-rs"></a>Magasin (ARRIM-RS)

Cette transaction utilise la méthode de publication pour stocker les représentations des études, des séries et des instances contenues dans la charge utile de la demande.

| Méthode | Path               | Description |
| :----- | :----------------- | :---------- |
| POST   | .. /studies         | Stocker les instances. |
| POST   | .. /studies/{study} | Stocker des instances pour une étude spécifique. |

`study`Le paramètre correspond à l’attribut DICOM StudyInstanceUID. Si elle est spécifiée, toute instance qui n’appartient pas à l’étude fournie est rejetée avec un `43265` code d’avertissement.

Le `Accept` ou les en-têtes suivants pour la réponse sont pris en charge :

* `application/dicom+json`

Les `Content-Type` en-têtes suivants sont pris en charge :

* `multipart/related; type="application/dicom"`
* `application/dicom`

> [!NOTE]
> Le serveur ne convertit **pas ou ne remplace pas** les attributs qui sont en conflit avec les données existantes. Toutes les données seront stockées comme indiqué.

Les éléments DICOM suivants doivent être présents dans chaque fichier DICOM qui tente d’être stocké :

* StudyInstanceUID
* SeriesInstanceUID
* SOPInstanceUID
* SOPClassUID
* PatientID

> [!NOTE]
> Tous les identificateurs doivent comprendre entre 1 et 64 caractères, et contenir uniquement des caractères alphanumériques ou les caractères spéciaux suivants : '. ', '-'.

Chaque fichier stocké doit avoir une combinaison unique de StudyInstanceUID, SeriesInstanceUID et SopInstanceUID. Le code d’avertissement est `45070` retourné si un fichier avec les mêmes identificateurs existe déjà.

Limite de la taille du fichier DICOM : la taille par défaut d’un fichier DICOM est limitée à 2 Go.

### <a name="store-response-status-codes"></a>Codes d’état de réponse du magasin

| Code                         | Description |
| :--------------------------- | :---------- |
| 200 (OK)                     | Toutes les instances SOP de la demande ont été stockées. |
| 202 (accepté)               | Certaines instances de la demande ont été stockées, mais d’autres ont échoué. |
| 204 (Pas de contenu)             | Aucun contenu n’a été fourni dans la demande de transaction Store. |
| 400 (Demande incorrecte)            | Format incorrect de la requête. Par exemple, l’identificateur d’instance d’étude fourni n’est pas conforme au format UID attendu. |
| 401 (Non autorisé)           | Le client n’est pas authentifié. |
| 403 (Interdit)              | L’utilisateur n’est pas autorisé. |
| 406 (non acceptable)         | L' `Accept` en-tête spécifié n’est pas pris en charge. |
| 409 (Conflit)               | Aucune des instances de la demande de transaction de magasin n’a été stockée. |
| 415 (Type de support non pris en charge) | Le fourni `Content-Type` n’est pas pris en charge. |
| 503 (service non disponible)    | Le service est indisponible ou occupé. Veuillez réessayer plus tard. |

### <a name="store-response-payload"></a>Charge utile de la réponse du magasin

La charge utile de la réponse remplit un DataSet DICOM avec les éléments suivants :

| Tag          | Nom                  | Description |
| :----------- | :-------------------- | :---------- |
| (0008, 1190) | RetrieveURL           | L’URL de récupération de l’étude si le StudyInstanceUID a été fourni dans la demande de magasin et qu’au moins une instance est stockée avec succès. |
| (0008, 1198) | FailedSOPSequence     | Séquence des instances qui n’ont pas pu être stockées. |
| (0008, 1199) | ReferencedSOPSequence | Séquence des instances stockées. |

Chaque jeu de données dans le `FailedSOPSequence` aura les éléments suivants (si le fichier DICOM qui tente d’être stocké a pu être lu) :

| Tag          | Nom                     | Description |
| :----------- | :----------------------- | :---------- |
| (0008, 1150) | ReferencedSOPClassUID    | Identificateur unique de la classe SOP de l’instance qui n’a pas pu être stockée. |
| (0008, 1150) | ReferencedSOPInstanceUID | Identificateur unique de l’instance SOP de l’instance qui n’a pas pu être stockée. |
| (0008, 1197) | FailureReason            | Code de raison pour laquelle cette instance n’a pas pu être stockée. |

Chaque jeu de données dans le `ReferencedSOPSequence` aura les éléments suivants :

| Tag          | Nom                     | Description |
| :----------- | :----------------------- | :---------- |
| (0008, 1150) | ReferencedSOPClassUID    | Identificateur unique de la classe SOP de l’instance qui n’a pas pu être stockée. |
| (0008, 1150) | ReferencedSOPInstanceUID | Identificateur unique de l’instance SOP de l’instance qui n’a pas pu être stockée. |
| (0008, 1190) | RetrieveURL              | URL de récupération de cette instance sur le serveur DICOM. |

Voici un exemple de réponse avec un `Accept` en-tête `application/dicom+json` :

```json
{
  "00081190":
  {
    "vr":"UR",
    "Value":["http://localhost/studies/d09e8215-e1e1-4c7a-8496-b4f6641ed232"]
  },
  "00081198":
  {
    "vr":"SQ",
    "Value":
    [{
      "00081150":
      {
        "vr":"UI","Value":["cd70f89a-05bc-4dab-b6b8-1f3d2fcafeec"]
      },
      "00081155":
      {
        "vr":"UI",
        "Value":["22c35d16-11ce-43fa-8f86-90ceed6cf4e7"]
      },
      "00081197":
      {
        "vr":"US",
        "Value":[43265]
      }
    }]
  },
  "00081199":
  {
    "vr":"SQ",
    "Value":
    [{
      "00081150":
      {
        "vr":"UI",
        "Value":["d246deb5-18c8-4336-a591-aeb6f8596664"]
      },
      "00081155":
      {
        "vr":"UI",
        "Value":["4a858cbb-a71f-4c01-b9b5-85f88b031365"]
      },
      "00081190":
      {
        "vr":"UR",
        "Value":["http://localhost/studies/d09e8215-e1e1-4c7a-8496-b4f6641ed232/series/8c4915f5-cc54-4e50-aa1f-9b06f6e58485/instances/4a858cbb-a71f-4c01-b9b5-85f88b031365"]
      }
    }]
  }
}
```

### <a name="store-failure-reason-codes"></a>Codes de raison de l’échec du magasin

| Code  | Description |
| :---- | :---------- |
| 272   | La transaction Store n’a pas stocké l’instance en raison d’une défaillance générale du traitement de l’opération. |
| 43264 | Échec de la validation de l’instance DICOM. |
| 43265 | L’instance fournie StudyInstanceUID ne correspondait pas au StudyInstanceUID spécifié dans la demande du magasin. |
| 45070 | Une instance DICOM avec les mêmes StudyInstanceUID, SeriesInstanceUID et SopInstanceUID a déjà été stockée. Si vous souhaitez mettre à jour le contenu, supprimez d’abord cette instance. |
| 45071 | Une instance DICOM est créée par un autre processus, ou la tentative précédente de création a échoué et le processus de nettoyage n’a pas encore pu être nettoyé. Supprimez d’abord l’instance avant d’essayer de la recréer. |

## <a name="retrieve-wado-rs"></a>Récupérer (WADO-RS)

Cette transaction de récupération offre une prise en charge pour la récupération des études, des séries, des instances et des frames stockés par référence.

| Méthode | Path                                                                    | Description |
| :----- | :---------------------------------------------------------------------- | :---------- |
| GET    | .. /studies/{study}                                                      | Récupère toutes les instances d’une étude. |
| GET    | .. /studies/{study}/metadata                                             | Récupère les métadonnées de toutes les instances d’une étude. |
| GET    | .. /studies/{study}/series/{series}                                      | Récupère toutes les instances d’une série. |
| GET    | .. /studies/{study}/series/{series}/metadata                             | Récupère les métadonnées de toutes les instances d’une série. |
| GET    | .. /studies/{study}/series/{series}/instances/{instance}                 | Récupère une instance unique. |
| GET    | .. /studies/{study}/series/{series}/instances/{instance}/metadata        | Récupère les métadonnées pour une instance unique. |
| GET    | .. /studies/{study}/series/{series}/instances/{instance}/frames/{frames} | Récupère un ou plusieurs frames à partir d’une seule instance. Pour spécifier plusieurs frames, utilisez une virgule pour séparer chaque frame à retourner. Par exemple,/Studies/1/Series/2/instance/3/frames/4, 5, 6 |

### <a name="retrieve-instances-within-study-or-series"></a>Récupérer des instances dans une étude ou une série

Les `Accept` en-têtes suivants sont pris en charge pour la récupération d’instances au sein d’une étude ou d’une série :


* `multipart/related; type="application/dicom"; transfer-syntax=*`
* `multipart/related; type="application/dicom";` (lorsque Transfer-Syntax n’est pas spécifié, 1.2.840.10008.1.2.1 est utilisé comme valeur par défaut)
* `multipart/related; type="application/dicom"; transfer-syntax=1.2.840.10008.1.2.1`
* `multipart/related; type="application/dicom"; transfer-syntax=1.2.840.10008.1.2.4.90`

### <a name="retrieve-an-instance"></a>Récupérer une instance

Le `Accept` ou les en-têtes suivants sont pris en charge pour la récupération d’une instance spécifique :

* `application/dicom; transfer-syntax=*`
* `multipart/related; type="application/dicom"; transfer-syntax=*`
* `application/dicom;` (lorsque Transfer-Syntax n’est pas spécifié, 1.2.840.10008.1.2.1 est utilisé comme valeur par défaut)
* `multipart/related; type="application/dicom"` (lorsque Transfer-Syntax n’est pas spécifié, 1.2.840.10008.1.2.1 est utilisé comme valeur par défaut)
* `application/dicom; transfer-syntax=1.2.840.10008.1.2.1`
* `multipart/related; type="application/dicom"; transfer-syntax=1.2.840.10008.1.2.1`
* `application/dicom; transfer-syntax=1.2.840.10008.1.2.4.90`
* `multipart/related; type="application/dicom"; transfer-syntax=1.2.840.10008.1.2.4.90`

### <a name="retrieve-frames"></a>Récupérer les frames

Les `Accept` en-têtes suivants sont pris en charge pour la récupération des frames :

* `multipart/related; type="application/octet-stream"; transfer-syntax=*`
* `multipart/related; type="application/octet-stream";` (lorsque Transfer-Syntax n’est pas spécifié, 1.2.840.10008.1.2.1 est utilisé comme valeur par défaut)
* `multipart/related; type="application/octet-stream"; transfer-syntax=1.2.840.10008.1.2.1`
* `multipart/related; type="image/jp2";` (lorsque Transfer-Syntax n’est pas spécifié, 1.2.840.10008.1.2.4.90 est utilisé comme valeur par défaut)
* `multipart/related; type="image/jp2";transfer-syntax=1.2.840.10008.1.2.4.90`

### <a name="retrieve-transfer-syntax"></a>Récupérer la syntaxe de transfert

Lorsque la syntaxe de transfert demandée est différente de celle du fichier d’origine, le fichier d’origine est transcodé en syntaxe de transfert demandée. Le fichier d’origine doit être de l’un des formats ci-dessous pour que le transcodage aboutisse. dans le cas contraire, le transcodage peut échouer :

* 1.2.840.10008.1.2 (Little endian implicite)
* 1.2.840.10008.1.2.1 (Little endian explicite)
* 1.2.840.10008.1.2.2 (VR-Big endian explicite)
* 1.2.840.10008.1.2.4.50 (processus de base JPEG 1)
* 1.2.840.10008.1.2.4.57 (JPEG sans perte)
* 1.2.840.10008.1.2.4.70 (valeur de sélection sans perte JPEG 1)
* 1.2.840.10008.1.2.4.90 (JPEG 2000 sans perte uniquement)
* 1.2.840.10008.1.2.4.91 (JPEG 2000)
* 1.2.840.10008.1.2.5 (RLE sans perte)

Un non pris en charge `transfer-syntax` entraînera `406 Not Acceptable` .

### <a name="retrieve-metadata-for-study-series-or-instance"></a>Récupérer les métadonnées (pour l’étude, la série ou l’instance)

Les `Accept` en-têtes suivants sont pris en charge pour l’extraction des métadonnées d’une étude, d’une série ou d’une instance :

* `application/dicom+json`

La récupération de métadonnées ne retourne pas d’attributs avec les représentations de valeur suivantes :

| Nom de la VR | Description            |
| :------ | :--------------------- |
| RÉGIE      | Autre octet             |
| DIAMÈTRE      | Autre double           |
| OF      | Autre valeur float            |
| OL      | Autre long             |
| OV      | Autre 64-bit très long |
| ENSEIGNEMENT      | Autre mot             |
| UN      | Unknown                |

### <a name="retrieve-metadata-cache-validation-for-study-series-or-instance"></a>Récupérer la validation du cache des métadonnées pour (étude, série ou instance)

La validation du cache est prise en charge à l’aide du `ETag` mécanisme. Dans la réponse à une demande de métadonnées, l’ETag est retourné en tant qu’en-têtes. Cet ETag peut être mis en cache et ajouté en tant qu' `If-None-Match` en-tête dans les demandes ultérieures pour les mêmes métadonnées. Deux types de réponses sont possibles si les données existent :

* Les données n’ont pas été modifiées depuis la dernière demande : la réponse HTTP 304 (non modifiée) sera envoyée sans corps de réponse.
* Les données ont été modifiées depuis la dernière demande : la réponse HTTP 200 (OK) sera envoyée avec l’ETag mis à jour. Les données requises sont également retournées en tant que partie du corps.

### <a name="retrieve-response-status-codes"></a>Récupérer les codes d’état de réponse

| Code                         | Description |
| :--------------------------- | :---------- |
| 200 (OK)                     | Toutes les données demandées ont été récupérées. |
| 304 (Non modifié)           | Les données demandées n’ont pas été modifiées depuis la dernière demande. Le contenu n’est pas ajouté au corps de la réponse dans ce cas. Pour plus d’informations, consultez la section ci-dessus récupération de la **validation du cache des métadonnées (pour l’étude, la série ou l’instance)**. |
| 400 (Demande incorrecte)            | Format incorrect de la requête. Par exemple, l’identificateur d’instance d’étude fourni n’est pas conforme au format UID attendu, ou l’encodage de la syntaxe de transfert demandé n’est pas pris en charge. |
| 401 (Non autorisé)           | Le client n’est pas authentifié. |
| 403 (Interdit)              | L’utilisateur n’est pas autorisé. |
| 404 (introuvable)              | La ressource DICOM spécifiée est introuvable. |
| 406 (non acceptable)         | L' `Accept` en-tête spécifié n’est pas pris en charge. |
| 503 (service non disponible)    | Le service est indisponible ou occupé. Veuillez réessayer plus tard. |

## <a name="search-qido-rs"></a>Recherche (QIDO-RS)

La requête basée sur l’ID pour les objets DICOM (QIDO) vous permet de rechercher des études, des séries et des instances par attributs.

| Méthode | Path                                            | Description                       |
| :----- | :---------------------------------------------- | :-------------------------------- |
| *Rechercher des études*                                                                         |
| GET    | .. /Studies ?...                                  | Rechercher des études                |
| *Rechercher une série*                                                                          |
| GET    | .. /Series ?...                                   | Rechercher une série                 |
| GET    |.. /Studies/{Study}/Series ?...                    | Rechercher des séries dans une étude      |
| *Rechercher des instances*                                                                       |
| GET    |.. /instances ?...                                 | Rechercher des instances              |
| GET    |.. /Studies/{Study}/instances ?...                 | Rechercher des instances dans une étude   |
| GET    |.. /Studies/{Study}/Series/{Series}/instances ?... | Rechercher des instances dans une série  |

Les `Accept` en-têtes suivants sont pris en charge pour la recherche :

- `application/dicom+json`

### <a name="supported-search-parameters"></a>Paramètres de recherche pris en charge

Les paramètres suivants sont pris en charge pour chaque requête :

| Clé              | Valeur (s) de prise en charge              | Nombre autorisé | Description |
| :--------------- | :---------------------------- | :------------ | :---------- |
| `{attributeID}=` | ajoutée                       | 0... N         | Recherchez la correspondance attribut/valeur dans la requête. |
| `includefield=`  | `{attributeID}`<br/>`all`   | 0... N         | Attributs supplémentaires à retourner dans la réponse. Les balises publiques et privées sont toutes deux prises en charge.<br/>Lorsque `all` est fourni. Reportez-vous à la [réponse de recherche](#search-response) pour plus d’informations sur les attributs qui seront renvoyés pour chaque type de requête.<br/>Si un mélange de {attributeID} et’all’est fourni, le serveur utilisera par défaut’all'. |
| `limit=`         | ajoutée                       | 0..1          | Valeur entière pour limiter le nombre de valeurs retournées dans la réponse.<br/>La valeur peut être comprise entre la plage 1 >= x <= 200. La valeur par défaut est 100. |
| `offset=`        | ajoutée                       | 0..1          | Ignorer les résultats {value}.<br/>Si un décalage est fourni plus grand que le nombre de résultats de la requête de recherche, une réponse 204 (pas de contenu) est retournée. |
| `fuzzymatching=` | `true` \| `false`             | 0..1          | Si la vraie correspondance approximative est appliquée à l’attribut PatientName. Il effectue une correspondance de mot de préfixe de n’importe quelle partie de nom à l’intérieur de la valeur PatientName. Par exemple, si PatientName est « John ^ Doe », « Joh », « do », « JO do », « Doe » et « John Doe » sont tous correspondants. Toutefois, « Ohn » ne correspond pas. |

#### <a name="searchable-attributes"></a>Attributs pouvant faire l’objet d’une recherche

Nous prenons en charge la recherche des attributs et des types de recherche suivants.

| Mot clé Attribute | Matériels | Série | Instance |
| :---------------- | :---: | :----: | :------: |
| StudyInstanceUID | X | X | X |
| PatientName | X | X | X |
| PatientID | X | X | X |
| AccessionNumber | X | X | X |
| ReferringPhysicianName | X | X | X |
| StudyDate | X | X | X |
| StudyDescription | X | X | X |
| SeriesInstanceUID |  | X | X |
| Multiples |  | X | X |
| PerformedProcedureStepStartDate |  | X | X |
| SOPInstanceUID |  |  | X |

#### <a name="search-matching"></a>Recherche de correspondance

Nous prenons en charge les types correspondants suivants.

| Type de recherche | Attribut pris en charge | Exemple |
| :---------- | :------------------ | :------ |
| Requête de plage | StudyDate | {attributeID} = {valeur1}-{value2}. Pour les valeurs de date/heure, nous avons pris en charge une plage inclusive dans la balise. Ce sera mappé à `attributeID >= {value1} AND attributeID <= {value2}` . |
| Correspondance exacte | Tous les attributs pris en charge | {attributeID} = {value1} |
| Correspondance approximative | PatientName | Correspond à n’importe quel composant du nom du patient qui commence par la valeur. |

#### <a name="attribute-id"></a>ID d’attribut

Les balises peuvent être encodées de nombreuses façons pour le paramètre de requête. Nous avons partiellement implémenté la norme telle que définie dans [PS 3.18 6.7.1.1.1](http://dicom.nema.org/medical/dicom/2019a/output/chtml/part18/sect_6.7.html#sect_6.7.1.1.1). Les encodages suivants pour une balise sont pris en charge :

| Valeur            | Exemple          |
| :--------------- | :--------------- |
| Communauté appartient | 0020000D         |
| {dicomKeyword}   | StudyInstanceUID |

Exemple de requête de recherche d’instances : **.. fois? Modalité = CT&00280011 = 512&includefield = 00280010&Limit = 5&offset = 0**

### <a name="search-response"></a>Réponse de recherche

La réponse sera un tableau de datasets DICOM. En fonction de la ressource, les attributs suivants sont retournés par *défaut* :

#### <a name="default-study-tags"></a>Étiquettes d’étude par défaut

| Tag          | Nom de l'attribut |
| :----------- | :------------- |
| (0008, 0,005) | SpecificCharacterSet |
| (0008, 0020) | StudyDate |
| (0008, 0030) | StudyTime |
| (0008, 0050) | AccessionNumber |
| (0008, 0056) | InstanceAvailability |
| (0008, 0090) | ReferringPhysicianName |
| (0008, 0201) | TimezoneOffsetFromUTC |
| (0010, 0010) | PatientName |
| (0010, 0020) | PatientID |
| (0010, 0030) | PatientBirthDate |
| (0010, 0040) | PatientSex |
| (0020, 0010) | StudyID |
| (0020, 000D) | StudyInstanceUID |

#### <a name="default-series-tags"></a>Balises de série par défaut

| Tag          | Nom de l'attribut |
| :----------- | :------------- |
| (0008, 0,005) | SpecificCharacterSet |
| (0008, 0060) | Multiples |
| (0008, 0201) | TimezoneOffsetFromUTC |
| (0008, 103E) | SeriesDescription |
| (0020, 000E) | SeriesInstanceUID |
| (0040, 0244) | PerformedProcedureStepStartDate |
| (0040, 0245) | PerformedProcedureStepStartTime |
| (0040, 0275) | RequestAttributesSequence |

#### <a name="default-instance-tags"></a>Balises d’instance par défaut

| Tag          | Nom d’attribut |
| :----------- | :------------- |
| (0008, 0,005) | SpecificCharacterSet |
| (0008, 0016) | SOPClassUID |
| (0008, 0018) | SOPInstanceUID |
| (0008, 0056) | InstanceAvailability |
| (0008, 0201) | TimezoneOffsetFromUTC |
| (0020, 0013) | NuméroInstance |
| (0028, 0010) | Lignes |
| (0028, 0011) | Colonnes |
| (0028, 0100) | BitsAllocated |
| (0028, 0008) | NumberOfFrames |

Si includefield = All, les attributs ci-dessous sont inclus avec les attributs par défaut. Avec les attributs par défaut, il s’agit de la liste complète des attributs pris en charge à chaque niveau de ressource.

#### <a name="other-study-tags"></a>Autres balises d’étude

| Tag          | Nom de l'attribut |
| :----------- | :------------- |
| (0008, 1030) | Description de l’étude |
| (0008, 0063) | AnatomicRegionsInStudyCodeSequence |
| (0008, 1032) | ProcedureCodeSequence |
| (0008, 1060) | NameOfPhysiciansReadingStudy |
| (0008, 1080) | AdmittingDiagnosesDescription |
| (0008, 1110) | ReferencedStudySequence |
| (0010, 1010) | Patientez |
| (0010, 1020) | Patienter |
| (0010, 1030) | PatientWeight |
| (0010, 2180) | Occupation |
| (0010, 21B0) | AdditionalPatientHistory |

#### <a name="other-series-tags"></a>Autres balises de série

| Tag          | Nom de l'attribut |
| :----------- | :------------- |
| (0020, 0011) | SeriesNumber |
| (0020, 0060) | Déplacement |
| (0008, 0021) | SeriesDate |
| (0008, 0031) | SeriesTime |

Les attributs suivants sont retournés :

* Tous les paramètres de requête match et uid dans l’URL de ressource.
* Attributs IncludeField pris en charge à ce niveau de ressource. 
* Si la ressource cible est toutes des séries, les attributs de niveau étude sont également retournés.
* Si la ressource cible est toutes les instances, les attributs de niveau étude et série sont également retournés.
* Si la ressource cible est des instances de l’étude, les attributs de niveau série sont également retournés.

### <a name="search-response-codes"></a>Codes de réponse de recherche

L’API de requête retourne l’un des codes d’état suivants dans la réponse :

| Code                      | Description |
| :------------------------ | :---------- |
| 200 (OK)                  | La charge utile de la réponse contient toutes les ressources correspondantes. |
| 204 (Pas de contenu)          | La recherche s’est terminée correctement, mais n’a retourné aucun résultat. |
| 400 (Demande incorrecte)         | Le serveur n’a pas pu exécuter la requête, car le composant de requête n’était pas valide. Le corps de la réponse contient les détails de l’échec. |
| 401 (Non autorisé)        | Le client n’est pas authentifié. |
| 403 (Interdit)           | L’utilisateur n’est pas autorisé. |
| 503 (service non disponible) | Le service est indisponible ou occupé. Veuillez réessayer plus tard. |

### <a name="extra-notes"></a>Remarques supplémentaires

* L’interrogation à l’aide de `TimezoneOffsetFromUTC` ( `00080201` ) n’est pas prise en charge.
* L’API de requête ne retourne pas 413 (entité de requête trop grande). Si la limite de réponse de requête demandée est en dehors de la plage acceptable, une demande incorrecte est retournée. Tout ce qui est demandé dans la plage acceptable sera résolu.
* Lorsque la ressource cible est une étude/série, il existe un risque d’incohérence entre les métadonnées de niveau étude/série sur plusieurs instances. Par exemple, deux instances peuvent avoir différents patientName. Dans ce cas, la dernière version gagne et vous pouvez rechercher uniquement les données les plus récentes.
* Les résultats paginés sont optimisés pour retourner d’abord l’instance la plus *récente* correspondante. Cela peut entraîner des enregistrements en double dans les pages suivantes, si des données plus récentes correspondant à la requête ont été ajoutées.
* La correspondance est en respectant la casse et les accents pour les types PN VR.
* La correspondance respecte la casse et respecte les accents pour les autres types String VR.

## <a name="delete"></a>Supprimer

Cette transaction ne fait pas partie de la norme officielle DICOMweb &trade; . Elle utilise la méthode DELETE pour supprimer des représentations des études, des séries et des instances du magasin.

| Méthode | Path                                                    | Description |
| :----- | :------------------------------------------------------ | :---------- |
| Suppression | .. /studies/{study}                                      | Supprimer toutes les instances pour une étude spécifique. |
| Suppression | .. /studies/{study}/series/{series}                      | Supprimer toutes les instances d’une série spécifique au sein d’une étude. |
| Suppression | .. /studies/{study}/series/{series}/instances/{instance} | Supprimer une instance spécifique au sein d’une série. |

`study`Les paramètres, `series` et `instance` correspondent respectivement aux attributs DICOM StudyInstanceUID, SeriesInstanceUID et SopInstanceUID.

Il n’existe aucune restriction sur l' `Accept` en-tête, l' `Content-Type` en-tête ou le contenu du corps de la requête.

> [!NOTE]
> Après une opération de suppression, les instances supprimées ne sont pas récupérables.

### <a name="response-status-codes"></a>Codes d'état de la réponse

| Code                         | Description |
| :--------------------------- | :---------- |
| 204 (Pas de contenu)             | Lorsque toutes les instances SOP ont été supprimées. |
| 400 (Demande incorrecte)            | Format incorrect de la requête. |
| 401 (Non autorisé)           | Le client n’est pas authentifié. |
| 403 (Interdit)              | L’utilisateur n’est pas autorisé. |
| 404 (introuvable)              | Lorsque la série spécifiée est introuvable dans une étude ou que l’instance spécifiée est introuvable dans la série. |
| 503 (service non disponible)    | Le service est indisponible ou occupé. Veuillez réessayer plus tard. |

### <a name="delete-response-payload"></a>Supprimer la charge utile de réponse

Le corps de la réponse est vide. Le code d’État est la seule information utile retournée.

### <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le service DICOM, consultez. 

>[!div class="nextstepaction"]
>[Vue d’ensemble du service DICOM](dicom-services-overview.md)
