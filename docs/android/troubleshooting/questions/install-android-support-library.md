---
title: Comment installer manuellement les bibliothèques de prise en charge Android requises par les packages Xamarin.Android.Support ?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A9CB8CA8-8A6D-405E-B84C-A16CE452C0F7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 107fcd39a64ef1d7758d211ab47a07e1ece52f4e
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757238"
---
# <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packages"></a>Comment installer manuellement les bibliothèques de prise en charge Android requises par les packages Xamarin.Android.Support ?

## <a name="example-steps-for-xamarinandroidsupportv4"></a>Exemples d’étapes pour Xamarin. Android. support. v4 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Téléchargez le package NuGet Xamarin. Android. support souhaité (par exemple, en l’installant avec le gestionnaire de package NuGet).

Utilisez `ildasm` pour vérifier la version du fichier **android_m2repository. zip** dont le package NuGet a besoin :

```cmd
ildasm /caverbal /text /item:Xamarin.Android.Support.v4 packages\Xamarin.Android.Support.v4.23.4.0.1\lib\MonoAndroid403\Xamarin.Android.Support.v4.dll | findstr SourceUrl
```

Exemple de sortie :

```cmd
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
```

Téléchargez **Android\_m2repository. zip** à partir de Google à l’aide de l’URL renvoyée par **Ildasm**. Vous pouvez également vérifier la version du _référentiel de prise en charge Android_ que vous avez actuellement installée dans le gestionnaire de Android SDK :

![« Gestionnaire de Android SDK avec le référentiel de prise en charge Android version 32 installé »](install-android-support-library-images/sdk-extras.png)

Si la version correspond à celle dont vous avez besoin pour le package NuGet, vous n’êtes pas obligé de télécharger quoi que ce soit de nouveau. Vous pouvez à la place recoder le répertoire **m2repository** existant situé sous **\\bonus Android** dans le _chemin du kit de développement logiciel (SDK_ ) (comme indiqué dans la partie supérieure de la fenêtre Android SDK Manager).

