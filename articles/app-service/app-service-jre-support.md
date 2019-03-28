---
title: Azure App Service pour la prise en charge du Runtime Windows Java
description: Cette rubrique fournit l’instruction d’exécution Java de prise en charge pour Azure App Service sur Windows.
author: bmitchell287
ms.author: brendm; joshuapa
ms.date: 3/22/2019
ms.topic: article
ms.service: app-service
manager: dougE
ms.openlocfilehash: ab0e9b9f272108745b629d2cb284f6c5ab52c76f
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58522955"
---
# <a name="java-runtime-statement-of-support-for-app-service-on-windows"></a>Instruction d’exécution Java de prise en charge pour App Service sur Windows

## <a name="jdk-versions-and-maintenance"></a>Versions JDK et maintenance

Le kit de développement Java (JDK) pris en charge d’Azure est [Zulu](https://www.azul.com/downloads/azure-only/zulu/) fourni par [Azul Systems](https://www.azul.com/).

Mises à jour de la version principale seront fournis via les nouvelles options d’exécution dans Azure App Service pour Windows. Les clients effectuent une mise à jour avec ces versions plus récentes de Java en configurant leur déploiement App Service et doivent s’occuper des tests et de s’assurer que la mise à jour majeure répond à leurs besoins.

Les kits JDK pris en charge sont automatiquement mis à jour tous les trimestres, en janvier, avril, juillet et octobre de chaque année.

## <a name="security-updates"></a>Mises à jour de sécurité

Les correctifs des principales vulnérabilités de sécurité seront publiés dès qu’Azul Systems les mettra à disposition. Une vulnérabilité « majeure » est définie par un score de base de 9.0 ou supérieur dans le système [NIST Common Vulnerability Scoring System, version 2](https://nvd.nist.gov/cvss.cfm).

## <a name="deprecation-and-retirement"></a>Dépréciation et retrait

Si un runtime Java pris en charge est retiré, les développeurs Azure utilisant le runtime affecté reçoivent un avis de dépréciation au moins six mois avant son retrait.

## <a name="local-development"></a>Développement local

Les développeurs peuvent télécharger l’édition Production du kit Azul Zulu Enterprise JDK à partir du [site de téléchargement d’Azul](https://www.azul.com/downloads/azure-only/zulu/) pour développer en local.

## <a name="development-support"></a>Prise en charge du développement

Prise en charge de produit pour le [Azure pris en charge Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) est disponible via Microsoft lors du développement pour Azure ou [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) avec un [qualifié de plan de support Azure](https://azure.microsoft.com/support/plans/).

## <a name="runtime-support"></a>Prise en charge du runtime

Les développeurs peuvent [signaler un problème](/azure/azure-supportability/how-to-create-azure-support-request) avec les kits JDK Zulu d’Azul via le support Azure s’ils ont un [plan de support éligible](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Étapes suivantes

Cette rubrique fournit l’instruction d’exécution Java de prise en charge pour Azure App Service sur Windows.
- Pour en savoir plus sur l’hébergement d’applications web avec Azure App Service, consultez [présentation d’App Service](overview.md).
- Pour plus d’informations sur Java sur le développement d’Azure, consultez [Azure pour le centre de développement Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable).
