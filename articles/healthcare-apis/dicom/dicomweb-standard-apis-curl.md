---
title: Utilisation &trade; d’API standard DICOMweb avec des API de santé sur Azure
description: Dans ce didacticiel, vous allez apprendre à utiliser les API standard DICOMweb avec la fonction de boucles.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.date: 07/16/2021
ms.author: aersoy
ms.openlocfilehash: afa4b294b71a3cde198001d204d4670cf8ca1ec1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121785118"
---
# <a name="using-dicomwebtrade-standard-apis-with-curl"></a>Utilisation &trade; d’API standard DICOMWeb avec l’accolade

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Ce didacticiel utilise la boucle pour illustrer l’utilisation du service DICOM.

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

>[!NOTE]
>Chacun de ces fichiers représente une instance unique et fait partie de la même étude. En outre, le carré vert et le triangle rouge font partie de la même série, tandis que le cercle bleu se trouve dans une série distincte.

## <a name="prerequisites"></a>Prérequis

Pour utiliser les &trade; API standard DICOMWeb, vous devez disposer d’une instance du service DICOM déployée. Si vous n’avez pas déjà déployé une instance du service DICOM, consultez [déployer le service DICOM à l’aide de l’portail Azure](deploy-dicom-services-in-azure.md).

Une fois que vous avez déployé une instance du service DICOM, récupérez l’URL de votre App service :

