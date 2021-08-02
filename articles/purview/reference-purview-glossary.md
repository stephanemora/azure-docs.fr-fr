---
title: Glossaire du produit Purview
description: Glossaire de la terminologie utilisée dans Azure Purview
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.topic: conceptual
ms.date: 06/08/2021
ms.openlocfilehash: ec011e4c9b0cab17bd9427020ba8842734e1f590
ms.sourcegitcommit: a434cfeee5f4ed01d6df897d01e569e213ad1e6f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111811909"
---
# <a name="azure-purview-product-glossary"></a>Glossaire du produit Azure Purview

Vous trouverez ci-dessous un glossaire de la terminologie utilisée dans Azure Purview.

## <a name="annotation"></a>Annotation
Informations associées à des ressources de données dans Azure Purview, comme les termes du glossaire et les classifications. Une fois appliquées, les annotations peuvent être utilisées dans la recherche pour faciliter la découverte des ressources de données. 
## <a name="approved"></a>Approved
État attribué à toute demande acceptée comme satisfaisante par l’individu ou le groupe désigné qui est en droit de changer l’état de la demande. 
## <a name="asset"></a>Asset
Tout objet unique stocké dans un catalogue de données Azure Purview.
> [!NOTE]
> Un objet unique dans le catalogue peut potentiellement représenter plusieurs objets dans le stockage. Par exemple, un ensemble de ressources est composé de nombreux fichiers de partition dans le stockage.
## <a name="azure-information-protection"></a>Azure Information Protection
Solution cloud qui prend en charge l’étiquetage des documents et des e-mails pour classifier et protéger les informations. Les éléments étiquetés peuvent être protégés par chiffrement, marqués d’un filigrane ou limités à des actions ou à des utilisateurs spécifiques, et sont liés à l’élément. Cette solution basée sur le cloud s’appuie sur Azure Rights Management Service (RMS) pour appliquer les restrictions. 
## <a name="business-glossary"></a>Glossaire métier
Liste pouvant faire l’objet d’une recherche des termes spécifiques utilisés par une organisation pour décrire son activité et leurs définitions. L’utilisation d’un glossaire métier peut contribuer à une utilisation cohérente des données au sein de l’organisation. 
## <a name="classification-report"></a>Rapport de classification
Rapport qui affiche des détails de classification clés sur les données analysées.  
## <a name="classification"></a>Classification
Type d’annotation utilisé pour identifier un attribut d’une ressource ou d’une colonne, comme « âge », « adresse e-mail » et « adresse postale ». Ces attributs peuvent être affectés lors des analyses ou ajoutés manuellement. 
## <a name="classification-rule"></a>Règle de classification
Une règle de classification est un ensemble de conditions qui déterminent la façon dont les données analysées doivent être classifiées lorsque le contenu correspond au modèle spécifié.  
## <a name="contact"></a>Contact
Personne associée à une entité dans le catalogue de données. 
## <a name="control-plane"></a>Plan de contrôle
Opérations qui gèrent les ressources dans votre abonnement, comme le contrôle d’accès en fonction du rôle et la stratégie Azure, et qui sont envoyées au point de terminaison Azure Resource Manager. 
## <a name="credential"></a>Informations d'identification
Vérification de l’identité ou de l’outil utilisé dans un système de contrôle d’accès. Les informations d’identification peuvent être utilisées pour authentifier un individu ou un groupe en vue d’accorder l’accès à une ressource de données. 
## <a name="data-catalog"></a>Data Catalog
Fonctionnalités Azure Purview qui permettent aux clients d’afficher et de gérer les métadonnées des ressources dans le patrimoine de données.
## <a name="data-map"></a>Data Map
Fonctionnalités Azure Purview qui permettent aux clients de gérer leur patrimoine de données : analyse, traçabilité, déplacement, etc.
## <a name="expert"></a>Expert
Personne au sein d’une organisation qui comprend le contexte complet d’une ressource de données ou d’un terme du glossaire. 
## <a name="fully-qualified-name-fqn"></a>Nom complet
Chemin qui définit l’emplacement d’une ressource dans sa source de données.  
## <a name="glossary-term"></a>Terme de glossaire
Entrée du glossaire métier qui définit un concept spécifique à une organisation. Les termes du glossaire peuvent contenir des informations sur des synonymes, acronymes et termes associés. 
## <a name="insights"></a>Insights
Zone dans Azure Purview dans laquelle vous pouvez consulter des rapports qui récapitulent les informations sur vos données.
## <a name="lineage"></a>Traçabilité
Correspond à la façon dont les données sont transformées et transmises de leur emplacement d’origine à leur destination. La compréhension de ce flux dans le patrimoine de données permet aux organisations de voir l’historique de leurs données et d’améliorer la résolution des problèmes ou l’analyse d’impact. 
## <a name="management-center"></a>Centre de gestion
Zone dans Azure Purview où vous pouvez gérer les connexions, les utilisateurs, les rôles et les informations d’identification.
## <a name="on-premises-data"></a>Données locales
Données se trouvant dans un centre de données contrôlé par un client, et non dans le cloud ou en tant que software as a service (SaaS) par exemple. 
## <a name="owner"></a>Propriétaire
Individu ou groupe en charge de la gestion d’une ressource de données.  
## <a name="purview-instance"></a>Instance Purview
Ressource Azure Purview unique. 
## <a name="registered-source"></a>Source inscrite
Source qui a été ajoutée à une instance Azure Purview et qui est maintenant gérée dans le cadre du catalogue de données. 
## <a name="related-terms"></a>Termes associés
Termes du glossaire liés à d’autres termes dans l’organisation.  
## <a name="resource-set"></a>Ensemble de ressources
Ressource unique représentant plusieurs fichiers ou objets partitionnés dans le stockage. Par exemple, Azure Purview stocke la sortie d’Apache Spark partitionnée dans un seul ensemble de ressources, et non dans des ressources individuelles pour chaque fichier. 
## <a name="role"></a>Rôle
Autorisations attribuées à un utilisateur au sein d’une instance Azure Purview. Les rôles, comme Conservateur de données Purview ou Lecteur de données Purview, déterminent ce qui peut être fait dans le produit. 
## <a name="scan"></a>Analyser
Processus Azure Purview qui examine une source ou un ensemble de sources et ingère les métadonnées qui s’y trouvent dans le catalogue de données. Les analyses peuvent être exécutées manuellement ou selon une planification à l’aide d’un déclencheur d’analyse. 
## <a name="scan-ruleset"></a>Ensemble de règles d’analyse
Ensemble de règles qui définissent les types de données et classifications qu’une analyse ingère dans un catalogue. 
## <a name="scan-trigger"></a>Déclencheur d’analyse
Planification qui détermine la fréquence d’une analyse. 
## <a name="sensitivity-label"></a>Étiquette de confidentialité
Annotations qui classifient et protègent les données d’une organisation. Azure Purview s’intègre à Microsoft Information Protection pour la création d’étiquettes de confidentialité. 
## <a name="sensitivity-label-report"></a>Rapport des étiquettes de confidentialité
Récapitulatif des étiquettes de confidentialité appliquées dans le patrimoine de données. 
## <a name="service"></a>Service
Produit qui fournit des fonctionnalités autonomes et qui est accessible aux clients par abonnement ou par licence. 
## <a name="source"></a>Source
Système dans lequel les données sont stockées. Les sources peuvent être hébergées dans différents emplacements, par exemple dans un cloud ou en local. Vous inscrivez et analysez des sources pour pouvoir les gérer dans Azure Purview. 
## <a name="source-type"></a>Type de source
Catégorisation des sources inscrites utilisées dans une instance Azure Purview, par exemple Azure SQL Database, Stockage Blob Azure, Amazon S3 ou SAP ECC. 
## <a name="steward"></a>Steward
Personne qui définit les normes pour un terme du glossaire. Elle est responsable du maintien des normes de qualité, de la nomenclature et des règles pour l’entité affectée. 

## <a name="next-steps"></a>Étapes suivantes

Pour prendre en main Azure Purview, consultez [Démarrage rapide : Créer un compte Azure Purview](create-catalog-portal.md).