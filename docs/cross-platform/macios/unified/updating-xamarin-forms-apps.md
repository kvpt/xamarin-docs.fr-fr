---
title: La mise à jour des applications Xamarin.Forms existantes
description: Ce document décrit les étapes qui doivent être remplies pour mettre à jour une application Xamarin.Forms à partir de l’API classique à l’API unifiée.
ms.prod: xamarin
ms.assetid: C2F0D1D1-256D-44A4-AAC9-B06A0CB41E70
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 36a4c6b66f7f724bfccc3c2a3b81c17f1d34a9c5
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67829736"
---
# <a name="updating-existing-xamarinforms-apps"></a>La mise à jour des applications Xamarin.Forms existantes

_Suivez ces étapes pour mettre à jour une application Xamarin.Forms existante pour utiliser l’API unifiée et mettre à jour vers la version 1.3.1_

> [!IMPORTANT]
> Xamarin.Forms 1.3.1 étant la première version qui prend en charge de l’API unifiée, l’ensemble de la solution doit être actualisé pour utiliser la dernière version en même temps que la migration de l’application iOS à unifiée. Cela signifie qu’en plus de la mise à jour le projet iOS pour la prise en charge unifié, vous devez également modifier le code dans _tous les_ les projets dans la solution.

La mise à jour est effectuée en deux étapes :

1. Migrer l’application iOS à l’API unifiée à l’aide de Visual Studio pour la build du Mac dans l’outil de migration.

    - Utilisez l’outil de migration pour mettre à jour automatiquement le projet.

    - Mise à jour iOS native API comme indiqué dans les instructions pour [mettre à jour des applications iOS](~/cross-platform/macios/unified/updating-ios-apps.md) (en particulier dans le Générateur de code personnalisé ou dépendance service).

2. Mettez à jour l’ensemble de la solution Xamarin.Forms version 1.3.

    1. Installez le package NuGet Xamarin.Forms 1.3.1.

    2. Mise à jour le `App` classe dans le code partagé.

    3. Mise à jour le `AppDelegate` dans le projet iOS.

    4. Mise à jour le `MainActivity` dans le projet Android.

    5. Mise à jour le `MainPage` dans le projet Windows Phone.

## <a name="1-ios-app-unified-migration"></a>1. iOS App (Unified Migration)

Partie de la migration nécessite une mise à niveau de Xamarin.Forms vers la version 1.3, qui prend en charge de l’API unifiée. Dans l’ordre pour les références d’assembly correct doit être créé, nous devons d’abord mettre à jour le projet iOS pour utiliser l’API unifiée.

### <a name="migration-tool"></a>Outil de migration

Cliquez sur le projet iOS afin qu’il est sélectionné, puis choisissez **projet > migrer vers l’API unifiée Xamarin.iOS...**  et acceptez le message d’avertissement qui s’affiche.

![](updating-xamarin-forms-apps-images/beta-tool1.png "Choisissez le projet > migrer vers l’API unifiée Xamarin.iOS... et j’accepte le message d’avertissement qui s’affiche")

Recevez automatiquement :

- Modifier le type de projet pour prendre en charge de l’API unifiée de 64 bits.
- Modifier la référence framework **Xamarin.iOS** (en remplaçant l’ancien **monotouch** référence).
- Modifiez les références d’espace de noms dans le code pour supprimer le `MonoTouch` préfixe.
- Mise à jour le **csproj** fichier à utiliser les cibles de génération correcte pour l’API unifiée.

**Nettoyer** et **Build** le projet pour vous assurer qu’aucune autre erreur à corriger. Aucune action supplémentaire ne doit être requise. Ces étapes sont expliquées plus en détail dans le [documents API unifiée](~/cross-platform/macios/unified/updating-ios-apps.md).

### <a name="update-native-ios-apis-if-required"></a>Mettre à jour des API iOS natif (si nécessaire)

