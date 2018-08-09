---
title: Format de fichier de propriétés et de métadonnées d’Azure Import/Export | Microsoft Docs
description: Découvrez comment spécifier les métadonnées et les propriétés d’un ou plusieurs objets blob qui font partie d’un travail d’importation ou d’exportation.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 5a886244b43ad006a95e9be0350d9c69fd987ad9
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526230"
---
# <a name="azure-importexport-service-metadata-and-properties-file-format"></a>Format de fichier de propriétés et de métadonnées du service Azure Import/Export
Vous pouvez spécifier les métadonnées et les propriétés d’un ou plusieurs objets blob dans le cadre d’un travail d’importation ou d’exportation. Pour définir les métadonnées ou les propriétés d’objets blob créés dans le cadre d’un travail d’importation, vous devez fournir un fichier de métadonnées ou de propriétés sur le disque dur contenant les données à importer. Pour un travail d’exportation, les métadonnées et les propriétés sont écrites dans un fichier de métadonnées ou de propriétés inclus sur le disque dur retourné.  
  
## <a name="metadata-file-format"></a>Format du fichier de métadonnées  
Le format d’un fichier de métadonnées est le suivant :  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Metadata>  
[<metadata-name-1>metadata-value-1</metadata-name-1>]  
[<metadata-name-2>metadata-value-2</metadata-name-2>]  
. . .  
</Metadata>  
```
  
|Élément XML|type|Description|  
|-----------------|----------|-----------------|  
|`Metadata`|Élément racine|Élément racine du fichier de métadonnées.|  
|`metadata-name`|Chaîne|facultatif. L’élément XML spécifie le nom des métadonnées de l’objet blob, et sa valeur spécifie la valeur du paramètre des métadonnées.|  
  
## <a name="properties-file-format"></a>Format du fichier de propriétés  
Le format d’un fichier de propriétés est le suivant :  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
[<Last-Modified>date-time-value</Last-Modified>]  
[<Etag>etag</Etag>]  
[<Content-Length>size-in-bytes<Content-Length>]  
[<Content-Type>content-type</Content-Type>]  
[<Content-MD5>content-md5</Content-MD5>]  
[<Content-Encoding>content-encoding</Content-Encoding>]  
[<Content-Language>content-language</Content-Language>]  
[<Cache-Control>cache-control</Cache-Control>]  
</Properties>  
```
  
|Élément XML|type|Description|  
|-----------------|----------|-----------------|  
|`Properties`|Élément racine|Élément racine du fichier de propriétés.|  
|`Last-Modified`|Chaîne|facultatif. Heure de dernière modification de l’objet blob. Travaux d’exportation uniquement.|  
|`Etag`|Chaîne|facultatif. Valeur ETag de l’objet blob. Travaux d’exportation uniquement.|  
|`Content-Length`|Chaîne|facultatif. Taille de l’objet blob en octets. Travaux d’exportation uniquement.|  
|`Content-Type`|Chaîne|facultatif. Type de contenu de l’objet blob.|  
|`Content-MD5`|Chaîne|facultatif. Hash MD5 de l’objet blob.|  
|`Content-Encoding`|Chaîne|facultatif. Codage du contenu de l’objet blob.|  
|`Content-Language`|Chaîne|facultatif. Langage du contenu de l’objet blob.|  
|`Cache-Control`|Chaîne|facultatif. Chaîne de contrôle du cache de l’objet blob.|  

## <a name="next-steps"></a>Étapes suivantes

Consultez les pages [Définir les propriétés d’objets blob](/rest/api/storageservices/set-blob-properties), [Définir les métadonnées d’objets blob](/rest/api/storageservices/set-blob-metadata) et [Définir et extraire les propriétés et métadonnées de ressources blob](/rest/api/storageservices/setting-and-retrieving-properties-and-metadata-for-blob-resources) pour obtenir des instructions détaillées sur la définition de propriétés et de métadonnées d’objets blob.
