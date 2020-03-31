---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/30/2019
ms.author: alkohli
ms.openlocfilehash: 94fe099984fae77c65658d7085a8540ff4f2448b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67177519"
---
Cette section décrit les limites du service Stockage Azure et les conventions d’affectation de noms requises pour les fichiers Azure, les objets blob de blocs Azure et les objets blob de pages Azure, telles qu’applicables au service Data Box Gateway/Data Box Edge. Examinez soigneusement les limites de stockage et suivez toutes les recommandations.

Pour les informations les plus récentes sur les limites du service de stockage Azure et les bonnes pratiques pour nommer les partages, les conteneurs et les fichiers, accédez à :

- [Affectation de noms et de références aux conteneurs](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Affectation de noms et de références aux partages](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Conventions d’objets blob de blocs et d’objets blob de pages](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> S’il existe des fichiers ou des répertoires qui dépassent les limites du service Stockage Azure, ou qui ne sont pas conformes aux conventions d’affectation de noms des fichiers/objets blob Azure, ces fichiers ou répertoires ne sont pas ingérés dans Stockage Azure par le biais du service Data Box Gateway/Data Box Edge.