---
title: Versions de Key Vault
description: Les différentes versions d’Azure Key Vault
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e8622dcc-59a3-4f4b-9f63-cd2232515a65
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: beb73be66f36ccf95fe27d4d8128106cd12722a8
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2018
ms.locfileid: "34011878"
---
# <a name="key-vault-versions"></a>Versions de Key Vault

## <a name="2016-10-01---managed-storage-account-keys"></a>01-10-2016 : clés de compte de stockage géré

Été 2017 : fonctionnalité Clés de compte de stockage ajoutée pour faciliter l’intégration au service Stockage Azure. Pour plus d’informations, consultez la rubrique relative à la [vue d’ensemble des clés de compte de stockage géré](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-storage-keys).

## <a name="2016-10-01---soft-delete"></a>01-10-2016 : suppression réversible

Été 2017 : fonctionnalité de suppression réversible ajoutée pour améliorer la protection des données de vos coffres de clés et objets de coffre de clés. Pour plus d’informations, consultez la rubrique [Vue d’ensemble de la suppression réversible d’Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).

## <a name="2015-06-01---certificate-management"></a>01-06-2015 : gestion des certificats

La fonctionnalité de gestion des certificats a été ajoutée à la version de disponibilité générale du 01-06-2015 le 26 septembre 2016.

## <a name="2015-06-01---general-availability"></a>01-06-2015 : disponibilité générale

La version de disponibilité générale du 01-06-2015 a été annoncée le 24 juin 2015.

Les modifications suivantes ont été apportées à cette version :

- Suppression d’une clé (champ « use » supprimé).
- Obtention des informations sur une clé (champ « use » supprimé).
- Importation d’une clé dans un coffre (champ « use » supprimé).
- Restauration d’une clé (champ « use » supprimé).
- Remplacement de « RSA_OAEP » par « RSA-OAEP » dans les algorithmes RSA. Voir [Présentation des clés, des secrets et des certificats](about-keys-secrets-and-certificates.md).

## <a name="2015-02-01-preview"></a>01-02-2015 : préversion 

Seconde préversion annoncée le 20 avril 2015. Pour plus d’informations, consultez le billet de blog [REST API Update](http://blogs.technet.com/b/kv/archive/2015/04/20/empty-3.aspx) (Mise à jour de l’API REST).

Les tâches suivantes ont été mises à jour :

- Liste des clés d’un coffre (ajout de la prise en charge de la pagination à l’opération).
- Liste des versions d’une clé (ajout de l’opération pour dresser la liste des versions d’une clé).
- Liste des secrets d’un coffre (ajout de la prise en charge de la pagination).
- Liste des versions d’un secret (ajout pour dresser la liste des versions d’un secret).
- Toutes les opérations (ajout de timestamps de création/mise à jour aux attributs).
- Création d’un secret (ajout d’un élément Content-Type aux secrets).
- Création d’une clé (ajout d’étiquettes en tant qu’informations facultatives).
- Création d’un secret (ajout d’étiquettes en tant qu’informations facultatives).
- Mise à jour d’une clé (ajout d’étiquettes en tant qu’informations facultatives).
- Mise à jour d’un secret (ajout d’étiquettes en tant qu’informations facultatives).
- Modification de la taille maximale des secrets de 10 Ko à 25 Ko. Voir [Présentation des clés, des secrets et des certificats](about-keys-secrets-and-certificates.md).

## <a name="2014-12-08-preview"></a>08-12-2014 : préversion

Première préversion annoncée le 8 janvier 2015.

## <a name="see-also"></a>Voir aussi
- [Présentation des clés, des secrets et des certificats](about-keys-secrets-and-certificates.md)
