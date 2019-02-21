---
title: Mettre à niveau l’appliance Collector dans Azure Migrate | Microsoft Docs
description: Fournit des informations sur les mises à niveau de l’appliance Azure Migrate Collector.
author: musa-57
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: hamusa
services: azure-migrate
ms.openlocfilehash: f29556c8f2a5aa727ce80632eaccf5e1ed6d7c1a
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415542"
---
# <a name="collector-update-release-history"></a>Historique des versions de mise à jour de Collector

Cet article résume les informations de mise à niveau pour l’appliance Collector dans [Azure Migrate](migrate-overview.md).

Azure Migrate Collector est une appliance légère qui permet de découvrir un environnement vCenter local dans le cadre d’une évaluation avant la migration vers Azure. [Plus d’informations](concepts-collector.md)

## <a name="continuous-discovery-upgrade-versions"></a>Découverte continue : versions de mise à niveau

Aucune mise à niveau n’est disponible pour l’appliance de découverte en continu.

## <a name="one-time-discovery-deprecated-now-previous-upgrade-versions"></a>Découverte unique (désormais dépréciée) : versions précédentes de mise à niveau

> [!NOTE]
> L’appliance de découverte unique est désormais dépréciée, car son utilisation dépend des paramètres de statistiques vCenter Server concernant la disponibilité des points de données de performances, et nécessite la collecte des données de compteurs de performance moyenne, ce qui a comme résultat d’attribuer une taille insuffisante aux machines virtuelles pour la migration vers Azure.

### <a name="version-10916-released-on-10292018"></a>Version 1.0.9.16 (publiée le 29/10/2018)

Contient des correctifs pour les problèmes de PowerCLI rencontrés lors de la configuration de l’appliance.

Valeurs de hachage pour le [package de mise à niveau 1.0.9.16](https://aka.ms/migrate/col/upgrade_9_16)

**Algorithme** | **Valeur de hachage**
--- | ---
MD5 | d2c53f683b0ec7aaf5ba3d532a7382e1
SHA1 | e5f922a725d81026fa113b0c27da185911942a01
SHA256 | a159063ff508e86b4b3b7b9a42d724262ec0f2315bdba8418bce95d973f80cfc

### <a name="version-10914"></a>Version 1.0.9.14

Valeurs de hachage pour le [package de mise à niveau 1.0.9.14](https://aka.ms/migrate/col/upgrade_9_14)

**Algorithme** | **Valeur de hachage**
--- | ---
MD5 | c5bf029e9fac682c6b85078a61c5c79c
SHA1 | af66656951105e42680dfcc3ec3abd3f4da8fdec
SHA256 | 58b685b2707f273aa76f2e1d45f97b0543a8c4d017cd27f0bdb220e6984cc90e

### <a name="version-10913"></a>Version 1.0.9.13

Valeurs de hachage pour le [package de mise à niveau 1.0.9.13](https://aka.ms/migrate/col/upgrade_9_13)

**Algorithme** | **Valeur de hachage**
--- | ---
MD5 | 739f588fe7fb95ce2a9b6b4d0bf9917e
SHA1 | 9b3365acad038eb1c62ca2b2de1467cb8eed37f6
SHA256 | 7a49fb8286595f39a29085534f29a623ec2edb12a3d76f90c9654b2f69eef87e


## <a name="run-an-upgrade"></a>Exécuter une mise à niveau

Vous pouvez mettre à niveau le collecteur vers la version la plus récente sans retélécharger le fichier OVA.

1. Téléchargez le dernier package de mise à niveau dans la liste ci-dessous.
2. Pour vous assurer que le correctif logiciel téléchargé est sécurisé, ouvrez la fenêtre de commande d’administrateur et exécutez la commande suivante pour générer le hachage du fichier ZIP. Le hachage généré doit correspondre au hachage mentionné pour la version spécifique :

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    Exemple : **C:\>CertUtil -HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.14.zip SHA256)**
3. Copiez le fichier zip sur la machine virtuelle de l’appliance Collector.
4. Cliquez avec le bouton droit sur le fichier zip, puis sélectionnez **Tout extraire**.
5. Cliquez avec le bouton droit sur **Setup.ps1** > **Exécuter avec PowerShell** et suivez les instructions d’installation.


## <a name="next-steps"></a>Étapes suivantes

- [Découvrez plus en détail](concepts-collector.md) l’appliance Collector.
- [Exécutez une évaluation](tutorial-assessment-vmware.md) pour les machines virtuelles VMware.
