---
title: Sources de données prises en charge par Azure Analysis Services | Microsoft Docs
description: Décrit les sources de données et les connecteurs pris en charge pour les modèles de données tabulaires 1200 et versions ultérieures dans Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c92baf5c97597a0161f402cc458e90bb3e637d6c
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74170657"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Sources de données prises en charge dans Azure Analysis Services

Les sources de données et connecteurs affichés dans Obtenir des données ou l’Assistant Importation dans Visual Studio concernent à la fois Azure Analysis Services et SQL Server Analysis Services. Cependant, toutes les sources de données et tous les connecteurs indiqués ne sont pas pris en charge dans Azure Analysis Services. Les types de sources de données auxquelles vous pouvez vous connecter dépendent de nombreux facteurs tels que le niveau de compatibilité du modèle, les connecteurs de données disponibles, le type d’authentification, les fournisseurs et la prise en charge de la passerelle de données locale. 

## <a name="azure-data-sources"></a>Sources de données Azure

|Source de données  |En mémoire  |DirectQuery  |
|---------|---------|---------|
|Azure SQL Database<sup>[2](#azsqlmanaged)</sup>     |   OUI      |    OUI      |
|Azure SQL Data Warehouse     |   OUI      |   OUI       |
|Stockage Blob Azure<sup>[1](#tab1400a)</sup>     |   OUI       |    Non      |
|Stockage Table Azure<sup>[1](#tab1400a)</sup>    |   OUI       |    Non      |
|Azure Cosmos DB<sup>[1](#tab1400a)</sup>     |  OUI        |  Non        |
|Azure Data Lake Store (Gen1)<sup>[1](#tab1400a)</sup>, <sup>[4](#gen2)</sup>      |   OUI       |    Non      |
|Azure HDInsight HDFS<sup>[1](#tab1400a)</sup>     |     OUI     |   Non       |
|Azure HDInsight Spark<sup>[1](#tab1400a)</sup>, <sup>[3](#databricks)</sup>     |   OUI       |   Non       |
||||

<a name="tab1400a">1</a> - Modèles tabulaires 1400 ou supérieurs uniquement.   
<a name="azsqlmanaged">2</a> - Azure SQL Database Managed Instance est pris en charge. Comme l'instance gérée s'exécute dans Azure VNet avec une adresse IP privée, le point de terminaison public doit être activé sur l'instance. Si cette option n'est pas activée, une passerelle de données locale est requise.    
<a name="databricks">3</a> - Azure Databricks avec connecteur Spark n’est actuellement pas pris en charge.   
<a name="gen2">4</a> – Le connecteur ADLS Gen2 n’est pas pris en charge pour le moment. Toutefois, le connecteur Stockage Blob peut être utilisé avec une source de données ADLS Gen2.


**Fournisseur**   
Les modèles en mémoire et DirectQuery qui se connectent aux sources de données Azure utilisent le fournisseur de données .NET Framework pour SQL Server.

## <a name="other-data-sources"></a>Autres sources de données

La connexion aux sources de données locales à partir d’un serveur Azure Analysis Services requiert une passerelle locale. Lorsque vous utilisez une passerelle, des fournisseurs 64 bits sont requis.

### <a name="in-memory-and-directquery"></a>En mémoire et DirectQuery

|Source de données | Fournisseur en mémoire | Fournisseur DirectQuery |
|  --- | --- | --- |
| SQL Server |Microsoft OLE DB Driver pour SQL Server MSOLEDBSQL (recommandé), SQL Server Native Client 11.0, Fournisseur de données .NET Framework pour SQL Server | Fournisseur de données .NET Framework pour SQL Server |
| SQL Server Data Warehouse |Microsoft OLE DB Driver pour SQL Server MSOLEDBSQL (recommandé), SQL Server Native Client 11.0, Fournisseur de données .NET Framework pour SQL Server | Fournisseur de données .NET Framework pour SQL Server |
| Oracle | Fournisseur OLE DB pour Oracle, Fournisseur de données Oracle pour .NET |Fournisseur de données Oracle pour .NET |
| Teradata |Fournisseur OLE DB pour Teradata, Fournisseur de données Teradata pour .NET |Fournisseur de données Teradata pour .NET |
| | | |

### <a name="in-memory-only"></a>En mémoire uniquement

|Source de données  |  
|---------|
|Base de données Access     |  
|Active Directory<sup>[1](#tab1400b)</sup>     |  
|Analysis Services     |  
|Système de plateforme d’analyse     |  
|Fichier CSV  |
|Dynamics CRM<sup>[1](#tab1400b)</sup>     |  
|Classeur Excel     |  
|Exchange<sup>[1](#tab1400b)</sup>     |  
|Dossier<sup>[1](#tab1400b)</sup>     |
|IBM Informix<sup>[1](#tab1400b)</sup> (Bêta) |
|Document JSON<sup>[1](#tab1400b)</sup>     |  
|Lignes issues d’un fichier binaire<sup>[1](#tab1400b)</sup>     | 
|MySQL Database     | 
|Flux OData<sup>[1](#tab1400b)</sup>     |  
|Requête ODBC     | 
|OLE DB     |   
|Base de données PostgreSQL<sup>[1](#tab1400b)</sup>    | 
|Objets Salesforce<sup>[1](#tab1400b)</sup> |  
|Rapports Salesforce<sup>[1](#tab1400b)</sup> |
|SAP HANA<sup>[1](#tab1400b)</sup>    |  
|SAP Business Warehouse<sup>[1](#tab1400b)</sup>    |  
|Liste SharePoint<sup>[1](#tab1400b)</sup>, <sup>[2](#filesSP)</sup>     |   
|Base de données Sybase     |  
|Fichier TXT  |
|Table XML<sup>[1](#tab1400b)</sup>    |  
||
 
<a name="tab1400b">1</a> - Modèles tabulaires 1400 ou supérieurs uniquement.   
<a name="filesSP">2</a> - Les fichiers contenus dans SharePoint en local ne sont pas pris en charge.

## <a name="specifying-a-different-provider"></a>Spécifier un autre fournisseur

Les modèles de données dans Azure Analysis Services peuvent nécessiter différents fournisseurs de données lors de la connexion à certaines sources de données. Dans certains cas, les modèles tabulaires de connexion aux sources de données à l’aide de fournisseurs natifs tels que SQL Server Native Client (SQLNCLI11) peuvent renvoyer une erreur. Si vous utilisez des fournisseurs natifs autres que MSOLEDBSQL, vous pouvez avoir le message d’erreur suivant : **Le fournisseur 'SQLNCLI11.1' n’est pas inscrit**. Ou bien, si vous disposez d’un modèle de requête directe (DirectQuery) de connexion à des sources de données locales et si vous utilisez des fournisseurs natifs, vous pouvez avoir le message d’erreur suivant : **Erreur lors de la création du jeu de lignes OLE DB. Syntaxe incorrecte à proximité de 'LIMIT'** .

Lorsque vous migrez un modèle tabulaire SQL Server Analysis Services local vers Azure Analysis Services, il peut être nécessaire de modifier le fournisseur.

**Pour spécifier un fournisseur**

1. Dans Visual Studio > **Explorateur de modèles tabulaires** > **Sources de données**, cliquez avec le bouton droit sur une connexion de source de données, puis sur **Modifier la source de données**.
2. Dans **Modifier la connexion**, cliquez sur **Avancé** pour ouvrir la fenêtre de propriétés avancées.
3. Dans **Définir les propriétés avancées** > **Fournisseurs**, sélectionnez le fournisseur approprié.

## <a name="impersonation"></a>Emprunt d’identité
Dans certains cas, il peut être nécessaire de spécifier un autre compte d’emprunt d’identité. Le compte d’emprunt d’identité peut être spécifié dans Visual Studio ou SSMS.

Pour les sources de données locales :

* Si vous utilisez l’authentification SQL, l’emprunt d’identité doit être le compte de service.
* Si vous utilisez l’authentification Windows, définissez l’utilisateur/le mot de passe Windows. Pour SQL Server, l’authentification Windows avec un compte d’emprunt d’identité spécifique est pris en charge pour les modèles de données en mémoire uniquement.

Pour les sources de données cloud :

* Si vous utilisez l’authentification SQL, l’emprunt d’identité doit être le compte de service.

## <a name="oauth-credentials"></a>Informations d’identification OAuth

Pour les modèles tabulaires au niveau de compatibilité 1 400 ou plus, Azure SQL Database, Azure SQL Data Warehouse, Dynamics 365 et la liste SharePoint prennent en charge les informations d’identification OAuth. Azure Analysis Services gère l’actualisation des jetons pour les sources de données OAuth afin d’éviter un dépassement des délais d’expiration pour les opérations d’actualisation de longue durée. Pour générer des jetons valides, définissez les informations d’identification à l’aide de SSMS.

## <a name="next-steps"></a>Étapes suivantes
[Passerelle locale](analysis-services-gateway.md)   
[Gérer votre serveur](analysis-services-manage.md)   

