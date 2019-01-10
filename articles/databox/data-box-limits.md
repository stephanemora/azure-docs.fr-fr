---
title: Limites d’Azure Data Box | Microsoft Docs
description: Décrit les limites du système et les tailles recommandées pour les connexions et les composants de Microsoft Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 12/20/2018
ms.author: alkohli
ms.openlocfilehash: 5849611ad346fc5ef1f0efd1e262d2ace8097520
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53723450"
---
# <a name="azure-data-box-limits"></a>Limites d’Azure Data Box

Tenez compte de ces limites quand vous déployez et utilisez votre solution Microsoft Azure Data Box. Le tableau suivant décrit ces limites pour Data Box.


## <a name="data-box-service-limits"></a>Limites du service Data Box

 - Si vous utilisez plusieurs comptes de stockage avec le service Data Box, ils doivent tous appartenir à la même région Azure.
 - Nous vous recommandons de ne pas utiliser plus de trois comptes de stockage. L’utilisation de plus de comptes de stockage peut affecter les performances.

## <a name="data-box-limits"></a>Limites du service Data Box

- Le service Data Box peut stocker jusqu’à 500 millions de fichiers.

## <a name="azure-storage-limits"></a>Limites du stockage Azure

Cette section décrit les limites du service Stockage Azure et les conventions de nommage en vigueur pour les fichiers Azure, les objets blob de blocs Azure et les objets blob de pages Azure, telles qu’applicables au service Data Box. Examinez soigneusement les limites de stockage et suivez toutes les recommandations.

Pour les informations les plus récentes sur les limites du service de stockage Azure et les bonnes pratiques pour nommer les partages, les conteneurs et les fichiers, accédez à :

