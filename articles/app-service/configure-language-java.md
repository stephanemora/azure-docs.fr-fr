---
title: Configurer Windows applications Java - Azure App Service | Microsoft Docs
description: Découvrez comment configurer des applications Java pour s’exécuter sur les instances de Windows par défaut dans Azure App Service.
keywords: service d’application Azure, application web, windows, oss, java
services: app-service
author: jasonfreeberg
manager: jeconnock
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe;cephalin
ms.custom: seodec18
ms.openlocfilehash: 82e8936a888cbc99088ab18423e55dd57a3c2e77
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65604140"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>Configurer un Windows application Java pour Azure App Service

Azure App Service permet aux développeurs Java pour rapidement générer, déployer et mettre à l’échelle leur Tomcat ou Java Standard Edition (SE) empaqueté des applications web sur un service entièrement géré basé sur Windows. Déployez des applications avec les plug-ins Maven à partir de la ligne de commande ou dans des éditeurs comme IntelliJ, Eclipse ou Visual Studio Code.

Ce guide fournit les principaux concepts et des instructions pour les développeurs Java à l’aide dans App Service. Si vous n’avez jamais utilisé Azure App Service, vous devez lire la [démarrage rapide de Java](app-service-web-get-started-java.md) première. Questions générales sur l’utilisation d’App Service qui ne sont pas spécifiques au développement Java sont traitées dans le [le Forum aux questions Windows App Service](faq-configuration-and-management.md).

> [!NOTE]
> Vous ne trouvez pas ce que vous recherchez ? Consultez le [FAQ sur les systèmes d’exploitation de Windows](faq-configuration-and-management.md) ou le [guide de configuration de Java Linux](containers/configure-language-java.md) pour plus d’informations sur le déploiement et la sécurisation de votre application Java.

## <a name="configuring-tomcat"></a>Configuration de Tomcat

Pour modifier de Tomcat `server.xml` ou autres fichiers de configuration, prenez tout d’abord une note de votre version principale de Tomcat dans le portail.

1. Rechercher le répertoire de base de Tomcat pour votre version en exécutant la `env` commande. Recherchez la variable d’environnement qui commence par `AZURE_TOMCAT`et correspond à votre version principale. Par exemple, `AZURE_TOMCAT85_HOME` pointe vers le répertoire de Tomcat pour Tomcat 8.5.
1. Une fois que vous avez identifié le répertoire de base de Tomcat pour votre version, copiez le répertoire de configuration pour `D:\home`. Par exemple, si `AZURE_TOMCAT85_HOME` avait une valeur de `D:\Program Files (x86)\apache-tomcat-8.5.37`, le chemin d’accès complet du répertoire copié configuration serait `D:\home\tomcat\conf`.

Pour terminer, redémarrez votre instance App Service. Vos déploiements doivent atteindre `D:\home\site\wwwroot\webapps` comme avant.

## <a name="java-runtime-statement-of-support"></a>Informations de prise en charge du runtime Java

### <a name="jdk-versions-and-maintenance"></a>Versions JDK et maintenance

Le kit de développement Java (JDK) pris en charge d’Azure est [Zulu](https://www.azul.com/downloads/azure-only/zulu/) fourni par [Azul Systems](https://www.azul.com/).

Mises à jour de la version principale seront fournis via les nouvelles options d’exécution dans Azure App Service pour Windows. Les clients effectuent une mise à jour avec ces versions plus récentes de Java en configurant leur déploiement App Service et doivent s’occuper des tests et de s’assurer que la mise à jour majeure répond à leurs besoins.

Les kits JDK pris en charge sont automatiquement mis à jour tous les trimestres, en janvier, avril, juillet et octobre de chaque année.

### <a name="security-updates"></a>Mises à jour de sécurité

Les correctifs des principales vulnérabilités de sécurité seront publiés dès qu’Azul Systems les mettra à disposition. Une vulnérabilité « majeure » est définie par un score de base de 9.0 ou supérieur dans le système [NIST Common Vulnerability Scoring System, version 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Dépréciation et retrait

Si un runtime Java pris en charge est retiré, les développeurs Azure utilisant le runtime affecté reçoivent un avis de dépréciation au moins six mois avant son retrait.

### <a name="local-development"></a>Développement local

Les développeurs peuvent télécharger l’édition Production du kit Azul Zulu Enterprise JDK à partir du [site de téléchargement d’Azul](https://www.azul.com/downloads/azure-only/zulu/) pour développer en local.

### <a name="development-support"></a>Prise en charge du développement

Prise en charge de produit pour le [Azure pris en charge Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) est disponible via Microsoft lors du développement pour Azure ou [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) avec un [qualifié de plan de support Azure](https://azure.microsoft.com/support/plans/).

### <a name="runtime-support"></a>Prise en charge du runtime

Les développeurs peuvent [signaler un problème](/azure/azure-supportability/how-to-create-azure-support-request) avec les kits JDK Zulu d’Azul via le support Azure s’ils ont un [plan de support éligible](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Étapes suivantes

Cette rubrique fournit l’instruction d’exécution Java de prise en charge pour Azure App Service sur Windows.

- Pour en savoir plus sur l’hébergement d’applications web avec Azure App Service, consultez [présentation d’App Service](overview.md).
- Pour plus d’informations sur Java sur le développement d’Azure, consultez [Azure pour le centre de développement Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable).
