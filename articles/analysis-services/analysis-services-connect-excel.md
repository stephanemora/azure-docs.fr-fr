---
title: Se connecter à Azure Analysis Services avec Excel | Microsoft Docs
description: Découvrez comment vous connecter à un serveur Azure Analysis Services à l’aide d’Excel.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b3ab749b4610f57e19ad1af0fc74d41340d7555b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34192987"
---
# <a name="connect-with-excel"></a>Connexion avec Excel

Une fois que vous avez créé un serveur et que vous y avez déployé un modèle tabulaire, les clients peuvent s’y connecter et commencer à explorer les données.


## <a name="connect-in-excel"></a>Se connecter dans Excel

La connexion à un serveur dans Excel est prise en charge grâce à la fonction Obtenir les données dans Excel 2016. La connexion à l’aide de l’Assistant d’importation de table dans Power Pivot n’est pas prise en charge. 

**Se connecter dans Excel 2016**

1. Dans Excel 2016, sur le ruban **Données**, cliquez sur **Obtenir les données externes** > **À partir d’autres sources** > **À partir d’Analysis Services**.

2. Dans l’Assistant de connexion de données, dans **Nom du serveur**, entrez le nom du serveur avec le protocole et l’URI. Par exemple, asazure://westcentralus.asazure.windows.net/advworks. Ensuite, dans **Informations d’identification d’ouverture de session**, sélectionnez **Utiliser le nom d’utilisateur et le mot de passe suivants**, puis tapez le nom d’utilisateur professionnel, par exemple nancy@adventureworks.com, et le mot de passe.

    > [!IMPORTANT]
    > Si vous vous connectez avec un compte Microsoft, Live ID, Yahoo, Gmail, etc., ou si vous devez vous connecter en utilisant l’authentification multifacteur, laissez le champ de mot de passe vide. Vous êtes invité à saisir un mot de passe après avoir cliqué sur Suivant.

    ![Se connecter à partir de l’ouverture de session Excel](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. Dans **Sélectionner une base de données et une table**, sélectionnez la base de données et un modèle ou une perspective, puis cliquez sur **Terminer**.
   
    ![Se connecter à partir du modèle de sélection Excel](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>Voir aussi
[Bibliothèques clientes](analysis-services-data-providers.md)   
[Gérer votre serveur](analysis-services-manage.md)     