Si vous avez ajouté le code natif iOS supplémentaires (par exemple, les convertisseurs personnalisés ou des services de dépendance) vous devrez peut-être effectuer des corrections de code manuelle supplémentaire. Recompilez votre application et reportez-vous à la [iOS mise à jour existant des instructions applications](~/cross-platform/macios/unified/updating-ios-apps.md) pour plus d’informations sur les modifications peuvent être nécessaires. [Ces conseils](~/cross-platform/macios/unified/updating-tips.md) aidera également à identifier les modifications requises.

## <a name="2-xamarinforms-131-update"></a>2. Mise à jour de Xamarin.Forms 1.3.1

Une fois l’application iOS a été mis à jour vers l’API unifiée, le reste de la solution doit être mis à jour vers la version 1.3.1 de Xamarin.Forms. notamment :

- La mise à jour le package Xamarin.Forms NuGet dans chaque projet.
- La modification du code pour utiliser le nouveau Xamarin.Forms `Application`, `FormsApplicationDelegate` (iOS), `FormsApplicationActivity` (Android), et `FormsApplicationPage` les classes (Windows Phone).

Ces étapes sont expliquées ci-dessous :

### <a name="21-update-nuget-in-all-projects"></a>2.1 NuGet de mise à jour dans tous les projets

Mettre à jour Xamarin.Forms à 1.3.1 de la préversion à l’aide du Gestionnaire de Package NuGet pour tous les projets dans la solution : Bibliothèque de classes portable (le cas échéant), iOS, Android et Windows Phone. Il est recommandé que vous avez **supprimer et rajouter** le package Xamarin.Forms NuGet pour mettre à jour vers la version 1.3.

> [!NOTE]
> Xamarin.Forms version 1.3.1 est actuellement en *préliminaires*. Cela signifie que vous devez sélectionner le **préliminaires** option dans NuGet via (une case à cocher-dans Visual Studio pour Mac) ou une-liste déroulante dans Visual Studio pour afficher la dernière version préliminaire.

> [!IMPORTANT]
> Si vous utilisez Visual Studio, vérifiez que la dernière version du Gestionnaire de Package NuGet est installée. Les versions antérieures de NuGet dans Visual Studio installera pas correctement la version unifiée de Xamarin.Forms 1.3.1. Accédez à **Outils > Extensions et mises à jour...**  , puis cliquez sur le **installé** liste pour vérifier que le **le Gestionnaire de Package NuGet pour Visual Studio** est au moins version 2.8.5. Si elle est plus ancienne, cliquez sur le **mises à jour** liste pour télécharger la dernière version.

Une fois que vous avez mis à jour le package NuGet à Xamarin.Forms 1.3.1, apportez les modifications suivantes dans chaque projet à mettre à niveau vers la nouvelle `Xamarin.Forms.Application` classe.

### <a name="22-portable-class-library-or-shared-project"></a>2.2 bibliothèque de classes portable (ou un projet partagé)

Modifier le **App.cs** fichier afin que :

- Le `App` classe hérite désormais de `Application`.
- Le `MainPage` propriété est définie sur la première page de contenu que vous souhaitez afficher.

```csharp
public class App : Application // superclass new in 1.3
{
    public App ()
    {
        // The root page of your application
        MainPage = new ContentPage {...}; // property new in 1.3
    }
```

Nous avons supprimé complètement le `GetMainPage` (méthode), mais de définir le `MainPage` *propriété* sur la `Application` sous-classe.

Cette nouvelle `Application` classe de base prend également en charge la `OnStart`, `OnSleep`, et `OnResume` des remplacements afin de vous aider à gérer le cycle de vie de votre application.

Le `App` classe est ensuite transmise à un nouveau `LoadApplication` méthode dans chaque projet d’application, comme décrit ci-dessous :

### <a name="23-ios-app"></a>Application iOS de 2,3

Modifier le **AppDelegate.cs** fichier afin que :

- La classe hérite de `FormsApplicationDelegate` (au lieu de `UIApplicationDelegate` précédemment).
- `LoadApplication` est appelé avec une nouvelle instance de `App`.

```csharp
[Register ("AppDelegate")]
public partial class AppDelegate :
    global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate // superclass new in 1.3
{
    public override bool FinishedLaunching (UIApplication app, NSDictionary options)
    {
        global::Xamarin.Forms.Forms.Init ();

        LoadApplication (new App ());  // method is new in 1.3

        return base.FinishedLaunching (app, options);
    }
}
```

