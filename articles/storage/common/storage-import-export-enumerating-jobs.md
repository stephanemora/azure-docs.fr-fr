---
title: Liste de tous vos travaux Azure Import/Export | Microsoft Docs
description: Découvrez comment répertorier tous les travaux d’un abonnement qui sont liés au service Azure Import/Export.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 017208c6fca7c4e55a45070f5aa21652e83e7e8d
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55462916"
---
# <a name="enumerating-jobs-in-the-azure-importexport-service"></a>Énumération des travaux dans le service Azure Import/Export
Pour énumérer tous les travaux d’un abonnement, appelez l’opération [Répertorier les travaux](/rest/api/storageimportexport/jobs#Jobs_List). `List Jobs` retourne une liste de travaux, ainsi que les attributs suivants :

-   le type de travail (importation ou exportation) ;

-   l’état du travail actuel ;

-   le compte de stockage associé au travail.

## <a name="next-steps"></a>Étapes suivantes

* [Utilisation de l’API REST du service Import/Export](storage-import-export-using-the-rest-api.md)
