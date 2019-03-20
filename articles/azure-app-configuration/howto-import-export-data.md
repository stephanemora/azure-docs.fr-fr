---
title: Importer ou exporter des données avec la Configuration de l’application Azure | Microsoft Docs
description: Découvrez comment importer ou exporter des données vers ou à partir de la Configuration de l’application Azure
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 846472e00bc048de906ee8e14f6de38e366f3571
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58225227"
---
# <a name="import-or-export-configuration-data"></a>Importer ou exporter des données de configuration

Les données de prend en charge la Configuration de l’application Azure importer et exporter des opérations. Utiliser ces opérations pour travailler avec des données de configuration dans les données en bloc et exchange entre votre magasin de configuration d’application et le code projet. Par exemple, vous pouvez configurer le magasin de configuration une seule application pour le test et l’autre pour la production. Vous pouvez ensuite copier entre eux via un fichier de paramètres de l’application afin que vous n’êtes pas obligé d’entrer des données à deux reprises.

Cet article fournit un guide pour l’importation et exportation de données avec la Configuration de l’application.

## <a name="import-data"></a>Importer des données

Importation apporte à stocker des données dans une Configuration d’application à partir d’une source existante, au lieu d’entrer manuellement l’il de configuration. Utilisez la fonction d’importation pour migrer des données dans un magasin de configuration d’application ou les données agrégées à partir de plusieurs sources. Configuration de l’application prend en charge l’importation à partir d’un fichier JSON, YAML ou propriétés.

Importer des données à l’aide la [Azure portal](https://aka.ms/azconfig/portal) ou [Azure CLI](./scripts/cli-import.md). À partir du portail Azure, procédez comme suit :

1. Accédez à votre magasin de configuration d’application et sélectionnez **Import/Export**.

2. Sur le **importation** onglet, sélectionnez **Source service** > **fichier de Configuration**.

3. Sélectionnez **de langue** > **type de fichier**.

4. Sélectionnez le **dossier** icône, puis accédez au fichier à importer.

    ![Fichier d’importation](./media/import-file.png)

5. Sélectionnez un **séparateur**et entrez éventuellement un **préfixe** à utiliser pour les noms de clé importées.

6. Si vous le souhaitez, sélectionnez un **étiquette**.

7. Sélectionnez **appliquer** pour terminer l’importation.

    ![Fichier d’importation terminé](./media/import-file-complete.png)

## <a name="export-data"></a>Exporter des données

Exportation écrit les données de configuration stockées dans la Configuration de l’application vers une autre destination. Utilisez la fonction d’exportation, par exemple, pour enregistrer les données dans un magasin de configuration d’application dans un fichier qui est incorporé avec le code de votre application pendant le déploiement.

Exporter des données à l’aide la [Azure portal](https://aka.ms/azconfig/portal) ou [Azure CLI](./scripts/cli-export.md). À partir du portail Azure, procédez comme suit :

1. Accédez à votre magasin de configuration d’application et sélectionnez **Import/Export**.

2. Sur le **exporter** onglet, sélectionnez **service cible** > **fichier de Configuration**.

3. Entrez éventuellement un **préfixe** et sélectionnez un **étiquette** et un point-à-temps pour l’exportation des clés.

4. Sélectionnez un **type de fichier** > **séparateur**.

5. Sélectionnez **appliquer** pour terminer l’exportation.

    ![Fichier d’exportation terminé](./media/export-file-complete.png)

## <a name="next-steps"></a>Étapes suivantes

* [Démarrage rapide : Créer une application web ASP.NET](./quickstart-aspnet-core-app.md)  