### <a name="23-android-app"></a>2.3 application android

Modifier le **MainActivity.cs** fichier afin que :

- La classe hérite de `FormsApplicationActivity` (au lieu de `FormsActivity` précédemment).
- `LoadApplication` est appelé avec une nouvelle instance de `App`

```csharp
[Activity (Label = "YOURAPPNAM", Icon = "@drawable/icon", MainLauncher = true,
ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
public class MainActivity :
    global::Xamarin.Forms.Platform.Android.FormsApplicationActivity // superclass new in 1.3
{
    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        global::Xamarin.Forms.Forms.Init (this, bundle);

        LoadApplication (new App ()); // method is new in 1.3
    }
}
```

### <a name="24-windows-phone-app"></a>2.4 Windows Phone application

Nous devons mettre à jour le **MainPage** -le XAML et le code-behind.

Modifier le **MainPage.xaml** fichier afin que :

- Élément racine XAML doit être `winPhone:FormsApplicationPage`.
- Le `xmlns:phone` attribut doit être *modifié* à `xmlns:winPhone="clr-namespace:Xamarin.Forms.Platform.WinPhone;assembly=Xamarin.Forms.Platform.WP8"`

Voici un exemple de mise à jour ; vous devez uniquement avoir à modifier ces éléments (le reste des attributs doit rester le même) :

```xml
<winPhone:FormsApplicationPage
   ...
   xmlns:winPhone="clr-namespace:Xamarin.Forms.Platform.WinPhone;assembly=Xamarin.Forms.Platform.WP8"
    ...>
</winPhone:FormsApplicationPage>
```

Modifier le **MainPage.xaml.cs** fichier afin que :

- La classe hérite de `FormsApplicationPage` (au lieu de `PhoneApplicationPage` précédemment).
- `LoadApplication` est appelé avec une nouvelle instance de la Xamarin.Forms `App` classe. Vous devrez peut-être qualifier entièrement cette référence dans la mesure où Windows Phone a son propre `App` classe déjà définie.

```csharp
public partial class MainPage : global::Xamarin.Forms.Platform.WinPhone.FormsApplicationPage // superclass new in 1.3
{
    public MainPage()
    {
        InitializeComponent();
        SupportedOrientations = SupportedPageOrientation.PortraitOrLandscape;

        global::Xamarin.Forms.Forms.Init();
        LoadApplication(new YOUR_APP_NAMESPACE.App()); // new in 1.3
    }
 }
```

### <a name="troubleshooting"></a>Résolution de problèmes

Parfois, vous verrez une erreur similaire à ceci après la mise à jour le package Xamarin.Forms NuGet. Cela se produit lorsque la mise à jour de NuGet ne supprime pas complètement les références aux anciennes versions de votre **csproj** fichiers.

>VOTRE\_PROJECT.csproj : Erreur : Ce projet fait référence à des packages NuGet qui sont manquants sur cet ordinateur. Activer la restauration des packages NuGet pour les télécharger.  Pour plus d'informations, consultez http://go.microsoft.com/fwlink/?LinkID=322105. Le fichier manquant est... /.. /packages/Xamarin.Forms.1.2.3.6257/build/portable-Win+net45+wp80+MonoAndroid10+MonoTouch10/Xamarin.Forms.targets. (VOTRE\_PROJET)

Pour corriger ces erreurs, ouvrez le **csproj** fichier dans un éditeur de texte et recherchez `<Target` les éléments qui font référence aux anciennes versions de Xamarin.Forms, telles que l’élément indiqué ci-dessous. Vous devez supprimer manuellement cet élément tout entier à partir de la **csproj** fichier et enregistrez les modifications.

