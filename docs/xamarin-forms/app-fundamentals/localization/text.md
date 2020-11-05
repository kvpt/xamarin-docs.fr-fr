---
title: Localisation de chaîne et d’image dans Xamarin.Forms
description: Xamarin.Forms les applications peuvent être localisées à l’aide de fichiers de ressources .NET.
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: 852B4ED3-2D2D-48A5-A759-A6591F6A1509
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 11/01/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: cb811972d622ffdcf6042e030528a88fb18abaf7
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93369803"
---
# <a name="no-locxamarinforms-string-and-image-localization"></a>Xamarin.Forms Localisation de chaînes et d’images

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/usingresxlocalization)

La localisation est le processus d’adaptation d’une application pour répondre à la langue ou aux exigences culturelles spécifiques d’un marché cible. Pour effectuer la localisation, le texte et les images d’une application devront peut-être être traduites dans plusieurs langues. Une application localisée affiche automatiquement le texte traduit en fonction des paramètres de culture de l’appareil mobile :

![Captures d’écran de l’application de localisation sur iOS et Android](text-images/localizationdemo-screenshots.png)

Le .NET Framework comprend un mécanisme intégré pour la localisation d’applications à l’aide de [fichiers de ressources resx](/dotnet/framework/resources/creating-resource-files-for-desktop-apps). Un fichier de ressources stocke du texte et d’autres contenus sous forme de paires nom/valeur qui permettent à l’application de récupérer du contenu pour une clé fournie. Les fichiers de ressources permettent de séparer le contenu localisé du code d’application.

L’utilisation de fichiers de ressources pour localiser Xamarin.Forms des applications vous oblige à effectuer les étapes suivantes :

