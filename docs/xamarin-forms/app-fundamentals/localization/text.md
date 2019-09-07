---
title: Localisation de chaînes et d’images
description: Vous pouvez localiser les applications Xamarin.Forms à l’aide de fichiers de ressources.NET.
ms.prod: xamarin
ms.assetid: 852B4ED3-2D2D-48A5-A759-A6591F6A1509
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/06/2016
ms.openlocfilehash: 8e347d5d0c80f7aaac121ca1e68eec618ff2ff88
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760994"
---
# <a name="localization"></a>Localisation

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingresxlocalization)

_Vous pouvez localiser les applications Xamarin.Forms à l’aide de fichiers de ressources.NET._

## <a name="overview"></a>Présentation

Le mécanisme intégré de localisation des applications .NET utilise des [fichiers RESX](https://msdn.microsoft.com/library/ekyft91f(v=vs.90).aspx) ainsi que les classes des espaces de noms `System.Resources` et `System.Globalization`. Les fichiers RESX contenant les chaînes traduites sont incorporés dans l’assembly Xamarin.Forms, ainsi qu’une classe générée par le compilateur qui fournit un accès fortement typé aux traductions. Vous pouvez ensuite récupérer le texte traduit dans le code.

### <a name="sample-code"></a>Exemple de code

Deux exemples sont associés à ce document :

- [UsingResxLocalization](https://github.com/xamarin/xamarin-forms-samples/tree/master/UsingResxLocalization) est une démonstration très simple des concepts expliqués. Les extraits de code ci-dessous proviennent tous de cet exemple.
- [TodoLocalized](https://github.com/xamarin/xamarin-forms-samples/tree/master/TodoLocalized) est une application de travail de base qui utilise ces techniques de localisation.

#### <a name="shared-projects-are-not-recommended"></a>Les projets partagés ne sont pas recommandés

L’exemple TodoLocalized inclut une [version de démonstration de projet partagé](https://github.com/xamarin/xamarin-forms-samples/tree/master/TodoLocalized/SharedProject/). Toutefois, en raison des limitations du système de build, les fichiers de ressources ne disposent pas d’un fichier **.designer.cs** généré, ce qui empêche d’accéder aux chaînes traduites fortement typées dans le code.

Le reste de ce document concerne les projets basés sur le modèle de bibliothèque Xamarin.Forms .NET Standard.

## <a name="globalizing-xamarinforms-code"></a>Globalisation du code de Xamarin.Forms

**La globalisation** d’une application consiste à la rendre « internationale ». Cela signifie écrire du code capable d’afficher différentes langues.

L’un des éléments clés de la globalisation d’une application est la génération de l’interface utilisateur sans texte *codé en dur*. À la place, tout ce qui est affiché à l’utilisateur doit être récupéré à partir d’un ensemble de chaînes traduites dans la langue choisie.

Dans ce document, nous allons voir comment utiliser les fichiers RESX pour stocker ces chaînes et les récupérer afin de les afficher en fonction des préférences de l’utilisateur.

Les exemples ciblent l’anglais, le français, l’espagnol, l’allemand, le chinois, le japonais, le russe et le portugais (Brésil). Vous pouvez traduire les applications en autant de langues que nécessaire.

> [!NOTE]
> Sur la plateforme Windows universelle, les fichiers RESW doivent être utilisés pour la localisation des notifications Push, à la place des fichiers RESX. Pour plus d’informations, consultez [Localisation UWP](/windows/uwp/design/globalizing/globalizing-portal/).

### <a name="adding-resources"></a>Ajout de ressources

La première étape de la globalisation d’une application de bibliothèque Xamarin.Forms .NET Standard consiste à ajouter les fichiers de ressources RESX destinés à stocker l’ensemble du texte utilisé dans l’application. Nous devons ajouter un fichier RESX contenant le texte par défaut, puis ajouter des fichiers RESX supplémentaires pour chaque langue que nous souhaitons prendre en charge.

#### <a name="base-language-resource"></a>Ressource de langue de base

Le fichier de ressources de base (RESX) contient les chaînes de langue par défaut (les exemples partent du principe que l’anglais est la langue par défaut). Ajoutez le fichier au projet de code commun Xamarin.Forms en cliquant avec le bouton droit sur le projet, puis en choisissant **Ajouter > Nouveau fichier**.

Choisissez un nom explicite, par exemple **AppResources**, puis appuyez sur **OK**.

[![Ajouter un fichier de ressources](text-images/resx-new-file-sml.png "Boîte de dialogue Nouveau fichier")](text-images/resx-new-file.png#lightbox "Boîte de dialogue Nouveau fichier")

Deux fichiers sont ajoutés au projet :

- **AppResources.resx**, un fichier dans lequel les chaînes traduisibles sont stockées au format XML
- **AppResources.designer.cs**, un fichier qui déclare une classe partielle contenant des références à tous les éléments créés dans le fichier XML RESX

L’arborescence de la solution montre les fichiers liés. Vous *devez* modifier le fichier RESX pour ajouter de nouvelles chaînes traduisibles. Le fichier **.designer.cs** ne doit *pas* être modifié.

![](text-images/appresources-tree.png "Fichier AppResources.resx")

##### <a name="string-visibility"></a>Visibilité des chaînes

Par défaut, quand des références aux chaînes fortement typées sont générées, elles sont `internal` pour l’assembly. En effet, l’outil de build par défaut pour les fichiers RESX génère le fichier **.designer.cs** avec des propriétés `internal`.

Sélectionnez le fichier **AppResources.resx**, puis affichez le panneau **Propriétés** pour voir où cet outil de build est configuré. La capture d’écran ci-dessous montre **l’outil personnalisé : ResXFileCodeGenerator**.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](text-images/vs-resx-internal-sml.png "Fenêtre Propriétés d’AppResources.Resx")](text-images/vs-resx-internal.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![](text-images/xs-resx-internal-sml.png "Panneau Propriétés d’AppResources.Resx")](text-images/xs-resx-internal.png#lightbox)

-----

Pour rendre `public` les propriétés de chaîne fortement typées, vous devez changer manuellement la configuration en **Outil personnalisé : PublicResXFileCodeGenerator**, comme indiqué dans la capture d’écran ci-dessous :

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](text-images/vs-resx-public-sml.png "Fenêtre Propriétés d’AppResources.Resx")](text-images/vs-resx-public.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![](text-images/xs-resx-internal-sml.png "Panneau Propriétés d’AppResources.Resx")](text-images/xs-resx-internal.png#lightbox)

[![](text-images/xs-resx-public-sml.png "Panneau Propriétés d’AppResources.Resx")](text-images/xs-resx-public.png#lightbox)

-----

Ce changement est facultatif. Il est obligatoire uniquement si vous souhaitez référencer des chaînes localisées dans différents assemblys (par exemple, si vous placez les fichiers RESX dans un autre assembly de votre code). L’exemple de cette rubrique laisse les chaînes `internal`, car elles sont définies dans le même assembly de bibliothèque Xamarin.Forms .NET Standard que celui où elles sont utilisées.

Il vous suffit de définir l’outil personnalisé dans le fichier RESX de base, comme indiqué ci-dessus. Vous n’avez pas besoin de définir *un* outil de build pour les fichiers RESX spécifiques à la langue décrits dans les sections suivantes.

##### <a name="editing-the-resx-file"></a>Modification du fichier RESX

Malheureusement, il n’existe pas d’éditeur RESX intégré dans Visual Studio pour Mac. L’ajout de nouvelles chaînes traduisibles nécessite l’ajout d’un nouvel élément `data` XML pour chaque chaîne. Chaque élément `data` peut contenir ce qui suit :

- L’attribut `name` (obligatoire), qui est la clé de cette chaîne à traduire. Il doit s’agir d’un nom de propriété C# valide. Aucun espace ni caractère spécial n’est donc autorisé.
- L’élément `value` (obligatoire), qui correspond à la chaîne réelle affichée dans l’application.
- L’élément `comment` (facultatif), qui peut contenir des instructions à l’intention du traducteur pour expliquer comment cette chaîne est utilisée.
- L’attribut `xml:space` (facultatif), qui permet de contrôler la façon dont l’espacement est conservé dans la chaîne.

Quelques exemples d’éléments `data` sont présentés ici :

```xml
<data name="NotesLabel" xml:space="preserve">
    <value>Notes:</value>
    <comment>label for input field</comment>
</data>
<data name="NotesPlaceholder" xml:space="preserve">
    <value>eg. buy milk</value>
    <comment>example input for notes field</comment>
</data>
<data name="AddButton" xml:space="preserve">
    <value>Add new item</value>
</data>
```

Au fur et à mesure de l’écriture de l’application, chaque texte affiché à l’utilisateur doit être ajouté au fichier de ressources RESX de base dans un nouvel élément `data`. Il est recommandé d’inclure les `comment` autant que possible pour garantir une traduction de haute qualité.

> [!NOTE]
> Visual Studio (notamment l’édition gratuite Community) contient un éditeur RESX de base. Si vous avez accès à un ordinateur Windows, cela peut être un moyen pratique d’ajouter et de modifier des chaînes dans des fichiers RESX.

#### <a name="language-specific-resources"></a>Ressources spécifiques aux langues

En règle générale, la traduction réelle des chaînes de caractères par défaut n’a pas lieu tant que de grands blocs de l’application n’ont pas été écrits (dans ce cas, le fichier RESX par défaut contient de nombreuses chaînes). Il est toujours judicieux d’ajouter les ressources spécifiques à une langue au début du cycle de développement, en incluant éventuellement du texte traduit automatiquement pour permettre le test du code de localisation.

Un fichier RESX supplémentaire est ajouté pour chaque langue que nous souhaitons prendre en charge.
Les fichiers de ressources spécifiques à une langue doivent respecter une convention de nommage particulière : utilisez le même nom de fichier que le fichier de ressources de base (par exemple **AppResources**) suivi d’un point (.) et du code de langue. Voici quelques exemples simples :

- **AppResources.fr.resx** - Traductions en français.
- **AppResources.es.resx** - Traductions en espagnol.
- **AppResources.de.resx** - Traductions en allemand.
- **AppResources.ja.resx** - Traductions en japonais.
- **AppResources.zh-Hans.resx** - Traductions en chinois (simplifié).
- **AppResources.zh-Hant.resx** - Traductions en chinois (traditionnel).
- **AppResources.pt.resx** - Traductions en portugais.
- **AppResources.pt-BR.resx** - Traductions en portugais (Brésil).

Le modèle général consiste à utiliser des codes de langue à deux lettres, mais il existe des exemples (par exemple le chinois) où un autre format est utilisé, et d’autres exemples (par exemple le portugais parlé au Brésil) où un identificateur de paramètres régionaux à quatre caractères est nécessaire.

Ces fichiers de ressources spécifiques à la langue *ne nécessitent pas* de classe partielle **.designer.cs**. Ils peuvent donc être ajoutés en tant que fichiers XML classiques, avec l’**action de génération : EmbeddedResource** définie. Cette capture d’écran montre une solution contenant des fichiers de ressources spécifiques à la langue :

![](text-images/appresources-langs.png "Fichiers de ressources spécifiques à la langue")

Quand vous développez une application et que vous ajoutez du texte au fichier RESX de base, vous devez l’envoyer aux traducteurs pour qu’ils traduisent chaque élément `data` et retournent un fichier de ressources spécifique à la langue (selon la convention de nommage indiquée) afin de l’inclure dans l’application. Voici quelques exemples de « traduction automatique » ci-dessous :

**AppResources.es.resx (espagnol)**

```xml
<data name="AddButton" xml:space="preserve">
    <value>Escribir un artículo</value>
    <comment>this string appears on a button to add a new item to the list</comment>
</data>
```

**AppResources.ja.resx (japonais)**

```xml
<data name="AddButton" xml:space="preserve">
    <value>新しい項目を追加</value>
    <comment>this string appears on a button to add a new item to the list</comment>
</data>
```

**AppResources.pt-BR.resx (portugais parlé au Brésil)**

```xml
<data name="AddButton" xml:space="preserve">
    <value>adicionar novo item</value>
    <comment>this string appears on a button to add a new item to the list</comment>
</data>
```

Seul l’élément `value` doit être mis à jour par le traducteur, l’élément `comment` n’est pas destiné à être traduit. N’oubliez pas : quand vous modifiez des fichiers XML, utilisez des séquences d’échappement pour les caractères réservés, par exemple utilisez `<`, `>`, `&` avec `&lt;`, `&gt;` et `&amp;`, s’ils apparaissent dans `value` ou `comment`.

<a name="incode" />

### <a name="using-resources-in-code"></a>Utilisation de ressources dans le code

Les chaînes des fichiers de ressources RESX sont utilisables dans le code de votre interface utilisateur via la classe `AppResources`. Le `name` affecté à chaque chaîne du fichier RESX devient une propriété de cette classe qui peut être référencée dans le code de Xamarin.Forms, comme indiqué ci-dessous :

```csharp
var myLabel = new Label ();
var myEntry = new Entry ();
var myButton = new Button ();
// populate UI with translated text values from resources
myLabel.Text = AppResources.NotesLabel;
myEntry.Placeholder = AppResources.NotesPlaceholder;
myButton.Text = AppResources.AddButton;
```

L’interface utilisateur sur iOS, Android et UWP (plateforme Windows universelle) se présente comme vous le souhaitez, sauf qu’il est maintenant possible de traduire l’application en plusieurs langues, car le texte est chargé à partir d’une ressource au lieu d’être codé en dur. Voici une capture d’écran montrant l’IU sur chaque plateforme avant la traduction :

![](text-images/simple-example-english.png "IU multiplateformes avant la traduction")

### <a name="troubleshooting"></a>Résolution des problèmes

#### <a name="testing-a-specific-language"></a>Test d’une langue spécifique

Il peut s’avérer difficile de faire passer le simulateur ou un appareil dans d’autres langues, en particulier durant la phase de développement, quand vous souhaitez tester rapidement différentes cultures.

Vous pouvez forcer le chargement d’une langue spécifique en définissant `Culture` comme indiqué dans l’extrait de code suivant :

```csharp
// force a specific culture, useful for quick testing
AppResources.Culture =  new CultureInfo("fr-FR");
```

Vous pouvez également utiliser cette approche (définir la culture directement sur la classe `AppResources`) pour implémenter un sélecteur de langue dans votre application (au lieu d’utiliser les paramètres régionaux de l’appareil).

#### <a name="loading-embedded-resources"></a>Chargement des ressources incorporées

L’extrait de code suivant est utile quand vous essayez de déboguer des problèmes relatifs à des ressources incorporées (par exemple des fichiers RESX). Ajoutez ce code à votre application (au début de son cycle de vie). Il va lister toutes les ressources incorporées dans l’assembly, en affichant l’identificateur complet de la ressource :

```csharp
using System.Reflection;
// ...
// NOTE: use for debugging, not in released app code!
var assembly = typeof(EmbeddedImages).GetTypeInfo().Assembly; // "EmbeddedImages" should be a class in your app
foreach (var res in assembly.GetManifestResourceNames())
{
    System.Diagnostics.Debug.WriteLine("found resource: " + res);
}
```

Dans le fichier **AppResources.Designer.cs** (vers la *ligne 33*), le *nom du gestionnaire des ressources* est spécifié dans son intégralité (par exemple `"UsingResxLocalization.Resx.AppResources"`) à l’image du code ci-dessous :

```csharp
System.Resources.ResourceManager temp =
        new System.Resources.ResourceManager(
                "UsingResxLocalization.Resx.AppResources",
                typeof(AppResources).GetTypeInfo().Assembly);
```

Examinez dans la **sortie de l’application** les résultats du code de débogage présenté ci-dessus pour vérifier que les ressources appropriées sont listées (à savoir `"UsingResxLocalization.Resx.AppResources"`).

Si ce n’est pas le cas, la classe `AppResources` ne peut pas charger ses ressources.
Consultez ce qui suit pour résoudre les problèmes de ressources introuvables :

- L’espace de noms par défaut du projet correspond à l’espace de noms racine du fichier **AppResources.Designer.cs**.
- Si le fichier **AppResources.resx** est situé dans un sous-répertoire, le nom de ce sous-répertoire doit faire partie de l’espace de noms *et* de l’identificateur de ressource.
- Le fichier **AppResources.resx** comporte l’**Action de génération : EmbeddedResource**.
- La case **Options du projet > Code source > Stratégies de noms .NET > Utiliser des noms de ressource de style Visual Studio** est cochée. Vous pouvez la décocher si vous le souhaitez. Toutefois, les espaces de noms utilisés pour référencer vos ressources RESX doivent être mis à jour dans l’application.

#### <a name="doesnt-work-in-debug-mode-android-only"></a>Ne fonctionne pas en mode DÉBOGAGE (Android uniquement)

Si les chaînes traduites fonctionnent correctement dans vos builds Android de MISE EN PRODUCTION mais pas pendant le débogage, cliquez avec le bouton droit sur **Projet Android**, sélectionnez **Options > Build > Build Android**, puis vérifiez que l’option **Déploiement d’assembly rapide** n’est PAS cochée. Cette option entraîne des problèmes de chargement de ressources. Vous ne devez pas l’utiliser si vous testez des applications localisées.

### <a name="displaying-the-correct-language"></a>Affichage de la langue appropriée

Jusqu’ici, nous avons vu comment écrire du code pour permettre l’utilisation de traductions, mais pas comment les faire apparaître. Le code de Xamarin.Forms peut tirer parti des ressources de .NET pour charger les traductions linguistiques appropriées. Toutefois, nous devons interroger le système d’exploitation de chaque plateforme pour déterminer quelle est la langue sélectionnée par l’utilisateur.

Dans la mesure où du code spécifique à la plateforme est nécessaire pour obtenir la préférence linguistique de l’utilisateur, utilisez un [service de dépendance](~/xamarin-forms/app-fundamentals/dependency-service/index.md) afin d’exposer ces informations dans l’application Xamarin.Forms et de les implémenter pour chaque plateforme.

Commencez par définir une interface pour exposer la culture par défaut de l’utilisateur, à l’image du code ci-dessous :

```csharp
public interface ILocalize
{
    CultureInfo GetCurrentCultureInfo ();
    void SetLocale (CultureInfo ci);
}
```

Utilisez ensuite le [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md) de la classe `App` de Xamarin.Forms pour appeler l’interface et affecter la valeur appropriée à la culture de nos ressources RESX. Notez que nous n’avons pas besoin de définir manuellement cette valeur pour la plateforme Windows universelle, car le framework de ressources reconnaît automatiquement la langue sélectionnée sur ces plateformes.

```csharp
if (Device.RuntimePlatform == Device.iOS || Device.RuntimePlatform == Device.Android)
{
    var ci = DependencyService.Get<ILocalize>().GetCurrentCultureInfo();
    Resx.AppResources.Culture = ci; // set the RESX for resource localization
    DependencyService.Get<ILocalize>().SetLocale(ci); // set the Thread for locale-aware methods
}
```

La ressource `Culture` doit être définie au premier chargement de l’application pour permettre l’utilisation des chaînes de langue appropriées. Vous pouvez éventuellement mettre à jour cette valeur en fonction d’événements spécifiques à la plateforme susceptibles d’être générés sur iOS ou sur Android, si l’utilisateur met à jour ses préférences linguistiques pendant l’exécution de l’application.

Les implémentations de l’interface `ILocalize` sont présentées dans la section [Code spécifique à la plateforme](#Platform-specific_Code) ci-dessous. Ces implémentations tirent parti de la classe d’assistance `PlatformCulture` :

```csharp
public class PlatformCulture
{
    public PlatformCulture (string platformCultureString)
    {
        if (String.IsNullOrEmpty(platformCultureString))
        {
            throw new ArgumentException("Expected culture identifier", "platformCultureString"); // in C# 6 use nameof(platformCultureString)
        }
        PlatformString = platformCultureString.Replace("_", "-"); // .NET expects dash, not underscore
        var dashIndex = PlatformString.IndexOf("-", StringComparison.Ordinal);
        if (dashIndex > 0)
        {
            var parts = PlatformString.Split('-');
            LanguageCode = parts[0];
            LocaleCode = parts[1];
        }
        else
        {
            LanguageCode = PlatformString;
            LocaleCode = "";
        }
    }
    public string PlatformString { get; private set; }
    public string LanguageCode { get; private set; }
    public string LocaleCode { get; private set; }
    public override string ToString()
    {
        return PlatformString;
    }
}
```

<a name="Platform-specific_Code" />

### <a name="platform-specific-code"></a>Code spécifique à la plateforme

Le code permettant de détecter la langue à afficher doit être spécifique à la plateforme, car iOS, Android et UWP exposent ces informations de manière légèrement différente. Le code du service de dépendance `ILocalize` est fourni ci-dessous pour chaque plateforme. Il est accompagné d’exigences supplémentaires spécifiques à la plateforme pour garantir un affichage correct du texte localisé.

Le code spécifique à la plateforme doit également gérer les cas où le système d’exploitation permet à l’utilisateur de configurer un identificateur de paramètres régionaux non pris en charge par la classe `CultureInfo` de .NET. Dans ces cas de figure, vous devez écrire du code personnalisé pour détecter les paramètres régionaux non pris en charge et les remplacer par des paramètres régionaux compatibles .NET.

#### <a name="ios-application-project"></a>Projet d’application iOS

Les utilisateurs iOS sélectionnent leur langue par défaut séparément de la culture de mise en forme de la date et de l’heure. Pour charger les ressources appropriées afin de localiser une application Xamarin.Forms, il nous suffit d’interroger le premier élément du tableau `NSLocale.PreferredLanguages`.

L’implémentation suivante du service de dépendance `ILocalize` doit être placée dans le projet d’application iOS. Dans la mesure où iOS utilise des traits de soulignement au lieu de tirets (ce qui correspond à la représentation .NET Standard), le code remplace le trait de soulignement avant d’instancier la classe `CultureInfo` :

```csharp
[assembly:Dependency(typeof(UsingResxLocalization.iOS.Localize))]

namespace UsingResxLocalization.iOS
{
    public class Localize : UsingResxLocalization.ILocalize
    {
        public void SetLocale (CultureInfo ci)
        {
            Thread.CurrentThread.CurrentCulture = ci;
            Thread.CurrentThread.CurrentUICulture = ci;
        }

        public CultureInfo GetCurrentCultureInfo ()
        {
            var netLanguage = "en";
            if (NSLocale.PreferredLanguages.Length > 0)
            {
                var pref = NSLocale.PreferredLanguages [0];
                netLanguage = iOSToDotnetLanguage(pref);
            }
            // this gets called a lot - try/catch can be expensive so consider caching or something
            System.Globalization.CultureInfo ci = null;
            try
            {
                ci = new System.Globalization.CultureInfo(netLanguage);
            }
            catch (CultureNotFoundException e1)
            {
                // iOS locale not valid .NET culture (eg. "en-ES" : English in Spain)
                // fallback to first characters, in this case "en"
                try
                {
                    var fallback = ToDotnetFallbackLanguage(new PlatformCulture(netLanguage));
                    ci = new System.Globalization.CultureInfo(fallback);
                }
                catch (CultureNotFoundException e2)
                {
                    // iOS language not valid .NET culture, falling back to English
                    ci = new System.Globalization.CultureInfo("en");
                }
            }
            return ci;
        }

        string iOSToDotnetLanguage(string iOSLanguage)
        {
            // .NET cultures don't support underscores
            string netLanguage = iOSLanguage.Replace("_", "-");

            //certain languages need to be converted to CultureInfo equivalent
            switch (iOSLanguage)
            {
                case "ms-MY":   // "Malaysian (Malaysia)" not supported .NET culture
                case "ms-SG":    // "Malaysian (Singapore)" not supported .NET culture
                    netLanguage = "ms"; // closest supported
                    break;
                case "gsw-CH":  // "Schwiizertüütsch (Swiss German)" not supported .NET culture
                    netLanguage = "de-CH"; // closest supported
                    break;
                // add more application-specific cases here (if required)
                // ONLY use cultures that have been tested and known to work
            }
            return netLanguage;
        }

        string ToDotnetFallbackLanguage (PlatformCulture platCulture)
        {
            var netLanguage = platCulture.LanguageCode; // use the first part of the identifier (two chars, usually);
            switch (platCulture.LanguageCode)
            {
                case "pt":
                    netLanguage = "pt-PT"; // fallback to Portuguese (Portugal)
                    break;
                case "gsw":
                    netLanguage = "de-CH"; // equivalent to German (Switzerland) for this app
                    break;
                // add more application-specific cases here (if required)
                // ONLY use cultures that have been tested and known to work
            }
            return netLanguage;
        }
    }
}
```

> [!NOTE]
> Les blocs `try/catch` de la méthode `GetCurrentCultureInfo` reproduisent le comportement de secours utilisé généralement avec les spécificateurs de paramètres régionaux : si la correspondance exacte est introuvable, une correspondance étroite doit être recherchée uniquement en fonction de la langue (premier bloc de caractères des paramètres régionaux).
>
> Dans le cas de Xamarin.Forms, certains paramètres régionaux sont valides dans iOS mais ne correspondent pas à un `CultureInfo` valide dans .NET. Le code ci-dessus tente de répondre à cette situation.
>
> Par exemple, l’écran iOS **Réglages > Général Langue &amp; Région** vous permet de configurer la **Langue** de votre téléphone en choisissant **Anglais** mais comme vous avez défini la **Région** à **Espagne**, cela donne la chaîne de paramètres régionaux `"en-ES"`. En cas d’échec de la création de `CultureInfo`, le code utilise uniquement les deux premières lettres pour sélectionner la langue d’affichage.
>
> Les développeurs doivent modifier les méthodes `iOSToDotnetLanguage` et `ToDotnetFallbackLanguage` pour prendre en compte les cas spécifiques liés aux langues prises en charge.

Certains éléments d’interface utilisateur définis par le système sont automatiquement traduits par iOS, par exemple le bouton **OK** du contrôle `Picker`. Pour forcer iOS à traduire ces éléments, nous devons indiquer les langues que nous prenons en charge dans le fichier **Info.plist**. Vous pouvez ajouter ces valeurs via **Info.plist > Source**, comme indiqué ici :

![Clés de localisation dans Info.plist](text-images/info-plist.png "Clés de localisation dans Info.plist")

Vous pouvez également ouvrir le fichier **Info.plist** dans un éditeur XML et modifier directement les valeurs :

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

Une fois que vous avez mis en place le service de dépendance et mis à jour le fichier **Info.plist**, l’application iOS peut afficher le texte localisé.

> [!NOTE]
> Notez qu’Apple traite le portugais de manière légèrement différente.
> D’après [la documentation](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW2) : _« Utilisez pt en tant qu’ID de langue pour le portugais utilisé au Brésil, et pt-PT en tant qu’ID de langue pour le portugais utilisé au Portugal »_ .
> En d’autres termes, quand vous choisissez la langue portugaise dans des paramètres régionaux non standard, la langue de secours est le portugais parlé au Brésil sur iOS, à moins d’écrire du code pour changer ce comportement (par exemple `ToDotnetFallbackLanguage` ci-dessus).

Pour plus d’informations sur la localisation iOS, consultez [Localisation iOS](~/ios/app-fundamentals/localization/index.md).

#### <a name="android-application-project"></a>Projet d’application Android

Android expose les paramètres régionaux sélectionnés via `Java.Util.Locale.Default` et utilise également le trait de soulignement comme séparateur au lieu du tiret (qui est remplacé par le code suivant). Ajoutez cette implémentation du service de dépendance au projet d’application Android :

```csharp
[assembly:Dependency(typeof(UsingResxLocalization.Android.Localize))]

namespace UsingResxLocalization.Android
{
    public class Localize : UsingResxLocalization.ILocalize
    {
        public void SetLocale(CultureInfo ci)
        {
            Thread.CurrentThread.CurrentCulture = ci;
            Thread.CurrentThread.CurrentUICulture = ci;
        }
        public CultureInfo GetCurrentCultureInfo()
        {
            var netLanguage = "en";
            var androidLocale = Java.Util.Locale.Default;
            netLanguage = AndroidToDotnetLanguage(androidLocale.ToString().Replace("_", "-"));
            // this gets called a lot - try/catch can be expensive so consider caching or something
            System.Globalization.CultureInfo ci = null;
            try
            {
                ci = new System.Globalization.CultureInfo(netLanguage);
            }
            catch (CultureNotFoundException e1)
            {
                // iOS locale not valid .NET culture (eg. "en-ES" : English in Spain)
                // fallback to first characters, in this case "en"
                try
                {
                    var fallback = ToDotnetFallbackLanguage(new PlatformCulture(netLanguage));
                    ci = new System.Globalization.CultureInfo(fallback);
                }
                catch (CultureNotFoundException e2)
                {
                    // iOS language not valid .NET culture, falling back to English
                    ci = new System.Globalization.CultureInfo("en");
                }
            }
            return ci;
        }
        string AndroidToDotnetLanguage(string androidLanguage)
        {
            var netLanguage = androidLanguage;
            //certain languages need to be converted to CultureInfo equivalent
            switch (androidLanguage)
            {
                case "ms-BN":   // "Malaysian (Brunei)" not supported .NET culture
                case "ms-MY":   // "Malaysian (Malaysia)" not supported .NET culture
                case "ms-SG":   // "Malaysian (Singapore)" not supported .NET culture
                    netLanguage = "ms"; // closest supported
                    break;
                case "in-ID":  // "Indonesian (Indonesia)" has different code in  .NET
                    netLanguage = "id-ID"; // correct code for .NET
                    break;
                case "gsw-CH":  // "Schwiizertüütsch (Swiss German)" not supported .NET culture
                    netLanguage = "de-CH"; // closest supported
                    break;
                    // add more application-specific cases here (if required)
                    // ONLY use cultures that have been tested and known to work
            }
            return netLanguage;
        }
        string ToDotnetFallbackLanguage(PlatformCulture platCulture)
        {
            var netLanguage = platCulture.LanguageCode; // use the first part of the identifier (two chars, usually);
            switch (platCulture.LanguageCode)
            {
                case "gsw":
                    netLanguage = "de-CH"; // equivalent to German (Switzerland) for this app
                    break;
                    // add more application-specific cases here (if required)
                    // ONLY use cultures that have been tested and known to work
            }
            return netLanguage;
        }
    }
}
```

> [!NOTE]
> Les blocs `try/catch` de la méthode `GetCurrentCultureInfo` reproduisent le comportement de secours utilisé généralement avec les spécificateurs de paramètres régionaux : si la correspondance exacte est introuvable, une correspondance étroite doit être recherchée uniquement en fonction de la langue (premier bloc de caractères des paramètres régionaux).
>
> Dans le cas de Xamarin.Forms, certains paramètres régionaux sont valides dans Android mais ne correspondent pas à un `CultureInfo` valide dans .NET. Le code ci-dessus tente de répondre à cette situation.
>
> Les développeurs doivent modifier les méthodes `iOSToDotnetLanguage` et `ToDotnetFallbackLanguage` pour prendre en compte les cas spécifiques liés aux langues prises en charge.

Une fois ce code ajouté au projet d’application Android, il peut afficher automatiquement les chaînes traduites.

> [!WARNING]
> Si les chaînes traduites fonctionnent correctement dans vos builds Android de MISE EN PRODUCTION mais pas pendant le débogage, cliquez avec le bouton droit sur **Projet Android**, sélectionnez **Options > Build > Build Android**, puis vérifiez que l’option **Déploiement d’assembly rapide** n’est PAS cochée. Cette option entraîne des problèmes de chargement de ressources. Vous ne devez pas l’utiliser si vous testez des applications localisées.

Pour plus d’informations sur la localisation Android, consultez [Localisation Android](~/android/app-fundamentals/localization.md).

#### <a name="universal-windows-platform"></a>Plateforme Windows universelle

Les projets UWP (plateforme Windows universelle) ne nécessitent pas le service de dépendance. À la place, cette plateforme définit automatiquement la culture de la ressource de manière appropriée.

##### <a name="assemblyinfocs"></a>AssemblyInfo.cs

Développez le nœud Propriétés du projet de bibliothèque .NET Standard, puis double-cliquez sur le fichier **AssemblyInfo.cs**. Ajoutez la ligne suivante au fichier pour définir l’anglais comme langue de ressources neutres de l’assembly :

```csharp
[assembly: NeutralResourcesLanguage("en")]
```

Cela permet d’informer le Gestionnaire des ressources de la culture par défaut de l’application. Ainsi, les chaînes définies dans le fichier RESX indépendant de la langue (**AppResources.resx**) sont correctement affichées quand l’application s’exécute avec l’un des paramètres régionaux anglais.

### <a name="example"></a>Exemple

Après la mise à jour des projets spécifiques à la plateforme, comme indiqué ci-dessus, et la recompilation de l’application avec les fichiers RESX traduits, les traductions mises à jour sont disponibles dans chaque application. Voici une capture d’écran de l’exemple de code traduit en chinois simplifié :

![](text-images/simple-example-hans.png "IU multiplateformes traduites en chinois simplifié")

Pour plus d’informations sur la localisation UWP, consultez [Localisation UWP](/windows/uwp/design/globalizing/globalizing-portal/).

## <a name="localizing-xaml"></a>Localisation XAML

Durant la génération d’une interface utilisateur Xamarin.Forms en XAML, le code de balise ressemble à ceci, avec des chaînes incorporées directement en XML :

```xaml
<Label Text="Notes:" />
<Entry Placeholder="eg. buy milk" />
<Button Text="Add to list" />
```

Dans l’idéal, nous pouvons traduire les contrôles d’interface utilisateur directement dans le code XAML, en créant une *extension de balisage*. Le code d’une extension de balisage qui expose les ressources RESX au format XAML est présenté ci-dessous. Vous devez ajouter cette classe au code commun de Xamarin.Forms (avec les pages XAML) :

```csharp
using System;
using System.Globalization;
using System.Reflection;
using System.Resources;
using Xamarin.Forms;
using Xamarin.Forms.Xaml;

namespace UsingResxLocalization
{
    // You exclude the 'Extension' suffix when using in XAML
    [ContentProperty("Text")]
    public class TranslateExtension : IMarkupExtension
    {
        readonly CultureInfo ci = null;
        const string ResourceId = "UsingResxLocalization.Resx.AppResources";

        static readonly Lazy<ResourceManager> ResMgr = new Lazy<ResourceManager>(
            () => new ResourceManager(ResourceId, IntrospectionExtensions.GetTypeInfo(typeof(TranslateExtension)).Assembly));

        public string Text { get; set; }

        public TranslateExtension()
        {
            if (Device.RuntimePlatform == Device.iOS || Device.RuntimePlatform == Device.Android)
            {
                ci = DependencyService.Get<ILocalize>().GetCurrentCultureInfo();
            }
        }

        public object ProvideValue(IServiceProvider serviceProvider)
        {
            if (Text == null)
                return string.Empty;

            var translation = ResMgr.Value.GetString(Text, ci);
            if (translation == null)
            {
#if DEBUG
                throw new ArgumentException(
                    string.Format("Key '{0}' was not found in resources '{1}' for culture '{2}'.", Text, ResourceId, ci.Name),
                    "Text");
#else
                translation = Text; // HACK: returns the key, which GETS DISPLAYED TO THE USER
#endif
            }
            return translation;
        }
    }
}
```

Les puces suivantes expliquent les éléments importants du code ci-dessus :

- La classe s’appelle `TranslateExtension`, mais par convention, nous pouvons y faire référence à l’aide de **Translate** dans notre code de balise.
- La classe implémente `IMarkupExtension`, qui est nécessaire au bon fonctionnement de Xamarin.Forms.
- `"UsingResxLocalization.Resx.AppResources"` sert d’identificateur de ressource pour nos ressources RESX. Il se compose de notre espace de noms par défaut, du dossier dans lequel se trouvent les fichiers de ressources et du nom de fichier RESX par défaut.
- La classe `ResourceManager` est créée à l’aide de `IntrospectionExtensions.GetTypeInfo(typeof(TranslateExtension)).Assembly)` pour déterminer l’assembly actuel à partir duquel les ressources sont chargées. Cette classe est mise en cache dans le champ statique `ResMgr`. Elle est créée en tant que type `Lazy` pour que sa création soit différée jusqu’à sa première utilisation dans la méthode `ProvideValue`.
- `ci` utilise le service de dépendance pour déterminer la langue choisie par l’utilisateur à partir du système d’exploitation natif.
- `GetString` est la méthode qui récupère la chaîne réelle traduite à partir des fichiers de ressources. Sur la plateforme Windows universelle, `ci` a une valeur null, car l’interface `ILocalize` n’est pas implémentée sur ces plateformes. Cela équivaut à appeler la méthode `GetString` avec seulement le premier paramètre. À la place, le framework de ressources reconnaît automatiquement les paramètres régionaux et récupère la chaîne traduite à partir du fichier RESX approprié.
- La gestion des erreurs a été incluse pour permettre le débogage des ressources manquantes en levant une exception (en mode `DEBUG` uniquement).

L’extrait XAML suivant montre comment utiliser l’extension de balisage. Deux étapes sont nécessaires à son fonctionnement :

1. Déclarez l’espace de noms personnalisé `xmlns:i18n` dans le nœud racine. `namespace` et `assembly` doivent correspondre exactement aux paramètres de projet. Dans cet exemple, ils sont identiques mais ils peuvent être différents dans votre projet.
2. Utilisez la syntaxe `{Binding}` pour les attributs qui contiennent normalement du texte afin d’appeler l’extension de balisage `Translate`. La clé de ressource est le seul paramètre obligatoire.

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        x:Class="UsingResxLocalization.FirstPageXaml"
        xmlns:i18n="clr-namespace:UsingResxLocalization;assembly=UsingResxLocalization">
    <StackLayout Padding="0, 20, 0, 0">
        <Label Text="{i18n:Translate NotesLabel}" />
        <Entry Placeholder="{i18n:Translate NotesPlaceholder}" />
        <Button Text="{i18n:Translate AddButton}" />
    </StackLayout>
</ContentPage>
```

La syntaxe plus détaillée suivante est également valide pour l’extension de balisage :

```xaml
<Button Text="{i18n:TranslateExtension Text=AddButton}" />
```

## <a name="localizing-platform-specific-elements"></a>Localisation d’éléments spécifiques à la plateforme

Bien que nous puissions prendre en charge la traduction de l’interface utilisateur dans le code de Xamarin.Forms, certains éléments doivent être traduits dans chaque projet spécifique à la plateforme. Cette section décrit la localisation :

- Application Name
- Images

L’exemple de projet inclut une image localisée appelée **flag.png**, référencée en C# comme suit :

```csharp
var flag = new Image();
switch (Device.RuntimePlatform)
{
    case Device.iOS:
    case Device.Android:
        flag.Source = ImageSource.FromFile("flag.png");
        break;
    case Device.UWP:
        flag.Source = ImageSource.FromFile("Assets/Images/flag.png");
        break;
}
```

L’image du drapeau est également référencée en XAML comme suit :

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

Toutes les plateformes résolvent automatiquement les références d’images de ce type en versions localisées des images, à condition que les structures de projet décrites ci-dessous soient implémentées.

### <a name="ios-application-project"></a>Projet d’application iOS

iOS utilise un standard de nommage pour désigner les répertoires qui contiennent les ressources d’images et de chaînes : il s’agit des répertoires de projets de localisation ou des répertoires **.lproj**. Ces répertoires peuvent contenir des versions localisées des images utilisées dans l’application ainsi que le fichier **InfoPlist.strings** qui permet de localiser le nom de l’application. Pour plus d’informations sur la localisation iOS, consultez [Localisation iOS](~/ios/app-fundamentals/localization/index.md).

#### <a name="images"></a>Images

Cette capture d’écran montre l’exemple d’application iOS et les répertoires **.lproj** spécifiques à la langue. Le répertoire espagnol appelé **es.lproj** contient les versions localisées de l’image par défaut ainsi que **flag.png** :

![](text-images/ios-resources.png "Répertoires de projets de localisation iOS")

Chaque répertoire de langue contient une copie de **flag.png** localisée pour cette langue. Si aucune image n’est fournie, le système d’exploitation utilise par défaut l’image du répertoire de langue par défaut. Pour une prise en charge complète de Retina, vous devez fournir des copies **@2x** et **@3x** de chaque image.

#### <a name="app-name"></a>Nom de l’application

Le contenu du fichier **InfoPlist.strings** correspond simplement à une paire clé-valeur qui permet de configurer le nom de l’application :

```csharp
"CFBundleDisplayName" = "ResxEspañol";
```

Quand l’application s’exécute, le nom et l’image de l’application sont tous deux localisés :

![](text-images/ios-imageicon.png "Exemple de localisation d’image et de texte d’application pour iOS")

### <a name="android-application-project"></a>Projet d’application Android

Android suit un autre schéma pour stocker les images localisées. Il utilise des répertoires **drawable** et **strings** distincts avec un suffixe de code de langue. Quand un code de paramètres régionaux à quatre lettres doit être fourni (par exemple zh-TW ou pt-BR), Android nécessite l’ajout d’un **r** après le tiret/avant le code de paramètres régionaux (par exemple zh-rTW ou pt-rBR). Pour plus d’informations sur la localisation Android, consultez [Localisation Android](~/android/app-fundamentals/localization.md).

#### <a name="images"></a>Images

Cette capture d’écran montre l’exemple Android avec des Drawables et des chaînes localisés :

![](text-images/android-resources.png "Répertoires de Drawables et de chaînes localisés pour Android")

Notez qu’Android n’utilise pas les codes zh-Hans et zh-Hant pour le chinois simplifié et le chinois traditionnel. À la place, il prend en charge uniquement les codes zh-CN et zh-TW spécifiques au pays.

Pour permettre la prise en charge des différentes résolutions d’images sur les écrans haute densité, créez des dossiers de langue supplémentaires avec les suffixes `-*dpi`, par exemple **drawables-es-mdpi**, **drawables-es-xdpi**, **drawables-es-xxdpi**, etc. Pour plus d’informations, consultez [Fourniture de ressources Android de remplacement](https://developer.android.com/guide/topics/resources/providing-resources.html#AlternativeResources).

#### <a name="app-name"></a>Nom de l’application

Le contenu du fichier **strings.xml** correspond simplement à une paire clé-valeur qui permet de configurer le nom de l’application :

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">ResxEspañol</string>
</resources>
```

Mettez à jour **MainActivity.cs** dans le projet d’application Android pour que `Label` référence le code XML des chaînes.

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true,
        ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
```

L’application localise désormais l’image et le nom de l’application. Voici une capture d’écran du résultat (en espagnol) :

![](text-images/android-imageicon.png "Exemple de localisation d’image et de texte d’application pour Android")

### <a name="universal-windows-platform-application-projects"></a>Projets d’application de la plateforme Windows universelle

La plateforme Windows universelle comporte une infrastructure de ressources qui simplifie la localisation des images et du nom de l’application. Pour plus d’informations sur la localisation UWP, consultez [Localisation UWP](/windows/uwp/design/globalizing/globalizing-portal/).

#### <a name="images"></a>Images

Vous pouvez localiser les images en les plaçant dans un dossier spécifique à la ressource, comme illustré dans la capture d’écran suivante :

![](text-images/uwp-image-folder-structure.png "Structure de dossiers de localisation d’image UWP")

Au moment de l’exécution, l’infrastructure de ressources Windows sélectionne l’image appropriée en fonction des paramètres régionaux de l’utilisateur.

## <a name="summary"></a>Récapitulatif

Vous pouvez localiser les applications Xamarin.Forms à l’aide de fichiers RESX et de classes de globalisation .NET. À l’exception d’une petite portion de code spécifique à la plateforme pour détecter la langue préférée par l’utilisateur, l’essentiel de l’effort de localisation est centralisé dans le code commun.

Les images sont généralement gérées de manière spécifique à la plateforme pour tirer parti de la prise en charge multirésolution fournie dans iOS et Android.

## <a name="related-links"></a>Liens associés

- [Exemple de localisation de fichier RESX](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingresxlocalization)
- [Exemple d’application TodoLocalized](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todolocalized)
- [Localisation multiplateforme](~/cross-platform/app-fundamentals/localization.md)
- [Localisation iOS](~/ios/app-fundamentals/localization/index.md)
- [Localisation Android](~/android/app-fundamentals/localization.md)
- [Localisation UWP](/windows/uwp/design/globalizing/globalizing-portal/)
- [Utilisation de la classe CultureInfo (MSDN)](https://msdn.microsoft.com/library/87k6sx8t%28v=vs.90%29.aspx)
- [Localisation et utilisation de ressources pour une culture spécifique (MSDN)](https://msdn.microsoft.com/library/s9ckwb4b%28v=vs.90%29.aspx)
