---
title: 'Azure Key Vault : Déplacer un coffre vers une autre région | Microsoft Docs'
description: Conseils pour le déplacement d’un coffre de clés vers une autre région.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another region.
ms.openlocfilehash: e65a723ac9daafdc09896a50e197034104408df2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82254070"
---
# <a name="moving-an-azure-key-vault-across-regions"></a>Déplacement d’un coffre Azure Key Vault d’une région à l’autre

## <a name="overview"></a>Vue d’ensemble

Key Vault ne prend pas en charge une opération de déplacement de ressources permettant de déplacer un coffre de clés vers une autre région. Cet article traitera des solutions de contournement possibles pour les entreprises qui doivent déplacer un coffre de clés vers une autre région. Chaque option a ses limites, et il est essentiel de comprendre les implications de ces solutions de contournement avant de les essayer dans un environnement de production.

Si vous devez déplacer un coffre de clés vers une autre région, la solution consiste à créer un coffre de clés dans la région souhaitée et à copier manuellement chaque secret du coffre de clés existant dans le nouveau coffre de clés. Cette opération peut être effectuée de l’une des manières suivantes.

## <a name="design-considerations"></a>Remarques sur la conception

* Les noms Key Vault sont globalement uniques. Vous ne pourrez pas réutiliser le même nom de coffre.

* Vous devrez reconfigurer les stratégies d’accès et les paramètres de configuration réseau dans le nouveau coffre de clés.

* Vous devrez reconfigurer la suppression réversible et la protection contre le vidage dans le nouveau coffre de clés.

* L’opération de sauvegarde et de restauration ne préserve pas les paramètres de rotation automatique. Vous devrez peut-être reconfigurer ces paramètres.

## <a name="option-1---use-the-key-vault-backup-and-restore-commands"></a>Option 1 : Utiliser les commandes de sauvegarde et de restauration du coffre de clés

Vous pouvez sauvegarder chaque secret, clé et certificat individuels dans votre coffre à l’aide de la commande de sauvegarde. Vos secrets seront téléchargés sous la forme d’un blob chiffré. Vous pouvez ensuite restaurer le blob dans votre nouveau coffre de clés. Les commandes sont documentées dans le lien ci-dessous.

[Commandes Azure Key Vault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/?view=azurermps-6.13.0#key_vault)

### <a name="limitations"></a>Limites

* Vous ne pouvez pas sauvegarder un coffre de clés dans une zone géographique et le restaurer dans une autre. En savoir plus sur les zones géographiques Azure. [Lien](https://azure.microsoft.com/global-infrastructure/geographies/)

* La commande de sauvegarde effectue une sauvegarde de toutes les versions de chaque secret. Si vous avez un secret comportant un grand nombre de versions antérieures (plus de 10), il y a un risque que la requête dépasse la taille de requête maximale autorisée et que l’opération échoue.

## <a name="option-2---manually-download-and-upload-secrets"></a>Option 2 : Télécharger et charger manuellement les secrets

Certains types de secrets peuvent être téléchargés manuellement. Par exemple, vous pouvez télécharger des certificats en tant que fichier .pfx. Cette option élimine les restrictions géographiques pour certains types de secrets, tels que les certificats. Vous pouvez charger les fichiers .pfx dans un coffre de clés de n’importe quelle région. Votre secret sera téléchargé dans un format non protégé par mot de passe. Vous serez responsable de la sécurisation de vos secrets une fois qu’ils auront quitté Key Vault pendant le déplacement.
