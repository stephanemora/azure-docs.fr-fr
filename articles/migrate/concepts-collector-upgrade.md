---
title: Mettre à niveau l’appliance Collector dans Azure Migrate | Microsoft Docs
description: Fournit des informations sur les mises à niveau de l’appliance Azure Migrate Collector.
author: musa-57
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 03/29/2019
ms.author: hamusa
services: azure-migrate
ms.openlocfilehash: 7cd44318716200d665ece9ffecc45225bdfb85eb
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58648671"
---
# <a name="collector-appliance-updates"></a>Mises à jour du collecteur

Cet article résume les informations de mise à niveau pour l’appliance Collector dans [Azure Migrate](migrate-overview.md).

Azure Migrate Collector est une appliance légère qui permet de découvrir un environnement vCenter local dans le cadre d’une évaluation avant la migration vers Azure. [Plus d’informations](concepts-collector.md)

## <a name="how-to-upgrade-the-appliance"></a>Comment mettre à niveau de l’appliance

Vous pouvez mettre à niveau le collecteur vers la version la plus récente sans retélécharger le fichier OVA.

1. Fermez toutes les fenêtres de navigateur et les ouvrent les fichiers/dossiers dans l’appliance.
2. Téléchargez le dernier package de mise à niveau à partir de la liste des mises à jour mentionnées dans cet article.
3. Pour vous assurer que le package téléchargé est sécurisé, ouvrez la fenêtre de commande d’administrateur et exécutez la commande suivante pour générer le hachage du fichier ZIP. Le hachage généré doit correspondre au hachage mentionné pour la version spécifique :

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    Exemple : **C:\>CertUtil -HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.14.zip SHA256)**
4. Copiez le fichier zip sur la machine virtuelle de l’appliance Collector.
5. Cliquez avec le bouton droit sur le fichier zip, puis sélectionnez **Tout extraire**.
6. Cliquez avec le bouton droit sur **Setup.ps1** > **Exécuter avec PowerShell** et suivez les instructions d’installation.

## <a name="collector-update-release-history"></a>Historique des versions de mise à jour de Collector

### <a name="continuous-discovery-upgrade-versions"></a>Découverte continue : versions de mise à niveau

#### <a name="version-101014-released-on-03292019"></a>Version 1.0.10.14 (publié le 29/03/2019)

Contient plusieurs améliorations de l’interface utilisateur.

Valeurs pour la mise à niveau de hachage [1.0.10.14 du package](https://aka.ms/migrate/col/upgrade_10_14)

**Algorithme** | **Valeur de hachage**
--- | ---
MD5 | 846b1eb29ef2806bcf388d10519d78e6
SHA1 | 6243239fa49c6b3f5305f77e9fd4426a392d33a0
SHA256 | fb058205c945a83cc4a31842b9377428ff79b08247f3fb8bb4ff30c125aa47ad

#### <a name="version-101012-released-on-03132019"></a>Version 1.0.10.12 (publié le 13/03/2019)

Contient des correctifs pour les problèmes dans la sélection d’Azure cloud dans l’appliance.

Valeurs pour la mise à niveau de hachage [1.0.10.12 du package](https://aka.ms/migrate/col/upgrade_10_12)

**Algorithme** | **Valeur de hachage**
--- | ---
MD5 | 27704154082344c058238000dff9ae44
SHA1 | 41e9e2fb71a8dac14d64f91f0fd780e0d606785e
SHA256 | c6e7504fcda46908b636bfe25b8c73f067e3465b748f77e50027e66f2727c2a9

### <a name="one-time-discovery-deprecated-now-previous-upgrade-versions"></a>Découverte unique (désormais dépréciée) : versions précédentes de mise à niveau

> [!NOTE]
> L’appliance de découverte unique est désormais dépréciée, car son utilisation dépend des paramètres de statistiques vCenter Server concernant la disponibilité des points de données de performances, et nécessite la collecte des données de compteurs de performance moyenne, ce qui a comme résultat d’attribuer une taille insuffisante aux machines virtuelles pour la migration vers Azure.

#### <a name="version-10916-released-on-10292018"></a>Version 1.0.9.16 (publiée le 29/10/2018)

Contient des correctifs pour les problèmes de PowerCLI rencontrés lors de la configuration de l’appliance.

Valeurs de hachage pour le [package de mise à niveau 1.0.9.16](https://aka.ms/migrate/col/upgrade_9_16)

**Algorithme** | **Valeur de hachage**
--- | ---
MD5 | d2c53f683b0ec7aaf5ba3d532a7382e1
SHA1 | e5f922a725d81026fa113b0c27da185911942a01
SHA256 | a159063ff508e86b4b3b7b9a42d724262ec0f2315bdba8418bce95d973f80cfc

#### <a name="version-10914"></a>Version 1.0.9.14

Valeurs de hachage pour le [package de mise à niveau 1.0.9.14](https://aka.ms/migrate/col/upgrade_9_14)

**Algorithme** | **Valeur de hachage**
--- | ---
MD5 | c5bf029e9fac682c6b85078a61c5c79c
SHA1 | af66656951105e42680dfcc3ec3abd3f4da8fdec
SHA256 | 58b685b2707f273aa76f2e1d45f97b0543a8c4d017cd27f0bdb220e6984cc90e

#### <a name="version-10913"></a>Version 1.0.9.13

Valeurs de hachage pour le [package de mise à niveau 1.0.9.13](https://aka.ms/migrate/col/upgrade_9_13)

**Algorithme** | **Valeur de hachage**
--- | ---
MD5 | 739f588fe7fb95ce2a9b6b4d0bf9917e
SHA1 | 9b3365acad038eb1c62ca2b2de1467cb8eed37f6
SHA256 | 7a49fb8286595f39a29085534f29a623ec2edb12a3d76f90c9654b2f69eef87e


## <a name="next-steps"></a>Étapes suivantes

- [Découvrez plus en détail](concepts-collector.md) l’appliance Collector.
- [Exécutez une évaluation](tutorial-assessment-vmware.md) pour les machines virtuelles VMware.
