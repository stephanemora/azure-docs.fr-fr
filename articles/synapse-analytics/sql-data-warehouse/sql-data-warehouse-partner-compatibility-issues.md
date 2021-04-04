---
title: Problèmes de compatibilité avec des applications tierces et Azure Synapse Analytics
description: Décrit les problèmes connus que des applications tierces peuvent rencontrer avec Azure Synapse
services: synapse-analytics
author: periclesrocha
ms.service: synapse-analytics
ms.topic: troubleshooting
ms.subservice: ''
ms.date: 11/18/2020
ms.author: procha
ms.reviewer: jrasnick
ms.openlocfilehash: a1031656eaa5125d07ae078773379270b26625e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98121377"
---
# <a name="compatibility-issues-with-third-party-applications-and-azure-synapse-analytics"></a>Problèmes de compatibilité avec des applications tierces et Azure Synapse Analytics

Les applications conçues pour SQL Server fonctionneront en toute transparence avec les pools SQL dédiés d’Azure Synapse. Dans certains cas, toutefois, les fonctionnalités et les éléments de langage couramment utilisés dans SQL Server peuvent ne pas être disponibles dans Azure Synapse, ou ils peuvent se comporter différemment.

Cet article répertorie les problèmes connus que vous pouvez rencontrer lorsque vous utilisez des applications tierces avec Azure Synapse Analytics. 

## <a name="tableau-error-an-attempt-to-complete-a-transaction-has-failed-no-corresponding-transaction-found"></a>Erreur de Tableau : « Une tentative de finalisation d’une transaction a échoué. Aucune transaction correspondante n’a été trouvée. »

À partir de la version 10.0.11038.0 du pool SQL dédié Azure Synapse, certaines requêtes Tableau effectuant des appels de procédure stockée peuvent échouer avec le message d’erreur suivant : «  **[Microsoft][Pilote ODBC 17 pour SQL Server][SQL Server]111214 ; Une tentative de finalisation d’une transaction a échoué. Aucune transaction correspondante n’a été trouvée.**  »

### <a name="cause"></a>Cause

Il s’agit d’un problème dans le pool SQL dédié Azure Synapse provoqué par l’introduction de nouvelles procédures stockées système qui sont appelées automatiquement par les pilotes ODBC et JDBC. L’une de ces procédures stockées système peut provoquer l’abandon de transactions ouvertes en cas d’échec de l’exécution. Ce problème peut se produire en fonction de la logique de l’application cliente.

### <a name="solution"></a>Solution
Les clients constatant ce problème particulier lorsqu’ils utilisent Tableau connecté à des pools SQL dédiés Azure Synapse doivent définir FMTONLY sur Oui dans la connexion SQL. Pour Tableau Desktop et Tableau Server, vous devez utiliser un fichier de personnalisation de la source de données Tableau (TDC, Tableau Data source Customization) pour vous assurer que Tableau transmet ce paramètre au pilote.  

> [!NOTE] 
> Microsoft n’assure pas la prise en charge des outils tiers. Bien que nous ayons testé que cette solution fonctionne avec Tableau Desktop 2020.3.2, vous devriez utiliser cette solution de contournement sur votre propre capacité.
>

* [Pour savoir comment effectuer des personnalisations globales à l’aide d’un fichier TDC sur Tableau Desktop, reportez-vous à la documentation de Tableau Desktop.](https://help.tableau.com/current/pro/desktop/en-us/odbc_customize.htm)
* [Pour savoir comment effectuer des personnalisations globales à l’aide d’un fichier TDC sur Tableau Server, consultez « Using a .TDC File with Tableau Server ».](https://kb.tableau.com/articles/howto/using-a-tdc-file-with-tableau-server)

L’exemple ci-dessous montre un fichier TDC Tableau qui transmet le paramètre FMTONLY=YES à la chaîne de connexion SQL :

```json
<connection-customization class='azure_sql_dw' enabled='true' version='18.1'>
    <vendor name='azure_sql_dw' />
    <driver name='azure_sql_dw' />
    <customizations>        
        <customization name='odbc-connect-string-extras' value='UseFMTONLY=yes' />
    </customizations>
</connection-customization>
```
Pour plus d’informations sur l’utilisation des fichiers TDC, contactez le support technique de Tableau. 

## <a name="see-also"></a>Voir aussi

* [Éléments de langage T-SQL pour le pool SQL dédié dans Azure Synapse Analytics.](./sql-data-warehouse-reference-tsql-language-elements.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)
* [Instructions T-SQL prises en charge pour un pool SQL dédié dans Azure Synapse Analytics.](./sql-data-warehouse-reference-tsql-statements.md)