Calcule le hachage MD5 de l’URL renvoyée par **Ildasm**. Mettez en forme la chaîne résultante pour utiliser toutes les lettres majuscules et aucun espace. Par exemple, ajustez `$url` la variable si nécessaire, puis exécutez les 2 lignes suivantes (en fonction [du C# code d’origine de Xamarin. Android](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208)) dans PowerShell :

```powershell
$url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"
(([System.Security.Cryptography.MD5]::Create()).ComputeHash([System.Text.Encoding]::UTF8.GetBytes($url)) | %{ $_.ToString("X02") }) -join ""
```

Exemple de sortie :

```powershell
F16A3455987DBAE5783F058F19F7FCDF
```

Copiez **Android\_m2repository. zip** dans le dossier **\\ %\\LocalAppData\\% Xamarin** | \. Renommez le fichier pour qu’il utilise le hachage MD5 de l’étape précédente de calcul du hachage MD5. Par exemple :

**%LOCALAPPDATA%\\Xamarin\\zips\\F16A3455987DBAE5783F058F19F7FCDF.zip**

Facultatif Décompressez le fichier dans **%\\LocalAppData\\% Xamarin Xamarin. Android. support\\.\\v4\\ 23.4.0.0 content** (création d’un **contenu\\m2repository** sous-répertoire). Si vous ignorez cette étape, la première Build qui utilise la bibliothèque prendra un peu plus de temps, car elle devra effectuer cette étape.
Le numéro de version du sous-répertoire (**23.4.0.0** dans cet exemple) n’est pas exactement identique à la version du package NuGet. Vous pouvez utiliser `ildasm` pour rechercher le numéro de version correct :

```cmd
ildasm /caverbal /text /item:Xamarin.Android.Support.v4 packages\Xamarin.Android.Support.v4.23.4.0.1\lib\MonoAndroid403\Xamarin.Android.Support.v4.dll | findstr /C:"string 'Version'"
```

Exemple de sortie :

```cmd
property string 'Version' = string('23.4.0.0')}
property string 'Version' = string('23.4.0.0')}
property string 'Version' = string('23.4.0.0')}
```

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Téléchargez le package NuGet Xamarin. Android. support souhaité (par exemple, en l’installant avec le gestionnaire de package NuGet).

Double-cliquez sur l’assembly _Xamarin. Android. support. v4_ sous la section _références_ du projet Android dans Visual Studio pour Mac pour ouvrir l’assembly dans le navigateur d’assembly. Assurez- vous que la liste déroulante _C#_ langue est définie sur et sélectionnez l’assembly _Xamarin. Android. support. v4_ de niveau supérieur dans l’arborescence de navigation de l’Explorateur d’assemblys. Localisez `SourceUrl` la propriété sous l’un `IncludeAndroidResourcesFrom` des `JavaLibraryReference` attributs ou :

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

Téléchargez **Android\_m2repository. zip** à partir de Google `SourceUrl` à l’aide du retourné par **Ildasm**. Vous pouvez également vérifier la version du _référentiel de prise en charge Android_ que vous avez actuellement installée dans le gestionnaire de Android SDK :

![« Gestionnaire de Android SDK avec le référentiel de prise en charge Android version 32 installé »](install-android-support-library-images/sdk-extras.png)

Si la version correspond à celle dont vous avez besoin pour le package NuGet, vous n’êtes pas obligé de télécharger quoi que ce soit de nouveau. Vous pouvez à la place recoder le répertoire **m2repository** existant situé sous **extras/Android** dans le _chemin du kit de développement logiciel (SDK_ ) (comme indiqué en haut de la fenêtre Android SDK Manager).

Calcule le hachage MD5 de l’URL renvoyée par **Ildasm**. Mettez en forme la chaîne résultante pour utiliser toutes les lettres majuscules et aucun espace. Par exemple, ajustez la chaîne d’URL en fonction des besoins, puis exécutez la commande suivante dans une invite de commandes **terminal. app** :

```bash
echo -n "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip" | md5 | tr '[:lower:]' '[:upper:]'
```

Une autre option consiste à utiliser `csharp` l’interpréteur pour exécuter [le même C# code que celui utilisé par Xamarin. Android](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208).
Pour ce faire, ajustez `url` la variable en fonction des besoins, puis exécutez la commande suivante dans une invite de commandes **terminal. app** :

```bash
csharp -e 'var url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"; string.Concat((System.Security.Cryptography.MD5.Create().ComputeHash(System.Text.Encoding.UTF8.GetBytes(url))).Select(b => b.ToString("X02")))'
```

Exemple de sortie :

```bash
F16A3455987DBAE5783F058F19F7FCDF
```

Copiez **\_Android m2repository. zip** dans le dossier **$Home/.local/share/xamarin/zips/** . Renommez le fichier pour qu’il utilise le hachage MD5 de l’étape précédente de calcul du hachage MD5. Par exemple :

**$HOME/.local/share/Xamarin/zips/F16A3455987DBAE5783F058F19F7FCDF.zip**

Facultatif Décompressez le fichier dans : 

**$HOME/.local/share/Xamarin/Xamarin.Android.Support.v4/23.4.0.0/content/**

(création d’un sous-répertoire **content/m2repository** ). Si vous ignorez cette étape, la première Build qui utilise la bibliothèque prendra un peu plus de temps, car elle devra effectuer cette étape.

Le numéro de version du sous-répertoire (**23.4.0.0** dans cet exemple) n’est pas exactement identique à la version du package NuGet. Comme dans l’étape **Ildasm** précédemment, vous pouvez utiliser le navigateur d’assembly dans Visual Studio pour Mac pour trouver le numéro de version correct. Recherchez la `Version` propriété sous l’un `IncludeAndroidResourcesFrom` des attributs ou `JavaLibraryReference` :

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

-----

## <a name="additional-references"></a>Références supplémentaires

- [Bogue 43245](https://bugzilla.xamarin.com/show_bug.cgi?id=43245) – erreur «Échec du téléchargement. Téléchargez {0} -le et placez-le {1} dans le répertoire.» et « veuillez installer le package :{0}« » disponible dans le programme d’installation du SDK» pour les messages d’erreur liés aux packages Xamarin. Android. support

### <a name="next-steps"></a>Étapes suivantes

Ce document décrit le comportement actuel à partir du 2016 août. La technique décrite dans ce document ne fait pas partie de la suite de tests stable pour Xamarin. elle pourrait donc s’arrêter à l’avenir.

Pour obtenir de l’aide, pour nous contacter ou, si le problème persiste même après l’utilisation des informations ci-dessus, consultez [les options de support disponibles pour Xamarin ?](~/cross-platform/troubleshooting/support-options.md) pour plus d’informations sur les options de contact, les suggestions et la façon de signaler un nouveau bogue si nécessaire. .
