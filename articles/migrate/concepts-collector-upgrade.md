---
title: Mettre à niveau l’appliance Collector dans Azure Migrate | Microsoft Docs
description: Fournit des informations sur les mises à niveau de l’appliance Azure Migrate Collector.
author: musa-57
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/28/2018
ms.author: hamusa
services: azure-migrate
ms.openlocfilehash: 6080096194ec5b11f1d5593fcbb6732d1551667f
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47431390"
---
# <a name="collector-update-release-history"></a>Historique des versions de mise à jour de Collector

Cet article résume les informations de mise à niveau pour l’appliance Collector dans [Azure Migrate](migrate-overview.md).

Azure Migrate Collector est une appliance légère qui permet de découvrir un environnement vCenter local dans le cadre d’une évaluation avant la migration vers Azure. [Plus d’informations](concepts-collector.md)


## <a name="one-time-discovery-upgrade-versions"></a>Découverte unique : versions de mise à niveau

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

### <a name="version-10911"></a>Version 1.0.9.11

Valeurs de hachage pour le [package de mise à niveau 1.0.9.11](https://aka.ms/migrate/col/upgrade_9_11)

**Algorithme** | **Valeur de hachage**
--- | ---
MD5 | 0e36129ac5383b204720df7a56b95a60
SHA1 | aa422ef6aa6b6f8bc88f27727e80272241de1bdf
SHA256 | 5f76dbbe40c5ccab3502cc1c5f074e4b4bcbf356d3721fd52fb7ff583ff2b68f

### <a name="version-1097"></a>Version 1.0.9.7

Valeurs de hachage pour le [package de mise à niveau 1.0.9.7](https://aka.ms/migrate/col/upgrade_9_7)

**Algorithme** | **Valeur de hachage**
--- | ---
MD5 | 01ccd6bc0281f63f2a672952a2a25363
SHA1 | 3e6c57523a30d5610acdaa14b833c070bffddbff
SHA256 | e3ee031fb2d47b7881cc5b13750fc7df541028e0a1cc038c796789139aa8e1e6

## <a name="continuous-discovery-upgrade-versions"></a>Découverte en continu : versions de mise à niveau

Aucune mise à niveau n’est disponible pour l’appliance de découverte en continu.

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
