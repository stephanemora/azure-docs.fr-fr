---
title: Utilisation &trade; des API standard DICOMweb avec C#-API de santé Azure
description: Dans ce didacticiel, vous allez apprendre à utiliser les API standard DICOMweb avec C#.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.date: 08/03/2021
ms.author: aersoy
ms.openlocfilehash: 2594ff27dd7e4bae5c5463c32a5d4ee3d481cfe8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121785034"
---
# <a name="using-dicomwebtrade-standard-apis-with-c"></a>Utilisation &trade; des API standard DICOMweb avec C #

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Ce didacticiel utilise C# pour illustrer l’utilisation du service DICOM.

Dans ce didacticiel, nous allons utiliser les [fichiers DICOM Sample. DCM](https://github.com/microsoft/dicom-server/tree/main/docs/dcms)suivants.

* Blue-Circle. DCM
* dicom-metadata.csv
* Green-Square. DCM
* Red-triangle. DCM

Le nom de fichier, studyUID, seriesUID et instanceUID de l’exemple de fichiers DICOM se présente comme suit :

| Fichier | StudyUID | SeriesUID | InstanceUID |
| --- | --- | --- | ---|
|Green-Square. DCM|1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420|1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652|1.2.826.0.1.3680043.8.498.12714725698140337137334606354172323212|
|Red-triangle. DCM|1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420|1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652|1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395|
|Blue-Circle. DCM|1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420|1.2.826.0.1.3680043.8.498.77033797676425927098669402985243398207|1.2.826.0.1.3680043.8.498.13273713909719068980354078852867170114|

> [!NOTE]
> Chacun de ces fichiers représente une instance unique et fait partie de la même étude. En outre, le carré vert et le triangle rouge font partie de la même série, tandis que le cercle bleu se trouve dans une série distincte.

## <a name="prerequisites"></a>Prérequis

Pour utiliser les &trade; API standard DICOMweb, vous devez disposer d’une instance du service DICOM déployée. Si vous n’avez pas déjà déployé une instance du service DICOM, consultez [déployer le service DICOM à l’aide de l’portail Azure](deploy-dicom-services-in-azure.md).

Une fois que vous avez déployé une instance du service DICOM, récupérez l’URL de votre App service :

1. Connectez-vous au [portail Azure](https://ms.portal.azure.com/).
1. Recherchez les **ressources récentes** et sélectionnez votre instance de service DICOM.
1. Copiez l' **URL du service** de votre service DICOM. Veillez à spécifier la version dans le cadre de l’URL lors de l’exécution des requêtes. Pour plus d’informations, consultez la documentation sur le contrôle [de version des API pour le service DICOM](api-versioning-dicom-service.md).

dans votre application, installez les packages de NuGet suivants :

*  [Client DICOM](https://microsofthealthoss.visualstudio.com/FhirServer/_packaging?_a=package&feed=Public&package=Microsoft.Health.Dicom.Client&protocolType=NuGet)

*  [FO-DICOM](https://www.nuget.org/packages/fo-dicom/)

## <a name="create-a-dicomwebclient"></a>Créer un DicomWebClient

Une fois que vous avez déployé votre service DICOM, vous allez créer un DicomWebClient. Exécutez l’extrait de code suivant pour créer DicomWebClient, que nous utiliserons pour le reste de ce didacticiel. assurez-vous que les deux NuGet packages sont installés comme indiqué précédemment. Si vous n’avez pas encore obtenu de jeton, consultez [obtenir un jeton d’accès pour le service DICOM à l’aide de Azure CLI](dicom-get-access-token-azure-cli.md).

```c#
string webServerUrl ="{Your DicomWeb Server URL}"
var httpClient = new HttpClient();
httpClient.BaseAddress = new Uri(webServerUrl);
IDicomWebClient client = new DicomWebClient(httpClient);
client.HttpClient.DefaultRequestHeaders.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", “{Your token value}”); 
```
Avec DicomWebClient, nous pouvons maintenant effectuer les opérations de stockage, de récupération, de recherche et de suppression.

## <a name="store-dicom-instances-stow"></a>Stocker les instances DICOM (ARRIMer)

À l’aide de la DicomWebClient que nous avons créée, nous pouvons désormais stocker des fichiers DICOM.

### <a name="store-single-instance"></a>Stocker une seule instance

Stocker une seule instance montre comment télécharger un seul fichier DICOM.

_Plus_

* POSTER/Studies

```c#
DicomFile dicomFile = await DicomFile.OpenAsync(@"{Path To blue-circle.dcm}");
DicomWebResponse response = await client.StoreAsync(new[] { dicomFile });
```

### <a name="store-instances-for-a-specific-study"></a>Stocker des instances pour une étude spécifique

Les instances de Store pour une étude spécifique montrent comment charger un fichier DICOM dans une étude spécifiée.

_Plus_

* POSTER/Studies/{Study}

```c#
DicomFile dicomFile = await DicomFile.OpenAsync(@"{Path To red-triangle.dcm}");
DicomWebResponse response = await client.StoreAsync(new[] { dicomFile }, "1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420");
```

Avant de passer à la partie suivante du didacticiel, téléchargez le `green-square.dcm` fichier à l’aide de l’une des méthodes précédentes.

## <a name="retrieving-dicom-instances-wado"></a>Récupération de la ou des instances DICOM (WADO)

Les extraits de code suivants montrent comment effectuer chacune des requêtes de récupération à l’aide du DicomWebClient créé précédemment.

Les variables suivantes seront utilisées dans le reste des exemples :

```c#
string studyInstanceUid = "1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420"; //StudyInstanceUID for all 3 examples
string seriesInstanceUid = "1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652"; //SeriesInstanceUID for green-square and red-triangle
string sopInstanceUid = "1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395"; //SOPInstanceUID for red-triangle
```

### <a name="retrieve-all-instances-within-a-study"></a>Récupération de toutes les instances dans une étude

Récupérer toutes les instances d’une étude récupère toutes les instances au sein d’une même étude.

_Plus_

* Obtient/Studies/{Study}

```c#
DicomWebResponse response = await client.RetrieveStudyAsync(studyInstanceUid);
```

Les trois fichiers DCM que nous avons téléchargés précédemment font partie de la même étude, de sorte que la réponse doit retourner les trois instances. Vérifiez que le code d’état de la réponse est OK et que les trois instances sont retournées.

### <a name="use-the-retrieved-instances"></a>Utiliser les instances récupérées

L’extrait de code suivant montre comment accéder aux instances récupérées. Il montre également comment accéder à certains champs des instances et comment l’enregistrer sous la forme d’un fichier DCM.

```c#
DicomWebAsyncEnumerableResponse<DicomFile> response = await client.RetrieveStudyAsync(studyInstanceUid);
await foreach (DicomFile file in response)
{
    string patientName = file.Dataset.GetString(DicomTag.PatientName);
    string studyId = file.Dataset.GetString(DicomTag.StudyID);
    string seriesNumber = file.Dataset.GetString(DicomTag.SeriesNumber);
    string instanceNumber = file.Dataset.GetString(DicomTag.InstanceNumber);

    file.Save($"<path_to_save>\\{patientName}{studyId}{seriesNumber}{instanceNumber}.dcm");
}
```

### <a name="retrieve-metadata-of-all-instances-in-study"></a>Récupérer les métadonnées de toutes les instances dans l’étude

Cette réponse récupère les métadonnées pour toutes les instances au sein d’une même étude.

_Plus_

* Obtient/Studies/{Study}/Metadata

```c#
DicomWebResponse response = await client.RetrieveStudyMetadataAsync(studyInstanceUid);
```

Les trois fichiers DCM que nous avons téléchargés précédemment font partie de la même étude, donc la réponse doit retourner les métadonnées des trois instances. Vérifiez que le code d’état de la réponse est OK et que toutes les métadonnées sont retournées.

### <a name="retrieve-all-instances-within-a-series"></a>Récupérer toutes les instances d’une série

Cette réponse récupère toutes les instances d’une même série.

_Plus_

* Obtient/Studies/{Study}/Series/{Series}


```c#
DicomWebResponse response = await client.RetrieveSeriesAsync(studyInstanceUid, seriesInstanceUid);
```

Cette série a deux instances (vert-carré et triangle rouge), de sorte que la réponse doit retourner les deux instances. Vérifiez que le code d’état de la réponse est OK et que les deux instances sont retournées.

### <a name="retrieve-metadata-of-all-instances-within-a-series"></a>Récupérer les métadonnées de toutes les instances d’une série

Cette réponse récupère les métadonnées pour toutes les instances au sein d’une même étude.

_Plus_

* Obtient/Studies/{Study}/Series/{Series}/Metadata

```c#
DicomWebResponse response = await client.RetrieveSeriesMetadataAsync(studyInstanceUid, seriesInstanceUid);
```

Cette série a deux instances (vert-carré et triangle rouge), de sorte que la réponse doit retourner des métadonnées pour les deux instances. Vérifiez que le code d’état de la réponse est OK et que les deux instances des métadonnées sont retournées.

### <a name="retrieve-a-single-instance-within-a-series-of-a-study"></a>Récupérer une instance unique au sein d’une série d’études

Cette requête récupère une instance unique.

_Plus_

* Obtient/Studies/{Study}/Series{Series}/instances/{instance}

```c#
DicomWebResponse response = await client.RetrieveInstanceAsync(studyInstanceUid, seriesInstanceUid, sopInstanceUid);
```

Cette réponse doit retourner uniquement le triangle rouge de l’instance. Vérifiez que la réponse a un code d’état OK et que l’instance est retournée.

### <a name="retrieve-metadata-of-a-single-instance-within-a-series-of-a-study"></a>Récupérer les métadonnées d’une instance unique au sein d’une série d’études

Cette requête récupère les métadonnées pour une instance unique au sein d’une même étude et série.

_Plus_

* Obtient/Studies/{Study}/Series/{Series}/instances/{instance}/Metadata

```c#
DicomWebResponse response = await client.RetrieveInstanceMetadataAsync(studyInstanceUid, seriesInstanceUid, sopInstanceUid);
```

Cette réponse doit retourner uniquement les métadonnées du triangle rouge de l’instance. Vérifiez que la réponse a un code d’état OK et que les métadonnées sont retournées.

### <a name="retrieve-one-or-more-frames-from-a-single-instance"></a>Récupérer un ou plusieurs frames à partir d’une seule instance

Cette requête récupère un ou plusieurs frames à partir d’une seule instance.

_Plus_

* Obtient/Studies/{Study}/Series/{Series}/instances/{instance}/frames/{frames}

```c#
DicomWebResponse response = await client.RetrieveFramesAsync(studyInstanceUid, seriesInstanceUid, sopInstanceUid, frames: new[] { 1 });

```

Cette réponse doit retourner le seul Frame à partir du triangle rouge. Vérifiez que la réponse a un code d’état OK et que le frame est retourné.

## <a name="query-dicom-qido"></a>Interroger DICOM (QIDO)

> [!NOTE]
> Reportez-vous à la [déclaration de conformité DICOM](dicom-services-conformance-statement.md#supported-search-parameters) pour les attributs DICOM pris en charge.

### <a name="search-for-studies"></a>Rechercher des études

Cette requête recherche une ou plusieurs études par attributs DICOM.

_Plus_

* /Studies ? StudyInstanceUID = {Study}

```c#
string query = $"/studies?StudyInstanceUID={studyInstanceUid}";
DicomWebResponse response = await client.QueryAsync(query);
```

Valide que la réponse comprend une étude et que le code de réponse est OK.

### <a name="search-for-series"></a>Rechercher une série

Cette requête recherche une ou plusieurs séries par attributs DICOM.

_Plus_

* /Series ? SeriesInstanceUID = {Series}

```c#
string query = $"/series?SeriesInstanceUID={seriesInstanceUid}";
DicomWebResponse response = await client.QueryAsync(query);
```

Valide que la réponse comprend une série et que le code de réponse est OK.

### <a name="search-for-series-within-a-study"></a>Rechercher des séries au sein d’une étude

Cette requête recherche une ou plusieurs séries au sein d’une même étude par les attributs DICOM.

_Plus_

* /Studies/{Study}/Series ? SeriesInstanceUID = {Series}

```c#
string query = $"/studies/{studyInstanceUid}/series?SeriesInstanceUID={seriesInstanceUid}";
DicomWebResponse response = await client.QueryAsync(query);
```

Valide que la réponse comprend une série et que le code de réponse est OK.

### <a name="search-for-instances"></a>Rechercher des instances

Cette requête recherche une ou plusieurs instances à l’aide d’attributs DICOM.

_Plus_

* /Instances ? SOPInstanceUID = {instance}

```c#
string query = $"/instances?SOPInstanceUID={sopInstanceUid}";
DicomWebResponse response = await client.QueryAsync(query);
```

Valide que la réponse comprend une instance et que le code de réponse est OK.

### <a name="search-for-instances-within-a-study"></a>Rechercher des instances dans une étude

Cette requête recherche une ou plusieurs instances au sein d’une même étude par les attributs DICOM.

_Plus_

* /Studies/{Study}/instances ? SOPInstanceUID = {instance}

```c#
string query = $"/studies/{studyInstanceUid}/instances?SOPInstanceUID={sopInstanceUid}";
DicomWebResponse response = await client.QueryAsync(query);
```

Valide que la réponse comprend une instance et que le code de réponse est OK.

### <a name="search-for-instances-within-a-study-and-series"></a>Rechercher des instances dans une étude et une série

Cette requête recherche une ou plusieurs instances au sein d’une seule étude et une seule série par attributs DICOM.

_Plus_

* /Studies/{Study}/Series/{Series}instances ? SOPInstanceUID = {instance}

```c#
string query = $"/studies/{studyInstanceUid}/series/{seriesInstanceUid}/instances?SOPInstanceUID={sopInstanceUid}";
DicomWebResponse response = await client.QueryAsync(query);
```

Valide que la réponse comprend une instance et que le code de réponse est OK.

## <a name="delete-dicom"></a>Supprimer DICOM

> [!NOTE]
> La suppression ne fait pas partie de la norme DICOM, mais a été ajoutée pour des raisons pratiques.

### <a name="delete-a-specific-instance-within-a-study-and-series"></a>Supprimer une instance spécifique dans une étude et une série

Cette demande supprime une seule instance au sein d’une seule étude et d’une seule série.

_Plus_

* SUPPRIMER/Studies/{Study}/Series/{Series}/instances/{instance}

```c#
string sopInstanceUidRed = "1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395";
DicomWebResponse response = await client.DeleteInstanceAsync(studyInstanceUid, seriesInstanceUid, sopInstanceUidRed);
```

Cette répose supprime l’instance de triangle rouge du serveur. Si l’opération réussit, le code d’état de la réponse ne contient pas de contenu.

### <a name="delete-a-specific-series-within-a-study"></a>Supprimer une série spécifique au sein d’une étude

Cette demande supprime une seule série (et toutes les instances enfants) au sein d’une même étude.

_Plus_

* SUPPRIMER/Studies/{Study}/Series/{Series}

```c#
DicomWebResponse response = await client.DeleteSeriesAsync(studyInstanceUid, seriesInstanceUid);
```

Cette réponse supprime l’instance de carré vert (il s’agit du seul élément restant dans la série) du serveur. Si l’opération réussit, le code d’état de la réponse ne contient pas de contenu.

### <a name="delete-a-specific-study"></a>Supprimer une étude spécifique

Cette demande supprime une seule étude (et toutes les séries enfants et instances).

_Plus_

* SUPPRIMER/Studies/{Study}

```c#
DicomWebResponse response = await client.DeleteStudyAsync(studyInstanceUid);
```

Cette réponse supprime l’instance de cercle bleu (il s’agit du seul élément restant dans la série) du serveur. Si l’opération réussit, le code d’état de la réponse ne contient pas de contenu.

### <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le service DICOM, consultez.

>[!div class="nextstepaction"]
>[Vue d’ensemble du service DICOM](dicom-services-overview.md)
