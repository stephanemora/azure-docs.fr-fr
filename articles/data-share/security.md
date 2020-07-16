---
title: Vue d’ensemble de la sécurité pour Azure Data Share
description: Vue d’ensemble de la sécurité pour Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 06/05/2020
ms.openlocfilehash: 10f31b74b461941b15f13e45f90b5fbc408c90fe
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108411"
---
# <a name="security-overview-for-azure-data-share"></a>Vue d’ensemble de la sécurité pour Azure Data Share

Cet article fournit une vue d’ensemble de la sécurité du service Azure Data Share.

## <a name="security-overview"></a>Présentation de la sécurité

Azure Data Share s’appuie sur la sécurité sous-jacente fournie par Azure pour protéger les données au repos et en transit. Les données sont chiffrées au repos, lorsque le magasin de données sous-jacent le permet. Les données en transit sont également chiffrées. Les métadonnées concernant un partage de données sont également chiffrées lorsqu’elles sont au repos et en transit. 

Les contrôles d’accès peuvent être définis au niveau des ressources Azure Data Share, afin que seules les personnes autorisées puissent y accéder. 

Azure Data Share tire parti de l’identité managée (anciennement MSI) pour accéder aux magasins de données utilisés pour le partage de données. Il n’y a pas d’échange d’informations d’identification entre un fournisseur de données et un consommateur de données. Pour plus d’informations sur l’identité managée, consultez [Identités managées pour les ressources Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities). Pour plus d’informations sur les rôles et les autorisations nécessaires pour partager des données, reportez-vous à [Rôles et conditions requises](concepts-roles-permissions.md).

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir comment commencer à partager des données, passez au tutoriel [Partager vos données](share-your-data.md).




