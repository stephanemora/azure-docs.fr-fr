---
title: Résolution des problèmes dans Azure Monitor Application Insights pour Java
description: Découvrez comment résoudre les problèmes liés à l’agent Java pour Azure Monitor Application Insights
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-java
ms.openlocfilehash: cea6e93999477f7f33daaf5440e161a0da6fb2a2
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112027836"
---
# <a name="troubleshooting-guide-azure-monitor-application-insights-for-java"></a>Guide de résolution des problèmes : Azure Monitor Application Insights pour Java

Dans cet article, nous aborderons certains des problèmes courants que vous pouvez rencontrer lors de l’instrumentation d’une application Java à l’aide de l’agent Java pour Application Insights. Nous abordons également les étapes à suivre pour résoudre ces problèmes. Application Insights est une fonctionnalité du service de plateforme Azure Monitor.

## <a name="check-the-self-diagnostic-log-file"></a>Vérifier le fichier journal de diagnostic automatique

Par défaut, Application Insights Java 3.x produit un fichier journal nommé `applicationinsights.log` dans le même répertoire que le fichier `applicationinsights-agent-3.1.1.jar`.

Ce fichier journal est le premier endroit où chercher des indications sur le problème rencontré.

## <a name="jvm-fails-to-start"></a>Échec du démarrage de la JVM

Si la JVM ne parvient pas à démarrer et indique l’erreur « Erreur lors de l’ouverture du fichier zip ou manifeste JAR manquant », essayez à nouveau de télécharger le fichier jar de l’agent, car il peut avoir été endommagé lors du transfert.

## <a name="upgrade-from-the-application-insights-java-2x-sdk"></a>Mettre à niveau à partir du Kit de développement logiciel (SDK) Application Insights pour Java 2.x

Si vous utilisez déjà le Kit de développement logiciel (SDK) Application Insights pour Java 2.x dans votre application, vous pouvez continuer à l’utiliser.
L’agent Application Insights Java 3.x le détecte, puis capture et met en corrélation toutes les données de télémétrie personnalisées que vous envoyez via le SDK 2.x, en empêchant toute collecte automatique effectuée par le SDK 2.x afin d’éviter d’avoir des données de télémétrie en double.
Pour plus d’informations, consultez [Mettre à niveau à partir du Kit de développement logiciel (SDK) Java 2.x](./java-standalone-upgrade-from-2x.md).

## <a name="upgrade-from-application-insights-java-30-preview"></a>Mise à niveau à partir d’Application Insights pour Java 3.0 Preview

Si vous effectuez une mise à niveau à partir de l’agent Java 3.0 Preview, examinez attentivement toutes les [options de configuration](./java-standalone-config.md). La structure JSON a été entièrement modifiée dans la version 3.0 en disponibilité générale (GA).

Ces modifications sont les suivantes :

-  Le nom du fichier config (anciennement `ApplicationInsights.json`) a été remplacé par `applicationinsights.json`.
-  Le nœud `instrumentationSettings` n’est plus présent. Tout le contenu de `instrumentationSettings` a été déplacé vers le niveau racine. 
-  Les nœuds de configuration comme `sampling`, `jmxMetrics`, `instrumentation` et `heartbeat` ont été déplacés de `preview` vers le niveau racine.

## <a name="some-logging-is-not-auto-collected"></a>Une partie de la journalisation n’est pas collectée automatiquement

La journalisation est capturée uniquement si elle est conforme au niveau configuré pour le framework de journalisation, et si elle correspond également au niveau configuré pour Application Insights.

Par exemple, si votre infrastructure de journalisation est configurée pour consigner `WARN` (et versions ultérieures) à partir du package `com.example`, et que Application Insights est configuré pour capturer `INFO` (et versions ultérieures), alors Application Insights capturera uniquement `WARN` (et versions ultérieures) à partir du package `com.example`.

La meilleure façon de savoir si une instruction de journalisation particulière respecte le seuil configuré des frameworks de journalisation est de confirmer qu’elle apparaît dans le journal des applications (par exemple, fichier ou console).

Si un objet d’exception est transmis à l’enregistreur d’événements, le message de journal (et les détails de l’objet d’exception) s’affiche dans le Portail Azure dans la table `exceptions` au lieu de la table `traces`.