1. [Créer des fichiers resx](#create-resx-files) contenant du texte traduit.
1. [Spécifiez la culture par défaut](#specify-the-default-culture) dans le projet partagé.
1. [Localisez le texte Xamarin.Forms dans ](#localize-text-in-xamarinforms).
1. [Localisez les images](#localize-images) en fonction des paramètres de culture pour chaque plateforme.
1. [Localisez le nom de l’application](#localize-the-application-name) sur chaque plateforme.
1. [Testez la localisation](#test-localization) sur chaque plateforme.

## <a name="create-resx-files"></a>Créer des fichiers resx

Les fichiers de ressources sont des fichiers XML avec une extension **. resx** qui sont compilés en fichiers de ressources binaires (. Resources) pendant le processus de génération. Visual Studio 2019 génère une classe qui fournit une API utilisée pour récupérer des ressources. Une application localisée contient généralement un fichier de ressources par défaut avec toutes les chaînes utilisées dans l’application, ainsi que des fichiers de ressources pour chaque langue prise en charge. L’exemple d’application possède un dossier **resx** dans le projet partagé qui contient les fichiers de ressources, et son fichier de ressources par défaut appelé **AppResources. resx**.

Les fichiers de ressources contiennent les informations suivantes pour chaque élément :

- **Nom** spécifie la clé utilisée pour accéder au texte dans le code.
- La **valeur** spécifie le texte traduit.
- **Comment** est un champ facultatif qui contient des informations supplémentaires.

::: zone pivot="windows"

Un fichier de ressources est ajouté avec la boîte de dialogue **Ajouter un nouvel élément** dans Visual Studio 2019 :

![Ajouter une nouvelle ressource dans Visual Studio 2019](text-images/pc-add-resource-file.png)

Une fois le fichier ajouté, des lignes peuvent être ajoutées pour chaque ressource de texte :

![Spécifier des ressources de texte par défaut dans un fichier. resx](text-images/pc-default-strings.png)

Le paramètre de liste déroulante de **modificateur d’accès** détermine la façon dont Visual Studio génère la classe utilisée pour accéder aux ressources. L’affectation de la valeur **public** ou **Internal** au modificateur d’accès entraîne la génération d’une classe générée avec le niveau d’accessibilité spécifié. Le fait de définir le modificateur d’accès sur **aucune génération de code** ne génère pas de fichier de classe. Le fichier de ressources par défaut doit être configuré pour générer un fichier de classe, ce qui entraîne l’ajout d’un fichier avec l’extension **. Designer.cs** au projet.

Une fois le fichier de ressources par défaut créé, des fichiers supplémentaires peuvent être créés pour chaque culture prise en charge par l’application. Chaque fichier de ressources supplémentaire doit inclure la culture de traduction dans le nom de fichier et doit avoir le **modificateur d’accès** défini sur **aucune génération de code**.

Lors de l’exécution, l’application tente de résoudre une demande de ressource par ordre de spécificité. Par exemple, si la culture de l’appareil est **en-US** , l’application recherche des fichiers de ressources dans cet ordre :

1. AppResources. en-US. resx
1. AppResources. fr. resx
1. AppResources. resx (par défaut)

La capture d’écran suivante montre un fichier de traduction en espagnol nommé **AppResources.es.cs** :

![Spécifier des ressources texte espagnoles par défaut dans un fichier. resx](text-images/pc-spanish-strings.png)

Le fichier de traduction utilise les mêmes valeurs de **nom** que celles spécifiées dans le fichier par défaut, mais contient les chaînes de langue espagnole dans la colonne **valeur** . En outre, le **modificateur d’accès** est défini sur **aucune génération de code**.

::: zone-end
::: zone pivot="macos"

Un fichier de ressources est ajouté avec la boîte de dialogue **Ajouter un nouveau fichier** dans Visual Studio 2019 pour Mac :

![Ajouter une nouvelle ressource dans Visual Studio 2019 pour Mac](text-images/mac-add-resource-file.png)

Une fois qu’un fichier de ressources par défaut a été créé, du texte peut être ajouté en créant des `data` éléments dans l' `root` élément dans le fichier de ressources :

```xml
<?xml version="1.0" encoding="utf-8"?>
<root>
    ...
    <data name="AddButton" xml:space="preserve">
        <value>Add Note</value>
    </data>
    <data name="NotesLabel" xml:space="preserve">
        <value>Notes:</value>
    </data>
    <data name="NotesPlaceholder" xml:space="preserve">
        <value>e.g. Get Milk</value>
    </data>
</root>
```

Vous pouvez créer un fichier de classe **. Designer.cs** en définissant une propriété d' **outil personnalisée** dans les options de fichier de ressources :

![Outil personnalisé spécifié dans les propriétés d’un fichier de ressources](text-images/mac-resx-properties.png)

La définition de l' **outil personnalisé** sur **PublicResXFileCodeGenerator** entraîne la génération d’une classe générée avec `public` accès. La définition de l' **outil personnalisé** sur **InternalResXFileCodeGenerator** entraînera l’accès à une classe générée `internal` . Une valeur d' **outil personnalisé** vide ne génère pas de classe. Le nom de la classe générée correspondra au nom du fichier de ressources. Par exemple, le fichier **AppResources. resx** entraînera la création d’une `AppResources` classe dans un fichier appelé **AppResources.Designer.cs**.

Des fichiers de ressources supplémentaires peuvent être créés pour chaque culture prise en charge. Chaque fichier de langage doit inclure la culture de traduction dans le nom de fichier afin qu’un fichier ciblant **es-MX** soit nommé **AppResources.es-MX. resx**.

Lors de l’exécution, l’application tente de résoudre une demande de ressource par ordre de spécificité. Par exemple, si la culture de l’appareil est **en-US** , l’application recherche des fichiers de ressources dans cet ordre :

1. AppResources. en-US. resx
1. AppResources. fr. resx
1. AppResources. resx (par défaut)

Les fichiers de traduction de langue doivent avoir les mêmes valeurs de **nom** que le fichier par défaut. Le code XML suivant montre le fichier de traduction espagnol nommé **AppResources. es. resx** :

```xml
<?xml version="1.0" encoding="utf-8"?>
<root>
    ...
    <data name="NotesLabel" xml:space="preserve">
        <value>Notas:</value>
    </data>
    <data name="NotesPlaceholder" xml:space="preserve">
        <value>por ejemplo . comprar leche</value>
    </data>
    <data name="AddButton" xml:space="preserve">
        <value>Agregar nuevo elemento</value>
    </data>
</root>
```

::: zone-end

## <a name="specify-the-default-culture"></a>Spécifier la culture par défaut

Pour que les fichiers de ressources fonctionnent correctement, l’application doit avoir un `NeutralResourcesLanguage` spécifié. Dans le projet partagé, le fichier **AssemblyInfo.cs** doit être personnalisé pour spécifier la culture par défaut. Le code suivant montre comment affecter la valeur `NeutralResourcesLanguage` en **-US** dans le fichier **AssemblyInfo.cs** :

```csharp
using System.Resources;

// The resources from the neutral language .resx file are stored directly
// within the library assembly. For that reason, changing en-US to a different
// language in this line will not by itself change the language shown in the
// app. See the discussion of UltimateResourceFallbackLocation in the
// documentation for additional information:
// https://docs.microsoft.com/dotnet/api/system.resources.neutralresourceslanguageattribute
[assembly: NeutralResourcesLanguage("en-US")]
```

> [!WARNING]
> Si vous ne spécifiez pas l' `NeutralResourcesLanguage` attribut, la `ResourceManager` classe retourne `null` des valeurs pour toutes les cultures sans fichier de ressources spécifique. Lorsque la culture par défaut est spécifiée, le `ResourceManager` retourne les résultats du fichier resx par défaut pour les cultures non prises en charge. Par conséquent, il est recommandé de toujours spécifier le `NeutralResourcesLanguage` afin que le texte soit affiché pour les cultures non prises en charge.

Une fois qu’un fichier de ressources par défaut a été créé et que la culture par défaut a été spécifiée dans le fichier **AssemblyInfo.cs** , l’application peut récupérer des chaînes localisées au moment de l’exécution.

Pour plus d’informations sur les fichiers de ressources, consultez [créer des fichiers de ressources pour les applications .net](/dotnet/framework/resources/creating-resource-files-for-desktop-apps).

## <a name="specify-supported-languages-on-ios"></a>Spécifier les langues prises en charge sur iOS

Sur iOS, vous devez déclarer toutes les langues prises en charge dans le fichier **info. plist** de votre projet. Dans le fichier **info. plist** , utilisez la vue **source** pour définir un tableau pour la `CFBundleLocalizations` clé et fournissez des valeurs qui correspondent aux fichiers resx. En outre, veillez à définir un langage attendu via la `CFBundleDevelopmentRegion` clé :

![Capture d’écran de l’éditeur info. plist montrant la section de localisations](text-images/info-plist.png)

Vous pouvez également ouvrir le fichier **info. plist** dans un éditeur XML et ajouter ce qui suit :

```xml
<key>CFBundleLocalizations</key>
<array>
    <string>de</string>
    <string>es</string>
    <string>fr</string>
    <string>ja</string>
    <string>pt</string> <!-- Brazil -->
    <string>pt-PT</string> <!-- Portugal -->
    <string>ru</string>
    <string>zh-Hans</string>
    <string>zh-Hant</string>
</array>
<key>CFBundleDevelopmentRegion</key>
<string>en</string>
```

> [!NOTE]
> Apple traite légèrement différemment du portugais. Pour plus d’informations, consultez [Ajout de langues](https://developer.apple.com/library/archive/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW2) sur Developer.Apple.com.

Pour plus d’informations, consultez [spécification des langues par défaut et prises en charge dans info. plist](~/ios/app-fundamentals/localization/index.md#specifying-default-and-supported-languages-in-infoplist).

## <a name="localize-text-in-no-locxamarinforms"></a>Localiser le texte dans Xamarin.Forms

Le texte est localisé dans Xamarin.Forms à l’aide de la `AppResources` classe générée. Cette classe est nommée en fonction du nom de fichier de ressources par défaut. Étant donné que l’exemple de fichier de ressources de projet est nommé **AppResources.cs** , Visual Studio génère une classe correspondante appelée `AppResources` . Les propriétés statiques sont générées dans la `AppResources` classe pour chaque ligne du fichier de ressources. Les propriétés statiques suivantes sont générées dans la classe de l’exemple d’application `AppResources` :

- AddButton
- NotesLabel
- NotesPlaceholder

L’accès à ces valeurs en tant que propriétés [x :static](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md#the-xstatic-markup-extension) autorise l’affichage du texte localisé en XAML :

```xaml
<ContentPage ...
             xmlns:resources="clr-namespace:LocalizationDemo.Resx">
    <Label Text="{x:Static resources:AppResources.NotesLabel}" />
    <Entry Placeholder="{x:Static resources:AppResources.NotesPlaceholder}" />
    <Button Text="{x:Static resources:AppResources.AddButton}" />
</ContentPage>
```

Le texte localisé peut également être récupéré dans le code :

```csharp
public LocalizedCodePage()
{
    Label notesLabel = new Label
    {
        Text = AppResources.NotesLabel,
        // ...
    };

    Entry notesEntry = new Entry
    {
        Placeholder = AppResources.NotesPlaceholder,
        //...
    };

    Button addButton = new Button
    {
        Text = AppResources.AddButton,
        // ...
    };

    Content = new StackLayout
    {
        Children = {
            notesLabel,
            notesEntry,
            addButton
        }
    };
}
```

Les propriétés de la `AppResources` classe utilisent la valeur actuelle de `System.Globalization.CultureInfo.CurrentUICulture` pour déterminer le fichier de ressources de culture à partir duquel récupérer les valeurs.

## <a name="localize-images"></a>Localiser des images

En plus de stocker du texte, les fichiers resx peuvent stocker des images et des données binaires. Toutefois, les périphériques mobiles ont une plage de tailles et densités d’écran, et chaque plateforme mobile possède des fonctionnalités permettant d’afficher des images dépendantes de la densité. Par conséquent, la fonctionnalité de localisation d’image de plateforme doit être utilisée au lieu de stocker des images dans des fichiers de ressources.

### <a name="localize-images-on-android"></a>Localiser des images sur Android

Sur Android, les drawables localisés (images) sont stockés à l’aide d’une convention d’affectation de noms pour les dossiers dans le répertoire **Resources** . Les dossiers sont nommés **dessinables** avec un suffixe pour la langue cible. Par exemple, le dossier Espagnol-langue est nommé **Drawable-es**.

Lorsqu’un code de paramètres régionaux à quatre lettres est requis, Android requiert un **r** supplémentaire à la suite du tiret. Par exemple, le dossier Mexique locale (es-MX) doit être nommé **Drawable-es-rMX**. Les noms de fichiers image dans chaque dossier de paramètres régionaux doivent être identiques :

![Images localisées dans le projet Android](text-images/pc-android-images.png)

Pour plus d’informations, consultez la page relative à la [localisation Android](~/android/app-fundamentals/localization.md).

### <a name="localize-images-on-ios"></a>Localiser des images sur iOS

Sur iOS, les images localisées sont stockées à l’aide d’une convention d’affectation de noms pour les dossiers dans le répertoire **Resources** . Le dossier par défaut est nommé **base. lproj**. Les dossiers spécifiques à une langue sont nommés avec le nom de langue ou de paramètres régionaux, suivi de **. lproj**. Par exemple, le dossier Espagnol-langue est nommé **es. lproj**.

Les codes locaux à quatre lettres fonctionnent comme les codes de langue à deux lettres. Par exemple, le dossier Mexique locale (es-MX) doit être nommé **es-MX. lproj**. Les noms de fichiers image dans chaque dossier de paramètres régionaux doivent être identiques :

![Images localisées dans le projet iOS](text-images/pc-ios-images.png)

> [!NOTE]
> iOS prend en charge la création d’un catalogue d’actifs localisé au lieu de l’utilisation de la structure de dossiers. lproj. Toutefois, ceux-ci doivent être créés et gérés dans Xcode.

Pour plus d’informations, consultez la page [localisation iOS](~/ios/app-fundamentals/localization/index.md).

### <a name="localize-images-on-uwp"></a>Localiser des images sur UWP

Sur UWP, les images localisées sont stockées à l’aide d’une convention d’affectation de noms pour les dossiers dans le répertoire **Assets/images** . Les dossiers sont nommés avec la langue ou les paramètres régionaux. Par exemple, le dossier Espagnol-langue est nommé **es** et le dossier des paramètres régionaux du Mexique doit être nommé **es-MX**. Les noms de fichiers image dans chaque dossier de paramètres régionaux doivent être identiques :

![Images localisées dans le projet UWP](text-images/pc-uwp-images.png)

Pour plus d’informations, consultez [Localisation UWP](/windows/uwp/design/globalizing/globalizing-portal/).

### <a name="consume-localized-images"></a>Utiliser des images localisées

Étant donné que chaque plateforme stocke des images avec une structure de fichiers unique, le code XAML utilise la `OnPlatform` classe pour définir la `ImageSource` propriété en fonction de la plateforme actuelle :

```xaml
<Image>
    <Image.Source>
        <OnPlatform x:TypeArguments="ImageSource">
            <On Platform="iOS, Android" Value="flag.png" />
            <On Platform="UWP" Value="Assets/Images/flag.png" />
        </OnPlatform>
    </Image.Source>
</Image>
```

> [!NOTE]
> L' `OnPlatform` extension de balisage offre un moyen plus concis de spécifier des valeurs spécifiques à la plateforme. Pour plus d’informations, consultez [OnPlatform Markup Extension](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension).

La source de l’image peut être définie en fonction de la `Device.RuntimePlatform` propriété dans le code :

```csharp
string imgSrc = Device.RuntimePlatform == Device.UWP ? "Assets/Images/flag.png" : "flag.png";
Image flag = new Image
{
    Source = ImageSource.FromFile(imgSrc),
    WidthRequest = 100
};
```

## <a name="localize-the-application-name"></a>Localiser le nom de l’application

Le nom de l’application est spécifié par plateforme et n’utilise pas les fichiers de ressources resx. Pour localiser le nom de l’application sur Android, consultez [localiser le nom de l’application sur Android](~/android/app-fundamentals/localization.md#stringsxml-file-format). Pour localiser le nom de l’application sur iOS, consultez [localiser le nom de l’application sur iOS](~/ios/app-fundamentals/localization/index.md#app-name). Pour localiser le nom de l’application sur UWP, consultez [localiser des chaînes dans le manifeste du package UWP](/windows/uwp/app-resources/localize-strings-ui-manifest).

## <a name="test-localization"></a>Localisation des tests

Il est préférable de tester la localisation en modifiant la langue de votre appareil. Il est possible de définir la valeur de `System.Globalization.CultureInfo.CurrentUICulture` dans le code, mais le comportement est incohérent sur les plateformes, ce qui n’est pas recommandé pour les tests.

Sur iOS, dans l’application paramètres, vous pouvez définir la langue de chaque application, spécifiquement, sans modifier la langue de votre appareil.

Sur Android, les paramètres de langue sont détectés et mis en cache au démarrage de l’application. Si vous modifiez des langues, vous devrez peut-être quitter et redémarrer l’application pour voir les modifications appliquées.

## <a name="related-links"></a>Liens connexes

- [Exemple de projet de localisation](/samples/xamarin/xamarin-forms-samples/usingresxlocalization)
- [Créer des fichiers de ressources pour les applications .NET](/dotnet/framework/resources/creating-resource-files-for-desktop-apps)
- [Localisation multiplateforme](~/cross-platform/app-fundamentals/localization.md)
- [Utilisation de la classe CultureInfo (MSDN)](/dotnet/api/system.globalization.cultureinfo)
- [Localisation Android](~/android/app-fundamentals/localization.md)
- [Localisation iOS](~/ios/app-fundamentals/localization/index.md)
- [Localisation UWP](/windows/uwp/design/globalizing/globalizing-portal/)
- [Localisation et utilisation de ressources pour une culture spécifique (MSDN)](/previous-versions/visualstudio/visual-studio-2008/s9ckwb4b(v=vs.90))