1. Connectez-vous au [portail Azure](https://ms.portal.azure.com/).
2. Recherchez les **ressources récentes** et sélectionnez votre instance de service DICOM.
3. Copiez l' **URL du service** de votre service DICOM. 
4. Si vous n’avez pas encore obtenu de jeton, consultez [obtenir un jeton d’accès pour le service DICOM à l’aide de Azure CLI](dicom-get-access-token-azure-cli.md). 

Pour ce code, nous allons accéder à un service Azure Preview public. Il est important de ne pas charger les informations d’intégrité privée (PHI).


## <a name="working-with-the-dicom-service"></a>Utilisation du service DICOM
 
La norme DICOMweb utilise &trade; beaucoup les `multipart/related` requêtes http combinées aux en-têtes Accept spécifiques à DICOM. Les développeurs familiarisés avec d’autres API REST trouvent souvent les manipulations de DICOMweb &trade; standard. Toutefois, une fois qu’elle est en cours d’exécution, elle est facile à utiliser. Il suffit simplement de vous familiariser avec la prise en main.

Les commandes de boucle contiennent chacune au moins une des variables qui doivent être remplacées. Pour simplifier l’exécution des commandes, recherchez et remplacez les variables suivantes en les remplaçant par vos valeurs spécifiques :

* {URL du service} Il s’agit de l’URL permettant d’accéder à votre service DICOM configuré dans le Portail Azure, par exemple ```https://<workspacename-dicomservicename>.dicom.azurehealthcareapis.com``` . Veillez à spécifier la version dans le cadre de l’URL lors de l’exécution des requêtes. Pour plus d’informations, consultez la documentation sur le contrôle [de version des API pour le service DICOM](api-versioning-dicom-service.md).
* {Path-to-dicoms} : chemin d’accès au répertoire qui contient le fichier Red-triangle. DCM, tel que `C:/dicom-server/docs/dcms`
    * Veillez à utiliser des barres obliques comme séparateurs et à terminer le répertoire _sans_ barre oblique finale.


## <a name="uploading-dicom-instances-stow"></a>Chargement des instances DICOM (ARRIMer)

### <a name="store-instances-using-multipartrelated"></a>Store-instances-using-multipart/related

Cette requête a pour fonction de montrer comment charger des fichiers DICOM à l’aide de parties multiples/associées. 

>[!NOTE]
>Le service DICOM est plus strict que la norme DICOM. Toutefois, l’exemple ci-dessous illustre une demande de publication conforme étroitement à la norme.

_Plus_

* Chemin d’accès :.. /studies
* Méthode : POST
* Headers:
    * Accept : application/DICOM + JSON
    * Content-type : multipart/related ; type = "application/DICOM"
    * Autorisation : Bearer {valeur de jeton}
* Corps :
    * Content-type : application/DICOM pour chaque fichier téléchargé, séparé par une valeur limite

Certains langages de programmation et outils se comportent différemment. Par exemple, certains vous obligent à définir votre propre limite. Pour ceux-ci, vous devrez peut-être utiliser un en-tête Content-type légèrement modifié. Les éléments suivants ont été utilisés avec succès.
* Content-type : multipart/related ; tapez = "application/DICOM"; limite = ABCD1234
* Content-type : multipart/related ; limite = ABCD1234
* Content-type : multipart/related

```
curl --location --request POST "{Service URL}/v{version}/studies"
--header "Accept: application/dicom+json"
--header "Content-Type: multipart/related; type=\"application/dicom\""
--header "Authorization: Bearer {token value}"
--form "file1=@{path-to-dicoms}/red-triangle.dcm;type=application/dicom"
--trace-ascii "trace.txt"
```

### <a name="store-instances-for-a-specific-study"></a>Stocker des instances pour une étude spécifique

Cette demande montre comment charger des fichiers DICOM en utilisant des parties multiples/associées à une étude désignée.

_Plus_
* Chemin d’accès :.. /studies/{study}
* Méthode : POST
* Headers:
    * Accept : application/DICOM + JSON
    * Content-type : multipart/related ; type = "application/DICOM"
    * Autorisation : Bearer {valeur de jeton}
* Corps :
    * Content-type : application/DICOM pour chaque fichier téléchargé, séparé par une valeur limite

Certains langages de programmation et outils se comportent différemment. Par exemple, certains vous obligent à définir votre propre limite. Pour ceux-ci, vous devrez peut-être utiliser un en-tête Content-type légèrement modifié. Les éléments suivants ont été utilisés avec succès.

 * Content-type : multipart/related ; tapez = "application/DICOM"; limite = ABCD1234
 * Content-type : multipart/related ; limite = ABCD1234
 * Content-type : multipart/related

```
curl --request POST "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420"
--header "Accept: application/dicom+json"
--header "Content-Type: multipart/related; type=\"application/dicom\"
--header "Authorization: Bearer {token value}"
--form "file1=@{path-to-dicoms}/blue-circle.dcm;type=application/dicom"
```

### <a name="store-single-instance"></a>Store-instance unique

> [!NOTE]
> Il s’agit d’une API non standard qui permet le chargement d’un seul fichier DICOM sans devoir configurer la publication pour les parties multiples/associées. Bien que les poignées de boucles soient en plusieurs parties/associées, cette API permet aux outils tels que poster de charger des fichiers dans le service DICOM.

La méthode suivante est requise pour télécharger un seul fichier DICOM.

_Plus_
* Chemin d’accès :.. /studies
* Méthode : POST
* Headers:
   * Accept : application/DICOM + JSON
   * Type de contenu : application/DICOM
   * Autorisation : Bearer {valeur de jeton}
* Corps :
    * Contient un seul fichier DICOM comme octets binaires.

```
curl --location --request POST "{Service URL}/v{version}/studies"
--header "Accept: application/dicom+json"
--header "Content-Type: application/dicom"
--header "Authorization: Bearer {token value}"
--data-binary "@{path-to-dicoms}/green-square.dcm"
```

## <a name="retrieving-dicom-wado"></a>Récupération de DICOM (WADO)

### <a name="retrieve-all-instances-within-a-study"></a>Récupération de toutes les instances dans une étude

Cette requête récupère toutes les instances au sein d’une même étude et les retourne sous la forme d’une collection d’octets en plusieurs parties/associés.

_Plus_
* Chemin d’accès :.. /studies/{study}
* Méthode : GET
* Headers:
   * Accept : multipart/related ; tapez = "application/DICOM"; Transfer-Syntax = *
   * Autorisation : Bearer {valeur de jeton}

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420"
--header "Accept: multipart/related; type=\"application/dicom\"; transfer-syntax=*"
--header "Authorization: Bearer {token value}"
--output "suppressWarnings.txt"
```

Cette commande va affichera les octets téléchargés dans le fichier de sortie (suppressWarnings.txt), mais ce ne sont pas des fichiers DICOM directs, mais uniquement une représentation textuelle du téléchargement à parties multiples/associé.

### <a name="retrieve-metadata-of-all-instances-in-study"></a>Récupérer les métadonnées de toutes les instances dans l’étude

Cette requête récupère les métadonnées pour toutes les instances au sein d’une même étude.

_Plus_
* Chemin d’accès :.. /studies/{study}/metadata
* Méthode : GET
* Headers:
   * Accept : application/DICOM + JSON
   * Autorisation : Bearer {valeur de jeton}

Cette commande va affichera les octets téléchargés dans le fichier de sortie (suppressWarnings.txt), mais ce ne sont pas des fichiers DICOM directs, mais uniquement une représentation textuelle du téléchargement à parties multiples/associé.

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/metadata"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```

### <a name="retrieve-all-instances-within-a-series"></a>Récupérer toutes les instances d’une série

Cette requête récupère toutes les instances d’une seule série, puis les retourne sous la forme d’une collection d’octets en plusieurs parties/associés.

_Plus_
* Chemin d’accès :.. /studies/{study}/series/{series}
* Méthode : GET
* Headers:
   * Accept : multipart/related ; tapez = "application/DICOM"; Transfer-Syntax = *
   * Autorisation : Bearer {valeur de jeton}

Cette commande va affichera les octets téléchargés dans le fichier de sortie (suppressWarnings.txt), mais il ne s’agit pas du fichier DICOM, mais uniquement d’une représentation textuelle du téléchargement à parties multiples/associé.

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652"
--header "Accept: multipart/related; type=\"application/dicom\"; transfer-syntax=*"
--header "Authorization: Bearer {token value}"
--output "suppressWarnings.txt"
```

### <a name="retrieve-metadata-of-all-instances-within-a-series"></a>Récupérer les métadonnées de toutes les instances d’une série

Cette requête récupère les métadonnées pour toutes les instances au sein d’une même étude.

_Plus_
* Chemin d’accès :.. /studies/{study}/series/{series}/metadata
* Méthode : GET
* Headers:
   * Accept : application/DICOM + JSON
   * Autorisation : Bearer {valeur de jeton}

```
curl --request GET "{Service URL}/v{version}/studies1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652/metadata"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```
 
### <a name="retrieve-a-single-instance-within-a-series-of-a-study"></a>Récupérer une instance unique au sein d’une série d’études

Cette requête récupère une instance unique et la retourne sous la forme d’un flux d’octets au format DICOM.

_Plus_
* Chemin d’accès :.. /studies/{study}/series{series}/instances/{instance}
* Méthode : GET
* Headers:
   * Accept : application/DICOM ; Transfer-Syntax = *
   * Autorisation : Bearer {valeur de jeton}

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652/instances/1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395"
--header "Accept: application/dicom; transfer-syntax=*"
--header "Authorization: Bearer {token value}"
--output "suppressWarnings.txt"
```

### <a name="retrieve-metadata-of-a-single-instance-within-a-series-of-a-study"></a>Récupérer les métadonnées d’une instance unique au sein d’une série d’études

Cette requête récupère les métadonnées pour une instance unique au sein d’une même étude et série.

_Plus_
* Chemin d’accès :.. /studies/{study}/series/{series}/instances/{instance}/metadata
* Méthode : GET
* Headers:
  * Accept : application/DICOM + JSON
  * Autorisation : Bearer {valeur de jeton}

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652/instances/1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395/metadata"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```

### <a name="retrieve-one-or-more-frames-from-a-single-instance"></a>Récupérer un ou plusieurs frames à partir d’une seule instance

Cette requête récupère un ou plusieurs frames à partir d’une seule instance, puis les retourne sous la forme d’une collection d’octets multiparts/associés. Plusieurs frames peuvent être récupérés en passant une liste de numéros de trames séparés par des virgules.  Toutes les instances DICOM avec des images ont au moins une image, qui est souvent simplement l’image associée à l’instance elle-même.

_Plus_
* Chemin d’accès :.. /Studies/{Study}/Series{Series}/instances/{instance}/frames/1, 2, 3
* Méthode : GET
* Headers:
   * Accept : multipart/related ; tapez = "application/octet-stream"; Transfer-Syntax = 1.2.840.10008.1.2.1 (valeur par défaut) ou
   * Accept : multipart/related ; tapez = "application/octet-stream"; Transfer-Syntax = * ou
   * Accept : multipart/related ; tapez = "application/octet-stream";
   * Autorisation : Bearer {valeur de jeton}

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652/instances/1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395/frames/1"
--header "Accept: multipart/related; type=\"application/octet-stream\"; transfer-syntax=1.2.840.10008.1.2.1"
--header "Authorization: Bearer {token value}"
--output "suppressWarnings.txt"
```

## <a name="query-dicom-qido"></a>Interroger DICOM (QIDO)

Dans les exemples suivants, nous allons Rechercher des éléments à l’aide de leurs identificateurs uniques. Vous pouvez également rechercher d’autres attributs, tels que `PatientName` .

### <a name="search-for-studies"></a>Rechercher des études

Cette requête permet de rechercher une ou plusieurs études par attributs DICOM.

Pour plus d’informations sur les attributs DICOM pris en charge, consultez l' [instruction de conformité DICOM](dicom-services-conformance-statement.md).

_Plus_
* Chemin d’accès :.. Stud? StudyInstanceUID = {Study}
* Méthode : GET
* Headers:
   * Accept : application/DICOM + JSON
   * Autorisation : Bearer {valeur de jeton}

```
curl --request GET "{Service URL}/v{version}/studies?StudyInstanceUID=1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```

### <a name="search-for-series"></a>Rechercher une série

Cette requête permet de rechercher une ou plusieurs séries à l’aide d’attributs DICOM.

Pour plus d’informations sur les attributs DICOM pris en charge, consultez l' [instruction de conformité DICOM](dicom-services-conformance-statement.md).

_Plus_
* Chemin d’accès :.. AX100? SeriesInstanceUID = {Series}
* Méthode : GET
* Headers:
   * Accept : application/DICOM + JSON
   * Autorisation : Bearer {valeur de jeton}

```
curl --request GET "{Service URL}/v{version}/series?SeriesInstanceUID=1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```

### <a name="search-for-series-within-a-study"></a>Rechercher des séries au sein d’une étude

Cette requête permet de rechercher une ou plusieurs séries au sein d’une même étude par les attributs DICOM.

Pour plus d’informations sur les attributs DICOM pris en charge, consultez l' [instruction de conformité DICOM](dicom-services-conformance-statement.md).

_Plus_
* Chemin d’accès :.. /studies/{study}/series? SeriesInstanceUID = {Series}
* Méthode : GET
* Headers:
   * Accept : application/DICOM + JSON
   * Autorisation : Bearer {valeur de jeton}

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series?SeriesInstanceUID=1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```

### <a name="search-for-instances"></a>Rechercher des instances

Cette requête permet de rechercher une ou plusieurs instances à l’aide d’attributs DICOM.

Pour plus d’informations sur les attributs DICOM pris en charge, consultez l' [instruction de conformité DICOM](dicom-services-conformance-statement.md).

_Plus_
* Chemin d’accès :.. fois? SOPInstanceUID = {instance}
* Méthode : GET
* Headers:
   * Accept : application/DICOM + JSON
   * Autorisation : Bearer {valeur de jeton}

```
curl --request GET "{Service URL}/v{version}/instances?SOPInstanceUID=1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```

### <a name="search-for-instances-within-a-study"></a>Rechercher des instances dans une étude

Cette requête permet de rechercher une ou plusieurs instances au sein d’une même étude par les attributs DICOM.

Pour plus d’informations sur les attributs DICOM pris en charge, consultez l' [instruction de conformité DICOM](dicom-services-conformance-statement.md).

_Plus_
* Chemin d’accès :.. /studies/{study}/instances? SOPInstanceUID = {instance}
* Méthode : GET
* Headers:
   * Accept : application/DICOM + JSON
   * Autorisation : Bearer {valeur de jeton} 

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/instances?SOPInstanceUID=1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```

### <a name="search-for-instances-within-a-study-and-series"></a>Rechercher des instances dans une étude et une série

Cette requête permet de rechercher une ou plusieurs instances au sein d’une seule étude et d’une seule série par attributs DICOM.

Pour plus d’informations sur les attributs DICOM pris en charge, consultez l' [instruction de conformité DICOM](dicom-services-conformance-statement.md) .

_Plus_
* Chemin d’accès :.. /studies/{study}/series/{series}/instances? SOPInstanceUID = {instance}
* Méthode : GET
* Headers:
   * Accept : application/DICOM + JSON
   * Autorisation : Bearer {valeur de jeton}

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652/instances?SOPInstanceUID=1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```


## <a name="delete-dicom"></a>Supprimer DICOM 

### <a name="delete-a-specific-instance-within-a-study-and-series"></a>Supprimer une instance spécifique dans une étude et une série

Cette demande supprime une seule instance au sein d’une seule étude et d’une seule série.

La suppression ne fait pas partie de la norme DICOM, mais elle a été ajoutée pour des raisons pratiques.

_Plus_
* Chemin d’accès :.. /studies/{study}/series/{series}/instances/{instance}
* Méthode : DELETE
* Headers:
   * Autorisation : Bearer {valeur de jeton}  

```
curl --request DELETE "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652/instances/1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395"
--header "Authorization: Bearer {token value}"
```

### <a name="delete-a-specific-series-within-a-study"></a>Supprimer une série spécifique au sein d’une étude

Cette demande supprime une seule série (et toutes les instances enfants) au sein d’une même étude.

La suppression ne fait pas partie de la norme DICOM, mais elle a été ajoutée pour des raisons pratiques.

_Plus_
* Chemin d’accès :.. /studies/{study}/series/{series}
* Méthode : DELETE
* Headers:
   * Autorisation : Bearer {valeur de jeton}

```
curl --request DELETE "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652"
--header "Authorization: Bearer {token value}"
```

### <a name="delete-a-specific-study"></a>Supprimer une étude spécifique

Cette demande supprime une seule étude (et toutes les séries enfants et instances).

La suppression ne fait pas partie de la norme DICOM, mais elle a été ajoutée pour des raisons pratiques.

_Plus_
* Chemin d’accès :.. /studies/{study}
* Méthode : DELETE
* Headers:
   * Autorisation : Bearer {valeur de jeton}

```
curl--request DELETE "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498
--header "Authorization: Bearer {token value}"
```

### <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le service DICOM, consultez.

>[!div class="nextstepaction"]
>[Vue d’ensemble du service DICOM](dicom-services-overview.md)