- [Affectation de noms et de références aux conteneurs](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Affectation de noms et de références aux partages](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Conventions d’objets blob de blocs et d’objets blob de pages](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> S’il existe des fichiers ou des répertoires qui dépassent les limites du service Stockage Azure, ou qui ne sont pas conformes aux conventions de nommage des fichiers/objets blob Azure, ces fichiers ou répertoires ne sont pas ingérés dans Stockage Azure par le biais du service Data Box.

## <a name="data-upload-caveats"></a>Mises en garde relatives au chargement de données

- Ne copiez pas les données directement dans des partages précréés. Vous devez créer un dossier dans le partage, puis copier les données dans ce dossier.
- Un dossier sous *StorageAccount_BlockBlob* et *StorageAccount_PageBlob* est un conteneur. Par exemple, les conteneurs sont créés sous la forme suivante *StorageAccount_BlockBlob/container* et *StorageAccount_PageBlob/container*.
- Chaque dossier créé directement sous *StorageAccount_AzureFiles* est traduit en partage de fichiers Azure.
- Si un objet Azure existant (tel qu’un objet blob ou un fichier) présent dans le cloud a le même nom que l’objet qui est en train d’être copié, le service Data Box remplace le fichier dans le cloud.
- Chaque fichier écrit dans les partages *StorageAccount_BlockBlob* et *StorageAccount_PageBlob* est chargé respectivement en tant qu’objet blob de blocs et objet blob de pages.
- Le stockage d’objets blob Azure ne prend pas en charge les répertoires. Si vous créez un dossier sous le dossier *StorageAccount_BlockBlob*, des dossiers virtuels sont alors créés dans le nom de l’objet blob. Pour Azure Files, la structure du répertoire est conservée.
- Une hiérarchie de répertoires vides (sans fichiers) créée sous les dossiers *StorageAccount_BlockBlob* et *StorageAccount_PageBlob* n’est pas chargée.
- S’il se produit des erreurs lors du chargement des données sur Azure, un journal des erreurs est créé dans le compte de stockage cible. Le chemin menant à ce journal des erreurs est disponible à l’issue du chargement ; vous pouvez consulter ce journal afin de procéder aux corrections. Ne supprimez pas les données de la source sans avoir préalablement vérifié les données chargées.

## <a name="azure-storage-account-size-limits"></a>Limites de la taille du compte de stockage Azure

Voici les limites de la taille des données qui sont copiées dans le compte de stockage. Assurez-vous que les données que vous chargez sont conformes à ces limites. Pour consulter les dernières informations relatives à ces limites, accédez à [Objectifs de mise à l’échelle du stockage Blob Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets) et [Objectifs de mise à l’échelle Azure Files](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets).

| Taille des données copiées dans le compte de stockage Azure                      | Limite par défaut          |
|---------------------------------------------------------------------|------------------------|
| Objet blob de blocs et objet blob de pages                                            | 500 Tio par compte de stockage. <br> Comprend les données à partir de toutes les sources, notamment Data Box.|
| Fichier Azure                                                          | 5 Tio par partage.<br> Tous les dossiers sous *StorageAccount_AzureFiles* doivent respecter cette limite.       |

## <a name="azure-object-size-limits"></a>Limites de taille des objets Azure

Voici les tailles des objets Azure qui peuvent être écrits. Assurez-vous que tous les fichiers chargés sont en conformité avec ces limites.

| Type d’objet Azure | Limite par défaut                                             |
|-------------------|-----------------------------------------------------------|
| Objet blob de blocs        | ~4.75 Tio                                                 |
| Objet blob de pages         | 8 Tio <br> Chaque fichier chargé dans le format d’objet blob de pages doit être de 512 octets alignés (un multiple entier), sinon le chargement échoue. <br> Les disques VHD et VHDX sont de 512 octets alignés. |
| Fichier Azure        | 1 Tio                                                      |

## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Conventions de nommage des objets blob de blocs,des objets blob de pages et des fichiers Azure

| Entité                                       | Conventions                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Noms de conteneur pour les objets blob de blocs et les objets blob de pages | Doit être un nom DNS valide dont la longueur est comprise entre 3 et 63 caractères. <br>  Doit commencer par une lettre ou un chiffre. <br> Ne peut contenir que des lettres minuscules, des chiffres et le trait d’union (-). <br> Chaque trait d’union (-) doit être immédiatement précédé et suivi d’une lettre ou d’un chiffre. <br> Les traits d’union consécutifs ne sont pas autorisés dans les noms. |
| Noms de partage pour les fichiers Azure                  | Identique à ce qui précède                                                                                                                                                                                                                                                                                                             |
| Noms de répertoires et de fichiers pour les fichiers Azure     |<li> Conservent et respectent la casse, et ne doivent pas dépasser 255 caractères. </li><li> Ne peuvent pas se terminer par une barre oblique avant (/). </li><li>Si renseignée, elle sera automatiquement supprimée. </li><li> Les caractères suivants ne sont pas autorisés : `" \ / : | < > * ?`</li><li> Les caractères d’URL réservées doivent être correctement placés dans une séquence d’échappement. </li><li> Les caractères de chemin d’URL illégal ne sont pas autorisés. Les points de code comme \uE000 ne sont pas des caractères Unicode valide. Certains caractères Unicode ou ASCII, comme les caractères de contrôle (0x00 to 0x1F, \u0081, etc.) ne sont pas autorisés. Pour les règles concernant les chaînes Unicode dans HTTP/1.1, consultez RFC 2616, Section 2.2 : Règles de base et RFC 3987. </li><li> Les noms de fichiers suivants ne sont pas autorisés : LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK$, le point (.) et les deux points (..).</li>|
| Noms d’objet blob pour les objets blob de blocs et les objets blob de pages      | </li><li>Les noms d’objet blob respectent la casse et peuvent contenir une combinaison de caractères. </li><li>Le nom d’objet blob doit comprendre entre 1 et 1 024 caractères. </li><li>Les caractères d’URL réservées doivent être correctement placés dans une séquence d’échappement. </li><li>Le nombre de segments de ligne comprenant le nom d’objet blob ne peut pas dépasser 254. Un segment de chemin représente la chaîne située entre des caractères délimiteurs consécutifs (par exemple, la barre oblique « / ») qui correspond au nom d’un répertoire virtuel.</li> |
