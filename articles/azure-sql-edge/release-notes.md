---
title: Notes de publication pour Azure SQL Edge
description: Notes de publication détaillant les nouveautés ou les modifications apportées aux images Azure SQL Edge
keywords: notes de publication SQL Server
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
ms.subservice: ''
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 07/27/2020
ms.openlocfilehash: 74e9772ada010d79e81ef36cae89ba586db73077
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/04/2020
ms.locfileid: "87553408"
---
# <a name="azure-sql-edge-release-notes"></a>Notes de publication Azure SQL Edge 

Cet article décrit les nouveautés et les modifications apportées à chaque nouvelle build d’Azure SQL Edge.

## <a name="ctp-22"></a>CTP 2.2
### <a name="sql-engine-build-number---15020001546"></a>Numéro de build du moteur SQL – 15.0.2000.1546
### <a name="fixes"></a>Correctifs
1. Prise en charge des conteneurs non racine 
2. Support pour la collecte de données d’utilisation et de diagnostic 
3. Mises à jour de la diffusion en continu T-SQL
   - Prise en charge des caractères Unicode pour les noms d’objets de flux
   - Améliorations du nettoyage de processus
   - Améliorations de la journalisation et des diagnostics
4. Amélioration des performances de l’ingestion de données

## <a name="ctp-21"></a>CTP 2.1 
### <a name="sql-engine-build-number---15020001545"></a>Numéro de build du moteur SQL - 15.0.2000.1545
### <a name="fixes"></a>Correctifs
1. Résoudre les modèles PREDICT avec ONNX pour gérer le problème CPUID dans ARM 
2. Correctif pour améliorer la gestion du chemin de défaillance au démarrage de la diffusion en continu TSQL 
3. Corrigez la valeur incorrecte du retard du filigrane dans les mesures du travail en l’absence de données. 
4. Corrigez le problème avec l’adaptateur de sortie lorsque l’adaptateur a un schéma variable entre les lots.  

## <a name="ctp-20"></a>CTP 2.0 
### <a name="sql-engine-build-number---15020001401"></a>Numéro de build du moteur SQL - 15.0.2000.1401
### <a name="whats-new"></a>Nouveautés
1.  Nom du produit mis à jour vers « Azure SQL Edge »
1.  Fonction Date_bucket

    i.  Support pour les types Date, Heure, DateHeure
3.  PREDICT avec ONNX
    
    i.  Paramètre RUNTIME requis pour ONNX 
    
4.  Support de diffusion en continu TSQL (préversion limitée) 
 
### <a name="known-issues"></a>Problèmes connus

1. <b>Problème :</b> Défaillances potentielles liées à l’application de dacpac au démarrage en raison d’un problème de synchronisation.

    <b>Solution de contournement :</b> Si vous redémarrez SQL Server ou le conteneur, l’application de dacpac sera relancée et le problème devra être résolu
### <a name="request-support"></a>Requête d'assistance
1. Vous pouvez demander de l’aide dans la [page de support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

4. Assurez-vous que les champs suivants sont sélectionnés : 
    * Type de problème : technique 
    * Service : IoT Edge
    * Type de problème : mon problème est lié à un module IoT Edge
    * Sous-type de problème ! Azure SQL Edge

   ![Échantillon de ticket de support](media/get-support/support-ticket.png)

## <a name="ctp-15"></a>CTP 1.5
### <a name="sql-engine-build-number---15020001331"></a>Numéro de build du moteur SQL - 15.0.2000.1331
### <a name="whats-new"></a>Nouveautés
1. Fonction Date_bucket
    
    i. Support pour le type DateTimeOffset
2. Modèles PREDICT avec ONNX

    i. support nvarchar
 
## <a name="ctp-14"></a>CTP 1.4
### <a name="sql-engine-build-number---15020001247"></a>Numéro de build du moteur SQL - 15.0.2000.1247
### <a name="whats-new"></a>Nouveautés
1.  Modèles PREDICT avec ONNX
 
    i.  Support varchar
    
    ii. Migration vers la version 1.0 du runtime ONNX 
2.  Support des fonctionnalités : les fonctionnalités suivantes sont désactivées :

    i.   Support CDC

    ii.  Table d’historique avec compression

    iii. Facteur d’échelle supérieur pour la lecture anticipée des journaux

    iv.  Pushdown des filtres ES en mode batch

    v.   Optimisations de lecture anticipée
 
## <a name="ctp-13"></a>CTP 1.3
### <a name="sql-engine-build-number---15020001147"></a>Numéro de build du moteur SQL - 15.0.2000.1147
### <a name="whats-new"></a>Nouveautés
1. Déploiement du Portail Azure IoT 

    i.   Support du déploiement d’images AMD64 et ARM

    ii.  Support de la création de travaux de diffusion en continu

    iii. Déploiement dacpac
2. Modèles PREDICT avec ONNX

    i. Supporte des types numériques
3. Support des fonctionnalités : les fonctionnalités suivantes sont désactivées :

    i.  Agrégat de pushdown pour l’analyse des banques de colonnes

    ii. Analyses de carrousel
4. Travail de réduction de l’empreinte et de la consommation de mémoire