Pour plus d’informations, consultez la [configuration de la journalisation collectée automatiquement](./java-standalone-config.md#auto-collected-logging).

## <a name="import-ssl-certificates"></a>Importer des certificats SSL

Cette section vous aide à dépanner et éventuellement à corriger les exceptions liées aux certificats SSL lors de l’utilisation de l’agent Java.

Deux chemins différents sont présentés ci-dessous pour résoudre ce problème :
* Si vous utilisez un magasin de clés Java par défaut
* Si vous utilisez un magasin de clés Java personnalisé

Si vous ne savez pas quel chemin suivre, vérifiez si vous disposez d’un argument JVM `-Djavax.net.ssl.trustStore=...`.
Si vous _n’avez pas_ un tel argument JVM, vous utilisez probablement le magasin de clés Java par défaut.
Si vous _avez_ un tel argument JVM, vous utilisez probablement un magasin de clés personnalisé, et l’argument JVM vous dirige vers votre magasin de clés personnalisé.

### <a name="if-using-the-default-java-keystore"></a>Si vous utilisez le magasin de clés Java par défaut :

En règle générale, le magasin de clés Java par défaut aura déjà tous les certificats racine de l’autorité de certification. Toutefois, il peut y avoir des exceptions, par exemple le certificat du point de terminaison d’ingestion, qui peut être signé par un autre certificat racine. Nous vous recommandons donc d’effectuer les trois étapes suivantes pour résoudre ce problème :

1.  Vérifiez si le certificat racine qui a été utilisé pour signer le point de terminaison d’Application Insights est déjà présent dans le magasin de clés par défaut. Par défaut, les certificats d’autorité de certification approuvés sont stockés dans `$JAVA_HOME/jre/lib/security/cacerts`. Pour répertorier les certificats dans un magasin de clés Java, utilisez la commande suivante :
    > `keytool -list -v -keystore $PATH_TO_KEYSTORE_FILE`
 
    Vous pouvez rediriger la sortie vers un fichier temporaire comme ceci (sera facile à rechercher plus tard).
    > `keytool -list -v -keystore $JAVA_HOME/jre/lib/security/cacerts > temp.txt`

2. Une fois que vous disposez de la liste des certificats, suivez ces [étapes](#steps-to-download-ssl-certificate) pour télécharger le certificat racine utilisé pour signer le point de terminaison d’Application Insights.

    Une fois le certificat téléchargé, générez un hachage SHA-1 sur le certificat à l’aide de la commande ci-dessous :
    > `keytool -printcert -v -file "your_downloaded_root_certificate.cer"`
 
    Copiez la valeur SHA-1 et vérifiez si cette valeur est présente dans le fichier « temp.txt » que vous avez enregistré précédemment.  Si vous ne trouvez pas la valeur SHA-1 dans le fichier temporaire, cela signifie que le certificat racine téléchargé est manquant dans le magasin de clés Java par défaut.


3. Importez le certificat racine dans le magasin de clés Java par défaut à l’aide de la commande suivante :
    >   `keytool -import -file "the cert file" -alias "some meaningful name" -keystore "path to cacerts file"`
 
    Dans ce cas, ce sera
 
    > `keytool -import -file "your downloaded root cert file" -alias "some meaningful name" $JAVA_HOME/jre/lib/security/cacerts`


### <a name="if-using-a-custom-java-keystore"></a>Si vous utilisez un magasin de clés Java personnalisé :

Si vous utilisez un magasin de clés Java personnalisé, vous devrez peut-être y importer les certificats SSL racines des points de terminaison d’Application Insights.
Pour résoudre ce problème, nous vous recommandons d’effectuer les deux étapes suivantes :
1. Suivez ces [étapes](#steps-to-download-ssl-certificate) pour télécharger le certificat racine à partir du point de terminaison d’Application Insights.
2. Utilisez la commande suivante pour importer le certificat SSL racine dans le magasin de clés Java personnalisé :
    > `keytool -importcert -alias your_ssl_certificate -file "your downloaded SSL certificate name.cer" -keystore "Your KeyStore name" -storepass "Your keystore password" -noprompt`

### <a name="steps-to-download-ssl-certificate"></a>Procédure de téléchargement du certificat SSL

1.  Ouvrez votre navigateur préféré, puis accédez à l’URL `IngestionEndpoint` qui est incluse dans la chaîne de connexion utilisée pour instrumenter votre application.

    :::image type="content" source="media/java-ipa/troubleshooting/ingestion-endpoint-snippet.png" alt-text="Capture d’écran montrant une chaîne de connexion Application Insights." lightbox="media/java-ipa/troubleshooting/ingestion-endpoint-snippet.png":::

2.  Sélectionnez l’icône **Afficher les informations du site** (verrou) dans le navigateur, puis sélectionnez l’option **Certificat**.

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-icon-capture.png" alt-text="Capture d’écran de l’option Certificat dans les informations du site." lightbox="media/java-ipa/troubleshooting/certificate-icon-capture.png":::

3.  Au lieu de télécharger le certificat « feuille », vous devez télécharger le certificat « racine », comme indiqué ci-dessous. Plus tard, vous devez cliquer sur « Chemin d’accès du certificat » -> sélectionnez le certificat racine -> cliquez sur « Afficher le certificat ». Un nouveau menu de certificat s’affiche dans une fenêtre contextuelle, et vous pouvez télécharger le certificat à partir de ce nouveau menu.

    :::image type="content" source="media/java-ipa/troubleshooting/root-certificate-selection.png" alt-text="Capture d’écran montrant comment sélectionner le certificat racine." lightbox="media/java-ipa/troubleshooting/root-certificate-selection.png":::

4.  Accédez à l’onglet **Détails** et sélectionnez **Copier dans le fichier**.
5.  Sélectionnez le bouton **Suivant**, sélectionnez le format **X.509 encodé en base 64 (.CER)** , puis sélectionnez à nouveau **Suivant**.

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-export-wizard.png" alt-text="Capture d’écran de l’Assistant Exportation de certificat, avec un format sélectionné." lightbox="media/java-ipa/troubleshooting/certificate-export-wizard.png":::

6.  Spécifiez le fichier dans lequel vous souhaitez enregistrer le certificat SSL. Ensuite, sélectionnez **Suivant** > **Terminer**. Le message « L’exportation a réussi »doit s’afficher.

> [!WARNING]
> Vous devrez répéter ces étapes pour récupérer le nouveau certificat avant que le certificat actuel n’expire. Vous trouverez des informations sur l’expiration sous l’onglet **Détails** de la boîte de dialogue **Certificat**.
>
> :::image type="content" source="media/java-ipa/troubleshooting/certificate-details.png" alt-text="Capture d’écran montrant les détails du certificat SSL." lightbox="media/java-ipa/troubleshooting/certificate-details.png":::
