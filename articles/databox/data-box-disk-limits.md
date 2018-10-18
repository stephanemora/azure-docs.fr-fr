---
title: Limites d’Azure Data Box Disk | Microsoft Docs
description: Décrit les limites système et les tailles recommandées pour Microsoft Azure Data Box Disk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 09/04/2018
ms.author: alkohli
ms.openlocfilehash: 1a4fe30881f06d8af851a67f389a6faafbe3dfef
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49389460"
---
# <a name="azure-data-box-disk-limits-preview"></a>Limites d’Azure Data Box Disk (préversion)


Tenez compte de ces limites quand vous déployez et utilisez votre solution Microsoft Azure Data Box Disk. 

> [!IMPORTANT] 
> Azure Data Box Disk est disponible en préversion. Veuillez lire les [conditions d’utilisation de la préversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) avant de déployer cette solution. 


## <a name="data-box-service-limits"></a>Limites du service Data Box

 - Le service Data Box est disponible uniquement aux États-Unis, dans l’Union européenne, au Canada et en Australie dans toutes les régions Azure du cloud public Azure.
 - Un seul compte de stockage est pris en charge avec Data Box Disk.

## <a name="data-box-disk-performance"></a>Performances de Data Box Disk

Lors de tests utilisant des disques connectés via USB 3.0, les performances des disques ont atteint 430 Mo/s. Les chiffres réels varient en fonction de la taille de fichier utilisée. Pour les fichiers plus petits, vous pouvez constater des performances moins élevées.

## <a name="azure-storage-limits"></a>Limites du stockage Azure

Cette section décrit les limites du service Stockage Azure et les conventions de nommage en vigueur pour les fichiers Azure, les objets blob de blocs Azure et les objets blob de pages Azure, telles qu’applicables au service Data Box. Examinez soigneusement les limites de stockage et suivez toutes les recommandations.

Pour les informations les plus récentes sur les limites du service de stockage Azure et les bonnes pratiques pour nommer les partages, les conteneurs et les fichiers, accédez à :

- [Affectation de noms et de références aux conteneurs](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Affectation de noms et de références aux partages](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Conventions d’objets blob de blocs et d’objets blob de pages](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> S’il existe des fichiers ou des répertoires qui dépassent les limites du service Stockage Azure, ou qui ne sont pas conformes aux conventions de nommage des fichiers/objets blob Azure, ces fichiers ou répertoires ne sont pas ingérés dans Stockage Azure par le biais du service Data Box.

## <a name="data-upload-caveats"></a>Mises en garde relatives au chargement de données

- Ne copiez pas les données directement sur les disques. Copiez les données dans des dossiers *ObjetBlobDeBlocs* et *ObjetBlobDePages* créés au préalable.
- Un dossier présent sous *ObjetBlobDeBlocs* et *ObjetBlobDePages* est un conteneur. Par exemple, les conteneurs sont créés sous la forme suivante *ObjetBlobDeBlocs/conteneur* et *ObjetBlobDePages/conteneur*.
- Si un objet Azure existant (tel qu’un objet blob) présent dans le cloud a le même nom que l’objet qui est en train d’être copié, le disque Data Box remplace le fichier dans le cloud.
- Chaque fichier écrit dans les partages *ObjetBlobDeBlocs* et *ObjetBlobDePages* est chargé respectivement en tant qu’objet blob de blocs et objet blob de pages.
- Une hiérarchie de répertoires vides (sans fichiers) créée sous les dossiers *ObjetBlobDeBlocs* et *ObjetBlobDePages* n’est pas chargée.
- S’il se produit des erreurs lors du chargement des données sur Azure, un journal des erreurs est créé dans le compte de stockage cible. Le chemin menant à ce journal des erreurs est disponible dans le portail à l’issue du chargement ; vous pouvez consulter ce journal afin de procéder aux corrections. Ne supprimez pas les données de la source sans avoir préalablement vérifié les données chargées.

## <a name="azure-storage-account-size-limits"></a>Limites de la taille du compte de stockage Azure

Voici les limites de la taille des données qui sont copiées dans le compte de stockage. Assurez-vous que les données que vous chargez sont conformes à ces limites. Pour consulter les dernières informations relatives à ces limites, accédez à [Objectifs de mise à l’échelle du stockage Blob Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets) et [Objectifs de mise à l’échelle Azure Files](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets).

| Taille des données copiées dans le compte de stockage Azure                      | Limite par défaut          |
|---------------------------------------------------------------------|------------------------|
| Objet blob de blocs et objet blob de pages                                            | 500 To par compte de stockage. <br> Comprend les données à partir de toutes les sources, notamment Data Box Disk.|


## <a name="azure-object-size-limits"></a>Limites de taille des objets Azure

Voici les tailles des objets Azure qui peuvent être écrits. Assurez-vous que tous les fichiers chargés sont en conformité avec ces limites.

| Type d’objet Azure | Limite par défaut                                             |
|-------------------|-----------------------------------------------------------|
| Objet blob de blocs        | ~ 8 To                                                 |
| Objet blob de pages         | 1 To <br> (Chaque fichier chargé dans le format d’objet blob de pages doit être de 512 octets alignés [un multiple entier], sinon le chargement échoue. <br> Les disques VHD et VHDX sont de 512 octets alignés.) |


## <a name="azure-block-blob-and-page-blob-naming-conventions"></a>Conventions de nommage des objets blob de blocs et des objets blob de pages Azure

| Entité                                       | Conventions                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Noms de conteneur pour les objets blob de blocs et les objets blob de pages | Doit être un nom DNS valide dont la longueur est comprise entre 3 et 63 caractères. <br>  Doit commencer par une lettre ou un chiffre. <br> Ne peut contenir que des lettres minuscules, des chiffres et le trait d’union (-). <br> Chaque trait d’union (-) doit être immédiatement précédé et suivi d’une lettre ou d’un chiffre. <br> Les traits d’union consécutifs ne sont pas autorisés dans les noms. |
| Noms d’objet blob pour les objets blob de blocs et les objets blob de pages      | Les noms d’objet blob respectent la casse et peuvent contenir une combinaison de caractères. <br> Le nom d’objet blob doit comprendre entre 1 et 1 024 caractères. <br> Les caractères d’URL réservées doivent être correctement placés dans une séquence d’échappement. <br>Le nombre de segments de ligne comprenant le nom d’objet blob ne peut pas dépasser 254. Un segment de chemin représente la chaîne située entre des caractères délimiteurs consécutifs (par exemple, la barre oblique « / ») qui correspond au nom d’un répertoire virtuel. |


## <a name="next-steps"></a>Étapes suivantes
* Relire les [Conditions requises pour le système Data Box](data-box-system-requirements.md)
