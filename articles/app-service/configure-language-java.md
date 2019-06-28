---
title: Configurer des applications Windows Java - Azure App Service | Microsoft Docs
description: Découvrez comment configurer des applications Java pour les exécuter sur les instances Windows par défaut dans Azure App Service.
keywords: azure app service, application web, windows, oss, java
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65604140"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>Configurer une application Windows Java pour Azure App Service

Azure App Service permet aux développeurs Java de rapidement générer, déployer et mettre à l’échelle leurs applications web empaquetées Tomcat ou Java Standard Edition (SE) sur un service basé sur Windows entièrement managé. Déployez des applications avec les plug-ins Maven à partir de la ligne de commande ou dans des éditeurs comme IntelliJ, Eclipse ou Visual Studio Code.

Ce guide fournit les concepts et instructions clés aux développeurs Java qui utilisent App Service. Si vous n’avez jamais utilisé Azure App Service, commencez par lire [Démarrage rapide avec Java](app-service-web-get-started-java.md). Des questions générales sur l’utilisation d’App Service qui ne sont pas spécifiques au développement Java sont traitées dans [Questions fréquentes (FAQ) sur App Service Windows](faq-configuration-and-management.md).

> [!NOTE]
> Vous ne trouvez pas ce que vous recherchez ? Consultez la [FAQ sur les logiciels open source Windows](faq-configuration-and-management.md) ou le [guide de configuration de Java Linux](containers/configure-language-java.md) pour obtenir plus d’informations sur le déploiement et la sécurisation de votre application Java.

## <a name="configuring-tomcat"></a>Configuration de Tomcat

Pour modifier le `server.xml` ou d’autres fichiers de configuration Tomcat, notez tout d’abord votre version principale de Tomcat dans le portail.

1. Recherchez le répertoire de base Tomcat de votre version en exécutant la commande `env`. Recherchez la variable d’environnement qui commence par `AZURE_TOMCAT` et qui correspond à votre version principale. Par exemple, `AZURE_TOMCAT85_HOME` pointe vers le répertoire Tomcat 8.5 de Tomcat.
1. Une fois que vous avez identifié le répertoire de base Tomcat de votre version, copiez le répertoire de configuration dans `D:\home`. Par exemple, si `AZURE_TOMCAT85_HOME` avait une valeur de `D:\Program Files (x86)\apache-tomcat-8.5.37`, le chemin d’accès complet du répertoire de configuration copié serait `D:\home\tomcat\conf`.

Pour terminer, redémarrez votre instance App Service. Vos déploiements doivent atteindre `D:\home\site\wwwroot\webapps` comme avant.

## <a name="java-runtime-statement-of-support"></a>Informations de prise en charge du runtime Java

### <a name="jdk-versions-and-maintenance"></a>Versions JDK et maintenance

Le kit de développement Java (JDK) pris en charge d’Azure est [Zulu](https://www.azul.com/downloads/azure-only/zulu/) fourni par [Azul Systems](https://www.azul.com/).

Les mises à jour de la version majeure sont fournies via de nouvelles options de runtime dans Azure App Service pour Windows. Les clients effectuent une mise à jour avec ces versions plus récentes de Java en configurant leur déploiement App Service et doivent s’occuper des tests et de s’assurer que la mise à jour majeure répond à leurs besoins.

Les kits JDK pris en charge sont automatiquement mis à jour tous les trimestres, en janvier, avril, juillet et octobre de chaque année.

### <a name="security-updates"></a>Mises à jour de sécurité

Les correctifs des principales vulnérabilités de sécurité seront publiés dès qu’Azul Systems les mettra à disposition. Une vulnérabilité « majeure » est définie par un score de base de 9.0 ou supérieur dans le système [NIST Common Vulnerability Scoring System, version 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Dépréciation et retrait

Si un runtime Java pris en charge est retiré, les développeurs Azure utilisant le runtime affecté reçoivent un avis de dépréciation au moins six mois avant son retrait.

### <a name="local-development"></a>Développement local

Les développeurs peuvent télécharger l’édition Production du kit Azul Zulu Enterprise JDK à partir du [site de téléchargement d’Azul](https://www.azul.com/downloads/azure-only/zulu/) pour développer en local.

### <a name="development-support"></a>Prise en charge du développement

La prise en charge produit du [kit JDK Zulu d’Azul pris en charge par Azure](https://www.azul.com/downloads/azure-only/zulu/) est disponible via Microsoft si vous développez pour Azure ou [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) avec un [plan de support Azure éligible](https://azure.microsoft.com/support/plans/).

### <a name="runtime-support"></a>Prise en charge du runtime

Les développeurs peuvent [signaler un problème](/azure/azure-supportability/how-to-create-azure-support-request) avec les kits JDK Zulu d’Azul via le support Azure s’ils ont un [plan de support éligible](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Étapes suivantes

Cette rubrique fournit les informations de prise en charge du runtime Java pour Azure App Service sur Windows.

- Pour en savoir plus sur l’hébergement d’applications web avec Azure App Service, consultez [Présentation d’Azure App Service](overview.md).
- Pour plus d’informations sur le développement Java sur Azure, consultez le [Centre de développement Azure pour Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable).
