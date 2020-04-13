---
title: Recherche de votre signature de votre magasin de clés
ms.prod: xamarin
ms.assetid: 1b511fec-e6f6-453e-89c8-810aafb02b77
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 49d67db46416584248a9b15db606538f5ebfba79
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73021011"
---
# <a name="finding-your-keystores-signature"></a>Recherche de votre signature de votre magasin de clés

La signature MD5 ou SHA1 d’une application Xamarin.Android dépend du fichier **.keystore** qui a été utilisé pour signer l’APK. En règle générale, une build Debug et une build Release utilisent des fichiers **.keystore** différents.

## <a name="for-debug--non-custom-signed-builds"></a>Pour les builds Debug sans signature personnalisée

Xamarin.Android signe toutes les build Debug avec le même fichier **debug.keystore**. Ce fichier est généré lors de la première installation de Xamarin.Android. Les étapes ci-dessous décrivent en détail le processus de recherche de la signature MD5 ou SHA1 du fichier Xamarin.Android **debug.keystore** par défaut.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Recherchez le fichier Xamarin **debug.keystore** qui est utilisé pour signer l’application. Par défaut, le magasin de clés qui est utilisé pour signer les versions Debug d’une application Xamarin.Android se trouve à l’emplacement suivant :

**C:\\Utilisateurs\\*NOM_UTILISATEUR*\\AppData\\Local\\Xamarin\\Mono for Android\\debug.keystore**

Vous pouvez obtenir des informations sur un magasin de clés en exécutant la commande `keytool.exe` du JDK. Cet outil se trouve généralement à l'emplacement suivant :

**C:\\Program Files (x86)\\Java\\jdk*VERSION*\\bin\\keytool.exe**

Ajoutez le répertoire contenant le fichier **keytool.exe** à la variable d’environnement `PATH`.
Ouvrez une **invite de commandes** et exécutez `keytool.exe` à l’aide de la commande suivante :

```cmd
keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
```

Lorsqu’il est exécuté, **keytool.exe** doit générer le texte suivant. Les libellés **MD5:** et **SHA1:** identifient les signatures respectives :

```cmd
Alias name: androiddebugkey
Creation date: Aug 19, 2014
Entry type: PrivateKeyEntry
Certificate chain length: 1
Certificate[1]:
Owner: CN=Android Debug, O=Android, C=US
Issuer: CN=Android Debug, O=Android, C=US
Serial number: 53f3b126
Valid from: Tue Aug 19 13:18:46 PDT 2014 until: Sun Nov 15 12:18:46 PST 2043
Certificate fingerprints:
         MD5:  27:78:7C:31:64:C2:79:C6:ED:E5:80:51:33:9C:03:57
         SHA1: 00:E5:8B:DA:29:49:9D:FC:1D:DA:E7:EE:EE:1A:8A:C7:85:E7:31:23
         SHA256: 21:0D:73:90:1D:D6:3D:AB:4C:80:4E:C4:A9:CB:97:FF:34:DD:B4:42:FC:
08:13:E0:49:51:65:A6:7C:7C:90:45
         Signature algorithm name: SHA1withRSA
         Version: 3
```

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Recherchez le fichier Xamarin **debug.keystore** qui est utilisé pour signer l’application. Par défaut, le magasin de clés qui est utilisé pour signer les versions Debug d’une application Xamarin.Android se trouve à l’emplacement suivant :

**~/.local/share/Xamarin/Mono for Android/debug.keystore**

Vous pouvez obtenir des informations sur un magasin de clés en exécutant la commande **keytool** du JDK. Cet outil se trouve généralement à l'emplacement suivant :

