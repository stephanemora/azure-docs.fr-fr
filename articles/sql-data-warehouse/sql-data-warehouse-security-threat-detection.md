---
title: Détection de menaces - Azure SQL Data Warehouse | Microsoft Docs
description: Configurez la détection des menaces et explorez les événements suspects dans Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kavithaj
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 7ff23235e9681301984e13e346b23f277662bb5c
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43300932"
---
# <a name="threat-detection-in-azure-sql-data-warehouse"></a>Détection de menaces dans Azure SQL Data Warehouse
Configurez la détection des menaces et explorez les événements suspects dans Azure SQL Data Warehouse.

## <a name="what-is-threat-detection"></a>En quoi consiste la détection des menaces
Threat Detection permet de détecter les activités base de données anormales indiquant la présence potentielle de menaces de sécurité pour la base de données. 

Threat Detection fournit une nouvelle couche de sécurité qui permet aux clients de détecter les menaces potentielles et d’y répondre à mesure qu’elles se présentent en générant des alertes de sécurité sur les activités anormales. Les utilisateurs peuvent explorer les événements suspects à l’aide de l’ [audit d’Azure SQL Data Warehouse](sql-data-warehouse-auditing-overview.md) pour déterminer s’ils sont le résultat d’une tentative d’accès, d’une violation ou d’une exploitation des données dans l’entrepôt de données.
Threat Detection vous permet de réagir facilement aux menaces potentielles envers l'entrepôt de données sans avoir à acquérir une expertise de la sécurité ou à gérer des systèmes de surveillance de la sécurité avancés.

Par exemple, il détecte certaines activités de base de données anormales indiquant des tentatives d’injection SQL potentielles. L’injection SQL représente l’un des problèmes de sécurité auxquels sont le plus exposées les applications web, et est utilisée pour cibler les applications pilotées par des données. Les pirates exploitent les vulnérabilités des applications pour injecter des instructions SQL nuisibles dans les champs de saisie d’application afin de violer ou modifier les données contenues dans la base de données.

## <a name="set-up-threat-detection-for-your-database"></a>Configurer Threat Detection pour votre base de données
1. Lancez le Portail Azure sur [https://portal.azure.com](https://portal.azure.com).
2. Accédez au volet de configuration de l'entrepôt SQL Data Warehouse que voulez surveiller. Dans le panneau Paramètres, sélectionnez **Audit et détection des menaces**.
   
    ![Volet de navigation](media/sql-data-warehouse-security-threat-detection/1_td_click_on_settings.png)
3. Dans le panneau de configuration **Audit et détection des menaces**, **activez** l’audit pour afficher les paramètres Détection des menaces.
   
    ![Volet de navigation](media/sql-data-warehouse-security-threat-detection/2_td_turn_on_auditing.png)
4. **Activez** la détection des menaces.
5. Configurez la liste des adresses électroniques qui recevront les alertes de sécurité en cas de détection d'activités anormales sur l'entrepôt de données.
6. Cliquez sur **Enregistrer** dans le panneau de configuration **Audit et détection des menaces** pour enregistrer la stratégie d’audit et de détection des menaces que vous avez créée ou modifiée.
   
    ![Volet de navigation](media/sql-data-warehouse-security-threat-detection/3_td_turn_on_threat_detection.png)

## <a name="explore-anomalous-data-warehouse-activities-upon-detection-of-a-suspicious-event"></a>Explorer les activités anormales sur l'entrepôt de données en cas de détection d'un événement suspect
1. Vous recevrez une notification par courrier électronique lorsque des activités anormales sont détectées au niveau de la base de données. <br/>
   Le courrier électronique contiendra des informations sur l’événement de sécurité suspect, notamment la nature des activités anormales, le nom de la base de données, le nom du serveur et l’heure de l’événement. Il fournit également des informations sur les causes possibles et les mesures recommandées afin d’examiner et atténuer la menace potentielle pesant sur la base de données.<br/>
   
    ![Volet de navigation](media/sql-data-warehouse-security-threat-detection/4_td_email.png)
2. Dans le courrier électronique, cliquez sur le lien **Azure SQL Auditing Log** pour ouvrir le portail Azure et afficher les enregistrements d’audit pertinents au moment de l’événement suspect.
   
    ![Volet de navigation](media/sql-data-warehouse-security-threat-detection/5_td_audit_records.png)
3. Cliquez sur les enregistrements d’audit pour afficher plus de détails sur les activités de base de données suspects, comme l’instruction SQL, la cause de l’échec et l’adresse IP client.
   
    ![Volet de navigation](media/sql-data-warehouse-security-threat-detection/6_td_audit_record_details.png)
4. Dans le panneau des enregistrements d’audit, cliquez sur **Ouvrir dans Excel** pour ouvrir un modèle Excel préconfiguré à importer et exécuter une analyse plus approfondie du journal d’audit au moment de l’événement suspect.<br/>
   **Remarque :** dans Excel 2010 ou version ultérieure, les paramètres Power Query et **Combinaison rapide** sont requis.
   
    ![Volet de navigation](media/sql-data-warehouse-security-threat-detection/7_td_audit_records_open_excel.png)
5. Pour configurer le paramètre **Combinaison rapide** : sous l’onglet du ruban **POWER QUERY**, sélectionnez **Options** pour afficher la boîte de dialogue correspondante. Sélectionnez la section Confidentialité et choisissez la deuxième option « Ignore the Privacy Levels and potentially improve performance » :
   
    ![Volet de navigation](media/sql-data-warehouse-security-threat-detection/8_td_excel_fast_combine.png)
6. Pour charger les journaux d’audit SQL, vérifiez que les paramètres de l’onglet Paramètres sont correctement définis, puis sélectionnez le ruban « Données » et cliquez sur le bouton « Actualiser tout ».
   
    ![Volet de navigation](media/sql-data-warehouse-security-threat-detection/9_td_excel_parameters.png)
7. Les résultats s’affichent dans la feuille **SQL Audit Logs** , qui vous permet d’analyser de manière plus approfondie les activités anormales détectées et de limiter l’impact de l’événement de sécurité sur votre application.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, consultez [Sécuriser un entrepôt de données](sql-data-warehouse-overview-manage-security.md).
