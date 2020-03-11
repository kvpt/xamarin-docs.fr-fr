---
title: Comment installer manuellement les bibliothèques de prise en charge Android requises par les packages Xamarin.Android.Support ?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A9CB8CA8-8A6D-405E-B84C-A16CE452C0F7
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 99571e0b62592597bb1fffdc8d3ed8336fe050b2
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "73026929"
---
# <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packages"></a>Comment installer manuellement les bibliothèques de prise en charge Android requises par les packages Xamarin.Android.Support ?

## <a name="example-steps-for-xamarinandroidsupportv4"></a>Exemples d’étapes pour Xamarin. Android. support. v4 

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Téléchargez le package NuGet Xamarin. Android. support souhaité (par exemple, en l’installant avec le gestionnaire de package NuGet).

Utilisez `ildasm` pour vérifier la version de **android_m2repository. zip** dont le package NuGet a besoin :

```cmd
ildasm /caverbal /text /item:Xamarin.Android.Support.v4 packages\Xamarin.Android.Support.v4.23.4.0.1\lib\MonoAndroid403\Xamarin.Android.Support.v4.dll | findstr SourceUrl
```

Exemple de sortie :

```cmd
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
```

Téléchargez **android\_m2repository. zip** à partir de Google à l’aide de l’URL renvoyée par **Ildasm**. Vous pouvez également vérifier la version du _référentiel de prise en charge Android_ que vous avez actuellement installée dans le gestionnaire de Android SDK :

![« Gestionnaire de Android SDK avec le référentiel de prise en charge Android version 32 installé »](install-android-support-library-images/sdk-extras.png)

Si la version correspond à celle dont vous avez besoin pour le package NuGet, vous n’êtes pas obligé de télécharger quoi que ce soit de nouveau. Vous pouvez à la place recoder le répertoire **m2repository** existant situé sous **extras\\Android** dans le _chemin du kit de développement logiciel (SDK_ ) (comme indiqué en haut de la fenêtre Android SDK Manager).