**/System/Library/Java/JavaVirtualMachines/*VERSION*.jdk/Contents/Home/bin/keytool**

Ajoutez le répertoire contenant le fichier **keytool** à la variable d’environnement **PATH**.
Ouvrez un **Terminal** et exécutez **keytool** à l’aide de la commande suivante :

```bash
$ keytool -list -v -keystore ~/.local/share/Xamarin/Mono\ for\ Android/debug.keystore -alias androiddebugkey -storepass android -keypass android
```

Lorsqu’il est exécuté, **keytool** doit générer le texte suivant. Les libellés **MD5:** et **SHA1:** identifient les signatures respectives :

```bash
Alias name: androiddebugkey
Creation date: Apr 16, 2015
Entry type: PrivateKeyEntry
Certificate chain length: 1
Certificate[1]:
Owner: CN=Android Debug, O=Android, C=US
Issuer: CN=Android Debug, O=Android, C=US
Serial number: 76afa120
Valid from: Thu Apr 16 10:52:27 PDT 2015 until: Sat Apr 08 10:52:27 PDT 2045
Certificate fingerprints:
     MD5:  0A:D3:7E:80:3D:40:2A:23:89:B9:AB:9C:4B:B6:63:36
     SHA1: 89:33:8F:F2:C5:0C:91:08:4A:CF:04:A5:EC:4A:31:80:84:18:0D:D4
     SHA256: 91:AC:3E:2F:CB:EF:50:07:2B:E0:D9:8D:8B:C2:42:87:6A:85:02:86:EB:44:84:10:34:02:ED:35:CE:C6:38:47
     Signature algorithm name: SHA256withRSA
     Version: 3

Extensions:

#1: ObjectId: 2.5.29.14 Criticality=false
SubjectKeyIdentifier [
KeyIdentifier [
0000: 65 6C FE 67 8E CD CB 9A   01 CD 9F E8 25 9C A4 A3  el.g........%...
0010: 4F 4E CF 17                                        ON..
]
]
```

-----

## <a name="for-release--custom-signed-builds"></a>Pour les builds Release avec signature personnalisée

Pour les builds Release qui sont signées avec un fichier **.keystore** personnalisé, la procédure est la même que plus haut ; le fichier **.keystore** de mise en production remplaçant le fichier **debug.keystore** qui est utilisé par Xamarin.Android. Indiquez le mot de passe de votre magasin de clés et le nom d’alias correspondant au moment de création du fichier de magasin de clés de mise en production.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Lorsque l’Assistant **Distribuer** de Visual Studio est utilisé pour signer une application Xamarin.Android, le magasin de clés qui en résulte se trouve à l’emplacement suivant :

**C:\\Users\\*NOM_UTILISATEUR*\\AppData\\Local\\Xamarin\\Mono for Android\\Keystore\\*alias*\\*alias*.keystore**

Par exemple, si vous avez suivi les étapes de la section [Créer un nouveau certificat](~/android/deploy-test/signing/index.md#newcertvs) pour créer une nouvelle clé de signature, le magasin de clés qui en résulte se trouve à l’emplacement suivant :

**C:\\Utilisateurs\\*NOM_UTILISATEUR*\\AppData\\Local\\Xamarin\\Mono for Android\\Keystore\\chimp\\chimp.keystore**

Pour plus d’informations sur la signature d’une application Xamarin.Android, consultez [Signature du paquet d’application Android](~/android/deploy-test/signing/index.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Lorsque l’Assistant **Signer et distribuer...** de Visual Studio pour Mac est utilisé pour signer votre application, le magasin de clés qui en résulte se trouve à l’emplacement suivant :

**-/Bibliothèque/Développeur/Xamarin/Keystore/*alias*/*.keystore***

Par exemple, si vous avez suivi les étapes de la section [Créer un nouveau certificat](~/android/deploy-test/signing/index.md#newcertxs) pour créer une nouvelle clé de signature, le magasin de clés qui en résulte se trouve à l’emplacement suivant :

**~/Library/Developer/Xamarin/Keystore/chimp/chimp.keystore**

Pour plus d’informations sur la signature d’une application Xamarin.Android, consultez [Signature du paquet d’application Android](~/android/deploy-test/signing/index.md).

-----
