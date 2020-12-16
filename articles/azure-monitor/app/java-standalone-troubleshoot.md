---
title: Résolution des problèmes - Azure Monitor Application Insights pour Java
description: Résolution des problèmes dans Azure Monitor Application Insights pour Java
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-java
ms.openlocfilehash: cf27763f857cc1fd1aad5256d0c6cecf91251caf
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855539"
---
# <a name="troubleshooting-azure-monitor-application-insights-java"></a>Résolution des problèmes dans Azure Monitor Application Insights pour Java

Dans cet article, nous avons abordé certains des problèmes courants auxquels un utilisateur peut être confronté lors de l’instrumentation de l’application Java à l’aide de l’agent Java, ainsi que les étapes permettant de résoudre ces problèmes.

## <a name="self-diagnostic-log-file"></a>Fichier journal d’autodiagnostic

Par défaut, Application Insights pour Java 3.0 génère un fichier journal nommé `applicationinsights.log` dans le répertoire où se trouve le fichier `applicationinsights-agent-3.0.0.jar`.

Ce fichier journal est le premier endroit où chercher des indications sur le problème rencontré.

## <a name="upgrade-from-application-insights-java-2x-sdk"></a>Mise à niveau à partir du SDK Application Insights pour Java 2.x

Consultez [Mise à niveau à partir du SDK 2.x](./java-standalone-upgrade-from-2x.md).

## <a name="upgrade-from-30-preview"></a>Mise à niveau à partir de la version 3.0 (préversion)

Si vous effectuez une mise à niveau à partir de la version 3.0 (préversion), passez en revue très attentivement toutes les [options de configuration](./java-standalone-config.md), car la structure JSON est totalement différente dans la version 3.0 en disponibilité générale.

Ces modifications sont les suivantes :

1.  Le nom du fichier de configuration (anciennement `ApplicationInsights.json`) a été remplacé par `applicationinsights.json`.
2.  Le nœud `instrumentationSettings` n’est plus présent. Tout le contenu de `instrumentationSettings` a été déplacé vers le niveau racine. 
3.  Les nœuds de configuration comme `sampling`, `jmxMetrics`, `instrumentation` et `heartbeat` ont été déplacés de `preview` vers le niveau racine.

## <a name="ssl-certificate-issues"></a>Problèmes de certificat SSL

Si vous utilisez le magasin de clés Java par défaut, sachez que celui-ci dispose déjà de tous les certificats racine de l’autorité de certification. Vous n’aurez donc pas besoin d’importer d’autres certificats SSL.

Si vous utilisez un magasin de clés Java personnalisé, vous devrez peut-être y importer les certificats SSL de point de terminaison Application Insights.

### <a name="some-key-terminology"></a>Voici certains termes clés :
Un *magasin de clés* est un référentiel qui contient des certificats, des clés publiques et des clés privées. En règle générale, les distributions JDK comprennent un exécutable permettant de les gérer : `keytool`.

L’exemple suivant est une commande simple qui permet d’importer un certificat SSL dans le magasin de clés :

`keytool -importcert -alias your_ssl_certificate -file "your downloaded SSL certificate name".cer -keystore "Your KeyStore name" -storepass "Your keystore password" -noprompt`

### <a name="steps-to-download-and-add-the-ssl-certificate"></a>Étapes pour télécharger et ajouter le certificat SSL :

1.  Ouvrez votre navigateur préféré, puis accédez à l’URL `IngestionEndpoint` qui est incluse dans la chaîne de connexion utilisée pour instrumenter votre application, comme indiqué ci-dessous.

    :::image type="content" source="media/java-ipa/troubleshooting/ingestion-endpoint-url.png" alt-text="Chaîne de connexion Application Insights":::

2.  Cliquez sur l’icône « Afficher les informations du site » (verrou) dans le navigateur, puis cliquez sur l’option « Certificat » comme indiqué ci-dessous.

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-icon-capture.png" alt-text="Capture d’écran du certificat SSL":::

3.  Accédez à l’onglet Détails, puis cliquez sur Copier dans un fichier.
4.  Cliquez sur le bouton Suivant, sélectionnez le format « X.509 encodé en base 64(.CER) », puis sélectionnez Suivant.

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-export-wizard.png" alt-text="Assistant Exportation de certificats SSL":::

5.  Spécifiez le fichier dans lequel vous souhaitez enregistrer le certificat SSL. Cliquez sur Suivant pour terminer l’Assistant. Le message « L’exportation a réussi »doit s’afficher.
6.  Une fois que vous disposez du certificat, il est temps de l’importer dans un magasin de clés Java. Utilisez la [commande](#some-key-terminology) ci-dessus pour importer des certificats.

> [!WARNING]
> Vous devrez répéter ces étapes pour récupérer le nouveau certificat avant que le certificat actuel n’expire. Vous trouverez des informations sur l’expiration sous l’onglet « Détails » de la fenêtre contextuelle Certificat, comme indiqué ci-dessous.

:::image type="content" source="media/java-ipa/troubleshooting/certificate-details.png" alt-text="Détails sur les certificats SSL":::
