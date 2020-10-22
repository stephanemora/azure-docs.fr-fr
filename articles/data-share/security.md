---
title: Vue d’ensemble de la sécurité pour Azure Data Share
description: Vue d’ensemble de la sécurité pour Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 06/05/2020
ms.openlocfilehash: 5d815c27ecc7825f0bc1e6772654b094a799b63d
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92216553"
---
# <a name="security-overview-for-azure-data-share"></a>Vue d’ensemble de la sécurité pour Azure Data Share

Cet article fournit une vue d’ensemble de la sécurité du service Azure Data Share.

## <a name="security-overview"></a>Présentation de la sécurité

Azure Data Share s’appuie sur la sécurité sous-jacente fournie par Azure pour protéger les données au repos et en transit. Les données sont chiffrées au repos, lorsque le magasin de données sous-jacent le permet. Les données en transit sont également chiffrées. Les métadonnées concernant un partage de données sont également chiffrées lorsqu’elles sont au repos et en transit. 

Les contrôles d’accès peuvent être définis au niveau des ressources Azure Data Share, afin que seules les personnes autorisées puissent y accéder. 

Azure Data Share tire parti de l’identité managée (anciennement MSI) pour accéder aux magasins de données utilisés pour le partage de données. Il n’y a pas d’échange d’informations d’identification entre un fournisseur de données et un consommateur de données. Pour plus d’informations sur l’identité managée, consultez [Identités managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md). Pour plus d’informations sur les rôles et les autorisations nécessaires pour partager des données, reportez-vous à [Rôles et conditions requises](concepts-roles-permissions.md).

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir comment commencer à partager des données, passez au tutoriel [Partager vos données](share-your-data.md).