```csharp
  <Target Name="EnsureNuGetPackageBuildImports" BeforeTargets="PrepareForBuild">
    <PropertyGroup>
      <ErrorText>This project references NuGet package(s) that are missing on this computer. Enable NuGet Package Restore to download them.  For more information, see http://go.microsoft.com/fwlink/?LinkID=322105. The missing file is {0}.</ErrorText>
    </PropertyGroup>
    <Error Condition="!Exists('..\..\packages\Xamarin.Forms.1.2.3.6257\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10\Xamarin.Forms.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\..\packages\Xamarin.Forms.1.2.3.6257\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10\Xamarin.Forms.targets'))" />
  </Target>
```

Le projet doit être généré avec succès une fois que ces anciennes références sont supprimés.

## <a name="considerations"></a>Considérations

Les considérations suivantes doivent être prises en compte lors de la conversion d’un projet Xamarin.Forms existant à partir de l’API classique à la nouvelle API unifiée si cette application s’appuie sur un ou plusieurs composants ou un NuGet Package.

### <a name="components"></a>Composants

N’importe quel composant que vous avez inclus dans votre application doit également être mis à jour vers l’API unifiée ou vous obtiendrez un conflit lorsque vous essayez de compiler. Pour les composants inclus, remplacez la version actuelle par une nouvelle version à partir du Store de composant Xamarin qui prend en charge de l’API unifiée et effectuer un nettoyage de build. Tout composant qui n’a pas encore été converti par l’auteur, affichera un 32 bits uniquement avertissement dans le magasin de composants.

### <a name="nuget-support"></a>Prise en charge par NuGet

Bien que nous contribué les modifications apportées à NuGet pour travailler avec la prise en charge de l’API unifiée, il n’a pas été une nouvelle version de NuGet, donc nous évaluons l’obtention de NuGet pour reconnaître les nouvelles API.

En attendant, tout comme les composants, vous devrez passer n’importe quel NuGet Package que vous avez inclus dans votre projet vers une version qui prend en charge les API unifiée et effectuer un nettoyage de build par la suite.

> [!IMPORTANT]
> Si vous avez une erreur dans le formulaire _« erreur 3 ne peut pas inclure 'monotouch.dll' et 'Xamarin.iOS.dll' dans le même projet Xamarin.iOS : 'Xamarin.iOS.dll' est référencé explicitement, alors que « monotouch.dll » est référencé par ' xxx, Version = 0.0.000, Culture = neutral, PublicKeyToken = null » «_ après la conversion de votre application à l’API unifiée, il est généralement dû à avoir un composant ou un NuGet Package dans le projet qui n’a pas été mis à jour vers l’API unifiée. Vous devez supprimer le composant existant/NuGet, mise à jour vers une version qui prend en charge les API unifiée, puis effectuez une génération propre.

## <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>L’activation de 64 bits génère des applications Xamarin.iOS

Pour une application mobile Xamarin.iOS qui a été convertie vers l’API unifiée, le développeur doit toujours autorisent la création de l’application pour les ordinateurs 64 bits à partir des Options de l’application. Consultez la **l’activation de 64 bits s’appuie de Xamarin.iOS des applications** de la [considérations relatives à la plateforme 32/64 bits](~/cross-platform/macios/32-and-64/index.md#enable-64) génère de document pour obtenir des instructions détaillées sur l’activation de 64 bits.

## <a name="summary"></a>Récapitulatif

L’application Xamarin.Forms doit maintenant être mis à jour vers la version 1.3.1 et l’application iOS migrés vers l’API unifiée (qui prend en charge des architectures 64 bits sur la plateforme iOS).

Comme indiqué ci-dessus, si votre application Xamarin.Forms inclut le code natif telles que les convertisseurs personnalisés ou ces devrez peut-être également mettre à jour pour utiliser les nouveaux Types de services de dépendance [introduite dans l’API unifiée](~/cross-platform/macios/index.md).

## <a name="related-links"></a>Liens associés

- [La mise à jour des applications iOS](~/cross-platform/macios/unified/updating-apps.md)
- [La mise à jour des applications iOS](~/cross-platform/macios/unified/updating-ios-apps.md)
- [Utilisation de types natifs dans des applications multiplateformes](~/cross-platform/macios/native-types-cross-platform.md)
- [Conseils de mise à jour](~/cross-platform/macios/unified/updating-tips.md)
- [Différences d’API unifiée de vs classiques](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
