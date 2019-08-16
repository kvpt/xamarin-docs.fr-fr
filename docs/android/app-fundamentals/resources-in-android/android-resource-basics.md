---
title: Principes de base des ressources Android
ms.prod: xamarin
ms.assetid: ED32E7B5-D552-284B-6385-C3EDDCC30A4B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/01/2018
ms.openlocfilehash: 1df10c4b8eaa30ce417feb2abae7f52b2494edf6
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69526371"
---
# <a name="android-resource-basics"></a>Principes de base des ressources Android

Presque toutes les applications Android auront un certain nombre de ressources. au minimum, elles ont souvent les dispositions de l’interface utilisateur sous la forme de fichiers XML. Quand une application Xamarin. Android est créée pour la première fois, les ressources par défaut sont configurées par le modèle de projet Xamarin. Android:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Fichiers de ressources](android-resource-basics-images/01-resource-files-vs.png)
 
# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

![Fichiers de ressources](android-resource-basics-images/01-resource-files-xs.png)
 
-----

Les cinq fichiers qui composent les ressources par défaut ont été créés dans le dossier ressources:

- **Icon. png** &ndash; icône par défaut de l’application

- **Main. AXML** &ndash; le fichier de disposition de l’interface utilisateur par défaut pour une application. Notez que si Android utilise l’extension de fichier **. xml** , Xamarin. Android utilise l’extension de fichier **. AXML** .

- Strings **. xml** &ndash; table de chaînes pour faciliter la localisation de l’application

- **AboutResources. txt** &ndash; cette opération n’est pas nécessaire et peut être supprimée en toute sécurité. Il fournit simplement une vue d’ensemble globale du dossier ressources et des fichiers qu’il contient.

- Resource.Designer.cs&ndash; ce fichier est généré et géré automatiquement par Xamarin. Android et contient l’ID unique affecté à chaque ressource. Cette fonction est très similaire et identique à celle du fichier R. Java qu’une application Android écrite en Java aurait. Elle est automatiquement créée par les outils Xamarin. Android et sera régénérée de temps en temps.


## <a name="creating-and-accessing-resources"></a>Création et accès aux ressources

La création de ressources est aussi simple que l’ajout de fichiers au répertoire pour le type de ressource en question. La capture d’écran ci-dessous montre les ressources de type chaîne pour les paramètres régionaux allemands qui ont été ajoutés à un projet. Quand **Strings. xml** a été ajouté au fichier, l' **action de génération** a été automatiquement définie sur **AndroidResource** par les outils Xamarin. Android:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Action de génération pour Strings. XML définie sur AndroidResource](android-resource-basics-images/02-build-action-vs.png)
 
# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

![Action de génération pour Strings. XML définie sur AndroidResource](android-resource-basics-images/02-build-action-xs.png)
 
-----
 

Cela permet aux outils Xamarin. Android de compiler et d’incorporer correctement les ressources dans le fichier APK. Si, pour une raison quelconque, l' **action de génération** n’est pas définie sur **ressource Android**, les fichiers sont exclus du apk et toute tentative de chargement ou d’accès aux ressources entraîne une erreur d’exécution et l’application se bloque.

En outre, il est important de noter que même si Android ne prend en charge que les noms de fichiers en minuscules pour les éléments de ressource, Xamarin. Android est un peu plus indulgent avec. Il prend en charge les noms de fichiers en majuscules et en minuscules. La Convention pour les noms d’image consiste à utiliser des minuscules avec des traits de soulignement comme séparateurs (par exemple, **mon\_image\_Name. png**). Notez que les noms de ressources ne peuvent pas être traités si des tirets ou des espaces sont utilisés comme séparateurs.

Une fois les ressources ajoutées à un projet, il existe deux façons de les utiliser dans une application &ndash; par programmation (dans le code) ou à partir de fichiers XML.


## <a name="referencing-resources-programmatically"></a>Référencement de ressources par programmation

Pour accéder à ces fichiers par programme, un ID de ressource unique leur est affecté. Cet ID de ressource est un entier défini dans une classe spéciale `Resource`appelée, qui se trouve dans le fichier **Resource.Designer.cs**et ressemble à ceci:

```csharp
public partial class Resource
{
    public partial class Attribute
    {
    }
    public partial class Drawable {
        public const int Icon=0x7f020000;
    }
    public partial class Id
    {
        public const int Textview=0x7f050000;
    }
    public partial class Layout
    {
        public const int Main=0x7f030000;
    }
    public partial class String
    {
        public const int App_Name=0x7f040001;
        public const int Hello=0x7f040000;
    }
}
```

Chaque ID de ressource est contenu dans une classe imbriquée qui correspond au type de ressource. Par exemple, lorsque le fichier **Icon. png** a été ajouté au projet, Xamarin. Android a mis `Resource` à jour la classe, créant une classe `Drawable` imbriquée appelée avec une `Icon`constante dans nommée.
Cela permet au fichier **Icon. png** d’être référencé dans le code sous `Resource.Drawable.Icon`la forme. La `Resource` classe ne doit pas être modifiée manuellement, car toutes les modifications qui y sont apportées seront remplacées par Xamarin. Android.

Lorsque vous référencez des ressources par programme (dans le code), elles sont accessibles via la hiérarchie de classes de ressources qui utilise la syntaxe suivante:

```csharp
[<PackageName>.]Resource.<ResourceType>.<ResourceName>
```

- **PackageName** &ndash; Package qui fournit la ressource et requis uniquement lorsque des ressources d’autres packages sont utilisées.

- **ResourceType** &ndash; Il s’agit du type de ressource imbriquée qui se trouve dans la classe de ressource décrite ci-dessus.

- **Nom** de la ressource &ndash; il s’agit du nom de fichier de la ressource (sans l’extension) ou de la valeur de l’attribut Android: Name pour les ressources qui se trouvent dans un élément XML.


## <a name="referencing-resources-from-xml"></a>Référencement de ressources à partir de XML

Les ressources d’un fichier XML sont accessibles à l’aide d’une syntaxe spéciale:

```xml
@[<PackageName>:]<ResourceType>/<ResourceName>
```

- **PackageName** &ndash; package qui fournit la ressource et requis uniquement lorsque des ressources d’autres packages sont utilisées.

- **ResourceType** &ndash; Il s’agit du type de ressource imbriquée qui se trouve dans la classe de ressource.

- **Nom** de la ressource Il s’agit du nom de fichier de la ressource (*sans* l’extension de type de fichier) `android:name` ou de la valeur de l’attribut pour les ressources qui se trouvent dans un élément XML. &ndash;

Par exemple, le contenu d’un fichier de disposition, **main. AXML**, est le suivant:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:orientation="vertical"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent">
    <ImageView android:id="@+id/myImage"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:src="@drawable/flag" />
</LinearLayout>
```

Cet exemple a un [`ImageView`](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/imageview) qui requiert une ressource dessinable nommée **indicateur**. A son `src` attribut défini sur `@drawable/flag`. `ImageView` Lorsque l’activité démarre, Android examine la ressource de répertoire **/dessinable** pour un fichier nommé **Flag. png** (l’extension de fichier peut être un autre format d’image, comme **Flag. jpg**) et charger ce fichier et l’afficher `ImageView`dans le.
Lorsque cette application est exécutée, elle doit ressembler à l’image suivante:

![ImageView localisé](android-resource-basics-images/03-localized-screenshot.png)
