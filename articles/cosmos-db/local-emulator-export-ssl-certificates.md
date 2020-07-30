---
title: Exporter les certificats de l’émulateur Azure Cosmos DB
description: Lors du développement des langages et des runtimes qui n’utilisent pas le magasin de certificats Windows, vous devrez exporter et gérer les certificats TLS/SSL. Cet article vous fournit des instructions pas à pas.
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/23/2019
author: deborahc
ms.author: dech
ms.custom: devx-track-python, devx-track-java
ms.openlocfilehash: e1321c0d5b1f83ffcfd3f46384dfb3af792c9b8b
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87373094"
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs"></a>Exporter les certificats de l’émulateur Azure Cosmos DB pour une utilisation avec Java, Python et Node.js

[**Téléchargement de l’émulateur**](https://aka.ms/cosmosdb-emulator)

L’émulateur Azure Cosmos DB fournit un environnement local qui émule le service Azure Cosmos DB à des fins de développement, notamment pour l’utilisation des connexions TLS. Cet article vous explique comment exporter les certificats TLS/SSL pour une utilisation dans les langages et les runtimes qui ne s’intègrent pas avec le magasin de certificats Windows, comme Java, qui utilise son propre [magasin de certificats](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html), Python, qui utilise des [wrappers de socket](https://docs.python.org/2/library/ssl.html) et Node.js qui utilise [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback). Pour en savoir plus sur l’émulateur, consultez l’article [Utilisation de l’émulateur Azure Cosmos DB pour le développement et le test](./local-emulator.md).

Ce tutoriel décrit les tâches suivantes :

> [!div class="checklist"]
> * Rotation des certificats
> * Exportation du certificat TLS/SSL
> * Apprentissage de l’utilisation du certificat dans Java, Python et Node.js

## <a name="certification-rotation"></a>Rotation de certification

Les certificats de l’émulateur local Azure Cosmos DB sont générés lors de la première exécution de l’émulateur. Il existe deux certificats. L’un des deux est utilisé pour connecter l’émulateur local, l’autre pour gérer les clés secrètes au sein de l’émulateur. Le certificat à exporter est le certificat de connexion présentant le nom convivial DocumentDBEmulatorCertificate.

Pour régénérer les certificats, cliquez sur **Réinitialiser les données** dans l’émulateur Azure Cosmos DB exécuté dans la barre Windows, tel que représenté ci-dessous. Si vous régénérez les certificats et les avez installés dans le magasin de certificats Java ou les utilisez ailleurs, vous devez les mettre à jour pour maintenir la connexion de votre application à l’émulateur local.

:::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png" alt-text="Émulateur local Azure Cosmos DB – Réinitialiser les données":::

## <a name="how-to-export-the-azure-cosmos-db-tlsssl-certificate"></a>Comment exporter le certificat TLS/SSL Azure Cosmos DB

1. Démarrez le gestionnaire de certificats Windows en exécutant certlm.msc, puis accédez au dossier Personnel > Certificats et ouvrez le certificat portant le nom convivial **DocumentDbEmulatorCertificate**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-1.png" alt-text="Émulateur local Azure Cosmos DB – Exporter (étape 1)":::

2. Cliquez sur **Détails**, puis sur **OK**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-2.png" alt-text="Émulateur local Azure Cosmos DB – Exporter (étape 2)":::

3. Cliquez sur **Copier dans un fichier...** .

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-3.png" alt-text="Émulateur local Azure Cosmos DB – Exporter (étape 3)":::

4. Cliquez sur **Suivant**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-4.png" alt-text="Émulateur local Azure Cosmos DB – Exporter (étape 4)":::

5. Cliquez sur l’option de **refus d’exportation de la clé privée**, puis sur **Suivant**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-5.png" alt-text="Émulateur local Azure Cosmos DB – Exporter (étape 5)":::

6. Cliquez sur **X.509 encodé en base 64 (.cer)** , puis sur **Suivant**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-6.png" alt-text="Émulateur local Azure Cosmos DB – Exporter (étape 6)":::

7. Attribuez un nom au certificat. Ici, cliquez sur **documentdbemulatorcert**, puis sur **Suivant**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-7.png" alt-text="Émulateur local Azure Cosmos DB – Exporter (étape 7)":::

8. Cliquez sur **Terminer**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-8.png" alt-text="Émulateur local Azure Cosmos DB – Exporter (étape 8)":::

## <a name="how-to-use-the-certificate-in-java"></a>Comment utiliser le certificat en Java

Lors de l’exécution d’applications Java ou MongoDB qui utilisent le client Java, il est plus simple d’installer le certificat dans le magasin de certificats Java par défaut que de transmettre les indicateurs `-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>"`. Par exemple, l’application de démonstration Java incluse (`https://localhost:8081/_explorer/index.html`) dépend du magasin de certificats par défaut.

Suivez les instructions figurant dans [Ajout d'un certificat au magasin de certificats d'autorité de certification Java](https://docs.microsoft.com/azure/java-add-certificate-ca-store) pour importer le certificat X.509 dans le magasin de certificats Java par défaut. Gardez à l’esprit que vous travaillerez dans le répertoire %JAVA_HOME% durant l’exécution de keytool.

Vous pouvez également créer et exécuter un script « bash » qui effectue cette opération automatiquement :
```bash
#!/bin/bash

# If emulator was started with /AllowNetworkAccess, replace the below with the actual IP address of it:
EMULATOR_HOST=localhost
EMULATOR_PORT=8081
EMULATOR_CERT_PATH=/tmp/cosmos_emulator.cert
openssl s_client -connect ${EMULATOR_HOST}:${EMULATOR_PORT} </dev/null | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > $EMULATOR_CERT_PATH
# delete the cert if already exists
sudo $JAVA_HOME/bin/keytool -cacerts -delete -alias cosmos_emulator
# import the cert
sudo $JAVA_HOME/bin/keytool -cacerts -importcert -alias cosmos_emulator -file $EMULATOR_CERT_PATH
```

Une fois que le certificat TLS/SSL « Azure Cosmos DBEmulatorCertificate » est installé, votre application doit être en mesure de se connecter et d’utiliser l’émulateur local Azure Cosmos DB. Si les problèmes persistent, suivez les recommandations de l’article [Debugging SSL/TLS Connections](https://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html) (Débogage des connexions SSL/TLS). Il est très probable que le certificat n’est pas installé dans le magasin %JAVA_HOME%/jre/lib/security/cacerts. Par exemple, si vous disposez de plusieurs versions de Java installées, votre application peut utiliser un magasin cacaerts différent de celui mis à jour.

## <a name="how-to-use-the-certificate-in-python"></a>Comment utiliser le certificat en python

Par défaut, le [SDK Python (version 2.0.0 ou ultérieure)](sql-api-sdk-python.md) pour l’API SQL ne tente pas d’utiliser le certificat TLS/SSL lors de la connexion à l’émulateur local. Si vous souhaitez utiliser la validation TLS, vous pouvez suivre les exemples de la documentation des [wrappers de socket Python](https://docs.python.org/2/library/ssl.html).

## <a name="how-to-use-the-certificate-in-nodejs"></a>Comment utiliser le certificat en Node.js

Par défaut, le [SDK Node.js (version 1.10.1 ou ultérieure)](sql-api-sdk-node.md) pour l’API SQL ne tente pas d’utiliser le certificat TLS/SSL lors de la connexion à l’émulateur local. Si vous souhaitez utiliser la validation TLS, vous pouvez suivre les exemples de la [documentation Node.js](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback).

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez :

> [!div class="checklist"]
> * Rotation des certificats
> * Exportation du certificat TLS/SSL
> * Apprentissage de l’utilisation du certificat dans Java, Python et Node.js

Pour plus d’informations sur Azure Cosmos DB, vous pouvez maintenant passer à la section Concepts. 

> [!div class="nextstepaction"]
>[Niveaux de cohérence des données paramétrables dans Azure Cosmos DB](../cosmos-db/consistency-levels.md)
