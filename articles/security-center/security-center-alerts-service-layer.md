---
title: Détection des menaces pour la couche des services Azure - Azure Security Center
description: Cette rubrique présente les alertes des couches de service Azure disponibles dans Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 08/25/2019
ms.author: memildin
ms.openlocfilehash: d4033989830323856ac14ed06eea7df74806f128
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2020
ms.locfileid: "75665705"
---
# <a name="threat-detection-for-the-azure-service-layer-in-azure-security-center"></a>Détection des menaces pour la couche des services Azure dans Azure Security Center

Cette rubrique présente les alertes Azure Security Center disponibles lors de la supervision des couches de service Azure suivantes :

* [Couche réseau Azure](#network-layer)
* [Couche Gestion Azure (Azure Resource Manager) (préversion)](#management-layer)
* [Azure Key Vault](#azure-keyvault)

## Couche Réseau Azure<a name="network-layer"></a>

L’analytique de la couche réseau de Security Center est basée sur des exemples de [données IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export), qui sont des en-têtes de paquets collectés par des routeurs de base Azure. En fonction de ce flux de données, les modèles Machine Learning de Security Center identifient et signalent les activités de trafic malveillantes. Security Center utilise également la base de données Microsoft Threat Intelligence pour enrichir les adresses IP.

Certaines configurations réseau peuvent empêcher Security Center de générer des alertes sur une activité réseau suspecte. Pour que Security Center génère des alertes réseau, assurez-vous que :

- Votre machine virtuelle a une adresse IP publique (ou se trouve sur un équilibreur de charge avec une adresse IP publique).

- Le trafic de sortie réseau de votre machine virtuelle n’est pas bloqué par une solution IDS externe.

- La même adresse IP a été attribuée à votre machine virtuelle pendant toute l’heure pendant laquelle la communication suspecte s’est produite. Cela s’applique également aux machines virtuelles créées dans le cadre d’un service géré (par exemple, AKS, Databricks).

Pour obtenir la liste des alertes de la couche réseau Azure, consultez le [Tableau de référence des alertes](alerts-reference.md#alerts-azurenetlayer).

Pour plus d’informations sur la façon dont Security Center peut utiliser les signaux liés au réseau pour appliquer la protection contre les menaces, consultez [Détections de DNS heuristiques dans Security Center](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/).


## Couche de gestion Azure (Azure Resource Manager) (préversion)<a name ="management-layer"></a>

La couche de protection de Security Center basée sur Azure Resource Manager est disponible en préversion.

Security Center offre une couche supplémentaire de protection en utilisant les événements Azure Resource Manager, considérée comme le plan de contrôle pour Azure. En analysant les enregistrements Azure Resource Manager, Security Center détecte les opérations inhabituelles ou potentiellement dangereuses dans l’environnement d’un abonnement Azure.

Pour obtenir la liste des alertes Azure Resource Manager (préversion), consultez le [Tableau de référence des alertes](alerts-reference.md#alerts-azureresourceman).



>[!NOTE]
> Plusieurs des analyses précédentes sont alimentées par Microsoft Cloud App Security. Pour tirer parti de ces analyses, vous devez activer une licence Cloud App Security. Si vous avez une licence Cloud App Security, ces alertes sont activées par défaut. Pour les désactiver :
>
> 1. Dans le panneau **Security Center**, sélectionnez **Stratégie de sécurité**. Pour l’abonnement que vous souhaitez modifier, sélectionnez **Modifier les paramètres**.
> 2. Sélectionnez **Détection des menaces**.
> 3. Sous **Activer les intégrations**, décochez la case **Autoriser Microsoft Cloud App Security à accéder à mes données** et sélectionnez **Enregistrer**.

>[!NOTE]
>Security Center stocke les données de client liées à la sécurité dans la même zone géographique que la ressource. Si Microsoft n’a pas encore déployé Security Center dans la zone géographique de la ressource, il stocke les données aux États-Unis. Quand Cloud App Security est activé, ces informations sont stockées selon les règles d’emplacement géographique de Cloud App Security. Pour plus d’informations, consultez [Stockage des données pour les services non régionaux](https://azuredatacentermap.azurewebsites.net/).

## Azure Key Vault (préversion)<a name="azure-keyvault"></a>

Azure Key Vault est un service cloud qui protège les clés et secrets de chiffrement comme les certificats, chaînes de connexion et mots de passe. 

Azure Security Center inclut la protection native avancée Azure contre les menaces pour Azure Key Vault qui fournit une couche supplémentaire de renseignements de sécurité. Security Center détecte les tentatives inhabituelles et potentiellement dangereuses d’accès ou d’exploitation des comptes Key Vault. Cette couche de protection vous permet de traiter efficacement les menaces sans pour autant être un expert en sécurité ni avoir besoin de gérer des systèmes tiers de supervision de la sécurité.  

Quand des activités anormales se produisent, Security Center affiche des alertes et les envoie éventuellement par e-mail aux administrateurs d’abonnement. Ces alertes fournissent des détails sur l’activité suspecte, ainsi que des suggestions pour examiner et corriger les menaces. 

> [!NOTE]
> Ce service n’est pas disponible actuellement dans les régions Azure Government et de cloud souverain.

Pour obtenir la liste des alertes Azure Key Vault, consultez le [Tableau de référence des alertes](alerts-reference.md#alerts-azurekv).