Calcule le hachage MD5 de l’URL renvoyée par **Ildasm**. Mettez en forme la chaîne résultante pour utiliser toutes les lettres majuscules et aucun espace. Par exemple, ajustez la variable `$url` en fonction des besoins, puis exécutez les 2 lignes suivantes (en fonction [du code d’origine C# de Xamarin. Android](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208)) dans PowerShell :

```powershell
$url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"
(([System.Security.Cryptography.MD5]::Create()).ComputeHash([System.Text.Encoding]::UTF8.GetBytes($url)) | %{ $_.ToString("X02") }) -join ""
```

Exemple de sortie :

```powershell
F16A3455987DBAE5783F058F19F7FCDF
```

Copiez **android\_m2repository. zip** dans le dossier **% LocalAppData%\\Xamarin\\compresse\\** . Renommez le fichier pour qu’il utilise le hachage MD5 de l’étape précédente de calcul du hachage MD5. Par exemple :

**% LOCALAPPDATA%\\Xamarin\\compresse\\F16A3455987DBAE5783F058F19F7FCDF. zip**

Facultatif Décompressez le fichier dans **% LocalAppData%\\Xamarin\\Xamarin. Android. support. v4\\23.4.0.0\\content\\** (création d’un **contenu\\** sous-répertoire m2repository). Si vous ignorez cette étape, la première Build qui utilise la bibliothèque prendra un peu plus de temps, car elle devra effectuer cette étape.
Le numéro de version du sous-répertoire (**23.4.0.0** dans cet exemple) n’est pas exactement identique à la version du package NuGet. Vous pouvez utiliser `ildasm` pour trouver le numéro de version correct :

```cmd
ildasm /caverbal /text /item:Xamarin.Android.Support.v4 packages\Xamarin.Android.Support.v4.23.4.0.1\lib\MonoAndroid403\Xamarin.Android.Support.v4.dll | findstr /C:"string 'Version'"
```

Exemple de sortie :

```cmd
property string 'Version' = string('23.4.0.0')}
property string 'Version' = string('23.4.0.0')}
property string 'Version' = string('23.4.0.0')}
```

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Téléchargez le package NuGet Xamarin. Android. support souhaité (par exemple, en l’installant avec le gestionnaire de package NuGet).

Double-cliquez sur l’assembly _Xamarin. Android. support. v4_ sous la section _références_ du projet Android dans Visual Studio pour Mac pour ouvrir l’assembly dans le navigateur d’assembly. Assurez- vous que la liste déroulante _C#_ langue est définie sur et sélectionnez l’assembly _Xamarin. Android. support. v4_ de niveau supérieur dans l’arborescence de navigation de l’Explorateur d’assemblys. Localisez la propriété `SourceUrl` sous l’un des attributs `IncludeAndroidResourcesFrom` ou `JavaLibraryReference` :

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

Téléchargez **android\_m2repository. zip** à partir de Google à l’aide de la `SourceUrl` retournée par **Ildasm**. Vous pouvez également vérifier la version du _référentiel de prise en charge Android_ que vous avez actuellement installée dans le gestionnaire de Android SDK :

![« Gestionnaire de Android SDK avec le référentiel de prise en charge Android version 32 installé »](install-android-support-library-images/sdk-extras.png)

Si la version correspond à celle dont vous avez besoin pour le package NuGet, vous n’êtes pas obligé de télécharger quoi que ce soit de nouveau. Vous pouvez à la place recoder le répertoire **m2repository** existant situé sous **extras/Android** dans le _chemin du kit de développement logiciel (SDK_ ) (comme indiqué en haut de la fenêtre Android SDK Manager).

Calcule le hachage MD5 de l’URL renvoyée par **Ildasm**. Mettez en forme la chaîne résultante pour utiliser toutes les lettres majuscules et aucun espace. Par exemple, ajustez la chaîne d’URL en fonction des besoins, puis exécutez la commande suivante dans une invite de commandes **terminal. app** :

```bash
echo -n "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip" | md5 | tr '[:lower:]' '[:upper:]'
```

Une autre option consiste à utiliser l’interpréteur de `csharp` pour exécuter [le même C# code que celui utilisé par Xamarin. Android](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208).
Pour ce faire, ajustez la variable `url` en fonction des besoins, puis exécutez la commande suivante dans une invite de commandes **terminal. app** :

```bash
csharp -e 'var url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"; string.Concat((System.Security.Cryptography.MD5.Create().ComputeHash(System.Text.Encoding.UTF8.GetBytes(url))).Select(b => b.ToString("X02")))'
```

Exemple de sortie :

```bash
F16A3455987DBAE5783F058F19F7FCDF
```

Copiez **android\_m2repository. zip** dans le dossier **$Home/.local/share/xamarin/zips/** . Renommez le fichier pour qu’il utilise le hachage MD5 de l’étape précédente de calcul du hachage MD5. Par exemple :

**$HOME/.local/share/Xamarin/zips/F16A3455987DBAE5783F058F19F7FCDF.zip**

Facultatif Décompressez le fichier dans : 

**$HOME/.local/share/Xamarin/Xamarin.Android.Support.v4/23.4.0.0/content/**

(création d’un sous-répertoire **content/m2repository** ). Si vous ignorez cette étape, la première Build qui utilise la bibliothèque prendra un peu plus de temps, car elle devra effectuer cette étape.

Le numéro de version du sous-répertoire (**23.4.0.0** dans cet exemple) n’est pas exactement identique à la version du package NuGet. Comme dans l’étape **Ildasm** précédemment, vous pouvez utiliser le navigateur d’assembly dans Visual Studio pour Mac pour trouver le numéro de version correct. Recherchez la propriété `Version` sous l’un des attributs `IncludeAndroidResourcesFrom` ou `JavaLibraryReference` :

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

-----

## <a name="additional-references"></a>Références supplémentaires

- [Bogue 43245](https://bugzilla.xamarin.com/show_bug.cgi?id=43245) – erreur «Échec du téléchargement. Téléchargez {0} et placez-le dans le répertoire {1}». et « veuillez installer le package : «{0}» disponible dans le programme d’installation du kit de développement logiciel (SDK)» pour les messages d’erreur relatifs aux packages Xamarin. Android. support

### <a name="next-steps"></a>Étapes suivantes

Ce document décrit le comportement actuel à partir du 2016 août. La technique décrite dans ce document ne fait pas partie de la suite de tests stable pour Xamarin. elle pourrait donc s’arrêter à l’avenir.

Pour obtenir de l’aide, pour nous contacter ou, si le problème persiste même après avoir pris en charge les informations ci-dessus, consultez [les options de support disponibles pour Xamarin ?](~/cross-platform/troubleshooting/support-options.md) pour plus d’informations sur les options de contact, les suggestions, ainsi que la façon de signaler un nouveau bogue si nécessaire.
